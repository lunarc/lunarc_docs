# Using GPU nodes

COSMOS has three GPU partitions serving different purposes. This page explains what is available, how to choose the right partition, and how to write a job script that requests GPU resources.

## Available GPU partitions

| Partition | GPU | CPU | Cores | RAM | Use case |
|---|---|---|---|---|---|
| `gpua100` | NVIDIA A100 80 GB | AMD EPYC 7413 | 48 | 512 GB | Compute — batch jobs |
| `gpua100i` | NVIDIA A100 40 GB | Intel Xeon Gold 6226R | 32 | 384 GB | Compute — batch jobs |
| `gpua40` | NVIDIA A40 | AMD EPYC 7413 | 48 | — | Interactive visualisation only |

!!! warning "`gpua40` is for interactive work only"
    The `gpua40` partition is reserved for interactive graphical work via the [Desktop on Demand](../getting_started/gfxlauncher.md). Do not submit batch compute jobs to this partition — they may be terminated without warning.

## Choosing a partition

**Use `gpua100`** if you need the largest GPU memory (80 GB per GPU) or the highest single-GPU performance. These nodes are allocated exclusively — your job gets the entire node (48 cores, 512 GB RAM, 1 A100) regardless of how many cores you request. Your project is charged for the full node.

**Use `gpua100i`** if you need less memory or want to share nodes. Four nodes are available, two with 1 GPU and two with 2 GPUs, so multiple jobs can run on the same node simultaneously. Specify your resource needs explicitly (see examples below).

**Use the Desktop on Demand** if you need GPU-accelerated interactive work such as visualisation or a graphical application. Open the [LUNARC HPC Desktop](../getting_started/using_hpc_desktop.md), select an application from the menu, and choose an AMD/NVIDIA A40 resource in the launcher.

## Job script examples

### A100 80 GB (gpua100)

These nodes are exclusive — one job per node:

```bash
#!/bin/bash
#SBATCH -t 01:00:00
#SBATCH -J gpu_job
#SBATCH -p gpua100

module purge
module load <your-cuda-module>

./my_gpu_program
```

Your job has access to all 48 cores, 512 GB RAM, and the full A100 80 GB GPU.

### A100 40 GB (gpua100i) — single GPU

Request 16 cores and 1 GPU to fit on any of the four nodes:

```bash
#!/bin/bash
#SBATCH -t 01:00:00
#SBATCH -J gpu_job
#SBATCH -p gpua100i
#SBATCH -N 1
#SBATCH --ntasks-per-node=16
#SBATCH --gres=gpu:1

module purge
module load <your-cuda-module>

./my_gpu_program
```

The default memory allocation (5300 MB/core) applies. To use the full per-core memory available on these nodes:

```bash
#SBATCH --mem-per-cpu=11800
```

### A100 40 GB (gpua100i) — two GPUs

```bash
#!/bin/bash
#SBATCH -t 01:00:00
#SBATCH -J gpu_job
#SBATCH -p gpua100i
#SBATCH -N 1
#SBATCH --ntasks-per-node=32
#SBATCH --gres=gpu:2

module purge
module load <your-cuda-module>

./my_gpu_program
```

!!! note
    The `gpua100i` nodes must not be used for CPU-only jobs that do not use the GPU.

## CUDA software and toolchains

GPU applications typically require a CUDA-enabled software module or a CUDA toolchain for compilation. Use `module spider` to find what is available:

```bash
module spider CUDA
```

For compiling your own GPU code, COSMOS provides CUDA-based toolchains. The recommended choices are:

| Toolchain | Components |
|---|---|
| `gcccuda` | GCC + CUDA |
| `gompic` | GCC + CUDA + OpenMPI |
| `goolfc` | GCC + CUDA + OpenMPI + OpenBLAS + FFTW + ScaLAPACK |
| `iccifortcuda` | Intel compilers + CUDA |
| `intelcuda` | Intel compilers + CUDA + Intel MPI + MKL |

Load a toolchain before compiling:

```bash
module load gcccuda/2023a
```

See [Compiling code and using toolchains](manual_modules_toolchains.md) for full details.

## Monitoring GPU jobs

Use `jobinfo` to monitor your job as normal:

```bash
jobinfo -u $USER
```

Once a GPU job is running, you can check GPU utilisation from the compute node using `nvidia-smi`. To run it from the login node against your job:

```bash
srun --jobid=<jobid> --pty nvidia-smi
```

---

**Author:**
(LUNARC)

**Last Updated:**
2026-03-30
