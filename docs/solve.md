---
title: Solving DAE system
layout: default
nav_order: 8
---

Work in progress
{: .label .label-red }

# Solving DAE system

Once the DAE system is defined (see [Classes to define DAE system](classes.html)), we are ready to start solving it.
Generally, there are two ways of calling the main `dae-cpp` solver from the program and start computation:

- Use the [`System`](#system-class) class, which serves as a wrapper for the lower-level [`daecpp::solve(...)`](#daecppsolve-function) function. The class [`System`](#system-class) contains the [Solver Options](solver-options.html) and [Solution Holder](solution-manager.html#solution-holder-class) objects. The latter is used to save the solution vector every time step. Both objects are public members of the [`System`](#system-class) class and can be accessed by the user. It's a very easy class to use (see [Quick Start example](quick-start.html)), but it does not allow the user to define a custom [Solution Manager](solution-manager.html).
- Use the lower-level [`daecpp::solve(...)`](#daecppsolve-function) function directly, providing the user-defined [DAE system classes](#system-class), [Solver Options](solver-options.html), [Solution Manager](solution-manager.html), etc., as arguments. This is a more flexible approach (see [Simple DAE](https://github.com/dae-cpp/dae-cpp/blob/master/examples/simple_dae/simple_dae.cpp) example).

In this section, we consider both approaches to start the computation.

## `System` class

Serves as a wrapper for lower level [`daecpp::solve(...)`](#daecppsolve-function) function calls. Contains the [Solver Options](solver-options.html) and [Solution Holder](solution-manager.html#solution-holder-class) objects.

### `System` class public members

### `System` class constructor

### `System` class `solve(...)` method

### Example

## `daecpp::solve(...)` function

Integrates the system of DAEs.

### Example
