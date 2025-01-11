---
title: Quick Start
layout: default
nav_order: 1.5
---

# Quick Start

## Example

Consider the following (trivial) DAE system as a quick example:

$$
\left\{
    \begin{alignedat}{3}
        \dot x & = y, \\
        y & = \cos(t),
    \end{alignedat}
\right.
$$

with the initial condition:

$$
\left\{
    \begin{alignedat}{3}
        x\rvert_{t=0} & = 0, \\
        y\rvert_{t=0} & = 1.
    \end{alignedat}
\right.
$$

This system contains one simple differential equation and one algebraic equation. The analytic solution is the following:

$$
\left\{
    \begin{alignedat}{3}
        x(t) & = \sin(t), \\
        y(t) & = \cos(t).
    \end{alignedat}
\right.
$$

Below is a simplified procedure of defining and solving the given DAE system using `dae-cpp`.

## Solving the system

### Step 0. Include `dae-cpp` header into the project

```cpp
#include <dae-cpp/solver.hpp>
```

Optionally, the `daecpp` namespace can be added to the project:

```cpp
using namespace daecpp;
```

Alternatively, use `daecpp::` prefix for all `dae-cpp` types and classes.

### Step 1. Define the mass matrix of the system

The mass matrix contains only one non-zero element:

$$
\mathbf{M} =
\begin{vmatrix}
1 & 0 \\
0 & 0
\end{vmatrix}.
$$

```cpp
struct MyMassMatrix
{
    void operator()(sparse_matrix &M, const double t)
    {
        M(0, 0, 1.0); // Row 0, column 0, non-zero element 1.0
    }
};
```

For more information about defining the mass matrix, see [Mass Matrix class](mass-matrix.html) description.

### Step 2. Define the vector function (RHS) of the system

```cpp
struct MyRHS
{
    void operator()(state_type &f, const state_type &x, const double t)
    {
        f[0] = x[1];          // y
        f[1] = cos(t) - x[1]; // cos(t) - y
    }
};
```

For more information about defining the vector function (RHS) of the system, see [Vector Function class](vector-function.html) description.

### Step 3. Set up the DAE system

```cpp
MyMassMatrix mass; // Mass matrix object
MyRHS rhs;         // Vector-function object

System my_system(mass, rhs); // Defines the DAE system object
```

For more information about the `System` class, see [Solving DAE system](solve.html) section.

### Step 4. Solve the system

```cpp
state_vector x0{0, 1}; // The initial state vector (initial condition)
double t{1.0};         // The integration interval: t = [0, 1.0]

my_system.solve(x0, t); // Solves the system with the given initial condition `x0` and time `t`
```

or simply

```cpp
my_system.solve({0, 1}, 1.0);
```

Vector of solution vectors `x` and the corresponding vector of times `t` will be stored in `my_system.sol.x` and `my_system.sol.t`, respectively.

The entire C++ code is provided in the [Quick Start example](https://github.com/dae-cpp/dae-cpp/blob/master/examples/quick_start/quick_start.cpp).

For more information about using `solve` functions, see [Solving DAE system](solve.html) section.

### (Optional) Step 5. Define the Jacobian matrix to boost the computation speed

Differentiating the RHS w.r.t. $$x$$ and $$y$$ gives the following Jacobian matrix:

$$
\mathbf{J} =
\begin{vmatrix}
0 & 1 \\
0 & -1
\end{vmatrix}.
$$

This matrix can be defined in the code as

```cpp
struct MyJacobian
{
    void operator()(sparse_matrix &J, const state_vector &x, const double t)
    {
        J.reserve(2);  // Pre-allocates memory for 2 non-zero elements (optional)
        J(0, 1, 1.0);  // Row 0, column 1, non-zero element 1.0
        J(1, 1, -1.0); // Row 1, column 1, non-zero element -1.0
    }
};
```

Then add the user-defined Jacobian to the `solve()` method:

```cpp
my_system.solve(x0, t, MyJacobian()); // Starts the computation with Jacobian
```

Defining the analytic Jacobian matrix can significantly speed up the computation (especially for big systems).

If deriving the Jacobian matrix manually is not a feasible task (e.g., due to a very complex non-linear RHS), the solver allows the user to specify only the positions of non-zero elements of the Jacobian matrix (i.e., the Jacobian matrix shape). All the derivatives will be calculated automatically with a very small computation time penalty (compared to the manually derived analytic Jacobian).

For more information about defining the Jacobian matrix, see [Jacobian Matrix class](jacobian-matrix.html) description.

### (Optional) Step 6. Tweak the solver options

For example, restrict the maximum time step:

```cpp
my_system.opt.dt_max = 0.1;           // Update `dt_max`
my_system.solve(x0, t, MyJacobian()); // Restart the computation
```

See [Solver Options class](solver-options.html) description for more information.
