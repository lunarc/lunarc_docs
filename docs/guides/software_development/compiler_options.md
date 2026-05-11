# Compiler options

COSMOS has two CPU architectures, and choosing the right compiler and flags for each gives the best performance:

| Nodes | CPU | Architecture | Best compiler choice |
|---|---|---|---|
| Standard (48-core) | AMD EPYC 7413 (Milan) | Zen 3 | AMD AOCC or GCC |
| Intel (32-core) | Intel Xeon Gold 6226R (Cascade Lake) | Cascade Lake | Intel or GCC |

Before production runs, always test with bounds-checking and debug flags enabled first, then recompile with optimisation flags once the code is correct.

!!! tip "Compiling for the right architecture"
    Flags like `-march=native` detect the CPU of the machine where you compile. If you compile on the login node, this may not match the compute node architecture. Either compile in an [interactive session](../../manual/manual_interactive.md) on the target node type, or specify the architecture explicitly using the flags below.

## Compiler commands and flags

=== "AMD AOCC"

    AOCC (AMD Optimizing C/C++ Compiler) is based on LLVM/Clang and gives the best performance on the AMD Milan nodes that make up the majority of COSMOS.

    ```bash
    module load AOCC
    ```

    Use `module spider AOCC` to find available versions.

    **Compiler commands:**

    | Language | Command |
    |---|---|
    | C | `clang` |
    | C++ | `clang++` |
    | Fortran | `flang` |

    **Debug flags:**

    ```text
    -g -O0
    ```

    **Optimisation flags for AMD Milan (Zen 3):**

    ```text
    -O3 -march=znver3 -ffast-math
    ```

    `-march=znver3` targets the Zen 3 microarchitecture of the EPYC 7413 processors in the standard COSMOS nodes, enabling AVX2 and other Milan-specific instructions.

=== "GCC"

    GCC is the broadly compatible choice and works well on both node types.

    ```bash
    module load GCC
    ```

    **Compiler commands:**

    | Language | Command |
    |---|---|
    | C | `gcc` |
    | C++ | `g++` |
    | Fortran | `gfortran` |

    **Debug flags:**

    ```text
    -g -O0 -Wall -fbounds-check
    ```

    **Optimisation flags for AMD Milan (standard nodes):**

    ```text
    -O3 -march=znver3 -funroll-loops
    ```

    **Optimisation flags for Intel Cascade Lake (32-core nodes):**

    ```text
    -O3 -march=cascadelake -funroll-loops
    ```

    **Problem solving — stack overflow / segmentation fault:**

    GCC (like most compilers) allocates local arrays on the stack by default. Large arrays can exhaust the stack and cause segmentation faults. Move them to static or heap allocation, or increase the stack size limit:

    ```bash
    ulimit -s unlimited
    ```

=== "Intel"

    The Intel compilers give good performance on the Intel Cascade Lake 32-core nodes. They can also be used on the AMD nodes, but AOCC or GCC will typically produce faster code there.

    ```bash
    module load intel
    ```

    **Compiler commands:**

    | Language | Command |
    |---|---|
    | C | `icx` |
    | C++ | `icpx` |
    | Fortran | `ifx` |

    !!! note "Classic vs oneAPI compilers"
        Newer Intel toolchains provide the oneAPI compilers `icx`, `icpx`, and `ifx`. Older versions provided `icc`, `icpc`, and `ifort`. Check which commands are available after loading the module with `which icx` or `which icc`.

    **Debug flags:**

    ```text
    -g -O0 -traceback -check all
    ```

    (`-check all` and `-traceback` are Fortran-specific; for C/C++ use `-g -O0`.)

    **Optimisation flags for Intel Cascade Lake:**

    ```text
    -O3 -march=cascadelake -unroll
    ```

    Or let the compiler detect the host architecture automatically:

    ```text
    -O3 -xHost
    ```

    **Problem solving — stack overflow / segmentation fault:**

    The Intel Fortran compiler places arrays on the stack by default. For large arrays this can cause segmentation faults. Instruct the compiler to use the heap instead:

    ```text
    -heap-arrays
    ```

    To only move arrays above a certain size (in kilobytes) to the heap:

    ```text
    -heap-arrays 64
    ```

## Compiling for debugging

When preparing code for debugging, compile everything with debug support and without optimisation:

```text
-g -O0
```

Recompile with optimisation flags only after debugging is complete.

## Profiling

COSMOS provides several profiling tools including `gprof` (GCC), `Valgrind`, `gperftools`, and `Score-P` for MPI/OpenMP tracing. See [What profiling tools are available on COSMOS?](../../manual/faq/manual_faq_software.md#what-profiling-tools-are-available-on-cosmos) in the FAQ for usage examples.

---

**Author:**
(LUNARC)

**Last Updated:**
2026-03-30
