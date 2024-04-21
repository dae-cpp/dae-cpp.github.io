---
title: Solution Manager class
layout: default
nav_order: 6
---

# Solution Manager class

The Solution Manager class can serve as a solution observer and/or as an event function that stops computation when a certain event occurs. Solution Manager functor will be called every time step, providing the time `t` and the corresponding solution vector `x` for the user for further post-processing. If the functor returns an integer which is not equal to `0` (i.e., `true`), the computation will immediately stop.

The solver provides a derived from the Solution Manager class called [`daecpp::Solution`](#solution-class), which writes the solution vector `x` and time `t` every time step or every specific times defined by the user into the [Solution Holder](#solution-holder) object. The user can access this object for further post-processing, printing the solution on the screen or writing it to a file.

{: .important }
By default, the DAE solver does nothing with the solution, i.e., it does not print it on the screen or save it anywhere. It is the user's responsibility to define a Solution Manager object that processes the solution (saves it to a vector, to a file, etc.). The solver, however, provides a basic Solution Manager called [`daecpp::Solution`](#solution-class).

## Solution Manager definition

The Solution Manager class `daecpp::SolutionManager` is a virtual class that provides an interface to define the Solution Manager.
In order to make use of this class, the user should inherit it and overload the `()` operator:

```cpp
class UserDefinedSolutionManager : public daecpp::SolutionManager
{
public:
    int operator()(const daecpp::state_vector &x, const double t)
    {
        // Solution Manager definition

        return 0; // Returns 0 by default or an integer != 0 to stop the computation
    }
};
```

The operator `()` will be called every time step, providing the user with the current state `x` and the corresponding time `t`.
If the functor returns non-zero integer, the computation will stop.
The user is free to define the solution container and save the solution within the Solution Manager class. The solver provides [`SolutionHolder`](#solution-holder) helper class that stores solution vectors `x` and the corresponding times `t`.

{: .note }
The type of vector `x` in the class definition above is `daecpp::state_vector`, which is an alias of `std::vector<float_type>` type. See [`dae-cpp` types](https://dae-cpp.github.io/prerequisites.html#dae-cpp-types) section for more information.

## Example

In the following example, we define a custom Solution Manager that performs the following 3 tasks:

- writes the element `x[0]` of the solution `x` and time `t` into `std::vector` every time step,
- prints time `t` and `x[0]` on the screen every time step,
- stops computation if `x[0]` becomes negative.

```cpp
class UserDefinedSolutionManager : public daecpp::SolutionManager
{
    daecpp::state_vector &m_x_sol; // Vector of solutions x[0]
    std::vector<double> &m_t_sol;  // Vector of the corresponding times t

public:
    UserDefinedSolutionManager(daecpp::state_vector &x_sol, std::vector<double> &t_sol)
        : m_x_sol(x_sol), m_t_sol(t_sol) {}

    int operator()(const daecpp::state_vector &x, const double t)
    {
        const auto x_0 = x[0]; // Solution element of interest

        m_x_sol.push_back(x_0); // Writes x[0] every time step
        m_t_sol.push_back(t);   // Writes time t every time step

        std::cout << t << '\t' << x_0 << '\n'; // Prints t and x[0] on screen

        if (x_0 < 0.0)
        {
            return -1; // Stops computation if x[0] is negative
        }

        return 0;
    }
};
```

{: .warning }
Vectors `x_sol` and `t_sol` should be defined before calling the `UserDefinedSolutionManager` constructor and must live until the end of the computation. They shouldn't be passed to the constructor as temporary objects and must **not** go out of scope before finishing the system solving (otherwise, this will lead to dangling references `&m_x_sol` and `&m_t_sol` in the class definition above). 

Similar to the mass matrix, vector function, Jacobian matrix definitions, inhereting the `daecpp::SolutionManager` class is a good practice (it serves as a blueprint), but it is not necessary. The user is allowed to define custom Solution Managers without inhereting `daecpp::SolutionManager`.

----

# Solution Holder class

The Solution Holder class `daecpp::SolutionHolder` is a helper class that stores solution vectors `x` and the corresponding times `t`. In addition, it provides a utility method that prints the entire solution or selected elements of the solution versus time on the screen.
The objects of the Solution Holder class can be used with the user-defined Solution Managers or together with the [Solution](#solution-class) class.

## Solution Holder class public data

| Variable | Type | Description |
| -------- | ---- | ----------- |
| `x`      | [`std::vector<daecpp::state_vector>`](prerequisites.html#dae-cpp-types) | Vector of solution vectors |
| `t`      | `std::vector<double>` | Vector of the corresponding solution times |

Thus, for example, the solution time `t_end` (time at the final time step) and the corresponding solution `x_end` can be accessed by

```cpp
daecpp::SolutionHolder sol;

double t_end = sol.t.back();               // Time `t_end` at the last time step
daecpp::state_vector x_end = sol.x.back(); // Solution vector at time `t_end`
```

## Solution Holder class methods

## `void print(const std::vector<std::size_t> &ind = {}) const`

<br>
A helper function to print `x` and `t` on screen.
By default, prints the entire vector `x` and the corresponding time `t`.

### Parameter:

- (optional) `ind` - vector of solution indices (out of range indices will be ignored) for printing (`std::vector<std::size_t>`)

### Example:

```cpp
daecpp::SolutionHolder sol;

// Prints only 0th, 1st, and 42nd elements of the solution vector
sol.print({0, 1, 42});
```

----

# Solution class

Solution class `daecpp::Solution` is derived from `daecpp::SolutionManager` and serves as a basic solution *observer* that writes solution vectors `x` and the corresponding times `t` every time step or every specific time from `t_output` vector (that can be optionally provided in the constructor) into `daecpp::SolutionHolder` class object. The Solution class is also used in the [`daecpp::System`](solve.html) class as a default Solution Manager.

## Solution class constructor

## `Solution(SolutionHolder &sol, const std::vector<double> &t_output)`

<br>
Constructs Solution class object.

### Parameters:

- `sol` - [`daecpp::SolutionHolder`](#solution-holder-class) object that will store the solution
- (optional) `t_output` - a vector of output times for writing (`std::vector<double>`)

### Example:

```cpp
daecpp::SolutionHolder sol; // Solution Holder
daecpp::Solution mgr(sol);  // Solution Manager
```

Alternatively, in order to write the solution at time `t_output`, which is equal to, for example, `1.0`, `2.0`, and `3.0` only:

```cpp
daecpp::Solution mgr(sol, {1.0, 2.0, 3.0}); // Solution Manager that filters time `t`
```

After solving the system, the solution will be stored in the `sol` object ([Solution Holder](#solution-holder-class)).
