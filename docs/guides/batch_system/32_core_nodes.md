# Using the 32-core nodes

LUNARC's resources offers a new partition to its users.   This partition features compute nodes with more modern Cascade Lake CPUs, larger core count and more memory than the standard nodes.  These nodes are placed in a partition named *lu32*.   The name reflects that these nodes feature 32 cores per node. 

This document assumes you are familar with LUNARC resources. Here we focus on the key differences between the standard partition and the new *lu32*-partition from a user perspective.

## Hardware
The nodes inside the *lu32*-partition feature more modern CPUs than the nodes in the standard partition.  Both partitions contain Intel CPUs.  The CPUs in the standard nodes are commonly referred to as *Haswell*, while the CPUs in the *lu32* partition are commonly referred to as *Cascade Lake*.  Software executables build for *Haswell* CPUs should run in the *lu32* partition.  On the other hand, software specifically built for the new *Cascade Lake nodes* is **not** expected to work on the older *Haswell* nodes.

The *Cascade Lake* CPUs feature additional instructions, not availabe on the *Haswell* CPUs.  For a typical scientific computing job, the AVX512 instructions are the most interesting instructions.  Testing by LUNARC support has shown that code utilising these instructions can be up to 50% faster than code utilising AVX2 instructions.  

### Key differences between the Haswell and Cascade Lake nodes
|                | Aurora standard Haswell node | Aurora/COSMOS standard Cascade Lake node | COSMOS AMD Milan node             |
|----------------|:----------------------------:|:----------------------------------------:|:---------------------------------:|
|Cores per node: |      20 cores per node       |       32 cores per node                  | 48 cores per node
|Memory per node:|      64 GB per node	        |               192 GB per node            | 256 GB per node
|Memory per core:| 3100 MB per core (SLURM default) |     6000 MB per core                 | 5000 MB per core |
|AVX vector instructions: | AVX, AVX2            | AVX, AVX2, **AVX512**                   | AVX, AVX2 |


## Software

### Using installed software
LUNARC supports, installs, and maintains a substantial number of software titles on LUNARC services.  By default, after login, users will be able to select from the software titles suitable for the standard *Haswell* nodes and the new *Cascade Lake* nodes.  These software titles are expected to give decent performance on the *Cascade Lake*. They are not optimised for the more modern CPUs.  Depending on the nature of a software title, switching to software titles built for the *Cascade Lake* CPUs may or may not yield performance benefits.

!!! info
    Users not using the *lu32*-partition, whose required software has not been optimised for the *Cascade Lake* nodes or who achieve satisfactory performance from the standard software do not need to change their workflows.  The module system will continue to work as it always did.  The only change is, that they will see a new module `SoftwareTree/Haswell` appearing in the list of loaded modules.

#### Switching to Cascade Lake software
To change to the software titles compiled for the *Cascade Lake* CPUs, users need to unload the `SoftwareTree/Haswell` module and load the `SoftwareTree/Cascade` module.  Since the `SoftwareTree` modules are *sticky* they have to be purged by force.  Change from the *Haswell* software to the *Cascade Lake* software is accomplished as follows:

```
module --force purge
module load SoftwareTree/Cascade
```
This should work on the frontend nodes (e.g. aurora.lunarc.lu.se or the HPC desktop) and within a batch script (aka. SLURM script).  Once this is loaded, the commands `module avail` and `module spider` can be used to enquire about available software.

!!! info
    * Software in the `SoftwareTree/Cascade` is not expected to work on the frontend nodes.
    * Only a subset of the available software titles has been built for the Cascade Lake CPUs.  Use the standard (Haswell) software if the software you need is not available for Cascade Lake
    * If you require additional software titles in `SoftwareTree/Cascade`, please contact LUNARC support.   Priority will be given to requests from research groups that helped to finance the relevant nodes and codes that consume substantial amounts of CPU time.


### Building your own software

Many users compile their own software.  To obtain executables and binaries which are optimised for the *Cascade Lake* CPUs, it is necessary to instruct the compilers to do so.   This can be accomplished via cross compiling or by building the software on a compute node by e.g. submitting a batch job with the build instructions.   For complex build proceedures, the latter might actually be easier.

#### Cross compiling on a frontend node
The Aurora frontend node `aurora.lunarc.lu.se` as well as the nodes utilised for the LUNARC HPC desktop feature *Haswell* CPUs.  To generate code optimised for *Cascade Lake* processors you need to instruct the compiler to generate code intended to be used on a different architecture than utilised for the compilation.  These instructions depend on the compiler.

=== "GCC compiler"

    When using the GCC compiler to compile C, C++ and/or Fortran code the `-march` compiler option has to be used to specify the CPU whose instructions set is to be used.  In addition one can use the `-mtune` option to instruct the compiler to tune the code for a specific CPU.  The latter may or may not lead to a faster code.  When cross compiling for the *lu32*-partition with the GCC compiler, the following compiler options are recommended in addition to the ones you are typically using:

    ```
    -march=cascadelake -mtune=cascadelake
    ```

    Older versions of GCC will not support code generation for *Cascade Lake* CPUs.

=== "Intel compiler"

    Similar to what is described for the GCC compiler, when using the Intel compiler, you need to instruct it that you want to obtain code for *Cascade Lake* CPUs.  In case of recent versions of the Intel compiler, we recommend using the compiler options in addition to the ones you are typically using:

    ```
    -xCASCADELAKE -mtune=cascadelake
    ```

    Older versions of the Intel compiler will not support code generation for the *Cascade Lake* CPU.

#### Compiling on compute node, inside a batch script
Alternatively you can compile on a compute node of the *lu32*-partition.  It is recommended to have a separate compilation job with a suitable number of cores on a single node and not to include the build of the code in a production job.

When compiling on a *Cascade Lake* compute node one can instruct the compilers to generate code optimised for the architecture used for the building step.   It is advisable to use compilers and libraries inside the ```SoftwareTree/Cascade```.  

=== "GCC compiler"

    When compiling on a compute note with the GCC compiler we recommend adding the options:

    ```
    -march=native -mtune=native
    ```

    to the compilation options.

=== "Intel compiler"

    When compiling on a compute note with the Intel compiler we recommend adding the option:

    ```
    -xHost 
    ```

    to the compilation options.  In addtion one can specify `-mtune=cascadelake` as well.

## Batch scheduler
This document assumes that the reader is familiar with the SLURM scheduler as deployed for years on the standard Aurora nodes.   We only document the extra bits required to utilise the *lu32*-partition.
### Selecting the *lu32*-partition
To instruct the scheduler to execute your job to run on the *lu32*-partition you need to add a line to your batch script:

```
#SBATCH -p lu32
```
### Cores per node
As the name indicates the nodes feature 32 cpu cores.  If your job can efficiently use all of them you can request all cores by specifying:

```
#SBATCH --tasks-per-node=32
```
### Memory specification
The default memory per CPU core on Aurora is set to 3100 MB.  Since the nodes in the *lu32*-partition have more memory, you can specify up to 6000 MB per core without being penalised:

```
#SBATCH --mem-per-cpu=6000 
```
### Shebang
There are currently issues when specifying `#!/bin/sh` for a job script. Scripts work when specifying:

```
#!/bin/bash  
``` 

---

**Author:**
(LUNARC)

**Last Updated:**
2022-10-05