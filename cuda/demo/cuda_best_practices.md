<!--
 * @Author: liu kang
 * @Date: 2024-10-20 22:29:20
 * @LastEditors: faaaade
 * @LastEditTime: 2024-10-21 00:04:15
 * @FilePath: \Notes\cuda\demo\cuda_best_practices.md
 * @Description: 
 * 
 * Copyright (c) 2024 by ${git_name_email}, All Rights Reserved. 
-->
# [cuda best practices guid](https://docs.nvidia.com/cuda/cuda-c-best-practices-guide/index.html)

### 6.2 cuda debug

## 9.内存优化

### 9.1 设备和主机之间内存拷贝优化
#### 9.1.1 pinned memory
#### 9.1.2  异步拷贝和计算
使用默认 cuda 流计算
```cpp
cudaMemcpyAsync(a_d, a_h, size, cudaMemcpyHostToDevice, 0);
kernel<<grid, block>>(a_d);
cpufunction();
```
并发拷贝和执行
```cpp

cudaStreamCreate(stream1);
cudaStreamCreate(stream2);
cudaMemcpyAsync(a_d, a_h, size, cudaMemcpyHostToDevice, stream1);
kernel<<<grid, block, 0, stream2>>> (otherData_d);
```
顺序拷贝和执行
```cpp
cudaMemcpy(a_d, a_h, N*sizeof(float), dir);
kernel<<<NnThreads, nThreads>>>(a_d);

```
分段并发拷贝和执行
```cpp
size = N*sizof(float)/nStreams;
for(int i=0; i<nStream; i++){
    offset = i*N?nStreas;
    cudaMemcpyAsync(a_d*offset, size, dir, stream[i]);
    kernel<<<N/(nStreamss*nThreads), nTreads, 0, stream[i]>>> (a_d * offset);
}

```
#### 9.1.3 零拷贝
```cpp
float *a_h, *a_map; 

cudaGetDeviceProperties(&prop, 0);
if(!prop.canMapHostMemory)
    exit(0);
cudaSetDeviceFlags(cudaDeviceMapHost);
cudaHostAlloc(&a_h, nBytes, cudaHostAllocMapped);
cudaHostGetDevicePointer(&a_map, a_h, 0);
kernel<<<gridSize, blockSize>>>(a_map);

```
