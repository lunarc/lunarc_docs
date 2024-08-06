# Frequently asked questions - Jobs

## What is the maximum job time on LUNARC resources?

We allow batch jobs to ask for up to 168 hours, which is 7 days.

## How do I use a Lund University project

When using a Lund University project (LU project), you have to specify the project name, the partition and in some cases a reservation.  This is explained in our [batch system guide](https://lunarc-documentation.readthedocs.io/en/latest/batch_system/#specifying-a-project-and-partition-for-users-with-lu-projects-or-multiple-projects).

## I want to use the COSMOS GPU nodes

Please review our [batch system guide](https://lunarc-documentation.readthedocs.io/en/latest/manual/submitting_jobs/manual_specifying_requirements/#accessing-gpus) for the required modification to your submission script.  The [software guide](https://lunarc-documentation.readthedocs.io/en/latest/manual/manual_modules_toolchains/#cuda-based-toolchains-for-gpu-nodes) provides an overview on Compiler, Cuda and MPI support for the GPU nodes. 

## Can I prevent my job from restarting in case of a node failure

By default the SLURM job scheduler restarts jobs in case of e.g. a failing node.  This is not always desirable.  Adding a line
```bash
#SBATCH --no-requeue
```
to the header portion of your job script prevents this behaviour.

---

**Authors:**
Joachim Hein (LUNARC)
Rebecca Pitts (LUNARC)

**Last Updated:**
2022-08-31
