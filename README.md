# BUDDY MLIR

MLIR-Based Ideas Landing Project ([Project page](https://buddy-compiler.github.io/)).

## Getting Started

The default build system uses LLVM/MLIR as an external library. 
We also provide a [one-step build strategy](#one-step) for users who only want to use our tools.
Please make sure [the dependencies](https://llvm.org/docs/GettingStarted.html#requirements) are available on your machine.

### LLVM/MLIR Dependencies

Before building, please make sure [the dependencies](https://llvm.org/docs/GettingStarted.html#requirements) are available
on your machine.

### Clone and Initialize

```
$ git clone git@github.com:buddy-compiler/buddy-mlir.git
$ cd buddy-mlir
$ git submodule update --init
```

### Build and Test LLVM/MLIR/CLANG

```
$ cd buddy-mlir
$ mkdir llvm/build
$ cd llvm/build
$ cmake -G Ninja ../llvm \
    -DLLVM_ENABLE_PROJECTS="mlir;clang" \
    -DLLVM_TARGETS_TO_BUILD="host;RISCV" \
    -DLLVM_ENABLE_ASSERTIONS=ON \
    -DCMAKE_BUILD_TYPE=RELEASE
$ ninja check-mlir check-clang
```

If your target machine includes a Nvidia GPU, you can use the following configuration:

```
$ cmake -G Ninja ../llvm \
    -DLLVM_ENABLE_PROJECTS="mlir;clang" \
    -DLLVM_TARGETS_TO_BUILD="host;RISCV;NVPTX" \
    -DMLIR_ENABLE_CUDA_RUNNER=ON \
    -DLLVM_ENABLE_ASSERTIONS=ON \
    -DCMAKE_BUILD_TYPE=RELEASE
```

To enable MLIR Python bindings, please use the following configuration:

```
$ cmake -G Ninja ../llvm \
    -DLLVM_ENABLE_PROJECTS="mlir;clang" \
    -DLLVM_TARGETS_TO_BUILD="host;RISCV" \
    -DLLVM_ENABLE_ASSERTIONS=ON \
    -DCMAKE_BUILD_TYPE=RELEASE \
    -DMLIR_ENABLE_BINDINGS_PYTHON=ON \
    -DPython3_EXECUTABLE=[path_to_python_executable]
```

If your target machine has lld installed, you can use the following configuration:

```
$ cmake -G Ninja ../llvm \
    -DLLVM_ENABLE_PROJECTS="mlir;clang" \
    -DLLVM_TARGETS_TO_BUILD="host;RISCV" \
    -DLLVM_USE_LINKER=lld \
    -DLLVM_ENABLE_ASSERTIONS=ON \
    -DCMAKE_BUILD_TYPE=RELEASE
```

### Build buddy-mlir

If you have previously built the llvm-project, you can replace the $PWD with the path to the directory where you have successfully built the llvm-project.

```
$ cd buddy-mlir
$ mkdir build
$ cd build
$ cmake -G Ninja .. \
    -DMLIR_DIR=$PWD/../llvm/build/lib/cmake/mlir \
    -DLLVM_DIR=$PWD/../llvm/build/lib/cmake/llvm \
    -DLLVM_ENABLE_ASSERTIONS=ON \
    -DCMAKE_BUILD_TYPE=RELEASE
$ ninja
$ ninja check-buddy
```

To utilize the Buddy Compiler Python package, please ensure that the MLIR Python bindings are enabled and use the following configuration:

```
$ cmake -G Ninja .. \
    -DMLIR_DIR=$PWD/../llvm/build/lib/cmake/mlir \
    -DLLVM_DIR=$PWD/../llvm/build/lib/cmake/llvm \
    -DLLVM_ENABLE_ASSERTIONS=ON \
    -DCMAKE_BUILD_TYPE=RELEASE \
    -DBUDDY_MLIR_ENABLE_PYTHON_PACKAGES=ON \
    -DPython3_EXECUTABLE=[path_to_python_executable]
```

If you want to add domain-specific framework support, please add the following cmake options:

| Framework  | Enable Option | Other Options |
| -------------- | ------------- | ------------- |
| OpenCV  | `-DBUDDY_ENABLE_OPENCV=ON`  | Add `-DOpenCV_DIR=</PATH/TO/OPENCV/BUILD/>` or install OpenCV release version on your local device. |

<h3 id="one-step">One-step building strategy</h3>

If you only want to use our tools and integrate them more easily into your projects, you can choose to use the one-step build strategy.

```
$ cmake -G Ninja -Bbuild \
    -DCMAKE_BUILD_TYPE=Release \
    -DLLVM_ENABLE_PROJECTS="mlir;clang" \
    -DLLVM_TARGETS_TO_BUILD="host;RISCV" \
    -DLLVM_EXTERNAL_PROJECTS="buddy-mlir" \
    -DLLVM_EXTERNAL_BUDDY_MLIR_SOURCE_DIR="$PWD" \
    -DLLVM_ENABLE_ASSERTIONS=ON \
    -DCMAKE_BUILD_TYPE=RELEASE \
    llvm/llvm
$ cd build
$ ninja check-mlir check-clang
$ ninja
$ ninja check-buddy
```
## Dialects

### Bud Dialect

Bud dialect is designed for testing and demonstrating.

### DIP Dialect

DIP dialect is designed for digital image processing abstraction.

## Tools

### buddy-opt

The buddy-opt is the driver for dialects and optimization in buddy-mlir project. 

### buddy-lsp-server

This program should be a drop-in replacement for `mlir-lsp-server`, supporting new dialects defined in buddy-mlir. To use it, please directly modify mlir LSP server path in VSCode settings (or similar settings for other editors) to:

```json
{
    "mlir.server_path": "YOUR_BUDDY_MLIR_BUILD/bin/buddy-lsp-server",
}
```

After modification, your editor should have correct completion and error prompts for new dialects such as `rvv` and `gemmini`.

### AutoConfig Mechanism

The `AutoConfig` mechanism is designed to detect the target hardware and configure the toolchain automatically.

## Examples

The purpose of the examples is to give users a better understanding of how to use the passes and the interfaces in buddy-mlir. Currently, we provide three types of examples.

- IR level conversion and transformation examples.
- Domain-specific application level examples.
- Testing and demonstrating examples.

For more details, please see the [documentation of the examples](./examples/README.md).
