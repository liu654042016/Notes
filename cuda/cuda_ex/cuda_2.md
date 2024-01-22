1. 第一个 gpu 程序
*  平方和
```cpp
__global__ void square(float* d_out, float* d_in){
    int idx = threadIdx.x;
    float f = d_in[idx];
    d_out[idx] = f*f;
}
int main(int argc, char** argv){
    const int ARRAY_SIZE = 64;
    const int ARRAY_BYTES = ARRAY_SIZE * sizeof(float);

    float h_in{ARRAY_SIZE};
    for(int i=0; i<ARRAY_SIZE ; i++){
        h_in[i] = float(i);
    }
    float h_out{ARRAY_SIZE};

    // declare GPU memory pointers
    float* d_in;
    float* d_out;

    cudaMalloc((void**) &d_in, ARRAY_SIZE);
    cudaMalloc((void**) &d_out, ARRAY_SIZE);

    cudaMemcpy(d_int, h_in, ARRAY_BYTES, cudaMemcpyHostToDevice);

    square<<<1, ARRAY_SIZE>>>(d_out, d_in);

    cudaMemcpy(h_out, d_out, ARRAY_SIZE, cudaMemcpyDeviceToHost);

    for(int i =0; i<ARRAY_SIZE; i++){

    }

    cudaFree(d_in);
    cudaFree(d_out);
}
```
* kernel 加载 - 1d 模式
```cpp
1. 网格 (grid) 是 1d 的，线程(block)是1D
int idx = blockIdx.x*blockDim.x+threadIdx.x;
//加载方式
Kernel<<<numBlock, threadxPerBlock>>>(argv)
2. 网格是 1 D，线程块是2D
int idx = blockIdx.x*blockDim.x*blockDim.y + threadIdx.y*blockDim.x+threadIdx.x;
//加载方式
dim3 dmBlock(x,y);
Kernel<<<numBlock, dimBlock>>>(argv);
3. 网格是1d的， 线程块是3d的
int idx = blockIdx.x*blockDim.x*blockDim.y*blockDim.z+threadIdx.z*blockDim.y*blockDim.x+threadIdx.y*blockDim.x+threadIdx.x
加载方式
dim3 dimBlock(x,y,z)
Kernel<<<numBlock, dimBlock>>>(argv)
```
* kernel 加载 - 2d 模式
```cpp
1.网格是2d,线程块是1d的
int blockId = blockIdx.y * gridDim.x + blockIdx.x;
int Idx = blockId * blockDim.x + threadIdx.x;
//加载
dim3 dimGrid(x,y);
Kernel<<<dimGrid, threadsPerBlock>>>(argv);
2. 网格是2d，线程块是2d
int blockId = blockIdx.y * gridDim.x + blockIdx.x;
int Idx = blockId * (blockDim.x * blockDim.y) + (threadIdx.y * blockDim.x) + threadIdx.x;
//加载
dim3 dimGrid(x1,y1), dimBlock(x2, y2);
Kernel<<<dimGrid, dimBlock>>>(argv);
3. 网格是 2d 线程块是3d
int blockId = blockIdx.y * gridDim.x + blockIdx.x;
int Idx = blockId * (blockDim.x * blockDim.y * blockDim.z) + (threadIdx.z * (blockDim.x*blockDim.y) )+ (threadIdx.y*blockDim.x) + threadIdx.x;
//加载
dim3 dimGrid(x1, y1), dimBlock(x2, y2, z2);
Kernel<<<dimGrid, dimBlock>>>(argv);
```
## 3.cuda 中的各种内存代码的使用
### 3.1 本地变量
```cpp
__global__ void use_local_memory_GPU(float in){
    float f;
    f = in;
}

use_local_memory_GPU<<<1,128>>>(2.0f);
```
### 3.2 全局变量
```cpp
__global__ void use_global_memory_GPU(float *array){
    array[threadIdx.x] = 2.0f * (float) threadIdx.x;
}

float h_arr[128];
float *d_arr;

cudaMolloc((void**) &d_arr, sizeof(float)*128)；
cudaMemcpy((void*)d_arr, (void *)h_arr, sizeof(float)*128, cudaMemcpyHostToDevice);
use_global_memory_GPU<<<1,128>>>(d_arr);
cudaMemcpy((void*)h_arr, (void*)d_arr,sizeof(float)*128, cudaMemcpyDeviceToHost);

```
### 3.3 共享变量
```cpp
__global__ void use_shared_memory_GPU(float *array){
    int i, index = threadIdx.x;
    float average , sum = 0.0f;

    //lifetime as the thread block
    sh_arr[index] = array[index];
    __syncthreads();

    for(i=0; i<index; i++){sum+=sh_arr[i];}
    average = sum /(index + 1.0f);

    if(array[index] > average){array[index] = average;}
    sh_arr[index] = 3.14;
}
use_shared_memory_GPU<<<1,128>>>(d_arr);
cudaMemcpy((void*)h_arr, (void*)d_arr, sieof(float)*128, cudaMemcpyHostToDevice);

```
## 4 cuda 同步操作
### 4.1 原子操作
```cpp
__global__ void increment_naive(int *g){
    int i = blockIdx.x * blockDim.x + threadIdx.x;

    i = i % ARRAY_SIZE;
    g[i] = g[i] + 1;
}
__global__ void increment_atomic(int *g){
    int i = blockIdx.x * blockDim.x + threadIdx.x;

    i = i % ARRAY_SIZE;
    atomicAdd(&g[i], 1);
}
```
### 4.2 同步函数
* _syncthreads()
* 线程块内线程同步
* 保证线程块内所有线程都执行到统一位置
```cpp
__global__ void use_shared_memory_GPU(float *array){
    int i, index = threadIdx.x;
    float average, sum = 0.0f;

    __shared__ float sh_arr[128];
    __syncthreads();

    for(i=0; i<index; i++){sum += sh_arr[i];}
    average = sum / (index+1.0f);

    if(array[index] > average) {array[index]=average;}

    sh_arr[index] = 3.14;
}
```
* _threadfence() 执行结果对grid中的所有线程可见
* _threadfence_block()  执行结果对block中的所有线程可见

