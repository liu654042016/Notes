# tvm
1. 代码生成的基本流程
代码生成的基本流程可以分为以下几个步骤：
* 高层次优化（High-Level Optimization）：对 Relay IR 或 TensorIR 进行高层次优化。
* 低层次优化（Low-Level Optimization）：将高层次 IR 转换为低层次 IR，并进行相应的优化。
* 代码生成（Code Generation）：将低层次 IR 转换为目标代码。

2. 高层次优化
在 TVM 中，高层次优化主要通过 Relay 或 TensorIR 来完成。通过各种优化 Pass，可以对计算图进行变换和优化。
```python
import tvm
from tvm import relay

# 定义一个简单的 Relay 计算图
data = relay.var("data", relay.TensorType((1, 3, 224, 224), "float32"))
weight = relay.var("weight", relay.TensorType((64, 3, 7, 7), "float32"))
conv = relay.nn.conv2d(data, weight, kernel_size=(7, 7), padding=(3, 3))
relu = relay.nn.relu(conv)
func = relay.Function([data, weight], relu)

# 创建 Relay 模块
mod = tvm.IRModule.from_expr(func)

# 应用高层次优化 Pass
with tvm.transform.PassContext(opt_level=3):
    mod = relay.transform.InferType()(mod)
    mod = relay.transform.FuseOps(fuse_opt_level=2)(mod)
    mod = relay.transform.ToANormalForm()(mod)
```

3. 低层次优化
低层次优化通过将 Relay 或 TensorIR 转换为低层次的 tir（Tensor Intermediate Representation），然后进行相应的优化。
```python
# 从 Relay IR 转换为 TensorIR
with tvm.transform.PassContext(opt_level=3):
    func = relay.build_module.bind_params_by_name(mod["main"], {})
    mod = tvm.driver.build_module.relay_to_tir(mod, {"main": func})

```

4. 代码生成
代码生成是将低层次 IR 转换为目标代码的过程。TVM 提供了多种后端代码生成器，如 LLVM、CUDA、OpenCL 等。
5. 自定义代码生成
TVM 允许用户自定义代码生成器，以支持新的硬件或优化现有硬件的代码生成过程。
5.1 自定义 TIR Pass
可以通过定义自定义的 TIR Pass 来修改低层次 IR 的优化和生成过程：
```python
from tvm import tir

# 自定义 TIR Pass
def custom_pass(stmt):
    # 在这里对 stmt 进行修改和优化
    return stmt

# 注册自定义 Pass
tir.transform.PassContext.current().add_custom_pass(custom_pass, "tir.custom_pass")

# 应用自定义 Pass
with tvm.transform.PassContext(opt_level=3):
    mod = tir.transform.ApplyCustomPass()(mod)

```
5.2 自定义代码生成器
可以通过继承 TVM 提供的代码生成基类，来实现自定义的代码生成器：
```python
from tvm.codegen import build_module

class CustomCodeGen(build_module.CodeGenBase):
    def __init__(self):
        super(CustomCodeGen, self).__init__()

    def generate_code(self, stmt, target):
        # 实现自定义的代码生成逻辑
        pass

# 使用自定义代码生成器
custom_codegen = CustomCodeGen()
with tvm.transform.PassContext(opt_level=3):
    mod = tvm.build(mod, target=target, codegen=custom_codegen)

```