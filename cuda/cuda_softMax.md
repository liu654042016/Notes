# [softMax](https://zhuanlan.zhihu.com/p/341059988)

## 每个 Warp 处理一行或两行元素
```cpp
template<typename LOAD, typename STORE, typename ComputeType, int pack_size, int cols_per_thread,
         int thread_group_width, int rows_per_access, bool padding, Algorithm algorithm>
__global__ void SoftmaxWarpImpl(LOAD load, STORE store, const int64_t rows, const int64_t cols) {
  //LOAD、STORE 分别代表输入输出
  //ComputeType 代表计算类型
  //pack_size 代表向量化访存操作的 pack 元素的个数，我们将几个元素 pack 起来读写，提升带宽利用率
  //cols_per_thread 代表每个线程处理的元素个数
  //thread_group_width 代表处理元素的线程组的宽度
  //rows_per_access 代表每个 thread_group 一次处理的行数
  // padding 代表当前是否做了padding
  static_assert(cols_per_thread % pack_size == 0, "");
  static_assert(thread_group_width <= kWarpSize, "");
  static_assert(kWarpSize % thread_group_width == 0, "");


  constexpr int num_packs = cols_per_thread / pack_size;
  assert(cols <= cols_per_thread * thread_group_width);
  ComputeType buf[rows_per_access][cols_per_thread];
  const int global_thread_group_id = blockIdx.x * blockDim.y + threadIdx.y;
  const int num_global_thread_group = gridDim.x * blockDim.y;
  const int lane_id = threadIdx.x;

  //加载一行数据
  for (int64_t row = global_thread_group_id * rows_per_access; row < rows;
       row += num_global_thread_group * rows_per_access) {
    ComputeType thread_max[rows_per_access];
#pragma unroll
    for (int row_id = 0; row_id < rows_per_access; ++row_id) {
      thread_max[row_id] = -Inf<ComputeType>();
      ComputeType* row_buf = buf[row_id];
#pragma unroll
      for (int pack_id = 0; pack_id < num_packs; ++pack_id) {
        const int col = (pack_id * thread_group_width + lane_id) * pack_size;
        if (!padding || col < cols) {
          load.template load<pack_size>(row_buf + pack_id * pack_size, row + row_id, col);
#pragma unroll
          for (int i = 0; i < pack_size; ++i) {
            thread_max[row_id] = max(thread_max[row_id], row_buf[pack_id * pack_size + i]);
          }
        } else {
#pragma unroll
          for (int i = 0; i < pack_size; ++i) {
            row_buf[pack_id * pack_size + i] = -Inf<ComputeType>();
          }
        }
      }
    }
    ComputeType warp_max[rows_per_access];
//计算每行的最大值
#pragma unroll
    for (int row_id = 0; row_id < rows_per_access; ++row_id) {
      warp_max[row_id] = WarpAllReduce<MaxOp, ComputeType, thread_group_width>(thread_max[row_id]);
    }
    ComputeType thread_sum[rows_per_access];
//计算每行的和
#pragma unroll
    for (int row_id = 0; row_id < rows_per_access; ++row_id) {
      thread_sum[row_id] = 0;
      ComputeType* row_buf = buf[row_id];
#pragma unroll
      for (int i = 0; i < cols_per_thread; ++i) {
        if (algorithm == Algorithm::kSoftmax) {
          row_buf[i] = Exp(row_buf[i] - warp_max[row_id]);
          thread_sum[row_id] += row_buf[i];
        } else if (algorithm == Algorithm::kLogSoftmax) {
          row_buf[i] -= warp_max[row_id];
          thread_sum[row_id] += Exp(row_buf[i]);
        } else {
          __trap();
        }
      }
    }
    ComputeType warp_sum[rows_per_access];
#pragma unroll
    for (int row_id = 0; row_id < rows_per_access; ++row_id) {
      warp_sum[row_id] = WarpAllReduce<SumOp, ComputeType, thread_group_width>(thread_sum[row_id]);
    }

    //计算结果
#pragma unroll
    for (int row_id = 0; row_id < rows_per_access; ++row_id) {
      ComputeType* row_buf = buf[row_id];
#pragma unroll
      for (int i = 0; i < cols_per_thread; ++i) {
        if (algorithm == Algorithm::kSoftmax) {
          row_buf[i] = Div(row_buf[i], warp_sum[row_id]);
        } else if (algorithm == Algorithm::kLogSoftmax) {
          row_buf[i] -= Log(warp_sum[row_id]);
        } else {
          __trap();
        }
      }
    //计算存储
#pragma unroll
      for (int i = 0; i < num_packs; ++i) {
        const int col = (i * thread_group_width + lane_id) * pack_size;
        if (!padding || col < cols) {
          store.template store<pack_size>(row_buf + i * pack_size, row + row_id, col);
        }
      }
    }
  }
}


```