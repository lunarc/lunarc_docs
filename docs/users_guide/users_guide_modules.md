Author: Joachim Hein (LUNARC)

![Node icon](../images/node_icon.jpg "Node icon")

## Hierarchical module naming scheme

With the start of the Aurora service, LUNARC is using a hierarchical module naming scheme.  Hierarchical modules ensure that the correct shared libraries are available when running an application while keeping the screen output of standard module commands such as **module avail** manageable.

### Hierachical naming scheme concept

When logging into the system, you only get access to those modules that do not require any special dynamic libraries.  After *loading a compiler module* you obtain access to those packages that have been built with that specific compiler and depend on its shared libraries. For many compilers, this will include one or more matching MPI libraries.  After loading an MPI library additional software packages, depending on this pair (compiler & MPI library), will become available.  Users should take note that in many cases loading an MPI library is required for software that doesn't depend on it.

## Using Modules

The module system on Aurora uses the Lua-based [Lmod](https://www.tacc.utexas.edu/research-development/tacc-projects/lmod) software.

### Loading packages

The command

```bash
module avail
```

shows the modules that can currently be accessed.  The output will look similar to

```bash
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

In this example, you can see modules and versions located in 3 directories.  Any of these modules can be accessed directly.  To obtain access to the software inside e.g. the toolchain module `foss/2016a` one loads the module by issuing

```bash
module load foss/2016a
```

Many modules will load several modules, which they depend on. As the output from **module avail** version 2016a is marked as the default version, the command

```bash
module load foss
```
would have the same effect unless the default changes, which it may do, if

 * You load a module
 * The LUNARC team installs another version of the software

So if you require a specific version, the LUNARC team strongly recommends not relying on defaults, but explicitly specify the version you are after.

To see what modules you have currently loaded use

```bash
module list
```

In a hierarchical module naming scheme, the command **module avail** is not as useful as it is in a flat module naming scheme which LUNARC deployed on earlier services.   In many situations **module avail** resulted in the desired action, one has to use the **module spider** command which is described in the text below.

### Purging the loaded modules

Many modules will load a number extra of modules, which they depend on. When unloading a  module, these dependencies will typically not be unloaded.  For that reason, we currently recommend using

```bash
module purge
```

when loaded modules are no longer needed. You would then start loading the modules required for the next task you need to accomplish from scratch.

## Searching for all software packages

In practical use, the command **module spider** is key to searching for packages in a Lmod-based hierarchical module naming scheme.  To get an overview of the software installed on Aurora, simply type:

```bash
module spider
```
at the command prompt.  This will create an output similar to:

```bash
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

### Searching for a specific package

If you are looking for a specific package and have an idea of what its name might be, you can give this as an argument to **module spider**.   This argument is case insensitive.  

#### Example: Accessing a Gromacs version

For example, when looking to run Gromacs:

```bash
module spider gromacs
```

You obtain output similar to:

```bash
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

This tells you that the multi-threaded version 5.0.4 and the hybrid version 5.0.5 are installed.  If you want to use the version 5.0.5 issue the command:

```bash
module spider GROMACS/5.0.5-hybrid
``` 

You get the following output

```bash
---------------------------------------------------------------------------------
  GROMACS: GROMACS/5.0.5-hybrid
---------------------------------------------------------------------------------

    This module can only be loaded through the following modules:

      icc/2016.1.150-GCC-4.9.3-2.25  impi/5.1.2.150
      ifort/2016.1.150-GCC-4.9.3-2.25  impi/5.1.2.150

... 
```

This lists the modules you have to load before accessing Gromacs.  In this case, you have two options, we choose the first option.  We load

```bash
module load icc/2016.1.150-GCC-4.9.3-2.25 impi/5.1.2.150
```

After which we can load the GROMACS-installation:

```bash
module load GROMACS/5.0.5-hybrid
```

Loading this module will load several additional modules required for GROMACS to work.

#### Example accessing R

This is another example of how to access a specific software package. This time we want to run the statistical software package R.

```bash
module spider R
```

One gets:

```bash
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

```bash
module spider R/3.2.3
```

next and get the following info:

```bash
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

```bash
module load GCC/4.9.3-binutils-2.25
module load OpenMPI/1.8.8
module load R/3.2.3
```

and have access to R.

### Working with a standard set of modules

Many users of the LUNARC systems conduct similar tasks for many days, e.g. using the same pieces of software on different sets of data.  In this case, users should consider creating **user collections** of modules.  Working with user collections is a better alternative to e.g. adding **module load** statements to e.g. your **.bashrc** file.

#### Creating and restoring user collections

To create a user collection load the modules required for the task at hand.  The command 

```bash
module save
```
would then store the currently loaded modules as a default collection. Alternatively, you can create a named collection

```bash
module save collection-name
```

Where you can choose any name you like as **collection-name**.  

!!! warning 

    The module system will remember whether a module was loaded as default (no version specified) or whether you loaded a specific version.  If you include default modules, the contents of your collection will change whenever the default changes.

When one restores a collection, this has two effects:

 1. Unloading/purging all currently loaded modules
 2. Loading all modules included in the collection

To restore your default collection

```bash
module restore
```
A named collection can be restored as follows

```bash
module restore collection-name
```

#### Enquiring about user collections

To get a list of all user collections you have created using the sub-command **savelist**:

```bash
module savelist
```

The sub-command **describe** shows you the modules included in the collection

```bash
module describe collection-name
```

If you want to remove a collection from your list:

```bash
module disable collection-name
```

which actually renames the collection in a way that it would no longer show with **module savelist**, but recovery of the collection is possible.

### Lmod cache

To improve the performance of the **module spider** command, lmod caches the entire module structure of the system.  The system cache, holding info about the modules installed by the LUNARC team, is updated, every time the LUNARC team installs new software. 

### Searching for modules graphically

It is also possible to browse the module tree and select the module using a graphical user interface. This interface can be launched from the command with the **ml-browse**:

```bash
ml-browse
```

