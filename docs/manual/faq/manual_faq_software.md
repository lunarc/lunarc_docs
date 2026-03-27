# Frequently asked questions - Software

## Can you install software for me?

Many packages are available through the module system. Search first with `module spider` before contacting the helpdesk — it may already be installed.

## I cannot find my software package in the module system

LUNARC uses a hierarchical module naming scheme, explained in the [Using installed software guide](../manual_modules.md). If you still cannot locate your package after using `module spider`, contact [LUNARC support](../../about/contact.md).

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
