Using installed software on Lunarc's Aurora service
================

Author: Joachim Hein (Lunarc)

![Node icon](images/node_icon.jpg "Node icon")

# Hierarchical module naming scheme
With the start of the Aurora service Lunarc is using an hierarchical module naming scheme.  Hierarchical modules ensure that the correct shared libraries are available when running an application, while keeping screen output of standard module commands such as `module avail` manageable.

## Hierachical naming scheme concept

When logging into the system, you only get access to those modules that do not require any special dynamic libraries.  After *loading a compiler module* you obtaining access to those packages that have been build with that specific compiler and depend on its shared libraries.  For many compilers this will include one or more matching MPI libraries.  After loading an MPI library additional software packages, depending on this pair (compiler & MPI library), will become available.  Users should take note that in many cases loading an MPI library is required for software that doesn't really depend on it.

# Using Modules

The module system on Aurora is utilising the Lua based [Lmod](https://www.tacc.utexas.edu/research-development/tacc-projects/lmod) software.

## Loading packages

The command

```
module avail
```

shows the modules that can currently be accessed.  The output will look similar to

```
--------------------------------- /sw/Modules/modulefiles/Core ---------------------------------
   matlab/8.5

-------------------------------- /sw/easybuild/modules/all/Core --------------------------------
   Bison/3.0.4                    gompi/2015b
   EasyBuild/2.5.0                gompi/2016a                        (D)
   EasyBuild/2.6.0         (D)    icc/2015.3.187-GNU-4.9.3-2.25
   GCC/4.9.3-binutils-2.25        icc/2016.1.150-GCC-4.9.3-2.25      (D)
   GCC/4.9.3-2.25                 iccifort/2015.3.187-GNU-4.9.3-2.25
   GCC/5.3.0               (D)    iccifort/2016.1.150-GCC-4.9.3-2.25 (D)
   GCCcore/4.9.3                  ifort/2015.3.187-GNU-4.9.3-2.25
   GNU/4.9.3-2.25                 ifort/2016.1.150-GCC-4.9.3-2.25    (D)
   Java/1.8.0_72                  iimpi/7.3.5-GNU-4.9.3-2.25
   M4/1.4.17                      iimpi/2016.01-GCC-4.9.3-2.25       (D)
   binutils/2.25                  intel/2015b
   flex/2.5.39                    intel/2016a
   foss/2015b                     intel/2016.01                      (D)
   foss/2016a              (D)    zlib/1.2.8

-------------------------------- /sw/lmod/lmod/modulefiles/Core --------------------------------
   lmod/6.0.24    settarg/6.0.24
```

In this example you can see modules and versions located in 3 directories.  Any of these modules can be accessed directly.  To obtain access to the software inside e.g. the toolchain module `foss/2016a` one loads the module by issueing

```
module load foss/2016a
```

Many modules will load a number of modules, which they depend on.
Since in the above output from `module avail` version 2016a is marked
as the default version, the command
```
module load foss
```
would have the same effect unless the default changes, which it may
do, if

 * You load a module
 * The lunarc team installs another version of the software

So if you require a specific version, the Lunarc team strongly
recommends to not rely on defaults, but explicitly specify the version
you are after.

To see what modules you have currently loaded use

```
module list
```

In a hierarchical module naming scheme the command `module avail` is
not as useful as it is in a flat module naming scheme which Lunarc
deployed on earlier services.   In many situations `module avail`
resulted in the desired action, one has to use the `module spider`
command which is described in the text below.

## Purging the loaded modules

Many modules will load a number extra of modules, which they depend on.
When unloading a  module, these dependencies will typically not be unloaded.  For
that reason we currently recommend using

```
module purge
```

when loaded modules are no longer needed.   You would then start
loading the modules required for the next task you need to accomplish
from scratch.

##Searching for all software packages

In practical use, the command `module spider` is key to search for packages in an Lmod based hierarchical module naming scheme.  To get an overview on the software installed on Aurora, simply type
```
module spider
```
at the command prompt.  This will create an output similar to:
```
---------------------------------------------------------------------
The following is a list of the modules currently available:
---------------------------------------------------------------------
  Autoconf: Autoconf/2.69

  Automake: Automake/1.15

  Autotools: Autotools/20150215

  Bison: Bison/3.0.4

  Boost: Boost/1.58.0-Python-2.7.9

  CMake: CMake/3.2.2, CMake/3.3.2

  Cube: Cube/4.3

  EasyBuild: EasyBuild/2.5.0, EasyBuild/2.6.0

  FFTW: FFTW/3.3.4

  GCC: GCC/4.9.3-binutils-2.25, GCC/4.9.3-2.25, GCC/5.3.0

  GCCcore: GCCcore/4.9.3

  GLib: GLib/2.42.1, GLib/2.46.0

  GNU: GNU/4.9.3-2.25

  GROMACS: GROMACS/5.0.4-mt, GROMACS/5.0.5-hybrid

  ...
```
This is a full list of the packages and versions available on the service.  
##Searching for a specific package
If you are looking for a specific package and have an idea on what its name might be, you can give this as an argument to `module spider`.   This argument is case insensitive.  
### Example: Accessing a Gromacs version
For example, when looking to run Gromacs:
```
module spider gromacs
```
You obtain output similar to:
```
---------------------------------------------------------------------------------
  GROMACS:
---------------------------------------------------------------------------------
     Versions:
        GROMACS/5.0.4-mt
        GROMACS/5.0.5-hybrid

---------------------------------------------------------------------------------
  To find detailed information about GROMACS please enter the full name.
  For example:

     $ module spider GROMACS/5.0.5-hybrid
---------------------------------------------------------------------------------
```
This tells you that the multi threaded version 5.0.4 and the hybrid version 5.0.5 are installed.  If you want to use the version 5.0.5 issue the command:
```bash
module spider GROMACS/5.0.5-hybrid
``` 
You get the following output
```
---------------------------------------------------------------------------------
  GROMACS: GROMACS/5.0.5-hybrid
---------------------------------------------------------------------------------

    This module can only be loaded through the following modules:

      icc/2016.1.150-GCC-4.9.3-2.25  impi/5.1.2.150
      ifort/2016.1.150-GCC-4.9.3-2.25  impi/5.1.2.150

... 
```
This lists the modules you have to load before accessing Gromacs.  In this case you have two options, we choose the first option.  We load
```
module load icc/2016.1.150-GCC-4.9.3-2.25 impi/5.1.2.150
```
After which we can load the gromacs installation:
```
module load GROMACS/5.0.5-hybrid
```
Loading this module will load a number of additional module require for Gromacs to work.

### Example accessing R

This is another example on how to access a specific software package.  This time we want to run the statistical software package R.
```
module spider R
```
One gets:
```
------------------------------------------------------------------
  R:
------------------------------------------------------------------
     Versions:
        R/3.2.1-bare
        R/3.2.1
        R/3.2.3

     Other possible modules matches:
        GCCcore  GROMACS  SuiteSparse  cURL  fixesproto  fontsproto  ...

------------------------------------------------------------------
  To find other possible module matches do:
      module -r spider '.*R.*'

------------------------------------------------------------------
  To find detailed information about R please enter the full name.
  For example:

     $ module spider R/3.2.1-bare
------------------------------------------------------------------
```
If we are interested in version 3.2.3, we do a
```
module spider R/3.2.3
```
next and get the following info:
```
------------------------------------------------------------------
  R: R/3.2.3
------------------------------------------------------------------

     Other possible modules matches:
        GCCcore, GROMACS, SuiteSparse, cURL, fixesproto, ...

    This module can only be loaded through the following modules:

      GCC/4.9.3-binutils-2.25  OpenMPI/1.8.8
 
...
```
The output states the two modules that need loading to get access to this R version.  We issue
```
module load GCC/4.9.3-binutils-2.25
module load OpenMPI/1.8.8
module load R/3.2.3
```
and have access to R.

## Lmod cache

To improve the performance of the `module spider` command, lmod caches
the entire module structure of the system.  This cache is currently
configured to be **updated once per day**.

This can have the effect that you see a slightly outdated version of the module tree, when using commands such as `module avail`or `module spider`. This should only be an issue with additions to the module tree made in the last hours. The command
```bash
module --ignore-cache spider
```
will force an update of the cache files in your account. 

The cache files private to your account are stored in the directory
`$HOME/.lmod.d/.cache/`.

# Compiling code and using toolchains

A significant portion of the Aurora software is build using the [EasyBuild](http://hpcugent.github.io/easybuild/) software framework.  This framework provides so called *Toolchains* which are utilised to build software.  Lunarc recommends to use toolchains when building software.  This includes compiling your own software outside the EasyBuild framework.

## Currently provided toolchains

### Toolchains for CPU nodes

**Toolchains based on the GCC compiler suite**

 * **GCC**: GCC
 * **foss**: GCC, OpenMPI, OpenBLAS, FFTW, BLACS, ScaLAPACK
 * **gompi**: GCC, OpenMPI
 * **gimpi**: GCC, Intel MPI
 * **gimkl**: GCC, Intel MPI, MKL

**Toolchains based on the Intel compiler suite**

 * **iccifort**: icc, ifort
 * **intel**: icc, ifort, Intel MPI, MKL
 * **iimpi**: icc, ifort, Intel MPI
 * **iomkl**: icc, ifort, OpenMPI, MKL
 * **iompi**: icc, ifort, OpenMPI

 **Toolchains based on the PGI compiler suite**
 
 * **PGI**: PGI
 * **pompi**: PGI, OpenMPI
 * **pomkl**: PGI, OpenMPI, MKL

### CUDA based toolchains for GPU nodes

**Toolchains based on the GCC compiler suite**

* **gcccuda**  GCC, CUDA
* **gompic** GCC, CUDA, OpenMPI
* **goolfc** GCC, CUDA, OpenMPI, OpenBLAS, FFTW, BLACS, ScaLAPACK

If you require additional toolchains, contact [Lunarc support](http://www.lunarc.lu.se/support/support-form/) to discuss your requirements.

## Selecting a toolchain
The above choices of toolchains is a bit overwhelming, in particular for new users.  We recommend to first make a choice of toolchain and then select a version.  Good choices for general use are the toolchains:

* **foss**, if you like to use the GCC compiler suite
* **gimkl**, if you like to use the GCC compiler suite with Intel's MKL performance library
* **intel**, if you like to use the Intel compiler suite
* **pomkl**, if you like to use the PGI compiler suite

**Example:** To check the foss versions available you
```
  module avail foss
```
and you get an output similar to
```

------------------ /sw/easybuild/modules/all/Core ------------------
   foss/2015a    foss/2015b    foss/2016a (D)

  Where:
   D:  Default Module

Use "module spider" to find all possible modules.
Use "module keyword key1 key2 ..." to search for all possible
modules matching any of the "keys".
```
This shows you that three versions of the foss toolchain are available, with version 2016a being the default.  The version numbering at the time of writing is a *time stamp*.  Version 2015a was released in the beginning of 2015, 2015b in the middle of 2015 and 2016a in the beginning of 2016.  If you load e.g. the `foss/2016a` module
```
module load foss/2016a
```
It will load a number of modules for you, incl. compiler, libraries and utilities.  The command 
```
module list
```
will now show you which compiler and library versions it will be using.  Please note that after loading a toolchain

*A number of modules become available, which rely on components inside this specific toolchain 
*The defaults of many modules change to a version that was build with the components inside the selected foss module

Selecting a version of the intel or the pomkl toolchain is very similar to selecting a foss module, just replace foss with intel or pomkl in the above examples.

## Compiling serial code using a toolchain
Once a toolchain module is selected, there are no differences from earlier Lunarc services when it comes to compiling serial code.
If you have loaded a toolchain build on top of a GCC compiler use the following commands to compile.

* **gcc**: C compiler
* **g++**: C++ compiler
* **gfortran**: Fortran compiler

If you have loaded a toolchain build on top of the Intel compiler use the following command to compile. 

* **icc**: C compiler
* **icpc**: C++ compiler
* **ifort**: Fortran compiler

If you have loaded a toolchain build on top of the PGI compiler, use the following command to compile:

* **pgcc**: C compiler
* **pgc++**: C++ compiler
* **pgf90**: Fortran compiler
* **pgf77**: Fortran77 compiler
 
In all cases please do not forget about compiler options, in particular optimisation flags.  You should have the toolchain used for compiling loaded when executing the code.

## Compiling MPI code using a toolchain
The commands you use to compile MPI code depend on the MPI library and the compiler you intend to use.  

### Toolchains using OpenMPI
When using a toolchain utilising **OpenMPI** (e.g. foss, iomkl, pomkl) use: 

* **mpicc**: MPI compiler for C code
* **mpicxx** or **mpic++*: MPI compiler for C++ code
* **mpifort**: MPI compiler for Fortran code
 
Inside your slurm job-script, executables build with OpenMPI need to get started using the `mpirun` command.  For MPI jobs not using threads we recommend using task binding.  A simple job script for standard MPI jobs (no threads, e.g. OpenMP) is available on Aurora
```
/sw/pkg/submissionsScripts/script_openmpi.sh
```
If you use this, you will need to modify it for your own needs.  A [more detailed guide](http://lunarc-documentation.readthedocs.org/en/latest/batch_system/) on the submission system is available.
 
**Remark:** In the latest OpenMPI releases the commands `mpif77` and `mpif90` have been depreciated.  Fortran users should switch to using `mpifort`.

### Toolchains using the Intel compiler and Intel MPI library
When using a toolchain utilising the **Intel MPI library** and the **Intel compiler** (e.g. intel, iimpi) use:

* **mpiicc**: MPI compiler for C code
* **mpiicpc**: MPI compiler for C++ code
* **mpiifort**: MPI compiler for Fortran code
 
Inside your slurm job-script, executables build with the Intel MPI library need to get started using the `srun` command.  Task binding is still under investigation and not yet available.  for A simple job script for standard MPI jobs is available on Aurora:
```bash
/sw/pkg/submissionsScripts/script_intelmpi.sh
```
If you use this, you will need to modify it for your own needs.  A [more detailed guide](http://lunarc-documentation.readthedocs.org/en/latest/batch_system/) on the submission system is available.


### Toolchains using the GCC compiler and Intel MPI library
When using a toolchain utilising the **Intel MPI library** and the **GCC compiler** (e.g. gimkl) use:

* **mpigcc**: MPI compiler for C code
* **mpigxx**: MPI compiler for C++ code
* **mpif90**: MPI compiler for Fortran 95 code

Executable build using this setup are also started with the `srun` command from inside a job-script as described [above](#toolchains-using-the-intel-compiler-and-intel-mpi-library).


