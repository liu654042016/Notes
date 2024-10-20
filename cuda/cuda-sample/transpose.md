<!--
 * @Author: liu kang
 * @Date: 2024-10-15 22:10:40
 * @LastEditors: faaaade
 * @LastEditTime: 2024-10-19 23:30:21
 * @FilePath: \Notes\cuda\cuda-sample\transpose.md
 * @Description: transpose cuda 的实现
 * 
 * Copyright (c) 2024 by ${git_name_email}, All Rights Reserved. 
-->

# transpose

## [普通版](https://blog.csdn.net/kunhe0512/article/details/131405148?spm=1001.2014.3001.5502)

### naive 实现

```cpp
// lanuch kernal
dim3 dimGrid((M+BLOCK_SIZE-1)/BLOCK_SIZE, (N+BLOCK_SIZE-1)/BLOCK_SIZE);
dim3 dimBlock(BLOCK_SIZE, BLOCK_SIZE);





//kernal 实现
__gloal__ void gpu_matrix_transpose(int in[N][M], int out[M][N]){
    int x = threadIdx.x + blockDim.x * blockIdx.x;
    int y = threadIdx.y + blockDim.y * blockIdx.y;

    if(x<M && y<N){
        out[x][y] = in[y][x];
    }
}

```

### shared memory 版本

```cpp
__global__ void gpu_shared_matrix_transpose(int in[N][M], int out [M][N]){
    int y = threadIdx.y + blockDimx.y*blockIdx.y;
    int x = threadIdx.x + blockDimx.x*blockIdx.x;

    __shared__ int ken[BLOCK_SIZE+1][BLOCK_SIZE+1];

    if(x<M && y<N){
        ken[threadIdx.y][threadIdx.x] = in[y][x];
    }

    __syncthreads();

    int x1 = threadIdx.x + blockDim.y * blockIdx.y;
    int y1 = threadIdx.y + blockDim.x * blockIdx.x;

    if(x1<N && y1<){
        out[y1][x1] = ken[threadIdx.x][threadIdx.y];
    }

}

```

### cuda sample transpose

```cpp
__global__ void copy(float *odata, float * idata, int width, int height){
    
    int xIndex = blockIdx.x * TILE_DIM + thredIdx.x;
    int yIndex = blockIdx.y * TILE_DIM + thredIdx.y;

    int index = xIndex + width * yIndex;

    for(int i=0; i<TILE_DIM; i+= BLOCK_ROWS){
        odata[index + i*width] = idata[index + i*width];
    }
}



__gloal__ void copyShareMem(float *odata, float *idata, int width, int height){
    cg::thread_block cta = cg::this_thread_block();
    __shared__ float tile[TILE_DIM][TILE_DIM];

    int xIndex = blockIdx.x*TILE_DIM + threadIdx.x;
    int yIndex = blockIdx.y*TILE_DIM + threadIdx.y;

    int index = xIndex + width * yIndex;

    for(int i = 0; i<TILE_DIM; i+=BLOCK_ROWS){
        if(xIndex<width & yIndex<height){
            tile[threadIdx.y][threadIdx.x] = idata[index];
        }
    }
    cg::sync(cta);

    for(int i=0; i<TILE_DIM; i+=BLOCK_ROWS){
        if(xIndex<height && yIndex<width){
            odata[index] = tile[threadIdx.y][threadIdx.x];
        }
    }
}
```