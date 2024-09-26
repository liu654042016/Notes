## [optim gemm](https://blog.csdn.net/kunhe0512/article/details/131369624)

### base

```cpp
 //a[][]*b[][]=c[][]
//
//
//                  b00 b01 b02 b03
//                  b10 b11 b12 b13
//                  b20 b21 b22 b23
//                  b30 b31 b32 b33
//
//a00 a01 a02 a03   c00 c01 c02 c03
//a10 a11 a12 a13   c10 c11 c12 c13
//a20 a21 a22 a23   c20 c21 c22 c23
//a30 a31 a32 a33   c30 c31 c32 c33
//
//c21 = a20*b01+a21*b11+a22*b12+a23*b13
//
//share memory
//                      sb_0_0   sb_0_1
//
//                      b00 b01   b02 b03
//                      b10 b11   b12 b13
//
//                      sb_1_0    sb_1_1
//
//                      b20 b21   b22 b23
//                      b30 b31   b32 b33
//
//
//ab_0_0    ab_0_1      cb_0_0    cb_0_1 
//
//a00 a01   a02 a03     C00 C01   C02 C03 
//a10 a11   a12 a13     C10 C11   C12 C13
//
//ab_1_0    ab_1_1      cb_1_0    cb_1_1
//
//a20 a21   a22 a23     C20 C21   C22 C23
//a30 a31   a32 a33     C30 C31   C32 C33
//
//cb_11 =  ab_1_0 * sb_0_1 + ab_1_1*sb_1_1
                 
                 


__global__ void gpu_matrix_mult(int *a, int *b, int *c, const int size){
    int y = blockDim.y * blockIdx.y + threadIdx.y;
    int x = blockDim.x * blockIdx.x + threadIdx.x;

    int tmp = 0;
    if(x<size && y<size){
        for( int step = 0; step <size; ++step){
            tmp += a[y*size+step]*b[step*sze+x];
        }
        c[y*size+x] = tmp;
    }
}


#define M 1000
#define N 500
#define K 1000

#define BLOCK_SIZE 1000
// lanch kernel
//a[M,N] b[N,K] c[M,K]
unsigned int grid_x = (K+BLOCK_SIZE - 1)/BLOCK_SIZE// k 列 是 x 坐标
unsigned int grid_y = (M+BLOCK_SIZE - 1)/BLOCK_SIZE// M 行 是 y 坐标

dim3 dimGrid(grid_x, grid_y)
dim3 dimBlock(BLOCK_SIZE, BLOCK_SIZE)

gpu_matrix<<<dimGrid,dimBlock>>>(a,b,c_gpu, M, N, K)

__global__ void gpu_matirx(int* a, int* b, int* c, int m, int n, int k){
    __shared__ int sub_a[BLOCK_SIZE][BLOCK_SIZE];
    __shared__ int sub_b[BLOCK_SIZE][BLOCK_SIZE];

    int x = blockIdx.x * blockDim.X + threadIdx.x; //输出矩阵 c 列 0:k
    int y = blockIdy.y * blockDim.y + threadIdx.y; //输出矩阵 c 行 0:m

    int tmp = 0;
    int idx;
    //a[m,n]
    //b[n,k]
    //c[m,k]
    for(int step = 0; step<= n/BLOCK_SIZE; step++){
        // load a 矩阵 a[m,n]
        // 加载一行数据  a 矩阵 按 n 方向切分
        int step_x = step*BLOCK_SIZE + threadIdx.x; 
        int step_y = y;

        idx = step_y*n + step_x;
        if(step_x>=n || step_y>=m){
            sub_a[threadIdx.y][threadIdx.x] = 0;
        }else{
            sub_a[threadIdx.y][threadIdx.x] = a[idx];
        }

        // load b 矩阵 b[n,k]
        // load 一列数据 沿 n 方向切分
        step_x = x;
        step_y = step*BLOCK_SIZE + threadIdx.y;
        idx = step_y*k + step_x;

        if(step_x>=k || step_y >=n){
            sub_b[threadIdx.y][threadIdx.x] = 0;
        }else{
            sub_b[threadIdx.y][threadIdx.x] = b[idx];
        }

        __syncthreads();

        for(int i=0; i<BLOCK_SIZE; i++){
            tmp+=sub_a[threadIdx.y][i] * sub_b[i][threadIdx.x];
        }
        __syncthreads();
    }
    // c[m,k]
    if(x<k && y<m){
        c[y*k+x] = tmp;
    }
}

```

### transpose

