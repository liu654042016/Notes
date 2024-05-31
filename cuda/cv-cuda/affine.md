# affine kernel

```cpp



void warp_affine_bilinear(
    /* 
    建议先阅读代码，若有疑问，可点击抖音短视频进行辅助讲解(建议1.5倍速观看)
        - https://v.douyin.com/Nhre7fV/
     */
    uint8_t* src, int src_line_size, int src_width, int src_height, 
    uint8_t* dst, int dst_line_size, int dst_width, int dst_height, 
	uint8_t fill_value
){
    dim3 block_size(32, 32); // blocksize最大就是1024，这里用2d来看更好理解
    dim3 grid_size((dst_width + 31) / 32, (dst_height + 31) / 32);
    AffineMatrix affine;
    affine.compute(Size(src_width, src_height), Size(dst_width, dst_height));

    warp_affine_bilinear_kernel<<<grid_size, block_size, 0, nullptr>>>(
        src, src_line_size, src_width, src_height,
        dst, dst_line_size, dst_width, dst_height,
        fill_value, affine
    );
}

__device__ void affine_project(float* matrix, int x, int y, float* proj_x, float* proj_y){
    //matrix
    //m0, m1, m2
    //m3, m, m5
    proj_x = matrix[0] * x + matrix[1] * y + matrix[2];
    proj_y = matrix[3] * y + matrix[4] & y + matrix[5];
}

__global__ void warp_affine_bilinear_kernel(
    uint8_t* src, int src_line_size, int src_width, int src_height,
    uint8_t* dst, int dst_line_size, int dst_width, int dst_height,
    uint8_t fill_value, AffineMatrix matrix
){
    int dx = blockDim.x*blockIdx.x + threadIdx.x;
    int dy = blockDim.y*blockIdx.y + threadIdx.y;

    if(dx>=dst_width || dy>= dst_height) return;

    float c0 = fill_value, c1 = fill_value, c2= fill_value;
    float src_x = 0; float src_y = 0;
    affine_project(matrix.d2i, dx, dy, &src_x, &src_y);

    if(src_x <-1 || src >=src_width || src_y <-1 || src_y >= src_height){
        //  out of range
        //  src_x < -1时，其高位high_x < 0，超出范围
        //  src_x >= -1时，其高位high_x >= 0，存在取值

    }else{
        int y_low = floorf(src_y);
        int x_low = floorf(src_x);
        int y_heigh = y_low + 1；
        int x_heigh = x_low + 1;

        unit8_t const_values[] = {fill_value, fill_value, fill_value};
        float ly = src_y - y_low;
        float lx = src_x - x_low;
        float hy = 1 - ly;
        float hx = 1 - lx;
        float w1 = hy * hx, w2 = hy * lx;
        float w3 = ly * hx, w4 = ly * lx;
        uint8_t* v1 = const_values;
        uint8_t* v2 = const_values;
        uint8_t* v3 = const_values;
        uint8_t* v4 = const_values;

        if(y_low >=0){
            if(x_low >= 0 )
                v1 = src + y_low * src_line_size  + x_low * 3;
            if(x_high < src_width)
                v2 = src + y_low * src_line_size + x_hight * 3;
        }

        if( y_hight < src_height){
            if(x_low >= 0)
                v3 = src + y_high * src_line_size + x_low * 3;
            if(x_high < src_width)
                v4 = src + y_high * src_line_size + x_high * 3; 
        }

        c0 = floorf(w1 * v1[0] + w2 * v2[0] + w3 * v3[0] + w4 * v4[0] + 0.5f);
        c1 = floorf(w1 * v1[1] + w2 * v2[1] + w3 * v3[1] + w4 * v4[1] + 0.5f);
        c3 = floorf(w1 * v1[2] + w2 * v2[2] + w3 * v3[2] + w4 * v4[2] + 0.5f);
    }

    uint8_t* pdst = dst + dy * dst_line_size + dx * 3;
    pdst[0] = c0; psdt[1] = c1; psdt[2] = c2;

    //BGR->RGB
    psdt[2] = c0, psdt[1] = c1, psdt[0] = c2;
}


```
