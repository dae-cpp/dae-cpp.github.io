---
title: Solving DAE system
layout: default
nav_order: 8
---

# Solving DAE system

Once the DAE system is defined (see [Classes to define DAE system](classes.html)), we are ready to start solving it.
Generally, there are two ways of calling the main `dae-cpp` solver from the program and start computation:

- Use the [`System`](#system-class) class, which serves as a wrapper for the lower-level [`daecpp::solve(...)`](#daecppsolve-function) function. The class [`System`](#system-class) contains the [Solver Options](solver-options.html) and [Solution Holder](solution-manager.html#solution-holder-class) objects. The latter is used to save the solution vector every time step. Both objects are public members of the [`System`](#system-class) class and can be accessed by the user. It's a very easy class to use (see [Quick Start example](quick-start.html)), but it does not allow the user to define a custom [Solution Manager](solution-manager.html).
- Use the lower-level [`daecpp::solve(...)`](#daecppsolve-function) function directly, providing the user-defined [DAE system classes](#system-class), [Solver Options](solver-options.html), [Solution Manager](solution-manager.html), etc., as arguments. This is a more flexible approach (see [Simple DAE](https://github.com/dae-cpp/dae-cpp/blob/master/examples/simple_dae/simple_dae.cpp) example).

In this section, we consider both approaches to start the computation.

----

# System class

The `System` class serves as a wrapper for lower level [`daecpp::solve(...)`](#daecppsolve-function) function calls. Contains the [Solver Options](solver-options.html) and [Solution Holder](solution-manager.html#solution-holder-class) objects.

## System class public members

| Object | Type | Description |
| ------ | ---- | ----------- |
| `opt` | `SolverOptions` | Solver Options object. Provides default solver options which can be redefined by the user. See [Solver Options](solver-options.html) section. |
| `sol` | `SolutionHolder` | Solution Holder object. Stores solution vector and the corresponding time for the user. See [Solution Holder](solution-manager.html#solution-holder-class) section. |
| `status` | `daecpp::exit_code` | Exit code of the `solve(...)` method. See [Exit codes](#exit-codes) below. |

## System class constructor

The `System` class constructor is straightforward:

```cpp
daecpp::System(Mass mass, RHS rhs);
```

It takes the user-defined mass matrix object `mass` (see [Mass Matrix class](mass-matrix.html) section) and the vector function (the RHS of the system) object `rhs` (see [Vector Function class](vector-function.html) section). These two objects define the entire DAE system.

## System class `solve(...)` method

The `solve(...)` method initiates the DAE system solving algorithm, starts computation, and returns `daecpp::exit_code` (see [Exit codes](#exit-codes)) upon completion. The exit code will be also saved in the `status` public variable.

Here is the summary of the `solve(...)` method:

## `daecpp::exit_code solve(x0, t, [jacobian]);`

<br>

| Parameter | Type(s) | Description |
| --------- | ------- | ----------- |
| `x0` | `const daecpp::state_vector &` | The initial condition (initial state vector) |
| `t` | `const double` **or** `const std::vector<double> &` | Integration interval `[0, t]` **or** a vector of integration times (useful if the user need the output at particular times `t`) |
| `jacobian` | User-defined [Jacobian Matrix](jacobian-matrix.html) | **(Optional)** Jacobian matrix (matrix of the RHS derivatives) |

## Examples

A complete example is provided in the [Quick Start](quick-start.html) section.
Here are a few simplified examples of how to use the `System` class:

```cpp
MyMassMatrix mass; // Mass matrix object
MyRHS rhs;         // Vector-function object

daecpp::System my_system(mass, rhs); // Defines the DAE system object

state_vector x0{0, 1}; // The initial state vector (initial condition)
double t{1.0};         // The integration interval: t = [0, 1.0]

my_system.solve(x0, t); // Solves the system with the given initial condition `x0` and time `t`
```

Another example:

```cpp
daecpp::System my_system((MyMassMatrix()), (MyRHS())); // Note the parentheses

my_system.opt.verbosity = daecpp::verbosity::normal; // Update solver options

int status = my_system.solve({0, 1}, 1.0, MyJacobian()); // Solves the system with Jacobian

if(!status)
{
    my_system.sol.print(); // Prints solution on screen
}
```

Solution vector of vectors `x` and the corresponding vector of times `t` will be stored in `my_system.sol.x` and `my_system.sol.t`, respectively.

The entire C++ code is provided in the [Quick Start example](https://github.com/dae-cpp/dae-cpp/blob/master/examples/quick_start/quick_start.cpp).

----

# `daecpp::solve(...)` function

Integrates the system of DAEs with the given mass matrix, vector function (the RHS), Jacobian, initial state, time interval(s), solver options, and the given solution manager (observer and event function).

As in the `System` class, the `daecpp::solve(...)` function initiates the DAE system solving algorithm, starts computation, and returns `daecpp::exit_code` (see [Exit codes](#exit-codes)) upon completion.

Here is the summary of the `solve(...)` function:

## `daecpp::exit_code solve(mass, rhs, [jac], x0, t, sol_mgr, [opt]);`

<br>

| Parameter | Type(s) | Description |
| --------- | ------- | ----------- |
| `mass` | User-defined [Mass Matrix](mass-matrix.html) | Mass matrix of the DAE system |
| `rhs` | User-defined [Vector Function](vector-function.html) | Vector function (the RHS) of the DAE system |
| `jac` | User-defined [Jacobian Matrix](jacobian-matrix.html) | **(Optional)** Jacobian matrix (matrix of the RHS derivatives) |
| `x0` | `const daecpp::state_vector &` | The initial condition (initial state vector) |
| `t` | `const double` **or** `const std::vector<double> &` | Integration interval `[0, t]` **or** a vector of integration times (useful if the user need the output at particular times `t`) |
| `sol_mgr` | User-defined [Solution Manager](solution-manager.html) | Solution manager (solution observer and/or event function) |
| `opt` | `const daecpp::SolverOptions &` | **(Optional)** Solver options |

## Exit codes

The `solve(...)` function can produce the following exit codes upon computation completion:

| Exit code | Value | Description |
| --------- | ----- | ----------- |
| `exit_code::success` | `0` | Solving completed successfully |
| `exit_code::diverged` | `1` | Solution diverged (Newton method diverged) |
| `exit_code::linsolver_failed_decomposition` | `2` | Linear solver: failed matrix decomposition |
| `exit_code::linsolver_failed_solving` | `3` | Linear solver: failed linear system solving |
| `exit_code::unknown` | `10` | Unknown error (can be a bug, should never happen) |

## Examples

An example of using `daecpp::solve(...)` function is provided in the [simple_dae.cpp](https://github.com/dae-cpp/dae-cpp/blob/master/examples/simple_dae/simple_dae.cpp) source file.

Without user-defined Jacobian matrix and solver options:

```cpp
int main()
{
    daecpp::state_vector x0{0, 1}; // Initial condition: x = 0, y = 1
    double t_end{1.0};             // Solution interval: t = [0, t_end]

    daecpp::state_vector error; // Absolute error (defined in MyObserver class)

    // Solves the DAE system
    int status = daecpp::solve(MyMassMatrix(), MyRHS(),
                               x0, t_end,
                               MyObserver(error));

    // Prints the error at the very end of computation
    std::cout << "Abs. error: " << error.back() << '\n';

    return status;
}
```

With analytic Jacobian and a few redefined solver options:

```cpp
int main()
{
    daecpp::state_vector x0{0, 1}; // Initial condition: x = 0, y = 1
    double t_end{1.0};             // Solution interval: t = [0, t_end]

    daecpp::state_vector error; // Absolute error (defined in MyObserver class)

    // To add user-defined solver options:
    daecpp::SolverOptions opt;
    opt.verbosity = daecpp::verbosity::extra;
    opt.dt_max = 0.01;

    // Solves the DAE system
    int status = daecpp::solve(MyMassMatrix(), MyRHS(), MyJacobian(),
                               x0, t_end,
                               MyObserver(error),
                               opt);

    // Prints the error at the very end of computation
    std::cout << "Abs. error: " << error.back() << '\n';

    return status;
}
```
