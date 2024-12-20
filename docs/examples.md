---
title: Examples
layout: default
nav_order: 10
---

Work in progress
{: .label .label-red }

# Examples

## Quick Start

The Quick Start example can be found in the [Quick Start](quick-start.html) section and in the [quick_start.cpp](https://github.com/dae-cpp/dae-cpp/blob/master/examples/quick_start/quick_start.cpp) source file.
{: .fs-5 .fw-400 }

A basic example of how to start solving a very simple DAE using the [`System`](solve.html#system-class) class.

## Simple DAE

Simple DAE source file example: [simple_dae.cpp](https://github.com/dae-cpp/dae-cpp/blob/master/examples/simple_dae/simple_dae.cpp)
{: .fs-5 .fw-400 }

Here we solve another simple system of DAEs.
This example introduces [Solution Manager](solution-manager.html), that will work as a solution observer.

## Perovskite model

Perovskite model source file example: [perovskite.cpp](https://github.com/dae-cpp/dae-cpp/blob/master/examples/perovskite_model/perovskite.cpp)
{: .fs-5 .fw-400 }

A more sophisticated example, where we solve a big DAE system that describes the potential distribution and ion concentration in a perovskite solar cell.
We show how to add parameters to the user-defined mass matrix, vector function, and Jacobian.
This example also demonstrates a custom [Solution Manager](solution-manager.html) implementation that can work as an observer and event function.

## Flame propagation model

Flame propagation source file example: [flame_propagation.cpp](https://github.com/dae-cpp/dae-cpp/blob/master/examples/flame_propagation/flame_propagation.cpp)
{: .fs-5 .fw-400 }

When you light a match, the ball of flame grows rapidly until it reaches a critical size.
Then it remains at that size because the amount of oxygen being consumed by the combustion
in the interior of the ball balances the amount of oxigen available through the surface.
This example solves **stiff** equation of flame propagation for the scalar variable $$y(t)$$ which represents the radius of the ball.

## Jacobian matrix shape

Jacobian matrix shape source file example: [jacobian_shape.cpp](https://github.com/dae-cpp/dae-cpp/blob/master/examples/jacobian_shape/jacobian_shape.cpp)
{: .fs-5 .fw-400 }

This example demonstrates how to define the shape (structure) of the Jacobian matrix.
I.e., instead of providing the full analytic Jacobian (or not providing it at all, which is slow if the system is big), the user can specify the positions of non-zero elements in the Jacobian.
The solver will use automatic differentiation for the specified elements only.
This works (nearly) as fast as the analytic Jacobian without requiring the user to differentiate the vector function manually.

## Checking user-defined Jacobian

Jacobian matrix checking source file example: [jacobian_compare.cpp](https://github.com/dae-cpp/dae-cpp/blob/master/examples/jacobian_compare/jacobian_compare.cpp)
{: .fs-5 .fw-400 }

In this example, we do not solve any DAEs. Instead, we define a simple vector function, the corresponding Jacobian matrix, and then we use a built-in helper class `JacobianCompare` to compare our manually derived Jacobian with the one computed algorithmically from the vector function.
We will make a few mistakes in the analytic Jacobian on purpose to see what information `JacobianCompare` can provide.
