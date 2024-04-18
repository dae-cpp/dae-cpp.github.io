---
title: Sparse Matrix class
layout: default
nav_order: 2
---

# Sparse Matrix class

Sparse Matrix class `daecpp::sparse_matrix` defines sparse matrix holder in [three-array format](#three-array-format). 
It is used to define the [mass matrix](mass-matrix.html) and, optionally, the [Jacobian matrix](jacobian-matrix.html) of the DAE system.
The class provides a set of helper tools for the user to allocate memory, define and convert sparse matrices, etc., in a very straightforward way, e.g.:

```cpp
sparse_matrix M;                 // Creates empty sparse matrix M
M.reserve(2);                    // Reserves memory for 2 elements (optional)
M.add_element(0, 1, 12.0);       // Fills row 0, column 1 with value 12.0
M.add_element(1, 1, 42.0);       // Fills row 1, column 1 with value 42.0
std::cout << M.dense(3) << '\n'; // Prints matrix M on screen assuming it's a 3x3 matrix
```

## Three-array format

The three-array format defines a sparse matrix using two (unsigned) integer vectors of indices `i` and `j` and one floating-point vector `A` of the corresponding non-zero elements of the matrix. Only non-zero elements of the sparse matrix should be defined and stored. Theoretically, all three arrays can be empty. This will define a zero matrix where all elements are zeros.

{: .note }
All three arrays `i`, `j`, and `A` should be the same size. This can be (and will be) checked by calling the `void check()` method of the class `daecpp::sparse_matrix`.

Here is an example of a sparse matrix:

$$
\mathbf{M} =
\begin{vmatrix}
1 & 0 & 3 \\
0 & 2 & 0 \\
0 & 0 & 0
\end{vmatrix}.
$$

This matrix contains only 3 non-zero elements: `1`, `2`, and `3`.
Here is the matrix $$\mathbf{M}$$ definition in three-array format:

| `i` (row index) | `j` (column index) | `A` (non-zero element) |
| --------------- | ------------------ | ---------------------- |
| 0               | 0                  | 1.0                    |
| 1               | 1                  | 2.0                    |
| 0               | 2                  | 3.0                    |

The indices `i` and `j` define the position of the corresponding non-zero element `A` in the matrix. Index `i` is the row index and index `j` is the column index.

It doesn't matter in which order you define each element of the matrix.

{: .note }
The row and column numeration starts from `0`.

## Sparse Matrix class public data

| Variable | Type | Description |
| -------- | ---- | ----------- |
| `A`      | `std::vector<float_type>` | Vector of non-zero elements `A` |
| `i`      | `std::vector<int_type>`   | Vector of row indices `i`       |
| `j`      | `std::vector<int_type>`   | Vector of column indices `j`    |

For `float_type` and `int_type` definition, refer to the [Prerequisites](prerequisites.html#dae-cpp-types) section.

{: .note }
All three vectors are initially empty an NOT preallocated

## Sparse Matrix class methods

## `void operator()(int_type ind_i, int_type ind_j, float_type A_ij)`

<br>
Adds next non-zero element to the sparse matrix.
Duplicated elements will be summed up.

### Parameters:

- `ind_i` - row index of the element (`int_type`)
- `ind_j` - column index of the element (`int_type`)
- `A_ij`- non-zero element (`float_type`)

### Example:

```cpp
sparse_matrix M; // Creates empty sparse matrix M
M(0, 1, 42.0);   // Fills row 0, column 1 with value 42.0
M(1, 2, 10.0);   // Defines next non-zero element: row 1, column 2, value 10.0
M(0, 1, 2.0);    // Duplicated element is OK, it will be summed up, i.e., the value will be 44.0
```

----

## `void add_element(int_type ind_i, int_type ind_j, float_type A_ij)`

<br>
An alias to the operator `()` defined above.
Adds next non-zero element to the sparse matrix.
Duplicated elements will be summed up.

### Parameters:

- `ind_i` - row index of the element (`int_type`)
- `ind_j` - column index of the element (`int_type`)
- `A_ij`- non-zero element (`float_type`)

### Example:

```cpp
sparse_matrix M;           // Creates empty sparse matrix M
M.add_element(0, 1, 42.0); // Fills row 0, column 1 with value 42.0
M.add_element(1, 2, 10.0); // Defines next non-zero element: row 1, column 2, value 10.0
M.add_element(0, 1, 2.0);  // Duplicated element will be summed up, i.e., the value will be 44.0
```

----

## `void reserve(int_type N_elements)`

<br>
Reserves memory for `N_elements` non-zero elements. It is strongly advised to allocate memory before filling in the sparse matrix arrays to avoid multiple copying and reallocation.

### Parameter:

- `N_elements` - estimated number of non-zero elements (`int_type`)

### Example:

```cpp
sparse_matrix M; // Creates empty sparse matrix M
M.reserve(16);   // Pre-allocates memory for 16 non-zero elements
```

----

## `void clear()`

<br>
Clears the sparse matrix arrays and frees the memory. Invalidates iterators and pointers. The matrix becomes a *zero* matrix.

----

## `void check() const`

<br>
Performs a few basic checks of the matrix structure. Particularly, checks that the size of vectors `i`, `j`, and `A` is the same.
If it fails, the solver exits with error message (it is not possible to recover).

----

## `auto dense(int_type N) const`

<br>
Represents matrix in dense format. Suitable for printing using `std::cout`.

### Parameter:

- `N` - matrix size (square matrix `N` x `N`) (`int_type`)

### Returns:

- Dense matrix representation in `Eigen` format.

### Example:

```cpp
sparse_matrix M;                 // Creates empty sparse matrix M
M(0, 1, 42.0);                   // Fills row 0, column 1 with value 42.0
std::cout << M.dense(3) << '\n'; // Prints matrix on screen assuming it's a 3x3 matrix
```

----

## `Eigen::SparseMatrix<float_type> convert(int_type N) const`

<br>
Converts matrix from `dae-cpp` three-array format to `Eigen::SparseMatrix` format.

### Parameter:

- `N` - matrix size (square matrix `N` x `N`) (`int_type`)

### Returns:

- `Eigen::SparseMatrix<float_type>` (`core::eimat`) sparse matrix.

----

## `std::size_t N_elements() const`

<br>
Returns the number of non-zero elements in the matrix.
