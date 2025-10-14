# GPU Acceleration Guide

[简体中文](../zh_CN/GPU_GUIDE.md) | **English**

---

## Overview

Lamina supports GPU acceleration using the `@gpu` annotation syntax. Functions marked with `@gpu` are designated for GPU execution, leveraging Vulkan-based compute pipelines for parallel processing and high-performance numerical computations.

## Basic Usage

### Defining GPU-Accelerated Functions

Use the `@gpu` annotation before function definitions:

```lamina
// Regular function
func add(a, b) {
    return a + b;
}

// GPU-accelerated function
@gpu
func gpu_multiply(a, b) {
    return a * b;
}

@gpu
func gpu_power(base, exp) {
    return base ^ exp;
}
```

### Calling GPU Functions

GPU functions are invoked the same way as regular functions:

```lamina
var result1 = add(5, 3);           // CPU execution
var result2 = gpu_multiply(5, 3);  // GPU execution
var result3 = gpu_power(2, 10);    // GPU execution

print("CPU result:", result1);
print("GPU results:", result2, result3);
```

### Function Registration

When a GPU function is defined, the interpreter displays a confirmation message:

```
[GPU] Function 'gpu_multiply' marked for GPU acceleration
[GPU] Function 'gpu_power' marked for GPU acceleration
```

## Examples

### 1. Basic Mathematical Operations

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
// Output: 10! = 3628800
```

### 2. Fibonacci Sequence

```lamina
@gpu
func gpu_fibonacci(n) {
    if (n <= 1) {
        return n;
    }
    var a = 0;
    var b = 1;
    var i = 2;
    while (i <= n) {
        var temp = a + b;
        a = b;
        b = temp;
        i = i + 1;
    }
    return b;
}

print("Fibonacci(15) =", gpu_fibonacci(15));
// Output: Fibonacci(15) = 610
```

### 3. Vector Operations

```lamina
@gpu
func vector_dot_product(a1, a2, a3, b1, b2, b3) {
    return a1 * b1 + a2 * b2 + a3 * b3;
}

@gpu
func vector_magnitude(x, y, z) {
    var sum = x * x + y * y + z * z;
    return sqrt(sum);
}

print("Dot product:", vector_dot_product(1, 2, 3, 4, 5, 6));
print("Vector magnitude:", vector_magnitude(3, 4, 5));
```

### 4. Polynomial Evaluation

```lamina
@gpu
func gpu_polynomial(x, a, b, c) {
    // Compute ax² + bx + c
    return a * x * x + b * x + c;
}

print("2x² + 3x + 1 at x=5:", gpu_polynomial(5, 2, 3, 1));
// Output: 2x² + 3x + 1 at x=5: 66
```

### 5. Numerical Series

```lamina
@gpu
func gpu_power_sum(n, p) {
    // Compute 1^p + 2^p + 3^p + ... + n^p
    var sum = 0;
    var i = 1;
    while (i <= n) {
        sum = sum + i ^ p;
        i = i + 1;
    }
    return sum;
}

print("Sum of squares:", gpu_power_sum(10, 2));
// Output: Sum of squares: 385
```

## Implementation Status

### Current Features

- ✅ **Syntax Support**: `@gpu` annotation fully implemented in lexer and parser
- ✅ **Function Marking**: GPU functions are correctly identified and tagged
- ✅ **Type Compatibility**: Works seamlessly with Lamina's type system
- ✅ **Syntax Highlighting**: Pygments lexer updated for `@gpu` annotation

### In Development

- ⏳ **Vulkan Integration**: Compute shader pipeline implementation
- ⏳ **GPU Execution**: Actual GPU computation backend
- ⏳ **Memory Management**: GPU memory allocation and data transfer
- ⏳ **Performance Optimization**: Automatic parallelization and vectorization

### Current Behavior

In the current version, functions marked with `@gpu`:
- Are properly recognized and registered
- Display a `[GPU]` prefix in console output
- Execute on the CPU (Vulkan backend in development)
- Function identically to regular functions in terms of behavior

## Best Practices

### When to Use GPU Acceleration

GPU acceleration is most beneficial for:

1. **Parallel Computations**: Operations that can be executed independently
2. **Numerical Algorithms**: Matrix operations, linear algebra, FFT
3. **Iterative Calculations**: Loops with independent iterations
4. **Vector Operations**: Element-wise operations on large datasets

### When NOT to Use GPU Acceleration

Avoid GPU acceleration for:

1. **Simple Operations**: Small computations with minimal parallelism
2. **Recursive Functions**: Deep recursion doesn't parallelize well
3. **I/O Operations**: File access, user input, network communication
4. **Sequential Logic**: Operations that depend on previous results

### Performance Considerations

```lamina
// Good: Parallelizable computation
@gpu
func matrix_multiply(n) {
    // Each element computed independently
    var sum = 0;
    var i = 0;
    while (i < n) {
        sum = sum + i * i;
        i = i + 1;
    }
    return sum;
}

