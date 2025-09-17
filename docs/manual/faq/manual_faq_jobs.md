# Frequently asked questions - Jobs

## What is the maximum job time on LUNARC resources?

We allow batch jobs to ask for up to 168 hours, which is 7 days.

## How do I run an interactive application

LUNARC supports interactive HPC.  You need to connect to our system via the [LUNARC HPC desktop](../../getting_started/using_hpc_desktop.md).  Inside the HPC desktop you can use the [Desktop on-demand](../../getting_started/gfxlauncher.md) to start your application.

## How do I specify a project or reservation

Users with membership in multiple projects have to specify the project name and in some cases a reservation.  This is explained in our [batch system guide](../submitting_jobs/manual_specifying_requirements.md#specifying-a-project-allocation-and-partition).

## I want to use the COSMOS GPU nodes

Please review our [batch system guide](../submitting_jobs/manual_specifying_requirements.md#accessing-gpus) for the required modification to your submission script.  The [software guide](../manual_modules_toolchains.md#cuda-based-toolchains-for-gpu-nodes) provides an overview on Compiler, Cuda and MPI support for the GPU nodes. 

## Can I prevent my job from restarting in case of a node failure

By default the SLURM job scheduler restarts jobs in case of e.g. a failing node.  This is not always desirable.  Adding a line
```bash
#SBATCH --no-requeue
```
to the header portion of your job script prevents this behaviour.

---

**Authors:**
Joachim Hein (LUNARC),
Rebecca Pitts (LUNARC)

**Last Updated:**
2024-08-06