[reference](https://blog.csdn.net/weixin_55035144/article/details/130742866)
[reference_2](https://juejin.cn/post/7033312408005246984)

```cpp






//matrix transpose
//
//in    b00 b01 b02 | b03 b04 b05 | b06 b07 b08
//      b10 b11 b12 | b13 b14 b15 | b16 b17 b18
//      b20 b21 b22 | b23 b24 b25 | b26 b27 b28
//      ------------+-------------+------------
//      b30 b31 b32 | b33 b34 b35 | b36 b37 b38
//      b40 b41 b42 | b43 b44 b45 | b46 b47 b48
//      b50 b51 b52 | b53 b54 b55 | b56 b57 b58
//
//
//
//out   b00 b10 b20 | b30 b40 b50
//      b01 b11 b21 | b31 b41 b51
//      b02 b12 b22 | b32 b42 b52
//      ------------+------------
//      b03 b13 b23 | b33 b43 b53
//      b04 b14 b24 | b34 b44 b54
//      b05 b15 b25 | b35 b45 b55
//      ------------+------------
//      b06 b16 b26 | b36 b46 b56
//      b07 b17 b27 | b37 b47 b57
//      b08 b18 b28 | b38 b48 b58
//
//      
//
// B[i][j] = B[j]{i}
//shared memory
//t57 read b57 from global memroy to shared memroy
//t57 read b48 from shared memory
//t57 write b48 to blobal memory

#define BLOCK_SIZE 32
#define M 3000
#define N 1000

__managed__ int matrix[N][M]
__managed__ int gpu_result[M][N]
__managed__ int cpu_result[M][N]



// read 数据时合并访存了， 但是写的时候不连续所以没有合并访存
__global__ void gpu_matrix_transpose(int in[N][M], int out[M][N]){
    int x = threadIdx.x + blockDim.x * blockIdx.x;
    int y = threadIdx.y + blockDim.y * blockIdx.y;
    if(x<>M && y<N){
        out[x][y] = in[y][x];
    }
}

//bank 
//[0][1][2][3][4][5][6][7][8]...[27][28][29][30][31]
//
//
//

__global__ void gpu_shared_matrix_transpose(int in[N][M], int out[M][N]){
    int y = threadIdx.y + blockDim.y*blockIdx.y;
    int x = threadIdx.x + blockDim.x*blockIdx.x;

    __shared__ int ken[BLOCK_SIZE+1][BLOCK_SIZE+1];//ken[32] warp

    if(x<M && y<N){
        ken[threadIdx.y][threadIdx.x] = in[y][x];
    }
    __synctheads();

    int x1 = threadIdx.x + blockDim.y*blockIdx.y;
    int y1 = threadIdx.y + blockDim.x*blockIdx.x;
    if(x1<N && y1<M){
        out[y1][x1] = ken[threadIdx.x][threadIdx.y];//32 bank
    }
}

```

### [规约](https://blog.csdn.net/kunhe0512/article/details/131494479?spm=1001.2014.3001.5502)
```cpp
#define N 100000000
#define BLOCK_SIZE 256
#define GRID_SIZE 32

__managed__ int source[N];
__managed__ int gpu_result[1] = {0};

//source[N] 1+2+3+4+.........N
//cpu for loop
//gpu: 
//thread id step 0: tid0: source[0] + source[4]->source[0]
//                  tid1: source[1] + source[5]->source[1]
//                  tid2: source[3] + source[6]->source[3]
//                  tid3: source[4] + source[7]->source[4] 
//          step 1: tid0: source[0] + source[2]->source[0]
//                  tid1: source[1] + source[3]->source[1]
//          step 2: tid0: source[0] + source[1]->source[0]
//
//thread id: blockDim.x * blockIdx.x + threadIdx.x + step*blockDim.x*GridDim.x
//
//

__global__ void sum_gpu(int *in, int count, int *out){
    //grid_loop
    int shared_tmp = 0;
    for(int idx = blockDim.x*blockIdx.x+threadIdx.x; idx<count; idx+=blockDim.x*gridDim.x){
        shared_tmp += in[idx];
    }
    ken[threadIdx.x] = shared_tmp;
    __syncthreads();
    int tmp = 0;
    for(int tortal_threads = BLOCK_SIZE/2; total_threads>=1;toral_threads/=2){
        if(threadIdx.x<total_threads){
            tmp = ken[threadIdx.x] + ken[threadIdx.x+total_threads];
        }
        __syncthreads();
        if(threadIdx.x<total_threads){
            ken[threadIdx.x] = tmp;
        }
    }
    //block_sum->share memory[0]
    if(blockIdx.x*blockDim.x<count){
        if(threadIdx.x==0){
            atomicAdd(out, ken[0]);
        }
    }
}

```

### [topk/规约/ 2_pass 核函数](https://blog.csdn.net/kunhe0512/article/details/131581665?spm=1001.2014.3001.5502)
