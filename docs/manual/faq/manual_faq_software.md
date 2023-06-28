# Frequently asked questions - Software

## Could you please install software for me

Many packages that were installed in standard locations have now been moved to the module system.  Please search for the package using **module spider** before contacting the helpdesk.

## I can not find my software package in the module system

On LUNARC resources a hierarchical module naming scheme is deployed.  This is explained in the [Using installed software guide](/../manual/manual_modules/).  Please contact [LUNARC support](/../about/contact/) if you cannot locate your package with the **module spider** command.

## Loading a module unloads pre-requisites from a previously loaded module

Many software modules have complex pre-requisites. If you load multiple modules is can happen that they try to load conflicting modules as pre-requisites.  In this case the module loaded last unloads conflicting pre-requisistes from earlier loaded modules.  If that happens, the earlier loaded modules can become disfunctional and there is no guaranty that they still work.

You have the follwing options to resolve the issues:

 * Have a look, whether there are different versions of the modules installed that have common pre-requisites.  Acutally in many cases we have the same version of the software installed with different pre-requisites to help this issue.
 * Have a terminal dedicated to each of the modules.  This should work if the modules do not really interact with each other, but e.g. one module creates files which the other module reads.  Consider using the [Lunarc HPC desktop](https://lunarc-documentation.readthedocs.io/en/latest/using_hpc_desktop/) which easily allows having multiple terminals within a single session
 * If this does not work or does not work satisfactory, contact the [LUNARC helpdesk](http://www.lunarc.lu.se/support/support_form) and discuss your situation.

 
## My application fails because is can't find libgfortran.so.3
If you jobs fails with an error message similar to

```
error while loading shared libraries: libgfortran.so.3: cannot
open shared object file: No such file or directory
```
you are using software that has been compiled with a really old version of GCC.  Please recompile you program on COSMOS using a modern version of GCC.  Preferably a GCC version we provided as a module.

## I need LAPACK and BLAS - they used to be in /usr/lib64

Using an optimised BLAS library is important for achieving good performance.  The libraries in `/usr/lib64` are typically not highly optimised.  We currently support [OpenBLAS](https://www.openblas.net/) within the foss [toolchain](http://lunarc-documentation.readthedocs.org/en/latest/aurora_modules/#compiling-code-and-using-toolchains) and [MKL](https://software.intel.com/en-us/intel-mkl) in the intel, iomkl and gimkl toolchains.  Both libraries show excellent performance when e.g. used to build HPL (high performance linpack).

When using OpenBLAS, please be aware that you might [need to control the number of threads](#my-mpi-code-using-openblas-does-not-perform) spawned.


## How do I link my code against the MKL installation provided on Aurora

The Aurora modules providing MKL set the environment variable `MKLROOT` to assist the compiler in locating the library on the system.  To link your application we suggest consulting the [Intel® Math Kernel Library Link Line Advisor](https://software.intel.com/en-us/articles/intel-mkl-link-line-advisor) for the arguments needed. 


## My MPI code using OpenBLAS does not perform

OpenBLAS as installed in the foss [toolchains](https://lunarc-documentation.readthedocs.org/en/latest/aurora_modules/#compiling-code-and-using-toolchains) is compiled with thread support.  When OpenBLAS is used on Aurora, it will typically figure how many cores you are allocated on that node and spawn as many threads.  If this is not desirable (e.g. pure MPI code) you need to control the number of threads spawned by setting the `OMP_NUM_THREADS` variable.  For example for an MPI code running as many tasks as cores requested this is typically be set to:
```bash
export OMP_NUM_THREADS=1
```

## I like to run VASP on Aurora but it doesn't work

If you like to use VASP on Aurora, please ensure that you are registed on a license with the VASP developers in Vienna. To comply with the terms of our handling license, we have to confirm the validity of your license with the VASP developers before granting access. That will take normally take a few days but can take weeks under bad circumstances. Being named in a SUPR VASP group does not imply you are entitled to use VASP.

After registerering with the VASP team, contact the [LUNARC helpdesk](http://www.lunarc.lu.se/support/support_form) and provide the following information:

* Your LUNARC userid
* Name and Department of the license holder (typically your supervisor/research group leader)
* The number of the license
* Your name as stated on the license
* Whether you require VASP 4 or VASP 5 access

In order to be able to continue the provision of VASP to our ligitimate VASP users, we will not make any exception. 


The VASP5 executables on Aurora are compiled using Intel MPI.  They need to be started with `srun`.  The VASP6 executable is build with OpenMPI.   These executables need to be started with `mpirun`.  Refer to the sample section of our batch [system guide](https://lunarc-documentation.readthedocs.io/en/latest/batch_system/#mpi-job-using-20-tasks-per-node) for a more comprehensive discussion.


## My MPI application doesn't launch

On Aurora we support two MPI libraries: [OpenMPI](https://www.open-mpi.org/) and [Intel MPI](https://software.intel.com/en-us/intel-mpi-library).  Depending on the [toolchain](https://lunarc-documentation.readthedocs.org/en/latest/aurora_modules/#compiling-code-and-using-toolchains) utilised to compile your application different job launchers are required.  Executables build with OpenMPI need to be launched with `mpirun`, while applications build with Intel MPI need to be started with `srun`.  

## The SciPY installed on Aurora fails because of a missing library

This only concerns the SciPY installations build with the Intel compiler.  
This is a know issue, which we are currently trying to resolve.  If you access SciPY by loading the icc module and the impi module, the Fortran runtime will not be available to Python.  We recommend loading the matching intel module instead of the icc module.  Example:
```bash
module load intel/2016b
module load scipy/0.17.0-Python-2.7.11
```

## Visual Studio Code (code) doesn't work anymore

Microsofts code editor Visual Studio Code in its latest version uses a runtime library that is no longer suppoerted by CentOS 7. To fix this load the foss module before running Visual Studio Code

```bash
module load foss
code
```

---

**Author:**
Joachim Hein (LUNARC)

**Last Updated:**
2022-10-06