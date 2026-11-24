# cppgrm

[![License: AGPL v3](https://img.shields.io/badge/License-AGPL%20v3-blue.svg)](https://www.gnu.org/licenses/agpl-3.0)
[![C++ Standard](https://img.shields.io/badge/C%2B%2B-17-blue.svg)](https://en.cppreference.com/w/cpp/17)

`cppgrm` is a lightweight, **header-only** C++ port of the Global Relational Model (`grm.py`) engine. It solves sparse or dense systems of mathematical relations over node networks using weighted least squares, Householder QR factorization, Gauss-Newton iterations, and Levenberg-Marquardt damping.

## Features

- **Header-only:** No complicated compilation steps or pre-compiled binaries required; just drop `grm.hpp` into your project or fetch it via CMake.
- **Multiple Relation Types:**
  - **Log-linear relations:** Ideal for products, ratios, and power-laws (`sum(coeff * log10(node)) = rhs`).
  - **Sum relations:** Linear combinations of node values with arbitrary exponents (`sum(coeff * value^p) = K`).
  - **Arbitrary Functions (`fn`):** Custom equations with optional analytic gradients or automatic finite-difference fallback.
- **Robust Solver:** Combines warm-starts on linear subsets, exact steps for log-linear networks, damped Gauss-Newton, continuation methods for tight nonlinear constraints, and covariance/uncertainty estimation.

## Requirements

- A compiler supporting **C++17** (GCC, Clang, MSVC).
- No external dependencies (relies purely on the C++ Standard Library).

## Installation via CMake (FetchContent)

You can easily integrate `cppgrm` into your project using CMake's `FetchContent`:

```cmake
include(FetchContent)
FetchContent_Declare(cppgrm
    GIT_REPOSITORY https://github.com/innermost47/cppgrm.git
    GIT_TAG        main # Or use a specific tag like v1.0.0
    GIT_SHALLOW    ON
)
FetchContent_MakeAvailable(cppgrm)

# Link it to your target
target_link_libraries(your_target PRIVATE grm::cppgrm)
```

## Quick Example

```cpp
#include <iostream>
#include "grm.hpp"

int main() {
    grm::Network net;

    // Add nodes with initial guesses
    int a = net.add_node("a", 2.0);
    int b = net.add_node("b", 3.0);

    // Add a log-linear relation: log10(a) + log10(b) = log10(6)  ->  a * b = 6
    net.add_log("mult", {{"a", 1.0}, {"b", 1.0}}, std::log10(6.0));

    // Solve the network
    grm::Result res = net.solve();

    std::cout << "a = " << res["a"] << "\n";
    std::cout << "b = " << res["b"] << "\n";

    return 0;
}
```

## License

This project is licensed under the terms of the **GNU Affero General Public License v3.0 or later (AGPL-3.0-or-later)**. See the [LICENSE](LICENSE) file for details.

Copyright (C) 2026 Anthony Charretier