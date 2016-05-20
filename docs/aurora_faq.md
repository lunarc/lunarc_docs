# Frequently asked questions about Aurora
Author: Joachim Hein (Lunarc)

![Node icon](images/node_icon.jpg "Node icon")

## Do I need to copy my data from Alarik to Aurora?
Most users have two different types of storage.  They will be treated differently.

* **home space**, which is where you are located after login.  This is separate on Aurora and Alarik.  You need to **copy everything you want to keep** from your Alarik home space as soon as possible.  We plan to keep the Alarik home space available until **15 April 2016**. 
* **nobackup** or **workspace**, which is where most users hold the bulk of their data and start their jobs.  This storage area is accessed as `/lunarc/nobackup/users/<username>`.  The nobackup storage has been mounted on Aurora and is currently accessible from Alarik and Aurora.  No action by the users is required.  If you have programs and executables in your nobackup space, please read the entry on [executables](#will-alarik-executables-work-on-aurora).

In addition to these, some project have designated project space.  Project storage has been mounted on Aurora. No further action should be required.

## Will Alarik executables work on Aurora?
The processors deployed on Alarik and Aurora have different instructions sets.  In particular highly optimised executables are likely to fail with an *illegal instruction error*.  Further more different shared libraries are deployed on both systems.  Lunarc recommends to rebuild everything from the source code.

## What is the maximum job time on Aurora?
This has not been changed.  We still allow batch jobs to ask for up to 168 hours, which is 7 days.

## Could you please install software for me
Many packages that were installed in standard locations have now been moved to the module system.  Please search for the package using `module spider` before contacting the helpdesk.

## I can not find my software package in the module system
While the Lunarc support team did not manage to install all the software that was available on Alarik, most of the packages that were available and a few that weren't are already available.  However on Aurora a hierarchical module naming scheme is deployed.  This is explained in a special [software quide](http://lunarc-documentation.readthedocs.org/en/latest/aurora_modules/).  Please contact Lunarc support if you can't find your package with the `module spider`command.

## I need LAPACK and BLAS - they used to be in /usr/lib64
Using an optimised BLAS library is important for achieving good performance.  The libraries in `/usr/lib64` are typically not highly optimised.  We currently support [OpenBLAS](http://www.openblas.net/) within the foss [toolchain](http://lunarc-documentation.readthedocs.org/en/latest/aurora_modules/#compiling-code-and-using-toolchains) and [MKL](https://software.intel.com/en-us/intel-mkl) in the intel, iomkl and gimkl toolchains.  Both libraries show excellent performance when e.g. used to build HPL (high performance linpack).

When using OpenBLAS, please be aware that you might [need to control the number of threads](#my-mpi-code-using-openblas-does-not-perform) spawned.

## I need an ACML module
ACML is a highly optimised library which offers BLAS functionality among other things.  It is provided by AMD to work with the AMD processors, like the once deployed on Alarik.  Since Aurora utilises INTEL processors using ACML is no longer an option.  On Aurora we offer OpenBLAS and MKL instead of ACML.  Please refer to the question: ["I need LAPACK and BLAS"](#i-need-lapack-and-blas) for more details.

## My MPI code using OpenBLAS does not perform
OpenBLAS as installed in the foss [toolchains](http://lunarc-documentation.readthedocs.org/en/latest/aurora_modules/#compiling-code-and-using-toolchains) is compiled with thread support.  When OpenBLAS is used on Aurora, it will typically figure how many cores you are allocated on that node and spawn as many threads.  If this is not desirable (e.g. pure MPI code) you need to control the number of threads spawned by setting the `OMP_NUM_THREADS` variable.  For example for an MPI code running as many tasks as cores requested this is typically be set to:
```bash
export OMP_NUM_THREADS=1
```

## My MPI application doesn't launch
On Aurora we support two MPI libraries: [OpenMPI](https://www.open-mpi.org/) and [Intel MPI](https://software.intel.com/en-us/intel-mpi-library).  Depending on the [toolchain](http://lunarc-documentation.readthedocs.org/en/latest/aurora_modules/#compiling-code-and-using-toolchains) utilised to compile your application different job launchers are required.  Executables build with OpenMPI need to be launched with `mpirun`, while applications build with Intel MPI need to be started with `srun`.  

## The SciPY installed on Aurora fails because of a missing library
This is a know issue, which we are currently trying to resolve.  If you access SciPY by loading the icc module and the impi module, the Fortran runtime will not be available to Python.  We recommend loading the matching intel module instead of the icc module.  Example:
```bash
module load intel/2016a
module load scipy/0.16.1-Python-2.7.10
```