## 5. 并行化高效策略
### 5.1 归约
```cpp
//使用 global memory
__global__ void global_reduce_kernel(float *d_out, float * d_int){
    int myId = threadIdx.x + blockDim.x * blockIdx.x;
    int tid = threadIdx.x;

    for(unsigned int s = blockDim.x / 2; s > 0; s>>=1){
        if(tid < s){
            d_in[myId] += d_in[myId + s];
        }
        __syncthreads();
    }

    if(tid == 0){
        d_out[blockIdx.x] = d_in[myId];
    }
}
// 使用 shared memory
__global__ void shmem_reduce_kernel(float * d_out, const float * d_in){
    extern __shared__ float sdata[];

    int myId = threadIdx.x + blockDim.x * blockIdx.x;
    int tid = threadIdx.x;

    sdata[tid] = d_in[myId];
    __syncthreads();

    for(unsigned int s = blockDim.x/2 ; s>0 ; s>>=1){
        if(tid < s){
            sdata[tid] += sdata[tid +s];
        }
        __syncthreads();
    }   

    if(tid == 0){
        d_out[blockIdx.x] = sdata[0];
    }

}
```
### 5.2 扫描
```cpp
__global__ void global_scan(float* d_out, float* d_in){
    int idx = threadIdx.x;
    float out = 0.0f;

    d_out[idx] = d_in[idx];
    __syncthreads();
    for(int interpre= 1; interpre<sizeof(d_in); iterpre*=2){
        if(idx - interpre > = 0){
            out = d_out[idx] + d_out[idx - interpre];
        }
        __synctreads();
        if(idx-interpre>=0){
            d_out[idx] = out;
            out = 0.0f;
        }
    }
}
```