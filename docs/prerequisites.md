---
title: Prerequisites
layout: default
nav_order: 1.7
---

# Prerequisites

## `dae-cpp` header file

The main header file of the library is `dae-cpp/solver.hpp`. It contains all the necessary classes, solvers, and utilities. To include `dae-cpp` into the project, use

```cpp
#include <dae-cpp/solver.hpp>
```

## `dae-cpp` namespace

All classes and solvers exposed to the user are defined in the `daecpp` namespace. This means that all names from the `dae-cpp` library should be prefixed with `daecpp::`. Alternatively, use

```cpp
using namespace daecpp;
```

## `dae-cpp` types

| Type | Equivalent to | Note |
| ---- | ------------- | ---- |
| `daecpp::int_type` | `uint32_t` (default), <br> `uint64_t` if `DAECPP_LONG` is defined | Unsigned integer type, used for sparse matrix indices |
| `daecpp::float_type` | `double` (default), <br> `float` if `DAECPP_SINGLE` is defined | Floating point scalar, used for sparse matrix coefficients |
| `daecpp::state_vector` | `std::vector<float_type>` | State vector, used, for example, to define the initial condition |
| `daecpp::state_type` | `autodiff::VectorXreal` | State vector, used for the [vector function](vector-function.html) definition so that it can be automatically (algorithmically) differentiated using [`autodiff`](https://autodiff.github.io/) package |
| `daecpp::dual_type` | `autodiff::real` | Floating point "dual" number, used in the vector function for automatic differentiation |
