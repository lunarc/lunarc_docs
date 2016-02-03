Using supported software on Lunarc's Aurora service
================

Author: Joachim Hein (Lunarc)

# Hierachical module naming scheme
With the start of the Aurora service Lunarc is using an hierachical module naming scheme.  Hierachical modules ensure that the correct shared libraries are available when running an application, while keeping screen output of standard module commands such as `module avail` managable.

## Hierachical naming scheme concept

When loging into the system, you only get access to those modules that do not require any special dynamic libraries.  After *loading a compiler module* you obtaing access to those packages that have been build with that specific compiler and depend on its shared libraries.  For many compilers this will include one or more matching MPI libraries.  After loading an MPI library additional software packages, depending on this pair (compiler & MPI library), will become available.  Users should take note that in many cases loading an MPI library is required for software that doesn't really depend on it.

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

In a hierachical module naming scheme the command `module avail` is
not as useful as it is in a flat module naming scheme which Lunarc
deployed on earlier services.   In many situations `module avail`
resulted in the desired action, one has to use the `module spider`
command which is descripted in the text below.

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

##Seaching for all software packages

In practical use, the command `module spider` is key to search for packages in an Lmod based hierachical module naming scheme.  To get an overview on the software installed on Aurora, simply type
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
You obtain output simlar to:
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
```
module spider GROMACS/5.0.5-hybrid
``` 
You get the folling output
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
module load icc/2016.1.150-GCC-4.9.3-2.25
module load impi/5.1.2.150
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
and have acces to R.

## Lmod cache

To improve the performance of the `module spider` command, lmod caches
the entire module structure of the system.  This cache is currently
configured to be updated once in 24 hours.

This can have the effect that you see an outdated version of the
module tree, when using commands such as `module avail`or `module
spider`.   The cache file is stored in the directory
`$HOME/.lmod.d/.cache/` remove the cache file and lmod will recreate
it for you.

# Compiling code and using toolchains

A signficant portion of the Aurora software is build using the [EasyBuild](http://hpcugent.github.io/easybuild/) software framework.  This framework provides so called *Toolchains* which are utilised to build software.  Lunarc recommends to use toolchains when building software.  This includes compiling your own software outside the EasyBuild framework.

Lunarc actively maintains the following toolchains:

 * **foss**: GCC, OpenBLAS, FFTW, OpenMPI, BLACS, ScaLAPACK
 * **gompi**: GCC, OpenMPI
 * **intel**: icc, ifort, MKL, Intel MPI
 * **iimpi**: icc, ifort, Intel MPI
 * **iomkl**: icc, ifort, MKL, OpenMPI
 * **iompi**: icc, ifort, OpenMPI

If you require additional toolchains, contact [Lunarc support](http://www.lunarc.lu.se/support/support-form/) to discuss your requirements.

