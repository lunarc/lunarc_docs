# Compiling code and using toolchains

A significant portion of the COSMOS software is built using the [EasyBuild](http://hpcugent.github.io/easybuild/) software framework.  This framework provides so-called _Toolchains_ which are utilised to build software.  LUNARC recommends using toolchains when building software.  This includes compiling your software outside the EasyBuild framework.

## Currently provided toolchains

### Toolchains for CPU nodes

=== "GCC compiler toolchains"

    * **GCC**: GCC
    * **foss**: GCC, OpenMPI, OpenBLAS, FFTW, BLACS, ScaLAPACK
    * **gompi**: GCC, OpenMPI
    * **gomkl**: GCC, OpenMPI, MKL
    * **gfbf**: GCC, FlexiBLAS, FFTW  (no MPI)

=== "Intel compiler toolchains"

    * **intel**: icc, ifort, Intel MPI, MKL
    * **iimpi**: icc, ifort, Intel MPI
<!---    * **iomkl**: icc, ifort, OpenMPI, MKL
    * **iompi**: icc, ifort, OpenMPI
    * **iccifort**: icc, ifort
    ml spider didn't find those last 3--->

<!---
=== "PGI compiler toolchains" <--these no longer appear to be supported on COSMOS
 
    * **PGI**: PGI
    * **pompi**: PGI, OpenMPI
    * **pomkl**: PGI, OpenMPI, MKL
--->
## CUDA based toolchains for GPU nodes

=== "GCC compiler toolchains for GPU"

    * **gcccuda**:  GCC, CUDA
    * **gompic**: GCC, CUDA, OpenMPI
    * **goolfc**: GCC, CUDA, OpenMPI, OpenBLAS, FFTW, BLACS, ScaLAPACK

=== "Intel compiler toolchains for GPU"

    * **iccifortcuda**: icc, ifort, CUDA
    * **iimpic**: icc, ifort, CUDA, Intel MPI
    * **intelcuda**: icc, ifort, CUDA, Intel MPI, MKL

If you require additional toolchains, contact [LUNARC support](http://www.lunarc.lu.se/support/support-form/) to discuss your requirements.

## Selecting a toolchain

The above choices of toolchains are a bit overwhelming, in particular for new users.  We recommend first choosing a toolchain and then selecting a version.  Good choices for general use are the toolchains:

* **foss**, if you want to use the GCC compiler suite
* **gomkl**, if you want to use the GCC compiler suite with Intel's MKL performance library
* **intel**, if you want to use the Intel compiler suite

**Example:** To check the foss versions available you

```bash
  module avail foss
```
and you get an output similar to

```bash

------------------ /sw/easybuild/modules/all/Core ------------------
   foss/2015a    foss/2015b    foss/2016a (D)

  Where:
   D:  Default Module

Use "module spider" to find all possible modules.
Use "module keyword key1 key2 ..." to search for all possible
modules matching any of the "keys".
```

This shows you that three versions of the foss toolchain are available, with version 2016a being the default.  The version numbering at the time of writing is a *time stamp*.  Version 2022a was released at the beginning of 2022, 2022b in the middle of 2022 and 2023a at the beginning of 2023.  If you load e.g. the `foss/2023a` module

```bash
module load foss/2023a
```

It will load several modules for you, incl. compiler, libraries and utilities.  The command 

```bash
module list
```

will now show you which compiler and library versions it will be using.  Please note that after loading a toolchain

 * Several modules become available, which rely on components inside this specific toolchain 
 * The defaults of many modules change to a version that was built with the components inside the selected foss module

Selecting a version of the intel or the **pomkl** toolchain is very similar to selecting a foss module, just replace foss with intel or **pomkl** in the above examples.

## Compiling serial code using a toolchain

Once a toolchain module is selected, there are no differences from earlier LUNARC services when it comes to compiling serial code.
If you have loaded a toolchain build use the following commands to compile.

=== "GCC Toolchain"

    * **gcc**: C compiler
    * **g++**: C++ compiler
    * **gfortran**: Fortran compiler

=== "Intel Toolchain"

    * **icc**: C compiler
    * **icpc**: C++ compiler
    * **ifort**: Fortran compiler

<!---=== "PGI Toolchain"

    * **pgcc**: C compiler
    * **pgc++**: C++ compiler
    * **pgf90**: Fortran compiler
    * **pgf77**: Fortran77 compiler--->
 
In all cases please do not forget about compiler options, in particular optimisation flags.  You should have the toolchain used for compiling loaded when executing the code.

## Compiling MPI code using a toolchain

The commands you use to compile MPI code depend on the MPI library and the compiler you intend to use.  

### Toolchains using OpenMPI

When using a toolchain utilising **OpenMPI** (e.g. foss, iomkl, pomkl) use: 

 * **mpicc**: MPI compiler for C code
 * **mpicxx** or **mpic++*: MPI compiler for C++ code
 * **mpifort**: MPI compiler for Fortran code
 
Inside your slurm job-script, executables build with OpenMPI need to get started using the **mpirun** command.  For MPI jobs not using threads, we recommend using task binding.  A simple job script for standard MPI jobs (no threads, e.g. OpenMP) is available on COSMOS:

```bash
/sw/pkg/submissionsScripts/script_openmpi.sh
```

If you use this, you will need to modify it for your own needs.  A [more detailed guide](http://lunarc-documentation.readthedocs.org/en/latest/batch_system/) on the submission system is available.
 
!!! info 

    In the latest OpenMPI releases the commands `mpif77` and `mpif90` have been depreciated.  Fortran users should switch to using `mpifort`.

### Toolchains using the Intel compiler and Intel MPI library

When using a toolchain utilising the **Intel MPI library** and the **Intel compiler** (e.g. intel, iimpi) use:

* **mpiicc**: MPI compiler for C code
* **mpiicpc**: MPI compiler for C++ code
* **mpiifort**: MPI compiler for Fortran code
 
Inside your slurm job-script, executables build with the Intel MPI library need to get started using the `srun` command.  Task binding is still under investigation and not yet available. For a simple job script for standard MPI jobs is available on COSMOS:

```bash
/sw/pkg/submissionsScripts/script_intelmpi.sh
```

If you use this, you will need to modify it for your own needs.  A [more detailed guide](http://lunarc-documentation.readthedocs.org/en/latest/batch_system/) on the submission system is available.


### Toolchains using the GCC compiler and Intel MPI library

When using a toolchain utilising the **Intel MPI library** and the **GCC compiler** (e.g. gimkl) use:

* **mpigcc**: MPI compiler for C code
* **mpigxx**: MPI compiler for C++ code
* **mpif90**: MPI compiler for Fortran 95 code

Executable builds using this setup are also started with the **srun** command from inside a job-script as described [above](#toolchains-using-the-intel-compiler-and-intel-mpi-library).

---

**Author:**
(LUNARC)

**Last Updated:**
2024-06-18
