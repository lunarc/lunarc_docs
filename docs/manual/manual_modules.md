# Using Installed Software with Modules

LUNARC uses a **module system** to manage software environments on its clusters. This system makes it easy to load, switch, and combine different compilers, libraries, and applications while ensuring compatibility and reducing conflicts.

## What is a Module?

A *module* is a configuration file that sets up your environment to use a specific version of a software package. Modules allow you to:
- Load and unload software on demand
- Switch between different versions of the same software
- Automatically set environment variables and paths

## Hierarchical Module Naming Scheme

Starting with the Aurora service and continuing with COSMOS, LUNARC uses a hierarchical module naming scheme. Hierarchical modules ensure that the correct shared libraries are available when running an application while keeping the screen output of standard module commands such as **module avail** manageable.
### How the Hierarchy Works

When logging into the system, you only get access to those modules that do not require any special dynamic libraries. After *loading a compiler module* you obtain access to those packages that have been built with that specific compiler and depend on its shared libraries. For many compilers, this will include one or more matching MPI libraries. After loading an MPI library, additional software packages, depending on this pair (compiler & MPI library), will become available.

!!! important
  
    In many cases loading an MPI library is required for software that doesn't depend on it.

!!! tip 

    If you can't find a module you're looking for, try loading a compiler module first!

## Using Modules

The module system uses the Lua-based [Lmod](https://www.tacc.utexas.edu/research-development/tacc-projects/lmod) software.

### Quick Reference

| Command | Description |
|---------|-------------|
| `module avail` | List currently available modules |
| `module load <name>/<version>` | Load a specific module |
| `module list` | Show currently loaded modules |
| `module purge` | Unload all modules |
| `module spider <name>` | Search for a module (all versions) |
| `module unload <name>` | Unload a specific module |

### Loading packages

To see which modules are currently available, use:

```bash
module avail
```

This shows modules you can load right now. If you don't see what you need, try loading a compiler module first.

Example output:
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

You can see modules and versions located in different directories. The `(D)` indicates the default version. To load a module:

```bash
module load foss/2016a
```

Many modules will load several modules they depend on. Since version 2016a is marked as the default version `(D)`, you could also use:

```bash
module load foss
```

However, this is **not recommended** because the default may change when:
- You load another module
- The LUNARC team installs a newer version

!!! tip 

    **Best Practice:** Always specify the exact version you need to avoid surprises!

To see what modules you currently have loaded:

```bash
module list
```

> **Note:** In a hierarchical module naming scheme, `module avail` is less useful than in flat schemes. Often you need to use `module spider` instead (described below).

### Purging the loaded modules

Many modules will load a number extra of modules, which they depend on. When unloading a  module, these dependencies will typically not be unloaded.  For that reason, we currently recommend using

```bash
module purge
```

when loaded modules are no longer needed. You would then start loading the modules required for the next task you need to accomplish from scratch.

## Searching for Modules with `module spider`

The `module spider` command is essential for finding software in a hierarchical module system. It searches through all available modules, even those not currently accessible.

### Getting an Overview of All Software

To see all available software on the system:

```bash
module spider
```

This produces output like:

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

This shows all packages and their available versions.

### Searching for Specific Software

If you know the software name (or part of it), use it as an argument. The search is **case insensitive**:

```bash
module spider gromacs
```

#### Example: Finding GROMACS Versions

When searching for GROMACS:

```bash
module spider gromacs
```

You get output like:

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

This shows:
- **Multi-threaded version:** 5.0.4-mt 
- **Hybrid version:** 5.0.5-hybrid

### Getting Detailed Module Information

For specific loading instructions, query the exact module version:

```bash
module spider GROMACS/5.0.5-hybrid
``` 

This provides detailed information:

```bash
---------------------------------------------------------------------------------
  GROMACS: GROMACS/5.0.5-hybrid
---------------------------------------------------------------------------------

    This module can only be loaded through the following modules:

      icc/2016.1.150-GCC-4.9.3-2.25  impi/5.1.2.150
      ifort/2016.1.150-GCC-4.9.3-2.25  impi/5.1.2.150

... 
```

!!! important

    This shows the prerequisite modules you must load first! You have two options - let's use the first one:

```bash
# Load prerequisites first
module load icc/2016.1.150-GCC-4.9.3-2.25 impi/5.1.2.150

# Then load GROMACS
module load GROMACS/5.0.5-hybrid
```

!!! tip 

    The hierarchical system ensures compatibility between modules. Only compatible versions are shown together!

Loading this module automatically loads all required dependencies.

#### Example: Finding R Statistical Software

Let's find the R statistical package:

```bash
module spider R
```

Output:

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

Notice:
- **Three R versions available:** 3.2.1-bare, 3.2.1, and 3.2.3
- **Other matches:** Shows packages containing "R" in their name
- **Regex search tip:** Use `module -r spider '.*R.*'` for broader searches

For version 3.2.3 details:

```bash
module spider R/3.2.3
```

This shows the loading requirements:

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

To load R version 3.2.3:

```bash
# Load prerequisites
module load GCC/4.9.3-binutils-2.25
module load OpenMPI/1.8.8

# Load R
module load R/3.2.3
```

Now you have access to R!

## Working with Module Collections

If you regularly use the same set of modules, **user collections** are much better than adding `module load` commands to your `.bashrc` file.

### Creating Collections

First, load all the modules you need for your work:

```bash
module load GCC/4.9.3-binutils-2.25
module load OpenMPI/1.8.8
module load R/3.2.3
```

Then save them as a collection:

```bash
# Save as default collection
module save

# Or save with a specific name
module save my-r-environment
```

!!! warning 

    The module system remembers whether you loaded default versions or specific versions. If you use defaults, your collection will change when the system defaults change!

When one restores a collection, this has two effects:

 1. Unloading/purging all currently loaded modules
 2. Loading all modules included in the collection

### Restoring Collections

Restoring a collection will:
1. Unload all currently loaded modules
2. Load all modules from the collection

```bash
# Restore default collection
module restore

# Restore named collection
module restore my-r-environment
```

### Managing Collections

List all your collections:

```bash
module savelist
```

See what's in a collection:

```bash
module describe my-r-environment
```

Remove a collection:

```bash
module disable my-r-environment
```

> **Note:** `disable` doesn't permanently delete - it renames the collection so it won't show in `savelist`, but recovery is possible.

## System Performance and Caching

Lmod uses caching to improve `module spider` performance. The system cache is automatically updated when LUNARC installs new software.

## Graphical Module Browser

You can also browse and select modules using a graphical interface. Launch it with:

```bash
ml-browse
```

This provides a visual way to explore the module hierarchy and select modules to load.

---

**Author:** Joachim Hein (LUNARC)

**Last Updated:**
2022-10-06
