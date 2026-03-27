# Frequently asked questions - Jobs

## What is the maximum job time on LUNARC resources?

Batch jobs can request up to **168 hours** (7 days).

## How do I run an interactive application?

LUNARC supports interactive HPC. Connect via the [LUNARC HPC Desktop](../../../getting_started/using_hpc_desktop.md) and use the [Desktop on Demand](../../../getting_started/gfxlauncher.md) to launch your application.

## How do I specify a project or reservation?

Users with membership in multiple projects must specify the project name, and in some cases a reservation. This is explained in the [batch system guide](../submitting_jobs/manual_specifying_requirements.md#specifying-a-project-allocation-and-partition).

## I want to use the COSMOS GPU nodes

See the [batch system guide](../submitting_jobs/manual_specifying_requirements.md#accessing-gpus) for the required changes to your submission script. The [software guide](../manual_modules_toolchains.md#cuda-based-toolchains-for-gpu-nodes) covers compiler, CUDA, and MPI support for the GPU nodes.

## Can I prevent my job from restarting after a node failure?

By default, SLURM restarts jobs if a node fails. To disable this, add the following to the header of your job script:

```bash
#SBATCH --no-requeue
```

---

**Authors:**
Joachim Hein (LUNARC),
Rebecca Pitts (LUNARC)

**Last Updated:**
2024-08-06
