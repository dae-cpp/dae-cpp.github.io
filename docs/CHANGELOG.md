---
title: CHANGELOG
layout: default
nav_order: 11
---

# CHANGELOG

All notable user-facing changes to the `dae-cpp` project are documented in this page.

## v2.2.0

New
{: .label .label-green }

In version 2.2.x, the solver can reduce the time step size and redo the current time step until a certain condition is met.
This is useful if the solver needs to stop when, for example, one of the variables should reach the given value, and the solver should not "overshoot".
This version also includes several important updates, such as bug fixes and corrections for compiler warnings.

- Added `solver_command::command` enum with [Solution Manager](solution-manager.html) functor return values. Used to send "commands" to the solver from the user-defined Solution Manager
- The user can request the solver to decrease the time step size
- The user can request the solver to decrease the time step size and then redo the current time step
- Fix parentheses warning in `autodiff`
- Fix `-Wall` and `-Wextra` warnings (e.g., "reorder" and "unused variable" warnings)
- Switch from `uint32_t` to `int32_t` for `int_type`
- Switch from `uint64_t` to `int64_t` for `int_type` if `DAECPP_LONG` is defined
- Fix the relative error check
- Fix situation when the solver can decrease and then immediately increase the time step
- Fix tolerances for single precision

## v2.1.1

- Fixed bug when the solver could not recover from divergence and could not redo the last time step correctly
- Type `dual_type` deprecated but supported, added `state_value` instead as it is clearer
- Added `recover_from_linsolver_failure` solver option (`true` by default). Now the solver will try to recover from the linear solver failure (e.g., on the matrix decomposition step) by rolling back and decreasing the time step.

## v2.1.0

Version 2.1.x allows the user to define the shape (structure) of the Jacobian matrix. I.e., instead of providing analytic Jacobian (or not providing it at all, which is slow if the system is large enough),
the user can specify the positions of non-zero elements in the Jacobian. The solver will use automatic differentiation for the specified elements only. This works nearly as fast as analytic Jacobian without requiring the user to differentiate the vector function manually.

- Added [`daecpp::JacobianMatrixShape`](jacobian-matrix.html#jacobian-matrix-shape) and [`daecpp::VectorFunctionElements`](vector-function.html#element-by-element-vector-function-to-define-the-jacobian-shape) helper classes to define the Jacobian matrix shape and the vector function
- Added [`daecpp::JacobianCompare`](jacobian-matrix.html#jacobian-matrix-check) class that helps the user to compare the user-defined Jacobian (either defined explicitly or using Jacobian shape) with the one computed automatically from the system RHS
- Added [Jacobian shape](https://github.com/dae-cpp/dae-cpp/blob/master/examples/jacobian_shape/jacobian_shape.cpp) and [Jacobian compare](https://github.com/dae-cpp/dae-cpp/blob/master/examples/jacobian_compare/jacobian_compare.cpp) examples
- Updated `autodiff` to `v1.1.2`
- Updated Eigen (commit from 28th March 2024, see issue [#52](https://github.com/dae-cpp/dae-cpp/issues/52))
- Updated `googletest` from `v.1.14.x` to `v1.15.2`
- Added integration test that uses Jacobian derived from the given shape
- Added unit tests for all new classes (`daecpp::JacobianMatrixShape`, `daecpp::VectorFunctionElements`, `daecpp::JacobianCompare`)
- Minor solver updates

## v2.0.1

- Added [Flame Propagation](https://github.com/dae-cpp/dae-cpp/blob/master/examples/flame_propagation/flame_propagation.cpp) example (stiff equation)
- Added `daecpp::dual_type` for automatic differentiation of the vector function (used in [Flame Propagation](https://github.com/dae-cpp/dae-cpp/blob/master/examples/flame_propagation/flame_propagation.cpp) example)
- Added integration test based on the "Flame Propagation" example
- Updated build instructions

## v2.0.0

Redesigned version of the library. Consider this version as a public beta.
Please report any bugs using GitHub [issues](https://github.com/dae-cpp/dae-cpp/issues).

----

## v1.1

Retired
{: .label .label-yellow }

{: .highlight }
Version `1.x` has been archived in the [legacy](https://github.com/dae-cpp/dae-cpp/tree/legacy) branch of the repository.

- Tweaked the DAE solver
- Added more examples

----

## v1.0

Retired
{: .label .label-yellow }

First working version of the DAE solver.
