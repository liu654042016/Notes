## 自定义算子
1. 安装必要的工具
首先，你需要安装 PyTorch 及其扩展工具包 torch 和 torchvision。你还需要安装 torch.utils.cpp_extension。

bash
复制代码
pip install torch torchvision
2. 编写 C++ 扩展代码
创建一个简单的 C++ 文件，如 my_extension.cpp，实现一个简单的加法操作。

cpp
复制代码
// my_extension.cpp
#include <torch/extension.h>

// 定义加法操作
torch::Tensor add_tensors(torch::Tensor a, torch::Tensor b) {
    return a + b;
}

// 定义模块
PYBIND11_MODULE(TORCH_EXTENSION_NAME, m) {
    m.def("add_tensors", &add_tensors, "Add two tensors");
}
3. 编写编译脚本
创建一个 Python 脚本 setup.py，用于编译 C++ 扩展。

python
复制代码
# setup.py
from setuptools import setup
from torch.utils.cpp_extension import CppExtension, BuildExtension

setup(
    name='my_extension',
    ext_modules=[
        CppExtension(
            name='my_extension',
            sources=['my_extension.cpp'],
        ),
    ],
    cmdclass={
        'build_ext': BuildExtension
    }
)
4. 编译扩展
在终端中运行以下命令编译 C++ 扩展：

bash
复制代码
python setup.py build_ext --inplace
5. 使用扩展
编译完成后，你可以在 Python 中使用这个扩展。

python
复制代码
import torch
import my_extension

# 创建两个张量
a = torch.tensor([1.0, 2.0, 3.0])
b = torch.tensor([4.0, 5.0, 6.0])

# 调用自定义的加法操作
result = my_extension.add_tensors(a, b)
print(result)  # 输出 tensor([5.0, 7.0, 9.0])
6. 编写 CUDA 扩展（可选）
如果你需要编写 CUDA 扩展，可以按照以下步骤进行。首先，创建一个 CUDA 文件，如 my_extension.cu，实现一个简单的加法操作。

cuda
复制代码
// my_extension.cu
#include <torch/extension.h>

// CUDA 内核
__global__ void add_kernel(float* a, float* b, float* c, int64_t size) {
    int index = blockIdx.x * blockDim.x + threadIdx.x;
    if (index < size) {
        c[index] = a[index] + b[index];
    }
}

// 加法操作
torch::Tensor add_tensors_cuda(torch::Tensor a, torch::Tensor b) {
    auto c = torch::zeros_like(a);
    int64_t size = a.numel();
    int threads = 1024;
    int blocks = (size + threads - 1) / threads;
    add_kernel<<<blocks, threads>>>(a.data_ptr<float>(), b.data_ptr<float>(), c.data_ptr<float>(), size);
    return c;
}

// 定义模块
PYBIND11_MODULE(TORCH_EXTENSION_NAME, m) {
    m.def("add_tensors_cuda", &add_tensors_cuda, "Add two tensors (CUDA)");
}
更新 setup.py 文件以包含 CUDA 源文件：

python
复制代码
# setup.py
from setuptools import setup
from torch.utils.cpp_extension import BuildExtension, CUDAExtension

setup(
    name='my_extension',
    ext_modules=[
        CUDAExtension(
            name='my_extension',
            sources=['my_extension.cu'],
        ),
    ],
    cmdclass={
        'build_ext': BuildExtension
    }
)
编译 CUDA 扩展：

bash
复制代码
python setup.py build_ext --inplace
在 Python 中使用 CUDA 扩展：

python
复制代码
import torch
import my_extension

# 创建两个张量
a = torch.tensor([1.0, 2.0, 3.0], device='cuda')
b = torch.tensor([4.0, 5.0, 6.0], device='cuda')

# 调用自定义的 CUDA 加法操作
result = my_extension.add_tensors_cuda(a, b)
print(result)  # 输出 tensor([5.0, 7.0, 9.0], device='cuda:0')