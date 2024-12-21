---
title: Jacobian Matrix class
layout: default
parent: Classes to define DAE system
nav_order: 5
---

# Jacobian Matrix class

The Jacobian Matrix class defines the Jacobian matrix $$\mathbf{J}(\mathbf{x}, t)$$ of the DAE system.
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

{: .important }
The Jacobian Matrix class must not be used to save or post-process the solution vector `x` and time `t`. For this purpose, use the [Solution Manager](solution-manager.html) class.

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

{: .note }
*Automatic* (or *algorithmic*) differentiation is a [special technique](https://en.wikipedia.org/wiki/Automatic_differentiation) to compute the partial derivatives of a function by a computer program exactly (to working precision), by applying the [chain rule](https://en.wikipedia.org/wiki/Chain_rule). By automatic (algorithmic) Jacobian matrix in this documentation, we refer to the Jacobian computed using automatic differentiation.

The solver provides a helper class `daecpp::JacobianAutomatic` to compute the Jacobian matrix for the given vector function $$\mathbf{f}(\mathbf{x}, t)$$ algorithmically using [`autodiff`](https://autodiff.github.io/) package.
For relatively small systems, the user does not even need to define the Jacobian, not even automatic one. This will be handled by the solver itself. Behind the scenes, the solver will create an automatic Jacobian matrix object for the user, if analytic Jacobian is not provided.

For big systems, it is highly recommended to provide analytic (user-defined) Jacobian. However, in some cases, it can be difficult to derive the Jacobian matrix analytically. Or the user has provided the Jacobian matrix, but the solution diverges, which means there might be a bug in the Jacobian matrix definition. In these cases, the user can try to feed the automatic Jacobian matrix to the solver explicitly, or print out (or save to a file) both user-defined and automatic Jacobians for comparison to find possible errors in the matrix definition.

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

## Jacobian matrix shape

If defining the Jacobian matrix manually as it is described above is not a feasible task (e.g., due to a very complex non-linear RHS), the solver allows the user to specify only the positions of non-zero elements of the Jacobian matrix (i.e., the *Jacobian matrix shape*). In this case, all the derivatives will be calculated automatically with a very small computation time penalty (compared to the manually derived analytic Jacobian).

To define the Jacobian matrix shape, the user needs to define the vector function (RHS) of the system in an element-by-element (equation-by-equation) way using a custom class derived from [`daecpp::VectorFunctionElements`](vector-function.html#element-by-element-vector-function-to-define-the-jacobian-shape).
After that, use another class derived from `daecpp::JacobianMatrixShape` to specify the positions of each non-zero element in the Jacobian matrix.

Class `daecpp::JacobianMatrixShape` contains the following helper methods:

- `void add_element(const daecpp::int_type ind_i, const daecpp::int_type ind_j)` to define a single non-zero element *(i, j)*
- `void add_element(const daecpp::int_type ind_i, const std::vector<int_type> &ind_j)` to define an entire row *i* of non-zero elements using a vector of indices *j*
- `void clear()` to clear the array of non-zero elements
- `void reserve(const daecpp::int_type N_elements)` to reserve memory for `N_elements` non-zero elements of the Jacobian (which is recommended)

The constructor of `daecpp::JacobianMatrixShape` takes the vector function object derived from [`daecpp::VectorFunctionElements`](vector-function.html#element-by-element-vector-function-to-define-the-jacobian-shape).

Here is a simple example of defining 3 non-zero elements of the Jacobian:

```cpp
struct UserDefinedJacobianShape : daecpp::JacobianMatrixShape<UserDefinedRHS>
{
    explicit UserDefinedJacobianShape(UserDefinedRHS rhs) : daecpp::JacobianMatrixShape(rhs)
    {
        reserve(3);             // Reserves memory for 3 non-zero elements
        add_element(0, 1);      // Adds element (0, 1), e.g., row 0, column 1
        add_element(1, {0, 1}); // Adds elements (1, 0) and (1, 1)
    }
};
```

{: .important }
Note that the RHS should be defined using [`daecpp::VectorFunctionElements`](vector-function.html#element-by-element-vector-function-to-define-the-jacobian-shape), so the solver can call specific elements of the vector function individually to perform automatic differentiation element-by-element efficiently.

For more details, refer to the [Jacobian shape](https://github.com/dae-cpp/dae-cpp/blob/master/examples/jacobian_shape/jacobian_shape.cpp) example.

## Jacobian matrix check

User-defined Jacobians defined either analytically or using Jacobian shape can be conveniently compared to fully automatic Jacobian for debug purposes.
This can be achieved by using `daecpp::JacobianCompare` helper class.
The constructor of this class takes the user-defined Jacobian (defined analytically or from the Jacobian shape) and the vector function (RHS).
Then the object of the `daecpp::JacobianCompare` class can be called as a functor with the given state `x` and, optionally, time `t`.

Consider the following example:

```cpp
// Fill vectors x at which the Jacobian matrix will be tested
daecpp::state_vector x0 = {1.0, 2.0, 3.0, 4.0};
daecpp::state_vector x1 = {-0.5, -0.1, 0.5, 0.1};

// Check user-defined Jacobian at `x0` and `x1` and times t = 1 and 2
{
    auto jac_comparison = daecpp::JacobianCompare(UserJacobian(), UserRHS());

    auto N_errors_1 = jac_comparison(x0, 1.0);
    auto N_errors_2 = jac_comparison(x1, 2.0);
}
```

The functor `jac_comparison` returns the number of errors found in the Jacobian.
It also prints on screen a summary of all inconsistencies found in the user-defined Jacobian compared to the fully automatic one.
An example of such output is given below:

```txt
Jacobian matrix comparison summary at time t = 1:
-- Found 4 difference(s) compared to the automatic (reference) Jacobian:
----------------------------------------------------------------------------------------
 Row        | Column     | Reference value    | User-defined value | Absolute error
------------+------------+--------------------+--------------------+--------------------
 2          | 0          | 0.540302           | 0                  | -0.540302
 2          | 1          | 0                  | 0.540302           | 0.540302
 0          | 3          | 0.5                | 1                  | 0.5
 3          | 3          | 1                  | 0                  | -1
----------------------------------------------------------------------------------------
```

Here we can see that in this example:

- element (2, 1) should be (2, 0)
- element (0, 3) is incorrect (should be 0.5, not 1)
- element (3, 3) is not defined at all

{: .note }
Jacobian comparisons are element-by-element and hence can be slow. These comparisons are for the debug purposes only and should be removed from the production runs.

For more details, refer to the [Jacobian compare](https://github.com/dae-cpp/dae-cpp/blob/master/examples/jacobian_compare/jacobian_compare.cpp) example.
