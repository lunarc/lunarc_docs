# Frequently asked questions about Aurora
Author: Joachim Hein (Lunarc)

## I can not find my software package in the module system
While the Lunarc support team did not manage to install all the software that was available on Alarik, most of the packages that were available and a few that weren't are already available.  However on Aurora a hierarchical module naming scheme is deployed.  This is explained in a special [software quide](http://lunarc-documentation.readthedocs.org/en/latest/aurora_modules/).  Please contact Lunarc support if you can't find your package with the `module spider`command.

## I need LAPACK and BLAS - they used to be in /usr/lib64
Using an optimised BLAS library is important for achieving good performance.  The libraries in `/usr/lib64` are typically not highly optimised.  We currently support [OpenBLAS](http://www.openblas.net/) within the foss [toolchain](http://lunarc-documentation.readthedocs.org/en/latest/aurora_modules/#compiling-code-and-using-toolchains) and [MKL](https://software.intel.com/en-us/intel-mkl) in the intel, iomkl and gimkl toolchains.  Both libraries show excellent performance when e.g. used to build HPL (high performance linpack).

## I need an ACML module
ACML is a highly optimised library which offeres BLAS functionality among other things.  It is provided by AMD to work with the AMD processors, like the once deployed on Alarik.  Since Aurora utilises INTEL processors using ACML is no longer an option.  On Aurora we offer OpenBLAS and MKL instead of ACML.  Please refer to the question: [*I need LAPACK and BLAS*](#i-need-lapack-and-blas) for more details.

## My MPI application doesn't launch
On Aurora we support two MPI libraries: [OpenMPI](https://www.open-mpi.org/) and [Intel MPI](https://software.intel.com/en-us/intel-mpi-library).  Depending on the [toolchain](http://lunarc-documentation.readthedocs.org/en/latest/aurora_modules/#compiling-code-and-using-toolchains) utilised to compile your application different job launchers are required.  Executables build with OpenMPI need to be launched with `mpirun`, while applications build with Intel MPI need to bestarted with `srun`.  
