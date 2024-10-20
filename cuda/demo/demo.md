<!--
 * @Author: liu kang
 * @Date: 2024-10-20 16:12:59
 * @LastEditors: faaaade
 * @LastEditTime: 2024-10-20 22:29:02
 * @FilePath: \Notes\cuda\demo\demo.md
 * @Description: 
 * 
 * Copyright (c) 2024 by ${git_name_email}, All Rights Reserved. 
-->
# cuda demo

```cpp
float* devPtr;
int pitch;
cudaMallocPitch((void**)&devPtr, &pitch, width*sizeof(float), height);
myKernel<<<100, 512>>> (devPtr, pitch);

//device code
__goldal__ void myKernel(float* devPtr, int pitch){
    for(int r=; r<height; ++r){
        float* row = (float*)((char*)devPtr + r * pitch);
        for(int c =0; c<width; ++c){
            float element = row[c];
        }
    }
}

cudaChannelFormatDescchannelDesc = cudaCreateChannelDesc<float>();
cudaArray* cuArray;
cudaMallocArray(&cuArray, &channelDesc, width, height);



```

//流管理器
```cpp
cudaStream_t stream[2];

//创建两个流
for(int i=0; i<2; ++i){
    cudaStreamCreate(&stream[i]);

}

//执行流
for(int i=0; i<2; ++i){
    cudaMemcpyAsync(inputDevPtr+i*sze, hostPtr+i*size,size, cudaMemcpyHostToDevice, stream[i]);
}
for(int i=0; i<2 ;++i)
    myKernel<<<100, 512, 0, stream[i]>>>
        (outputDevPtr+i*size, inputDevPtr+i*size, size);
for(int i=0; i<2; ++i){
    cudaMemcyAsync(hostPtr + i*size, outputDevPtr + i*size, size, cudaMemcpyDeviceToHost,stream[i]);
cudaThreadSynvhronize();
}
```