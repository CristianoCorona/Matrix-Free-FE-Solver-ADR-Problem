
# Matrix-Free Solver for Advection-Diffusion-Reaction
![C++](https://img.shields.io/badge/C%2B%2B-20-blue.svg)
![MPI](https://img.shields.io/badge/MPI-Parallel-red.svg)
![Library](https://img.shields.io/badge/Library-deal.II-orange.svg)

A high-performance, parallel Finite Element solver for the Advection-Diffusion-Reaction (ADR) equation. The project is developed using the [deal.II](https://www.dealii.org/) finite element library and implements a **Matrix-Free** approach, coupled with a Matrix-Free Geometric Multigrid (GMG) preconditioner. A detailed description of the project developement can be found in the [report](https://github.com/CristianoCorona/Matrix-Free-FE-Solver-ADR-Problem/blob/main/report.pdf).

## Prerequisites

To compile and run this project, you need:
* A working C++ compiler with C++20 support.
* **CMake** (version >= 3.16).
* **MPI** (Message Passing Interface) for parallel execution.
* **deal.II** library (compiled with MPI and `p4est` support for distributed meshes).

## Compilation Instructions

The project uses the standard CMake build system. From the root directory of the repository, run the following commands:

```bash
# 1. Create a build directory
mkdir build
cd build

# 2. Configure the project with CMake
# (If deal.II is not installed in a standard path, append -DDEAL_II_DIR=/path/to/dealii)
cmake -DCMAKE_BUILD_TYPE=Release ..

# 3. Compile the executables using 4 parallel jobs
make -j4

```

## Executables Overview

The compilation process builds the core functionalities into two static libraries (`adr_core_free` and `adr_core_based`) and links them to multiple executables, divided into the following categories:

### 1. Verification (Method of Manufactured Solutions)
These executables use analytical manufactured solutions to compute $L^2$ and $H^1$ error norms and verify the theoretical Expected Order of Convergence (EOC).
* `validation_free` / `validation_based`: Solves a full Dirichlet homogeneous problem.
* `validation_inhomogeneous_free` / `validation_inhomogeneous_based`: Tests the hybrid explicit integration approach for non-homogeneous Dirichlet boundary conditions.

### 2. Strong Scaling & Performance Profiling
* `profiling_free` / `profiling_based`: Employs simple constant scalar fields to bypass transcendental function evaluations. Used to strictly isolate and measure the architectural throughput (MDoFs/s), cache misses, and strong scaling parallel efficiency on a fixed global mesh.

### 3. Weak Scaling
* `weak_scaling_free` / `weak_scaling_based`: Specifically designed for weak scaling benchmarks.


### Positional Arguments:

1. `<profiling>`: Integer flag (`0` or `1`).
* `0`: Standard mode. Exports the solution in `.vtu` format for ParaView visualization.
* `1`: Profiling mode. Bypasses heavy I/O operations to perform pure performance profiling and EOC analysis.


2. `<refinement>`: Integer specifying the target global mesh refinement level (default is `4`).
* In *validation* runs, the solver computes error norms iterating from level 2 up to this maximum.
* In *non-profiling* runs, VTU files are generated solely for this finest grid.


3. `<mg_flag>`: String flag for preconditioning.
* `-mg`: Enables the Geometric Multigrid preconditioner (with Chebyshev/Jacobi smoothers).
* Any other string (e.g., `-no-mg`) disables multigrid preconditioning.


4. `<p>`: Integer (from `1` to `12`) specifying the polynomial degree  of the continuous Galerkin finite element space  (default is `2`).

---

## Usage Examples

**1. Default Run (No Arguments)**
Runs the executable sequentially using default parameters (output enabled, refinement = 4, no multigrid, ):

```bash
./profiling_free

```

**2. Qualitative Validation (ParaView Export)**
Run on 4 MPI processes, export VTU files (`0`), up to refinement level 5, with Multigrid enabled (`-mg`), and polynomial degree :

```bash
mpirun -n 4 ./validation_free 0 5 -mg 3

```

## Acknowledgments
This project was developed for the **Numerical Methods for Partial Differntial Equations** course at **Politecnico di Milano** (A.Y. 2025-2026).

* **[Prof. Alfio Quarteroni](https://www.polimi.it/il-politecnico/il-politecnico-nella-storia/i-professori-emeriti/alfio-maria-quarteroni)** 
* **[Prof. Michele Bucelli](https://it.linkedin.com/in/michele-bucelli)**

The simulation used in the presentation for this project were, in part, performed on the HPC Cluster of the Department of Mathematics of Politecnico di Milano which was funded by MUR grant Dipartimento di Eccellenza 2023-2027.

## Authors
* **[Daniele Confalonieri](https://github.com/DanieleConfalonieri)**
* **[Cristiano Corona](https://github.com/CristianoCorona)**
* **[Giorgio Mantoan](https://github.com/GiorgioMantoan02)**
* **[Dmitrii Meshcheriakov](https://github.com/Dima765Me)**
