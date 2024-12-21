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

## Element-by-element vector function to define the Jacobian shape

For relatively small systems, there is no need to provide the Jacobian matrix (neither its shape), as it will be computed fast enough automatically.
If the user decides to provide manually derived Jacobian, it can be done using [Jacobian Matrix](jacobian-matrix.html) class.
However, if the user provides the [shape of the Jacobian matrix](jacobian-matrix.html#jacobian-matrix-shape), the vector function must be defined element-by-element, so the solver can call specific elements of the vector function individually to perform automatic differentiation.

The vector function from the [Examples](#examples) section above can be defined element-by-element by inheriting the `daecpp::VectorFunctionElements` class:

```cpp
struct UserDefinedVectorFunction : daecpp::VectorFunctionElements
{
    daecpp::dual_type equations(const daecpp::state_type &x, const double t, const daecpp::int_type i) const
    {
        if (i == 0)
            return x[2] + 1.0; // z + 1
        else if (i == 1)
            return x[0] * x[0] + x[1]; // x*x + y
        else if (i == 2)
            return 2.0 * t // 2*t
        else
        {
            // This should never happen
            ERROR("Index i in UserDefinedVectorFunction is out of boundaries");
        }
    }
};
```

Unlike `daecpp::VectorFunction`, here we define each element of the vector function individually, depending on the index `i`.
This obviously comes with some computational time penalty compared to the case where we define the entire vector function as an array.
However, defining the RHS element-by-element like in the example above will allow us to define only the positions of non-zero elements of the Jacobian without manually differentiating the entire vector function (which in some cases can be difficult and error-prone).

{: .note }
The class `VectorFunctionElements` is abstract, where `dual_type equations(const state_type &x, const double t, const int_type i) const` function must be implemented in the derived class.

After defining the vector function this way, define the [Jacobian matrix shape](jacobian-matrix.html#jacobian-matrix-shape).

For more detailed example, refer to the [Jacobian shape](https://github.com/dae-cpp/dae-cpp/blob/master/examples/jacobian_shape/jacobian_shape.cpp) example.
