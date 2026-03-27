# Using the lu32 partition

LUNARC offers a partition named *lu32* featuring nodes with Intel Cascade Lake CPUs, 32 cores per node, and more memory than the standard AMD Milan nodes. This document focuses on the key differences between the standard partition and the *lu32* partition from a user perspective.

## Hardware

The *lu32* nodes feature Intel Cascade Lake CPUs, while the standard COSMOS nodes use AMD Milan CPUs. Software executables built for AMD will generally run on the Cascade Lake nodes. However, software specifically built to exploit Cascade Lake instructions is **not** expected to work on AMD nodes.

The Cascade Lake CPUs feature AVX512 instructions not present on the AMD Milan CPUs. Testing by LUNARC support has shown that code using these instructions can be up to 50% faster than code using AVX2 instructions.

### Key differences between the Cascade Lake and AMD Milan nodes

| | COSMOS Cascade Lake node (lu32) | COSMOS AMD Milan node (standard) |
| --- | :---: | :---: |
| Cores per node | 32 | 48 |
| Memory per node | 192 GB | 256 GB |
| Memory per core | 6000 MB | 5000 MB |
| AVX vector instructions | AVX, AVX2, **AVX512** | AVX, AVX2 |

## Software

### Using installed software

LUNARC supports, installs, and maintains a substantial number of software titles. By default, users can select from software titles suitable for both node types. These titles are expected to give decent performance on the Cascade Lake nodes but are not optimised for them. Depending on the software, switching to titles built specifically for Cascade Lake may or may not yield performance benefits.

!!! info
    Users not using the *lu32* partition who achieve satisfactory performance from the standard software do not need to change their workflows. The only change they will notice is a new module `SoftwareTree/Haswell` appearing in the list of loaded modules.

#### Switching to Cascade Lake software

To change to software compiled for the Cascade Lake CPUs, unload the `SoftwareTree/Haswell` module and load `SoftwareTree/Cascade`. Since `SoftwareTree` modules are *sticky* they must be purged by force:

```bash
module --force purge
module load SoftwareTree/Cascade
```

This works on the frontend nodes (`cosmos.lunarc.lu.se`, `cosmos-dt.lunarc.lu.se`) and within a batch script. Once loaded, use `module avail` and `module spider` to explore available software.

!!! info
    - Software in `SoftwareTree/Cascade` is not expected to work on the frontend nodes.
    - Only a subset of available software titles has been built for Cascade Lake. Use the standard software if what you need is not available.
    - To request additional software titles in `SoftwareTree/Cascade`, contact LUNARC support.

### Building your own software

To obtain executables optimised for the Cascade Lake CPUs, you need to instruct the compilers to target that architecture. This can be done via cross-compilation on a frontend node or by compiling on a Cascade Lake compute node inside a batch job.

#### Cross compiling on a frontend node

The COSMOS frontend nodes feature AMD CPUs. To generate code for Cascade Lake processors, instruct the compiler to target a different architecture than the one used for compilation.

=== "GCC compiler"

    Add the following options to your compilation command:

    ```bash
    -march=cascadelake -mtune=cascadelake
    ```

    Older versions of GCC do not support code generation for Cascade Lake CPUs.

=== "Intel compiler"

    Add the following options to your compilation command:

    ```bash
    -xCASCADELAKE -mtune=cascadelake
    ```

    Older versions of the Intel compiler do not support Cascade Lake code generation.

#### Compiling on a compute node inside a batch script

Alternatively, compile on a compute node of the *lu32* partition. Use a separate compilation job rather than including the build in a production job.

When compiling on a Cascade Lake node, you can instruct the compiler to optimise for the native architecture:

=== "GCC compiler"

    ```bash
    -march=native -mtune=native
    ```

=== "Intel compiler"

    ```bash
    -xHost
    ```

## Batch scheduler

This section covers only the extra SLURM directives needed for the *lu32* partition.

### Selecting the lu32 partition

Add the following line to your batch script:

```bash
#SBATCH -p lu32
```

### Cores per node

The *lu32* nodes have 32 CPU cores. To use all cores on a node:

```bash
#SBATCH --ntasks-per-node=32
```

### Memory specification

The *lu32* nodes have more memory than the standard partition. You can request up to 6000 MB per core without penalty:

```bash
#SBATCH --mem-per-cpu=6000
```

### Shebang

There are currently issues when specifying `#!/bin/sh` for a job script. Use instead:

```bash
#!/bin/bash
```

---

**Author:**
(LUNARC)

**Last Updated:**
2023-01-31
