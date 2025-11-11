# Compiling Code with Toolchains

## What is a Toolchain?

A **toolchain** is a coordinated set of software development tools including compilers, libraries, and utilities that work together to build software. Think of it as a "recipe" that ensures all components are compatible with each other.

On COSMOS, most software is built using [EasyBuild](http://hpcugent.github.io/easybuild/) which provides these pre-configured toolchains. LUNARC strongly recommends using toolchains for all code compilation to ensure:

- **Compatibility:** All components are tested to work together
- **Performance:** Optimized library combinations for best performance  
- **Reproducibility:** Consistent build environments across systems
- **Support:** LUNARC can better help with issues when standard toolchains are used

!!! important

    Always use toolchains for compiling your code, whether using EasyBuild or compiling manually!

## Quick Reference: Choosing a Toolchain

| Use Case | Recommended Toolchain | Why? |
|----------|----------------------|------|
| **General CPU computing** | `foss` | Free, open-source, well-tested |
| **Performance-critical CPU** | `gomkl` or `intel` | Intel MKL provides optimized math libraries |
| **GPU computing** | `goolfc` or `intelcuda` | Includes CUDA support |
| **Maximum compatibility** | `foss` | Works everywhere, no licensing issues |
| **Intel processors** | `intel` | Optimized for Intel hardware |

!!! tip 
    
    New to toolchains? Start with `foss` - it's free, reliable, and works for most applications!

## Available Toolchains

COSMOS provides several pre-configured toolchains for different computing needs. Each toolchain includes specific compilers and libraries optimized to work together.

### CPU Toolchains

=== "GCC-Based Toolchains (Recommended for most users)"

    **GCC** - GNU Compiler Collection only
    
    - **Components:** GCC compilers only
    - **Use when:** Basic compilation, minimal dependencies
    - **Best for:** Simple programs, testing

    **foss** - Free Open Source Software ⭐ **MOST POPULAR**
    
    - **Components:** GCC, OpenMPI, OpenBLAS, FFTW, BLACS, ScaLAPACK
    - **Use when:** General parallel computing, open-source projects
    - **Best for:** Most HPC applications, PhD research, teaching

    **gompi** - GCC + OpenMPI
    
    - **Components:** GCC, OpenMPI
    - **Use when:** MPI programs without math libraries
    - **Best for:** Custom parallel applications

    **gomkl** - GCC + Intel Math Kernel Library
    
    - **Components:** GCC, OpenMPI, Intel MKL
    - **Use when:** Need optimized math performance with GCC
    - **Best for:** Math-intensive applications

    **gfbf** - GCC + FlexiBLAS + FFTW
    
    - **Components:** GCC, FlexiBLAS, FFTW (no MPI)
    - **Use when:** Serial math applications
    - **Best for:** Single-node numerical computing

=== "Intel Toolchains (For maximum performance)"

    **intel** - Intel Compiler Suite ⭐ **BEST PERFORMANCE**
    
    - **Components:** icc, ifort, Intel MPI, Intel MKL
    - **Use when:** Maximum performance on Intel processors
    - **Best for:** Production runs, performance-critical applications

    **iimpi** - Intel Compilers + Intel MPI
    
    - **Components:** icc, ifort, Intel MPI
    - **Use when:** Intel compilers without math libraries
    - **Best for:** Custom applications with Intel compilers
<!---    * **iomkl**: icc, ifort, OpenMPI, MKL
    * **iompi**: icc, ifort, OpenMPI
    * **iccifort**: icc, ifort
    ml spider didn't find those last 3--->

### GPU Toolchains

For applications requiring GPU acceleration, these toolchains include CUDA support:

=== "GCC + CUDA Toolchains"

    **gcccuda** - GCC + CUDA
    
    - **Components:** GCC, CUDA
    - **Use when:** Basic GPU programming with GCC
    - **Best for:** Learning CUDA, simple GPU applications

    **gompic** - GCC + CUDA + MPI
    
    - **Components:** GCC, CUDA, OpenMPI
    - **Use when:** Multi-GPU applications across nodes
    - **Best for:** Distributed GPU computing

    **goolfc** - Complete GCC + CUDA Stack ⭐ **RECOMMENDED FOR GPU**
    
    - **Components:** GCC, CUDA, OpenMPI, OpenBLAS, FFTW, BLACS, ScaLAPACK
    - **Use when:** Full-featured GPU+CPU computing
    - **Best for:** Complex applications using both GPU and CPU

=== "Intel + CUDA Toolchains"

    **iccifortcuda** - Intel Compilers + CUDA
    
    - **Components:** icc, ifort, CUDA
    - **Use when:** Intel compilers with GPU support
    - **Best for:** High-performance CPU+GPU code

    **iimpic** - Intel + CUDA + MPI
    
    - **Components:** icc, ifort, CUDA, Intel MPI
    - **Use when:** Intel MPI with GPU support
    - **Best for:** Multi-node GPU applications with Intel tools

    **intelcuda** - Complete Intel + CUDA Stack
    
    - **Components:** icc, ifort, CUDA, Intel MPI, MKL
    - **Use when:** Maximum performance GPU+CPU computing
    - **Best for:** Production GPU applications

!!! tip 
    
    Need a different toolchain? Contact [LUNARC support](https://supr.naiss.se/support/?centre_resource=c5) to discuss your requirements.

## How to Choose and Load a Toolchain

### Decision Guide

The variety of toolchains can seem overwhelming at first. Here's a simple decision process:

1. **Start here for most users:** Use `foss` - it's free, reliable, and works for 80% of applications
2. **Need GPU support?** Use `goolfc` (GCC-based) or `intelcuda` (Intel-based)
3. **Performance critical?** Try `intel` or `gomkl` for better math library performance
4. **Research/Academic use?** Stick with `foss` for maximum compatibility and reproducibility

### Finding Available Versions

Once you've chosen a toolchain type, check what versions are available:

```bash
module avail foss
```

Example output:

```bash
------------------ /sw/easybuild/modules/all/Core ------------------
   foss/2015a    foss/2015b    foss/2016a (D)

  Where:
   D:  Default Module
```

**Understanding version numbers:**
- Format: `YYYY[a|b]` (e.g., `2023a`, `2023b`)
- `2023a` = Released early 2023
- `2023b` = Released mid-2023  
- `(D)` = Default version

!!! tip 
    
    Version Selection Tip: Always specify exact versions in your scripts! Don't rely on defaults as they may change.

### Loading a Toolchain

Load your chosen toolchain with the specific version:

```bash
# Recommended: Specify exact version
module load foss/2023a

# Not recommended: Using default (may change)
module load foss
```

**What happens when you load a toolchain:**

1. Multiple related modules are loaded automatically
2. Compilers and libraries become available  
3. Other software built with this toolchain becomes accessible
4. Module defaults change to versions compatible with your toolchain

Check what was loaded:

```bash
module list
```

!!! tip

    Loading a toolchain makes many additional software packages available! Use `module avail` after loading to see new options.

## Compiling Serial Code

Once you've loaded a toolchain, compiling serial (single-processor) code works just like on any other system. The toolchain provides the compilers and libraries you need.

### Available Compilers

The compiler commands depend on which toolchain you loaded:

=== "GCC Toolchain (foss, gompi, etc.)"

    ```bash
    gcc      # C compiler
    g++      # C++ compiler  
    gfortran # Fortran compiler
    ```

    **Example: Compiling a C program**
    ```bash
    # Load toolchain first
    module load foss/2023a
    
    # Compile with optimization
    gcc -O3 -o myprogram myprogram.c
    ```

=== "Intel Toolchain (intel, iimpi, etc.)"

    ```bash
    icc      # C compiler
    icpc     # C++ compiler
    ifort    # Fortran compiler
    ```

    **Example: Compiling a Fortran program**
    ```bash
    # Load toolchain first
    module load intel/2023a
    
    # Compile with optimization
    ifort -O3 -o myprogram myprogram.f90
    ```

!!! important 

    🔧 Don't forget optimization flags! Always use `-O2` or `-O3` for production code.
    📦 Keep toolchain loaded: The same toolchain used for compilation should be loaded when running your program.

### Example Build Process

```bash
# 1. Load your chosen toolchain
module load foss/2023a

# 2. Compile with appropriate flags
gcc -O3 -march=native -o calculate calculate.c -lm

# 3. Verify the build worked
./calculate --version
```

## Compiling MPI Code

MPI (Message Passing Interface) compilation depends on both the MPI library and compiler in your toolchain. Here's how to compile and run MPI programs:

### MPI Compiler Commands

=== "OpenMPI Toolchains (foss, gompi, goolfc, etc.)"

    ```bash
    mpicc    # MPI C compiler
    mpicxx   # MPI C++ compiler (also: mpic++)
    mpifort  # MPI Fortran compiler
    ```

    **Example: Compiling MPI C code**
    ```bash
    # Load OpenMPI-based toolchain
    module load foss/2023a
    
    # Compile MPI program
    mpicc -O3 -o mpi_program mpi_program.c
    ```

    **Running OpenMPI programs:** Use `mpirun` in your job script
    ```bash
    #!/bin/bash
    #SBATCH --ntasks=16
    #SBATCH --time=1:00:00
    
    module load foss/2023a
    mpirun myprogram
    ```

    > **📋 Sample script:** `/sw/pkg/submissionsScripts/script_openmpi.sh`

=== "Intel MPI Toolchains (intel, iimpi, intelcuda, etc.)"

    ```bash
    mpiicc   # MPI C compiler
    mpiicpc  # MPI C++ compiler
    mpiifort # MPI Fortran compiler
    ```

    **Example: Compiling MPI Fortran code**
    ```bash
    # Load Intel MPI toolchain
    module load intel/2023a
    
    # Compile MPI program
    mpiifort -O3 -o mpi_program mpi_program.f90
    ```

    **Running Intel MPI programs:** Use `srun` in your job script
    ```bash
    #!/bin/bash
    #SBATCH --ntasks=16
    #SBATCH --time=1:00:00
    
    module load intel/2023a
    srun myprogram
    ```

    > **📋 Sample script:** `/sw/pkg/submissionsScripts/script_intelmpi.sh`

=== "GCC + Intel MPI (gimkl, etc.)"

    ```bash
    mpigcc  # MPI C compiler
    mpigxx  # MPI C++ compiler
    mpif90  # MPI Fortran compiler
    ```

    **Running:** Use `srun` (same as Intel MPI above)

!!! Tip

    * ⚡ Performance Tip: For pure MPI jobs (no OpenMP), use task binding for better performance.
    * 🔄 Consistency: Always use the same toolchain for compiling and running your MPI code.
    * 📖 More Info: See the [detailed batch system guide](http://lunarc-documentation.readthedocs.org/en/latest/batch_system/) for advanced job submission options.

!!! warning "Deprecated Commands"
    
    The OpenMPI commands `mpif77` and `mpif90` are deprecated. Use `mpifort` instead for all Fortran code.

## Troubleshooting and Best Practices

### Common Issues and Solutions

**Problem: "Command not found" after loading toolchain**

```bash
# Solution: Check if toolchain loaded correctly
module list
# If not loaded, reload:
module purge
module load foss/2023a
```

**Problem: Library linking errors**

```bash
# Solution: Ensure you're using the MPI compiler wrappers
# Wrong:
gcc -lmpi myprogram.c
# Right:
mpicc myprogram.c
```

**Problem: Different performance between identical code**

- Always use the same toolchain version for compilation and execution
- Check optimization flags: `-O2` or `-O3` make a huge difference
- For Intel processors, consider using Intel toolchains

### Best Practices Checklist

✅ **Always specify exact toolchain versions** in your scripts  
✅ **Use optimization flags** (`-O2` or `-O3`) for production code  
✅ **Keep the same toolchain loaded** for compilation and execution  
✅ **Use MPI compiler wrappers** (`mpicc`, `mpiicc`) instead of bare compilers  
✅ **Test with different toolchains** if performance is critical  
✅ **Document your toolchain choice** in your code/scripts for reproducibility  

### Quick Workflow Summary

```bash
# 1. Choose and load toolchain
module load foss/2023a

# 2. Compile your code
mpicc -O3 -o myprogram myprogram.c

# 3. Create job script with same toolchain
echo "module load foss/2023a" > myjob.sh
echo "mpirun myprogram" >> myjob.sh

# 4. Submit job
sbatch myjob.sh
```

### Getting Help

- **Sample job scripts:** Check `/sw/pkg/submissionsScripts/`
- **Detailed guides:** [Batch system documentation](http://lunarc-documentation.readthedocs.org/en/latest/batch_system/)
- **Support:** [LUNARC support portal](https://supr.naiss.se/support/?centre_resource=c5)

---

**Author:**
(LUNARC)

**Last Updated:**
2024-11-14
