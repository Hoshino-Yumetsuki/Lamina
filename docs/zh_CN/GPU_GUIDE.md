# GPU 加速使用指南

[简体中文](#简体中文) | [English](#english)

---

## 简体中文

### 概述

Lamina 支持使用 `@gpu` 注解标记函数以启用 GPU 加速。GPU 加速基于 Vulkan 计算管线，适用于大规模并行计算和数值运算。

### 基本语法

#### 定义 GPU 函数

使用 `@gpu` 注解在函数定义前：

```lamina
@gpu
func gpu_multiply(a, b) {
    return a * b;
}

@gpu
func gpu_power(base, exp) {
    return base ^ exp;
}
```

#### 调用 GPU 函数

GPU 函数的调用方式与普通函数完全相同：

```lamina
var result1 = gpu_multiply(5, 3);
var result2 = gpu_power(2, 10);
print("Result:", result1, result2);
```

### 示例

#### 1. 基础数学运算

```lamina
@gpu
func gpu_factorial(n) {
    var result = 1;
    var i = 1;
    while (i <= n) {
        result = result * i;
        i = i + 1;
    }
    return result;
}

print("10! =", gpu_factorial(10));
```

#### 2. 向量点积

```lamina
@gpu
func vector_dot_product(a1, a2, b1, b2, c1, c2) {
    return a1 * b1 + a2 * b2 + c1 * c2;
}

print("Dot product:", vector_dot_product(1, 2, 3, 4, 5, 6));
```

#### 3. 数值序列求和

```lamina
@gpu
func gpu_power_sum(n, p) {
    var sum = 0;
    var i = 1;
    while (i <= n) {
        sum = sum + i ^ p;
        i = i + 1;
    }
    return sum;
}

print("Sum of squares:", gpu_power_sum(10, 2));
```

### 注意事项

1. **函数注册提示**：定义 GPU 函数时，解释器会输出提示信息：
   ```
   [GPU] Function 'function_name' marked for GPU acceleration
   ```

2. **当前实现状态**：
   - ✅ 语法支持：`@gpu` 注解已完全支持
   - ✅ 函数标记：GPU 函数可以正确识别和标记
   - ⏳ GPU 执行：当前版本在 CPU 上执行，Vulkan 计算管线正在开发中

3. **适用场景**：
   - 大规模数值计算
   - 并行数学运算
   - 向量和矩阵操作
   - 迭代算法

4. **限制**：
   - GPU 函数参数和返回值与普通函数相同
   - 递归函数不建议使用 GPU 加速
   - 当前版本暂不支持直接操作 GPU 内存

### 未来计划

- [ ] 完整的 Vulkan 计算管线实现
- [ ] GPU 内存管理和数据传输优化
- [ ] 自动并行化和向量化
- [ ] GPU 性能分析工具
- [ ] 支持 CUDA 后端（可选）

---

## English

### Overview

Lamina supports GPU acceleration using the `@gpu` annotation to mark functions. GPU acceleration is based on the Vulkan compute pipeline and is suitable for large-scale parallel computing and numerical operations.

### Basic Syntax

#### Defining GPU Functions

Use the `@gpu` annotation before function definitions:

```lamina
@gpu
func gpu_multiply(a, b) {
    return a * b;
}

@gpu
func gpu_power(base, exp) {
    return base ^ exp;
}
```

#### Calling GPU Functions

GPU functions are called the same way as regular functions:

```lamina
var result1 = gpu_multiply(5, 3);
var result2 = gpu_power(2, 10);
print("Result:", result1, result2);
```

### Examples

#### 1. Basic Mathematical Operations

```lamina
@gpu
func gpu_factorial(n) {
    var result = 1;
    var i = 1;
    while (i <= n) {
        result = result * i;
        i = i + 1;
    }
    return result;
}

print("10! =", gpu_factorial(10));
```

#### 2. Vector Dot Product

```lamina
@gpu
func vector_dot_product(a1, a2, b1, b2, c1, c2) {
    return a1 * b1 + a2 * b2 + c1 * c2;
}

print("Dot product:", vector_dot_product(1, 2, 3, 4, 5, 6));
```

#### 3. Numerical Series Summation

```lamina
@gpu
func gpu_power_sum(n, p) {
    var sum = 0;
    var i = 1;
    while (i <= n) {
        sum = sum + i ^ p;
        i = i + 1;
    }
    return sum;
}

print("Sum of squares:", gpu_power_sum(10, 2));
```

### Notes

1. **Function Registration Message**: When defining GPU functions, the interpreter will output:
   ```
   [GPU] Function 'function_name' marked for GPU acceleration
   ```

2. **Current Implementation Status**:
   - ✅ Syntax Support: `@gpu` annotation fully supported
   - ✅ Function Marking: GPU functions properly identified and marked
   - ⏳ GPU Execution: Current version executes on CPU, Vulkan compute pipeline in development

3. **Use Cases**:
   - Large-scale numerical computations
   - Parallel mathematical operations
   - Vector and matrix operations
   - Iterative algorithms

4. **Limitations**:
   - GPU function parameters and return values same as regular functions
   - Recursive functions not recommended for GPU acceleration
   - Direct GPU memory access not yet supported in current version

### Future Plans

- [ ] Complete Vulkan compute pipeline implementation
- [ ] GPU memory management and data transfer optimization
- [ ] Automatic parallelization and vectorization
- [ ] GPU performance profiling tools
- [ ] CUDA backend support (optional)

---

## 快速参考

### 定义 GPU 函数
```lamina
@gpu
func function_name(params) {
    // function body
}
```

### 示例代码

完整示例请参考：
- `examples/gpu_test.lm` - 基础 GPU 功能测试
- `examples/gpu_advanced.lm` - 高级 GPU 计算示例

### 获取帮助

如有问题或建议，请：
1. 查阅项目 Wiki：https://github.com/Hoshino-Yumetsuki/Lamina/wiki
2. 提交 Issue：https://github.com/Hoshino-Yumetsuki/Lamina/issues
3. 加入讨论组交流
