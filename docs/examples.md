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
