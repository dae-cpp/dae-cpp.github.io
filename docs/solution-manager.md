---
title: Solution Manager class
layout: default
nav_order: 6
---

Work in progress
{: .label .label-red }

# Solution Manager class

The Solution Manager class can serve as a solution observer and/or as an event function that stops computation when a certain event occurs. Solution Manager functor will be called every time step, providing the time `t` and the corresponding solution `x` for further post-processing. If the functor returns an integer which is not equal to `0` (i.e., `true`), the computation will immediately stop.

The solver provides a derived from the Solution Manager class called `daecpp::Solution`, which writes the solution vector `x` and time `t` every time step or every specific time defined by the user into the [solution holder](#solution-holder) object. The user can access this object for further post-processing, printing the solution on the screen or writing it to a file.

## Solution holder

## Solution class
