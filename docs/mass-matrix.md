---
title: Mass Matrix class
layout: default
parent: Classes to define DAE system
nav_order: 3
---

# Mass Matrix class

The Mass matrix class defines the mass matrix $$\mathbf{M}(t)$$ of the DAE system written in the matrix-vector form:

$$\mathbf{M}(t) \frac{\mathrm{d}\mathbf{x}}{\mathrm{d}t} = \mathbf{f}(\mathbf{x}, t).$$

Note that in `dae-cpp`, the mass matrix can depend on time $$t$$ (but not on the state $$\mathbf{x}$$) and can be singular, which means that one or more of the rows of the matrix can contain zeros only, i.e., some of the equations in the system can be without time derivatives at all (nonlinear algebraic equations).

## Mass matrix definition

The main mass matrix class `daecpp::MassMatrix` is an abstract class that provides an interface to define the mass matrix $$\mathbf{M}(t)$$.
In order to make use of this class, the user should inherit it and overload the `()` operator:

```cpp
class UserDefinedMassMatrix : public daecpp::MassMatrix
{
public:
    void operator()(daecpp::sparse_matrix &M, const double t) const
    {
        // Mass matrix definition
    }
};
```

In the code above, the mass matrix `M` should be defined in the [three-array sparse format](sparse-matrix.html) and can depend on time `t`.
Initially, matrix `M` is empty and should be filled with non-zero elements.

## Examples

Consider the following mass matrix $$\mathbf{M}$$:

$$
\mathbf{M} =
\begin{vmatrix}
1 & 0 & 0 \\
0 & 2t & 0 \\
0 & 0 & 0
\end{vmatrix}.
$$

In the code, this mass matrix can be defined as

```cpp
class UserDefinedMassMatrix : public daecpp::MassMatrix
{
public:
    void operator()(daecpp::sparse_matrix &M, const double t) const
    {
        M.reserve(2);     // Pre-allocate memory for 2 non-zero elements
        M(0, 0, 1.0);     // Row 0, column 0, non-zero element 1.0
        M(1, 1, 2.0 * t); // Row 1, column 1, non-zero element (2 * t)
    }
};
```

Inhereting the `daecpp::MassMatrix` class is a good practice (it serves as a blueprint), however, the user is allowed to define mass matrices using their own custom classes, for example:

```cpp
struct UserDefinedMassMatrix
{
    void operator()(daecpp::sparse_matrix &M, const double t)
    {
        M.reserve(2);     // Pre-allocate memory for 2 non-zero elements
        M(0, 0, 1.0);     // Row 0, column 0, non-zero element 1.0
        M(1, 1, 2.0 * t); // Row 1, column 1, non-zero element (2 * t)
    }
};
```

{: .note }
It is recommended to pre-allocate memory for the mass matrix using `reserve(N_elements)` method, where `N_elements` is the number of non-zero elements in the matrix. If the number of non-zeros is difficult to estimate, it is better to overestimate `N_elements` than underestimate it to avoid unnecessary copying and memory reallocations.

For more information about defining the matrix in sparse format, refer to the [Sparse Matrix class](sparse-matrix.html) section.

## Identity mass matrix

A helper class `daecpp::MassMatrixIdentity` can be used to construct an identity matrix `N` by `N`:

```cpp
daecpp::MassMatrixIdentity identityMatrix(16); // Creates identity mass matrix 16x16
```

In the example above, we created an object `identityMatrix` which holds an identity matrix of size 16x16 in sparse format suitable for the solver.

## Zero mass matrix

Similar ot the example above, a helper class `daecpp::MassMatrixZero` can be used to construct a zero matrix (a matrix filled by zeros only). This matrix can be useful to solve pure algebraic systems without time derivatives.

```cpp
daecpp::MassMatrixZero zeroMatrix; // Creates empty mass matrix for algebraic system
```

Note that `daecpp::MassMatrixZero` does not require the matrix size (unlike `MassMatrixIdentity`), because zero matrix does not contain any non-zero elements. It's an empty sparse matrix, and its size will be derived from the initial state vector size.
