---
title: Utilities
layout: default
nav_order: 9
---

# Utility functions and constants

This section describes utility functions and constants that can be useful in the user's project.

## Get the solver version

The current version of `dae-cpp` can be obtained either by using the following macro:

```cpp
DAECPP_VERSION_MAJOR
DAECPP_VERSION_MINOR
DAECPP_VERSION_PATCH
```

or by accessing the following constants (`static constexpr uint16_t`) in the `daecpp` namespace:

```cpp
daecpp::version_major
daecpp::version_minor
daecpp::version_patch
```

## Timer

`dae-cpp` provides a simple timer based on `std::chrono::steady_clock` which is used internally in the solver, but it is also exposed for the user in the `daecpp` namespace.
The timer measures time spent by the program in the given scope and saves it in **milliseconds** in a variable of type `double`:

```cpp
double time{0.0}; // Stores time (in ms)
{
    daecpp::Timer timer(&time); // Starts the timer

    // << TASK >>

} // End of scope stops the timer
```

In the example above, we initialize the variable `time`, which will store the time in milliseconds.
Then we define the scope and create an object of class `daecpp::Timer` by passing the address of the variable `time`. Constructing this object starts the timer. When we reach the end of the scope that we defined, the `timer` object will be destroyed. This stops the timer, and the computation time (in milliseconds) will be written into the `time` variable.

Note that the `time` variable can be reused. We can start a new timer in another scope passing the address of `time` again. The computation time will be added to the previous value.
