# Compiler options

Several compilers are available to Lunarc users. Here we list the command names and options that often produce good performance for the most used compilers. More may be found by looking through the list of available modules obtained by the command

```bash
module avail 
```

Before running production runs, it is a good idea to test a program by running it with various checks turned on, for example to see that arrays are not indexed out of bounds. When the program has passed the test, it may be recompiled with optimization flags to get the best performance.

The compiler options given here are only suggestions of useful combinations, but not guaranteed to be optimal in all cases. To see what options are available check the man pages

```bash 
man compiler-command 
```

In terms of optimized performance, the Intel compilers usually give good results (fast programs) for both Intel (Sigrid) and AMD processors (Docenten, Sweet16). PathScale and Portland Group compilers are in some cases superior on AMD. The GNU compilers generally work, but are usually outperformed by other compilers.

## Compiler commands and switches

=== "Intel"

    **Intel compiler commands:**

    Adding the gnu compiler module:    

    ```bash
    module add intel 
    ```

    Fortran 77/90/95 compiler:

    ```bash
    ifort 
    ```
    C compiler:

    ```bash
    icc 
    ```
    C++ compiler:

    ```bash
    icpc 
    ```

    **Compiler Options:**

    Checking for mistakes

    ```
    -check all -g -traceback 
    ```

    Optimization flags:

    ```
    -O3 -xW 

    -O3 -ipo -unroll 

    -fast 
    ```

    **Problem solving**

    By default, the Intel compiler puts arrays on the stack and if they are too large, the program may crash with segmentation fault. A solution is to instruct the compiler to put the arrays on the heap instead.

    ```
    -heap-arrays 
    ```

    This may reduce performance and it is therefore possible to specify a minimum size of arrays to be put on the heap instead of the stack, when the size is known at compile time.

    ```
    -heap-arrays <size in kilobytes> 
    ```

=== "GNU Compilers"

    **GNU Compiler commands** 

    Adding the gnu compiler module:

    ```bash
    module add gcc
    ```

    Fortran 77/90/95 compiler:

    ```bash
    gfortran
    ```

    C compiler:

    ```bash
    gcc 
    ```

    C++ compiler:

    ```bash
    g++ 
    ```

    **Compiler Options:**

    Checking for mistakes:

    ```
    -fbounds-check -g 
    ```

    Optimization flags:

    ```
    -O3 -funroll-loops 
    ```

=== "PathScale"

    **PathScale compiler commands:** 

    Adding the pathscale module:

    ```bash
    module add pathscale 
    ```

    Fortan 77/90/95 compiler:

    ```bash
    pathf95 
    ```

    C compiler:

    ```bash
    pathcc 
    ```

    C++ compiler

    ```bash
    pathCC 
    ```

    **Compiler Options:**

    Checking for mistakes

    ```
    -C -g 
    ```

    Optimization flags:

    ```
    -O3 
    ```

=== "Portland Group"

    **Portland compiler commands** 

    Adding the portland compiler module:

    ```bash
    module add pgi 
    ```

    Fortran 77 compiler:

    ```bash
    pgf77 
    ```

    Fortran 90/95 compiler:

    ```bash
    pgf90
    ``` 

    C compiler:

    ```bash
    pgcc 
    ```

    C++ compiler:

    ```bash
    pgCC 
    ```

    **Compiler Options:**

    Checking for mistakes:

    ```
    -Mbounds -g 
    ```

    Optimization flags:

    ```
    -O3 -fastsse -Mvect 
    ```

    **Problem solving:**

    By default, objects, such as arrays, are limited to 2 GB. The option to allow larger objects is

    ```
    -mcmodel=medium 
    ```

---

**Author:**
(LUNARC)

**Last Updated:**
2022-10-05