# Frequently asked questions about Aurora
Author: Joachim Hein (LUNARC)

![Node icon](images/node_icon.jpg "Node icon")

## I have forgotten my login password for Aurora
To receive a new password, please phone LUNARC support on 0462224454 (+46462224454 outside Sweden) from the mobile phone you have registered in [SUPR](https://supr.snic.se/person/).  Additional questions will be asked to verify your identity.  

## I can not login after migrating to pocket pass
Many users have issues because of missing the [activation step](http://lunarc-documentation.readthedocs.io/en/latest/authenticator_howto/#important-last-step-activate-your-token).  To activate your token, connect to the [self-service portal](http://lunarc-documentation.readthedocs.io/en/latest/authenticator_howto/#accessing-the-self-service-portal), go into "tokens", choose "activate" and follow the instructions.

## Could your send my *one time password* for pocket pass activation to my new mobile phone number?
Please enter the new phone number into [SUPR](https://supr.snic.se/person/) and raise a [support request](http://www.lunarc.lu.se/support/support_form) to update our internal database for this change.

## What is the maximum job time on Aurora?
This has not been changed.  We still allow batch jobs to ask for up to 168 hours, which is 7 days.

## Could you please install software for me
Many packages that were installed in standard locations have now been moved to the module system.  Please search for the package using **module spider** before contacting the helpdesk.

## I can not find my software package in the module system
On Aurora a hierarchical module naming scheme is deployed.  This is explained in a special [software guide](http://lunarc-documentation.readthedocs.org/en/latest/aurora_modules/).  Please contact LUNARC support if you cannot locate your package with the **module spider** command.


## Loading a module unloads pre-requisites from a previously loaded module

Many software modules on Aurora have complex pre-requisites. If you load multiple modules is can happen that they try to load conflicting modules as pre-requisites.  In this case the module loaded last unloads conflicting pre-requisistes from earlier loaded modules.  If that happens, the earlier loaded modules can become disfunctional and there is no guaranty that they still work.

You have the follwing options to resolve the issues:

* Have a look, whether there are different versions of the modules installed that have common pre-requisites.  Acutally in many cases we have the same version of the software installed with different pre-requisites to help this issue.

* Have a terminal dedicated to each of the modules.  This should work if the modules do not really interact with each other, but e.g. one module creates files which the other module reads.  Consider using the [Lunarc HPC desktop](http://lunarc-documentation.readthedocs.io/en/latest/using_hpc_desktop/) which easily allows having multiple terminals within a single session

* If this does not work or does not work satisfactory, contact the [LUNARC helpdesk](http://www.lunarc.lu.se/support/support_form) and discuss your situation.

## How do I use a Lund University project

When using a Lund University project (LU project), you have to specify the project name, the partition and in some cases a reservation.  This is explained in our [batch system guide](http://lunarc-documentation.readthedocs.io/en/latest/batch_system/#specifying-a-project-and-partition-for-users-with-lu-projects-or-multiple-projects).

## I want to use the Aurora GPU nodes
To access the Aurora GPU nodes, you need to be a member of an [LU local project](https://supr.snic.se/round/2017locallu/). Please review our [batch system guide](http://lunarc-documentation.readthedocs.io/en/latest/batch_system/#accessing-gpus-in-the-aurora-lu-partition) for the required modification to your submission script.  The [software guide](http://lunarc-documentation.readthedocs.io/en/latest/aurora_modules/#cuda-based-toolchains-for-gpu-nodes) provides an overview on Compiler, Cuda and MPI support for the GPU nodes. 


## Can I prevent my job from restarting in case of a node failure

By default the Slurm job scheduler restarts jobs in case of e.g. a failing node.  This is not always desirable.  Adding a line
```bash
#SBATCH --no-requeue
```
to the header portion of your job script prevents this behaviour.


## I need LAPACK and BLAS - they used to be in /usr/lib64
Using an optimised BLAS library is important for achieving good performance.  The libraries in `/usr/lib64` are typically not highly optimised.  We currently support [OpenBLAS](http://www.openblas.net/) within the foss [toolchain](http://lunarc-documentation.readthedocs.org/en/latest/aurora_modules/#compiling-code-and-using-toolchains) and [MKL](https://software.intel.com/en-us/intel-mkl) in the intel, iomkl and gimkl toolchains.  Both libraries show excellent performance when e.g. used to build HPL (high performance linpack).

When using OpenBLAS, please be aware that you might [need to control the number of threads](#my-mpi-code-using-openblas-does-not-perform) spawned.


## How do I link my code against the MKL installation provided on Aurora
The Aurora modules providing MKL are set the environment variable `MKLROOT` to assist the compiler in locating the library on the system.  To link your application we suggest consulting the [Intel® Math Kernel Library Link Line Advisor](https://software.intel.com/en-us/articles/intel-mkl-link-line-advisor) for the arguments needed. 


## My MPI code using OpenBLAS does not perform
OpenBLAS as installed in the foss [toolchains](http://lunarc-documentation.readthedocs.org/en/latest/aurora_modules/#compiling-code-and-using-toolchains) is compiled with thread support.  When OpenBLAS is used on Aurora, it will typically figure how many cores you are allocated on that node and spawn as many threads.  If this is not desirable (e.g. pure MPI code) you need to control the number of threads spawned by setting the `OMP_NUM_THREADS` variable.  For example for an MPI code running as many tasks as cores requested this is typically be set to:
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


The VASP executables on Aurora are compiled using Intel MPI.  They need to be started with `srun`, refer to the sample section of our batch [system guide](http://lunarc-documentation.readthedocs.io/en/latest/batch_system/#mpi-job-using-20-tasks-per-node) for a more comprehensive discussion.


## My MPI application doesn't launch
On Aurora we support two MPI libraries: [OpenMPI](https://www.open-mpi.org/) and [Intel MPI](https://software.intel.com/en-us/intel-mpi-library).  Depending on the [toolchain](http://lunarc-documentation.readthedocs.org/en/latest/aurora_modules/#compiling-code-and-using-toolchains) utilised to compile your application different job launchers are required.  Executables build with OpenMPI need to be launched with `mpirun`, while applications build with Intel MPI need to be started with `srun`.  

## The SciPY installed on Aurora fails because of a missing library
This only concerns the SciPY installations build with the Intel compiler.  
This is a know issue, which we are currently trying to resolve.  If you access SciPY by loading the icc module and the impi module, the Fortran runtime will not be available to Python.  We recommend loading the matching intel module instead of the icc module.  Example:
```bash
module load intel/2016b
module load scipy/0.17.0-Python-2.7.11
```


