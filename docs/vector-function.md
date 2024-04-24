---
title: Vector Function class
layout: default
parent: Classes to define DAE system
nav_order: 4
---

# Vector Function class

The Vector Function class defines the (nonlinear) vector function (the RHS) $$\mathbf{f}(\mathbf{x}, t)$$ of the DAE system written in the matrix-vector form:

$$\mathbf{M}(t) \frac{\mathrm{d}\mathbf{x}}{\mathrm{d}t} = \mathbf{f}(\mathbf{x}, t).$$

The vector function depends on the state vector $$\mathbf{x}$$ and time $$t$$.

## Vector function definition

The main vector function class `daecpp::VectorFunction` is an abstract class that provides an interface to define the vector function $$\mathbf{f}(\mathbf{x}, t)$$.
In order to make use of this class, the user should inherit it and overload the `()` operator:

```cpp
class UserDefinedVectorFunction : public daecpp::VectorFunction
{
public:
    void operator()(daecpp::state_type &f, const daecpp::state_type &x, const double t) const
    {
        // Vector function definition
    }
};
```

The operator `()` takes the state vector `x` at time `t` and updates the vector function `f`.
Vector `f` is already pre-allocated with `f.size() == x.size()` and should be used in the function directly (without pre-allocation and `push_back`-like methods).
The elements of vectors `x` and `f` can be accessed using square brackets `[]`.

{: .note }
The type of vectors `f` and `x` is `daecpp::state_type`, which is an alias of [`autodiff`](https://autodiff.github.io/)'s type used to perform automatic (algorithmic) differentiation of the vector function `f`. See [`dae-cpp` types](https://dae-cpp.github.io/prerequisites.html#dae-cpp-types) section.

{: .important }
The Vector Function class must not be used to save or post-process the solution vector `x` and time `t`. For this purpose, use the [Solution Manager](solution-manager.html) class.

## Examples

Consider the following vector function $$\mathbf{f}(\mathbf{x}, t)$$, where $$\mathbf{x} = \{x,y,z\}$$, as an example:

$$
\mathbf{f}(\mathbf{x}, t) =
\begin{vmatrix}
z + 1 \\
x^2 + y \\
2t
\end{vmatrix}.
$$

In the code, this vector function can be defined as

```cpp
class UserDefinedVectorFunction : public daecpp::VectorFunction
{
public:
    void operator()(daecpp::state_type &f, const daecpp::state_type &x, const double t) const
    {
        f[0] = x[2] + 1.0;         // z + 1
        f[1] = x[0] * x[0] + x[1]; // x*x + y
        f[2] = 2.0 * t;            // 2*t
    }
};
```

Inhereting the `daecpp::VectorFunction` class is a good practice (it serves as a blueprint), however, the user is allowed to define vector functions using their own custom classes, for example:

```cpp
struct UserDefinedVectorFunction
{
    void operator()(daecpp::state_type &f, const daecpp::state_type &x, const double t)
    {
        f[0] = x[2] + 1.0;         // z + 1
        f[1] = x[0] * x[0] + x[1]; // x*x + y
        f[2] = 2.0 * t;            // 2*t
    }
};
```