// Not ideal: Sequential dependencies
func factorial_recursive(n) {
    // Each call depends on previous result
    if (n <= 1) {
        return 1;
    }
    return n * factorial_recursive(n - 1);
}
```

## Examples Repository

Complete working examples are available in the `examples/` directory:

- **`gpu_test.lm`**: Basic GPU annotation functionality
- **`gpu_advanced.lm`**: Advanced GPU computation examples

Run examples:
```bash
./lamina examples/gpu_test.lm
./lamina examples/gpu_advanced.lm
```

## Future Roadmap

### Phase 1: Foundation (Current)
- [x] `@gpu` syntax support
- [x] Function annotation and marking
- [x] Documentation and examples

### Phase 2: Vulkan Integration (In Progress)
- [ ] Vulkan SDK integration
- [ ] Compute shader compilation
- [ ] Basic GPU execution pipeline
- [ ] Memory management

### Phase 3: Optimization (Planned)
- [ ] Automatic parallelization
- [ ] Data transfer optimization
- [ ] Performance profiling tools
- [ ] Adaptive CPU/GPU selection

### Phase 4: Advanced Features (Future)
- [ ] Multi-GPU support
- [ ] CUDA backend (optional)
- [ ] GPU memory pools
- [ ] Async GPU execution

## Technical Details

### Annotation Syntax

The `@gpu` annotation is implemented as:
- **Token**: `TokenType::At` for the `@` symbol
- **Parser**: Recognizes `@gpu` before `func` keyword
- **AST**: `FuncDefStmt` has `is_gpu` boolean flag
- **Interpreter**: GPU functions trigger special handling

### Example AST Structure

```
@gpu
func example(x) { return x * 2; }

// Generates AST:
FuncDefStmt {
    name: "example",
    params: ["x"],
    is_gpu: true,
    body: { return x * 2 }
}
```

## Troubleshooting

### Issue: GPU function doesn't accelerate

**Reason**: Vulkan backend not yet implemented  
**Solution**: Current version executes on CPU. Full GPU execution coming in future updates.

### Issue: Syntax error with @gpu

**Possible causes**:
1. Check for typo: must be exactly `@gpu` (lowercase)
2. Ensure `@gpu` immediately precedes `func` keyword
3. No space between `@` and `gpu`

**Correct syntax**:
```lamina
@gpu
func myfunction(x) { return x; }
```

### Issue: Performance not improved

**Explanation**: GPU acceleration excels at parallel operations. For small or sequential operations, CPU may be faster due to GPU setup overhead.

## Contributing

Interested in contributing to GPU acceleration development?

1. Check open issues: https://github.com/Hoshino-Yumetsuki/Lamina/issues
2. Review the codebase: Focus on `interpreter/` directory
3. Read [CONTRIBUTING.md](../../CONTRIBUTING.md)
4. Submit pull requests with tests

## Support

- **Documentation**: https://github.com/Hoshino-Yumetsuki/Lamina/wiki
- **Issues**: https://github.com/Hoshino-Yumetsuki/Lamina/issues
- **Discussions**: Join the community chat

---

## Quick Reference

### Syntax
```lamina
@gpu
func function_name(params) {
    // function body
}
```

### Example
```lamina
@gpu
func gpu_square(n) {
    return n * n;
}

print(gpu_square(10));  // Output: 100
```

### Status
- Syntax: ✅ Implemented
- Execution: ⏳ In Development (currently CPU-based)
- Documentation: ✅ Complete

---

For more information, see the main [README](../../README.md) or the Chinese version of this guide: [GPU_GUIDE.md](../zh_CN/GPU_GUIDE.md)
