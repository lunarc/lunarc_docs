# Job execution environment

When you submit a job with `sbatch`, your current shell environment is copied into the job — including all loaded modules and any environment variables you have set.

!!! warning "Environment conflicts"
    Modules or variables set in your interactive session may conflict with what your job script expects. Best practice is to explicitly load all required modules inside the job script itself rather than relying on the inherited environment.

## Modules in job scripts

COSMOS uses a [hierarchical module naming scheme](../manual_modules.md). Load all required modules explicitly in your job script:

```bash
# Unload any inherited modules first
module purge

# Load the modules your job needs
module load foss/2023a
```

When building your own software, use a [toolchain module](../manual_modules_toolchains.md) to ensure the compiler, MPI library, and supporting libraries are consistent.

## SLURM environment variables

SLURM sets the following environment variables in every job. These are available inside your job script and in any programs it launches.

| Variable | Description | Example value |
| --- | --- | --- |
| `SLURM_JOB_ID` | Unique ID assigned to the job | `123456` |
| `SLURM_SUBMIT_DIR` | Directory from which `sbatch` was called | `/home/user/myjob` |
| `SLURM_JOB_NAME` | Name of the job (set with `-J`) | `myjob` |
| `SLURM_NNODES` | Number of nodes allocated | `4` |
| `SLURM_NTASKS` | Total number of tasks across all nodes | `192` |
| `SLURM_TASKS_PER_NODE` | Tasks per node | `48` |
| `SLURM_CPUS_PER_TASK` | CPUs per task (only set if `--cpus-per-task` was requested) | `4` |
| `SLURM_JOB_NODELIST` | List of allocated nodes | `an[001-004]` |
| `SLURM_JOB_PARTITION` | Partition the job is running in | `lu48` |

A common use of these variables is returning output to the submission directory regardless of where the job runs:

```bash
cp result.dat $SLURM_SUBMIT_DIR
```

For a full list of SLURM variables, run `man sbatch` on COSMOS.

## NAISS local scratch variable

NAISS provides a standard variable for the local scratch disk on each compute node:

| Variable | Description |
| --- | --- |
| `NAISS_TMP` | Local scratch disk on the allocated node — fast I/O, deleted when the job ends |

!!! note "Legacy variable name"
    The older name `SNIC_TMP` (from the previous SNIC era) is also still set and points to the same directory. New job scripts should use `NAISS_TMP`.

Using the local scratch disk is strongly recommended for I/O-intensive jobs. See [Using local disk to improve I/O performance](manual_local_disk.md) for details and example scripts.

---

**Author:**
(LUNARC)

**Last Updated:**
2022-07-27
