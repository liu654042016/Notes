<!--
 * @Author: liu kang
 * @Date: 2024-10-22 23:21:58
 * @LastEditors: faaaade
 * @LastEditTime: 2024-10-23 23:50:38
 * @FilePath: \Notes\cuda\cuda-learn-note\relu.md
 * @Description: 
 * 
 * Copyright (c) 2024 by ${git_name_email}, All Rights Reserved. 
-->
# relu 
## naive relu fp32 
relu x:N, y:N y=nax(0,x)
grid(N/256) block(256)
```cpp
__global__ void relu_fp32_kernel(float* x, float* y, int N){
    int idx = blockIdx.x*blockDim.x + threadIdx.x;
    if(idx<N) y[idx] = fmax(o, x[idx]);
}
```
## relu fp32 vec4
relu x:N , y:N y=max(0,x) vec4
grid(N/256/4), block(256/4)
```cpp
__gloal__ void relu_fp32x4_kernel(float* x, float* y, int N){
    int idx = 4 *  (blockIdx.x * blockDim.x + threadIdx.x);
    if(idx < N){
        float4 reg_x = FLOAT4(x[idx]);
        float4 reg_y;
        reg_y.x = fmaxf(0.0f, reg_x.x);
        reg_y.y = fmaxf(0.0f, reg_x.y);
        reg_y.z = fmaxf(0.0f, reg_x.z);
        reg_y.w = fmaxf(0.0f, reg_x.w);

        FLOAT4(y[idx]) = reg_y;
    }

}


```
## relu fp6 vec2
```cpp
__global__ void relu_fp16x2_kernel(half* xx, half* y, int N){
    int idx = 2 * (blockIdx.x*blockDim.x + threadIdx.x);
    if(idx < N){
        half2 reg_x = HALF2(x[idx]);
        half2 reg_y = HALF2(y[idx]);

        reg_y.x = _hmax(_float2half(0.0f), reg_x.x);
        reg_y.y = _hmax(_float2half(0.0f), reg_x.y);

        HALF2(y[idx]) = reg_y;
    }
}
```
## relu fp16 pack
```cpp
__global__ void relu_fp16x8_pack_kernel(half* x, half* y, int N){
    int idx = 8 * (blockIdx.x * blockDim.x + threadIdx.x);
    const half2 z2 = (__float2half(0.0f), __float2half(__0.0f));

    half pack_x[8], pack_y[8];

    LDST128BITS(pack_x[0]) = LDST128BITS(x[idx]);
    #pragma unroll
    for(int i=0; i<8 ;i++){
        HALF2(pack_y[i]) = _hamx2(HALF2(pack_x[i]), z2);
    }

    if((idx + 7) < N){LDST128BITS(y[idx]  LDST128BITS（pack_y[0])}
}

```

