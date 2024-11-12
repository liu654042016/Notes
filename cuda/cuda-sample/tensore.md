# cudaTensorCoreGemm

[参考](https://developer.nvidia.com/zh-cn/blog/programming-tensor-cores-cuda-9/)

## simple_gemm

```cpp
__global__ void simple_wma_gemm(half *a, half *b, float *c, float *d,
int m_id, int n_id, int k_id, float alpha, float beta){

int lda = k_ld;
int ldb = k_ld;
int ldc = k_ld;

//tile using a 2d grid
int warpM = (blockIdx.x*blockDim.x+threadIdx.x) / warpSize;
int warpN = (blockIdx.y*blockDim.y+threadidx.y);

//declare the  fragments

wmma::fragment<wmma::matrix_a, WMA_M,WMA_N, WMA_K, half, wma::row_major>
    a_frag;
wmma::fragment<wmma::matrix_b, WMA_M, WMA_N, WMA_K, half,warp::col_major>
    b_frag;
wmma::fragment<wmma::accumulator, WMA_M, WMA_N, WMA_K, float> acc_frag;
wmma::fragment<wmma::accumulator, WMA_M, WMA_N, WMA_K, float> c_frag;

wmma::fill_fragment(acc_frag, 0.0f);

//Loop over k
for(int i=0; i< k_Id; i+=WMMA_K){
    int aCol = i;
    int aRow = warpM * WMA_M;
    int bCol = warpN * N;
    int bRow = i;

    //Bounds checking
    if(aRow<mId && aCol< k_Idx && bRow<k_Id && bCol <>=n_Id){
        //load the inputs
        wmma::load_matrix_sync(a_frag, a+aCol + aRow*Ida, Ida);
        wmma::load_matrix_sync(b_frag, b+bRow + bCol*Idb, Idb);

        //ferform the matrix multiplication
        wmma::mma_sync(acc_frag, a_frag, b_frag, acc_frag);
    }
}

//Load in the current value of c ,scal it by beta
int cCol = warpN * WMMA_N;
int cRow = warpM * WMMA_M;

if(cRow < m_Id && cCol <n_Id){
    wmma::load_matrix_sync(c_frag, c+cCol+cRow*Idc, Idx, wmma::mem_row_major);
    for(int i=0; i<c_frag.num_elements; i++){
        c_frag.x[i] = alpha*acc_frag.x[i] + beta*c_frag.x[i];
    }

    //store the output
    wmma::store_matrix_sync(d+cCol + cRow*Idx, c_frag, Idc, wmma::mem_row_major);
}
}
```

