---
title: Installation and Testing
layout: default
nav_order: 1.2
---

# Installation and Testing

## Installation

- Download the library from `dae-cpp` repository on GitHub: [https://github.com/dae-cpp/dae-cpp](https://github.com/dae-cpp/dae-cpp).
- The solver is header-only, with all dependencies included. There is no need to pre-compile the sources, just copy `dae-cpp`, `Eigen`, and `autodiff` folders into your project.
- Optionally: examples and tests can be compiled using CMake (see [Testing](#testing)).

## Testing

For unit and integration testing, `dae-cpp` uses [GoogleTest](https://github.com/google/googletest), Google Testing and Mocking Framework, which can be downloaded with `dae-cpp` as a [submodule](https://git-scm.com/book/en/v2/Git-Tools-Submodules).
If you already have cloned the project without `--recurse-submodules` option, you can initialize and update `googletest` submodule by running

```bash
git submodule update --init
```

Then build and run the tests:

```bash
mkdir build && cd build
cmake ..
make
ctest
```

Note that you don't have to download and run the tests. The project is tested automatically on Ubuntu (`gcc` and `clang` compilers) and Windows (MSVC compiler) via GitHub Actions.
