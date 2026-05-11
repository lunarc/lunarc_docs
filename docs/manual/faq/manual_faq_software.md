# Frequently asked questions - Software

## I cannot find my software — what are my options?

Work through the following steps before submitting a support request.

### 1. Search the full module tree

LUNARC uses a hierarchical module system where not all software is visible by default. Use `module spider` to search the entire tree:

```bash
module spider <packagename>
```

The search is case-insensitive. If the package is installed, `module spider` will find it and show you which prerequisite modules to load first. See [Using installed software](../manual_modules.md) for details.

### 2. Consider self-service alternatives

If the software is not installed as a module, you may be able to set it up yourself without waiting for a support request:

**Conda / pip (Python packages)**
Install packages into a personal conda environment or with `pip --user`. This works well for Python libraries and tools. See the [Python guide](../../guides/applications/Python.md) for how to create and use conda environments on COSMOS.

**Containers (Apptainer)**
If the software has complex dependencies or requires a different OS environment, run it in a container. Many research tools have pre-built containers on Docker Hub or Singularity Hub that can be pulled and run directly on COSMOS. See the [containers guide](../../guides/containers/apptainer.md) for details.

### 3. Request a module installation

If the software is not available as a module and the self-service options above are not suitable, submit a support request via [SUPR](https://supr.naiss.se/support/?centre_resource=c5). Include the following to help the team process your request efficiently:

- **Software name and version** — the exact version you need
- **Where to get it** — a link to the project website, source repository, or documentation
- **Why it is needed** — a brief description of your use case
- **Dependencies** — any specific libraries, compilers, or toolchains it requires, if known
- **Urgency** — if you have a deadline, mention it

LUNARC installs software using [EasyBuild](https://easybuild.io/). If an EasyBuild recipe already exists for your software, installation is usually straightforward. For software without an existing recipe, the process takes longer.

## Loading a module unloads prerequisites from a previously loaded module

Software modules can have conflicting prerequisites. When you load a second module whose prerequisites clash with the first, SLURM will purge the conflicting ones — potentially breaking the earlier module.

Options to resolve this:

- Check whether different versions of the conflicting modules share common prerequisites. We often provide the same software built against different toolchains for this reason.
- Use a separate terminal for each module. This works when the modules produce or consume files rather than interacting directly. The [LUNARC HPC Desktop](../../getting_started/using_hpc_desktop.md) makes it easy to run multiple terminals in one session.
- If neither option works, contact the [LUNARC helpdesk](https://supr.naiss.se/support/?centre_resource=c5) to discuss your situation.

## My application fails because it can't find libgfortran.so.3

```text
error while loading shared libraries: libgfortran.so.3: cannot
open shared object file: No such file or directory
```

This means your software was compiled with a deprecated version of GCC. Recompile it on COSMOS using a newer GCC version — preferably one provided as a module.

## I need LAPACK and BLAS — they used to be in /usr/lib64

The libraries in `/usr/lib64` are not highly optimised. LUNARC provides:

- **[OpenBLAS](https://www.openblas.net/)** — included in the `foss` [toolchain](../manual_modules_toolchains.md)
- **[MKL](https://software.intel.com/en-us/intel-mkl)** — included in the `intel`, `iomkl`, and `gimkl` toolchains

Both deliver excellent performance. If you use OpenBLAS in an MPI job, see [below](#my-mpi-code-using-openblas-runs-slowly--does-not-perform-as-expected) for thread control.

## How do I link my code against the MKL installation on COSMOS?

LUNARC's MKL modules set the `MKLROOT` environment variable automatically. For the correct linker flags, use the [Intel MKL Link Line Advisor](https://software.intel.com/en-us/articles/intel-mkl-link-line-advisor).

## My MPI code using OpenBLAS runs slowly / does not perform as expected

OpenBLAS in the `foss` [toolchains](../manual_modules_toolchains.md#currently-provided-toolchains) is built with thread support and will spawn one thread per allocated core by default. For pure MPI jobs this is usually undesirable. Set `OMP_NUM_THREADS` to control it:

```bash
export OMP_NUM_THREADS=1
```

See also: [compiling MPI code with a toolchain](../manual_modules_toolchains.md#compiling-mpi-code-using-a-toolchain).

## I want to run VASP on COSMOS but it doesn't work

VASP requires a valid license registered with the VASP developers in Vienna. Being named in a SUPR VASP group does **not** grant access.

To request access:

1. Register your license with the VASP developers
2. Contact the [LUNARC helpdesk](https://supr.naiss.se/support/?centre_resource=c5&summary=Getting+access+to+VASP) with:
    - Your LUNARC user ID
    - Name and department of the license holder
    - License number
    - Your name as it appears on the license
    - Your email address as it appears on the license
    - Whether you need VASP 5 or VASP 6

Note: verification with the VASP developers typically takes a few days but can take longer. VASP executables are built with OpenMPI and must be launched with `mpirun`. See the [MPI example scripts](../example_job_scripts/manual_example_mpi_48_tasks.md) for reference.

## I need access to Amber

Amber 22 is licensed software. Review the license on the [Amber website](https://ambermd.org/GetAmber.php#amber), paying particular attention to the **Assignment Restrictions**.

Submit a [support request](https://www.lunarc.lu.se/getting-help/) confirming that you accept the license terms. Your account will then be enabled for the Amber module.

## What profiling tools are available on COSMOS?

Several profiling and performance analysis tools are installed as modules. Use `module spider <name>` to find available versions and prerequisites.

| Tool | Module | Best for |
|---|---|---|
| **gprof** | Part of GCC — no module needed | Basic CPU profiling of serial and threaded code |
| **Valgrind** | `Valgrind` | Memory error detection, cache profiling, call graphs |
| **gperftools** | `gperftools` | Low-overhead CPU and heap profiling (Google Performance Tools) |
| **Score-P** | `Score-P` | MPI and OpenMP performance analysis; produces traces for Scalasca and Vampir |

### gprof (GCC)

Compile with `-pg` to enable profiling instrumentation, run your program normally, then analyse the `gmon.out` file it produces:

```bash
gcc -O2 -pg -o myprogram myprogram.c
./myprogram
gprof myprogram gmon.out > profile.txt
```

### Valgrind

Valgrind runs your program in an instrumented environment. The default tool (`memcheck`) detects memory errors; `callgrind` profiles CPU usage:

```bash
module load Valgrind
valgrind ./myprogram                        # memory checking
valgrind --tool=callgrind ./myprogram       # CPU profiling
```

### Score-P (MPI / OpenMP)

Score-P instruments MPI and OpenMP programs to produce performance traces. Prefix your compile commands with `scorep`:

```bash
module load Score-P
scorep mpicc -O2 -o myprogram myprogram.c
```

Run the instrumented binary via your normal job script. Score-P writes trace data to a `scorep-*` directory for analysis with tools such as [Scalasca](https://www.scalasca.org/) or [Vampir](https://vampir.eu/).

## My MPI application doesn't launch

COSMOS supports two MPI libraries with different launchers:

| Library | Launcher |
| --- | --- |
| OpenMPI (foss toolchain) | `mpirun` |
| Intel MPI (intel toolchain) | `srun` |

Using the wrong launcher for the toolchain your application was built with is the most common cause of this issue. See the [toolchain guide](../manual_modules_toolchains.md#currently-provided-toolchains) for details.

---

**Author:**
Joachim Hein (LUNARC)

**Last Updated:**
2024-11-14
