---
title: Jacobian Matrix class
layout: default
parent: Classes to define DAE system
nav_order: 5
---

# Jacobian Matrix class

Jacobian matrix class defines the Jacobian matrix $$\mathbf{J}(\mathbf{x}, t)$$ of the DAE system.
This matrix can be obtained by differentiating the vector function (the RHS) $$\mathbf{f}(\mathbf{x}, t)$$ of the DAE system

$$\mathbf{M}(t) \frac{\mathrm{d}\mathbf{x}}{\mathrm{d}t} = \mathbf{f}(\mathbf{x}, t)$$

with respect to $$\mathbf{x}$$.

{: .important }
Providing the Jacobian matrix for the DAE solver is optional. If not provided by the user, the Jacobian matrix will be computed by the solver automatically, using algorithmic differentiation package [`autodiff`](https://autodiff.github.io/).
However, for big DAE systems, it is highly recommended to provide manually (analytically) derived Jacobian. This can save a lot of computation time for the solver and significantly speed it up.

## Jacobian matrix definition

The main Jacobian matrix class `daecpp::JacobianMatrix` is an abstract class that provides an interface to define the Jacobian matrix $$\mathbf{J}(\mathbf{x}, t)$$.
In order to make use of this class, the user should inherit it and overload the `()` operator:

```cpp
class UserDefinedJacobianMatrix : public daecpp::JacobianMatrix
{
public:
    void operator()(daecpp::sparse_matrix &J, const daecpp::state_vector &x, const double t) const
    {
        // Jacobian matrix definition
    }
};
```

In the code above, the Jacobian matrix `J` should be defined in the [three-array sparse format](sparse-matrix.html) and can depend on the current state vector $$\mathbf{x}$$ and time `t`.
Initially, matrix `J` is empty and should be filled with non-zero elements.

{: .note }
The type of vector `x` in the class definition above is `daecpp::state_vector`, which is an alias of `std::vector<float_type>` type. See [`dae-cpp` types](https://dae-cpp.github.io/prerequisites.html#dae-cpp-types) section for more information.

## Examples

Consider the following vector function $$\mathbf{f}(\mathbf{x}, t)$$ from the [Vector Function class](vector-function.html#examples) section:

$$
\mathbf{f}(\mathbf{x}, t) =
\begin{vmatrix}
z + 1 \\
x^2 + y \\
2t
\end{vmatrix}.
$$

Differentiation this vector w.r.t. $$\{x,y,z\}$$ gives us the following Jacobian matrix $$\mathbf{J}$$:

$$
\mathbf{J} =
\begin{vmatrix}
0 & 0 & 1 \\
2x & 1 & 0 \\
0 & 0 & 0
\end{vmatrix}.
$$

It has 3 non-zero elements and does not depend on time.
In the code, this Jacobian matrix can be defined as

```cpp
class UserDefinedJacobianMatrix : public daecpp::JacobianMatrix
{
public:
    void operator()(daecpp::sparse_matrix &J, const daecpp::state_vector &x, const double t) const
    {
        J.reserve(3);        // Pre-allocate memory for 3 non-zero elements
        J(0, 2, 1.0);        // Row 0, column 2, non-zero element 1.0
        J(1, 0, 2.0 * x[0]); // Row 1, column 0, non-zero element (2 * x)
        J(1, 1, 1.0);        // Row 1, column 1, non-zero element 1.0
    }
};
```

Inhereting the `daecpp::JacobianMatrix` class is a good practice (it serves as a blueprint), however, the user is allowed to define Jacobian matrices using their own custom classes, for example:

```cpp
struct UserDefinedJacobianMatrix
{
    void operator()(daecpp::sparse_matrix &J, const daecpp::state_vector &x, const double t)
    {
        J.reserve(3);        // Pre-allocate memory for 3 non-zero elements
        J(0, 2, 1.0);        // Row 0, column 2, non-zero element 1.0
        J(1, 0, 2.0 * x[0]); // Row 1, column 0, non-zero element (2 * x)
        J(1, 1, 1.0);        // Row 1, column 1, non-zero element 1.0
    }
};
```

{: .note }
It is recommended to pre-allocate memory for the Jacobian matrix using `reserve(N_elements)` method, where `N_elements` is the number of non-zero elements in the matrix. If the number of non-zeros is difficult to estimate, it is better to overestimate `N_elements` than underestimate it to avoid unnecessary copying and memory reallocations.

For more information about defining the matrix in sparse format, refer to the [Sparse Matrix class](sparse-matrix.html) section.

## Automatic Jacobian matrix

{: .important }
*Automatic* (or *algorithmic*) differentiation is a [special technique](https://en.wikipedia.org/wiki/Automatic_differentiation) to compute the partial derivatives of a function by a computer program exactly (to working precision), by applying the [chain rule](https://en.wikipedia.org/wiki/Chain_rule). By automatic (algorithmic) Jacobian matrix in this documentation, we refer to the Jacobian computed using automatic differentiation.

The solver provides a helper class `daecpp::JacobianAutomatic` to compute the Jacobian matrix for the given vector function $$\mathbf{f}(\mathbf{x}, t)$$ algorithmically using [`autodiff`](https://autodiff.github.io/) package.
For relatively small systems, the user does not even need to define the Jacobian, not even automatic one. This will be handled by the solver itself. Behind the scenes, the solver will create an automatic Jacobian matrix object for the user, if analytic Jacobian is not provided.

However, in some cases, it can be difficult to derive the Jacobian matrix analytically. Or if the user has provided the Jacobian matrix, but the solution diverges, which means there might be a bug in the Jacobian matrix definition. In these cases, the user can try to feed the automatic Jacobian matrix to the solver explicitly, or print out (or save to a file) both user-defined and automatic Jacobians for comparison to find possible errors in the matrix definition.

In order to construct an automatic Jacobian matrix object, the user needs to provide the vector function object `rhs` (see [Vector Function class](vector-function.html) section):

```cpp
// Creates automatic Jacobian object for the given user-defined RHS
daecpp::JacobianAutomatic automaticJacobian(rhs);
```

In the example above, we created an object `automaticJacobian` which computes automatic Jacobian in sparse format suitable for the solver.
To print the Jacobian out (for example, to compare with the user-defined analytic Jacobian), convert the matrix to dense format and use `std::cout`:

```cpp
daecpp::sparse_matrix J;        // Empty Jacobian matrix
daecpp::state_vector x(N, 1.0); // State vector of size `N` populated with `1.0`
double t = 1.0;                 // Time

// Computes automatic Jacobian for the given state `x` and time `t`
automaticJacobian(J, x, t);

// Prints out Jacobian in dense format
std::cout << J.dense(N) << '\n';
```

{: .highlight }
*User-defined* Jacobian vs *automatic* Jacobian comparison functionality (element by element) will be added in future versions of the solver.
