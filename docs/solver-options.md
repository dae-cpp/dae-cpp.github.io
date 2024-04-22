---
title: Solver Options class
layout: default
nav_order: 7
---

# Solver Options class

The Solver Options class, `daecpp::SolverOptions`, allows the user to redefine and fine-tune the DAE solver options. To do so, the user should create an object of the Solver Options class, update parameters of interest by accessing public variables (see [example](#example) below), and then provide the updated object to the solver function `solve`.

The table below contains a complete list of all options accessible via the Solver Options class.

## Solver options

| Option | Type | Default | Description |
| ------ | ---- | ------- | ----------- |
| `verbosity` | `int` | `verbosity::off` | Verbosity level. Can be `verbosity::off` (no output), `verbosity::normal` (prints basic information), or `verbosity::extra` (full output). |
| `dt_init` | `double` | `0.01` | Initial time step. Should be relatively small because the first time step is always of first order. |
| `dt_min` | `double` | `1e-10` | Minimum time step. If the solver has to reduce the time step below `dt_min` but still fails to converge, the simulation will stop. |
| `dt_max` | `double` | `1e10` | Maximum time step. The solver will **not** increase the time step above `dt_max`. |
| `atol` | `double` | `1e-6` | Absolute tolerance for the Newton algorithm |
| `rtol` | `double` | `1e-6` | Relative tolerance for the Newton algorithm |
| `max_err_abs` | `double` | `1e100` | Maximum absolute error. If the absolute error estimation exceeds `max_err_abs`, the Newton iterations will be considered as diverged. |
| `BDF_order` | `unsigned` | `4` | Order of the BDF implicit numerical integration method: `1` - first order BDF, `2` - BDF-2, `3` - BDF-3, `4` - BDF-4 |
| `Newton_scheme` | `unsigned` | `1` | Non-linear solver algorithm: <br> `0` - Classic Newton method (usually the most stable but the slowest), <br> `1` (default) - Quasi-Newton method I (balanced with focus on stability, updates Jacobian and performs factorization every 2nd iteration), <br> `2` - Quasi-Newton method II (balanced with focus on speed, updates Jacobian and performs factorization every 3rd iteration), <br> `3` - Quasi-Newton method III (can be the fastest but less stable, may require tweaking the time step increase/decrease thresholds, updates Jacobian and performs factorization every 4th iteration). |
| `is_mass_matrix_static` | `bool` | `false` | If `true`, the mass matrix will be updated only once (at the beginning of computation). Setting this option as `true` slightly speeds up the computation, but the mass matrix must be static (i.e., it must be independent on time). |
| `max_Jacobian_updates` | `int` | `8` | Maximum number of the Jacobian matrix updates and factorizations per time step. If the algorithm fails to converge after `max_Jacobian_updates` Jacobian matrix updates and factorizations per time step, the Newton iterations will be considered as diverged. The solver will try to roll back and decrease the time step. |
| `max_Newton_failed_attempts` | `unsigned` | `3` | If the Newton method fails to converge 'max_Newton_failed_attempts' times in a row, the solver will try to update Jacobian matrix every single iteration next time step (for Quasi-Newton methods). |
| `dt_increase_threshold_delta` | `int` | `0` | Time step amplification threshold delta. Can be negative or positive integer number. The solver increases the time step if the number of successful Newton iterations per time step is less than the threshold. <br> **Example:** If the solver increases the time step too often, decrease the time step amplification threshold by setting `dt_increase_threshold_delta` to `-1`. |
| `dt_decrease_threshold_delta` | `int` | `0` | Time step reduction threshold delta. Can be negative or positive integer number. The solver decreases the time step if the number of successful Newton iterations per time step is greater than the threshold. <br> **Example:** If the solver struggles to converge but keeps doing many Newton iterations without reducing the time step, decrease the time step reduction threshold by setting `dt_decrease_threshold_delta` to `-1` (or `-2` and less). |
| `solution_variability_control` | `bool` | `true` | Turns ON/OFF solution variability control. Solution variability control tightens up the adaptive time stepping algorithm, and it is ON by default. Switching it OFF can lead to a significant speed boost for big systems, but it can also lead to instability. |
| `variability_tolerance` | `double` | `1e-4` | Solution variability tolerance. Solution variability is the maximum relative change in the solution every time step. If the absolute value of the solution is very close to zero, the relative change can be very high, leading to unnecessary small time steps. To prevent this, the solver will ignore the values below the solution variability tolerance. Consider increasing variability tolerance if the solver reduces the time step too much. |
| `variability_threshold_low` | `double` | `0.15` | Solution variability lower threshold. The higher the value, the more likely the time step can be increased. If the maximum relative change in the solution is above the lower threshold, the time step will **not** be increased. |
| `variability_threshold_high` | `double` | `0.5` | Solution variability higher threshold. The higher the value, the less likely the time step will be reduced. If the maximum relative change in the solution is above the higher threshold, the time step will be reduced. |
| `dt_increase_factor` | `double` | `2.0` | Time step amplification factor. If the solution is stable enough, the solver will increase the time step by a factor of `dt_increase_factor`. |
| `dt_decrease_factor` | `double` | `2.0` | Time step reduction factor. If the solution is not stable enough, the solver will decrease the time step by a factor of `dt_decrease_factor`. |
| `num_threads` | `unsigned` | `1` | Number of threads (**not used yet**) |

{: .note }
In order to check the correctness of the user-defined solver options, the user can explicitly call the `void check() const` method from the `daecpp::SolverOptions` class. However, this function will be called automatically by the solver anyway. If one or more of the options are invalid, the `check()` method can throw an error or print a warning.

## Example

By default, the solver does not print any information on the screen during the computation. Let's increase the solver's verbosity level and, for example, restrict the maximum time step to `10.0`:

```cpp
daecpp::SolverOptions opt; // Solver Options object with default options

opt.verbosity = daecpp::verbosity::extra; // To print computation time and other info
opt.dt_max = 10.0;                        // To restrict the maximum time step
```

Object `opt` is now ready to be passed to the solver.

{: .note }
`daecpp::System` class contains built-in `daecpp::SolverOptions` object. See [Solving DAE system](solve.html) page for more information.
