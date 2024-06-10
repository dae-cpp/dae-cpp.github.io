---
title: Home
layout: home
nav_order: 1
---

![version](https://img.shields.io/badge/version-2.0.1-blue)

<p align="center">
<img src="../assets/images/logo.png" alt="dae-cpp logo" width="250"/>
</p>

{: .note }
This is a massively reworked and updated version of `dae-cpp`, which is incompatible with the previous version (`1.x`). If your project still relies on the old `dae-cpp`, it is archived in the [legacy](https://github.com/dae-cpp/dae-cpp/tree/legacy) branch.

## What is `dae-cpp`

`dae-cpp` is a cross-platform, header-only C++17 library for solving stiff and non-stiff systems of [Differential-Algebraic Equations](https://en.wikipedia.org/wiki/Differential-algebraic_system_of_equations) (DAE). DAE systems are equation systems that can contain both *differential* and *algebraic* equations and can be written in the following matrix-vector form:

$$\mathbf{M}(t) \frac{\mathrm{d}\mathbf{x}}{\mathrm{d}t} = \mathbf{f}(\mathbf{x}, t),$$

to be solved in the interval $$t \in [0, t_\mathrm{end}]$$ with the given initial condition $$\mathbf{x}\rvert_{t=0} = \mathbf{x}_0$$. Here $$\mathbf{M}(t)$$ is the mass matrix (can depend on time), $$\mathbf{x}(t)$$ is the state vector, and $$\mathbf{f}(\mathbf{x}, t)$$ is the (nonlinear) vector function of the state vector $$\mathbf{x}$$ and time $$t$$.

### How does it work

The DAE solver uses implicit Backward Differentiation Formulae (BDF) of orders I-IV with adaptive time stepping. Every time step, the BDF integrator converts the original DAE system to a system of nonlinear equations, which is solved using iterative [Quasi-Newton](https://en.wikipedia.org/wiki/Quasi-Newton_method) root-finding algorithm. The Quasi-Newton method reduces the problem further down to a system of linear equations, which is solved using [Eigen](https://eigen.tuxfamily.org/index.php?title=Main_Page), a versatile and fast C++ template library for linear algebra.
Eigen's sparse solver performs two steps: factorization (decomposition) of the Jacobian matrix and the linear system solving itself. This gives us the numerical solution of the entire DAE system at the current time step. Finally, depending on the convergence rate of the Quasi-Newton method, variability of the solution, and user-defined accuracy, the DAE solver adjusts the time step size and initiates a new iteration in time.

### The main features of the solver

- Header only, no pre-compilation required.
- Uses [automatic](https://en.wikipedia.org/wiki/Automatic_differentiation) (algorithmic, exact) differentiation (using [autodiff](https://autodiff.github.io/)) to compute the Jacobian matrix, if it is not provided by the user.
- Fourth-order implicit BDF time integrator that preserves accuracy even when the time step rapidly changes.
- A very flexible and customizable variable time stepping algorithm based on the solution stability and variability.
- Mass matrix can be non-static (can depend on time) and it can be singular.
- The library is extremely easy to use. A simple DAE can be set up using just a few lines of code (see [Quick Start](quick-start.html)).

### What's next?

- [Installation and Testing](installation.html)
- [Quick Start](quick-start.html)
