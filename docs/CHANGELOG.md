---
title: CHANGELOG
layout: default
nav_order: 11
---

# CHANGELOG

All notable user-facing changes to the `dae-cpp` project are documented in this page.

## v2.1.0

New
{: .label .label-green }

Version 2.1 allows the user to define the shape (structure) of the Jacobian matrix. I.e., instead of providing analytic Jacobian (or not providing it at all, which is slow if the system is large enough), the user can specify the positions of non-zero elements in the Jacobian. The solver will use automatic differentiation for the specified elements only. This works nearly as fast as analytic Jacobian without requiring the user to differentiate the vector function manually.

- Added `JacobianMatrixShape` and `VectorFunctionElements` helper classes to define the Jacobian matrix shape and the vector function
- Added `JacobianCompare` class that helps the user to compare the user-defined Jacobian (either defined explicitly or using Jacobian shape) with the one computed automatically from the system RHS
- Added `jacobian_shape` and `jacobian_compare` examples
- Updated `autodiff` to `v1.1.2`
- Updated Eigen (commit from 28th March 2024, see issue [#52](https://github.com/dae-cpp/dae-cpp/issues/52))
- Updated `googletest` from `v.1.14.x` to `v1.15.2`
- Added integration test that uses Jacobian derived from the given shape
- Added unit tests for all new classes (`JacobianMatrixShape`, `VectorFunctionElements`, `JacobianCompare`)
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
