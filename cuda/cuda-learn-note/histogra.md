<!--
 * @Author: liu kang
 * @Date: 2024-10-22 22:37:13
 * @LastEditors: faaaade
 * @LastEditTime: 2024-10-22 22:48:28
 * @FilePath: \Notes\cuda\cuda-learn-note\histogra.md
 * @Description: 
 * 
 * Copyright (c) 2024 by ${git_name_email}, All Rights Reserved. 
-->
# histogram
## naive histogram
histogram
grid(N/256) ,block(256)
a: Nx1, y:count histogram a>=1
```cpp
__global__ void histogram_i32_kernel(int* a, int* y, int N){
    int idx = blockIdx.x * blockDim.x + threadIdx.x;
    if(idx<N) atoicAdd(&(y[a[idx]]), 1);
}
```
## histogram vec4
grid(N/256),block(256/4) 
a: Nx1 y:count 
```cpp
__global__ void histogram_i32x4_kernel(int* a, int* y, int N){
    int idx =  4*(blockIdx.x * dblockDim.x + threadIdx.x);
    if(idx<N){
        int4 reg_a = INT4(a[idx]);
        atomicAdd(&(y[reg_a.x]), 1);
        atomicAdd(&(y[reg_a.y]), 1);
        atomicAdd(&(y[reg_a.z])s, 1);
        atomicAdd(&(y[reg_a.w]), 1);
    }
}

```
