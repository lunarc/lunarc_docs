# Job requirements for multiprocessor jobs

In HPC it is very common to have many processing elements working on a job. The extra processing power can be used to tackle problems too large for a single core, or to run many calculations within a single job submission.

## Terminology: nodes, processors, cores, tasks

| Term | Explanation | Number on COSMOS |
| --- | --- | --- |
| Node | A physical computer/server in a compute cluster | Over 200 standard CPU nodes |
| Processor | A multi-core processor housing many processing elements | 2 per node |
| Socket | The physical slot the processor plugs into — used as a synonym for processor | 2 per node |
| Core | Individual processing element | 48 per node |
| GPU | NVIDIA/AMD co-processor, used for compute or graphics | None on standard CPU nodes |
| Task | A software process — an instance of a running program with its own data and instruction stream. Serial and pure MPI programs use one task per core. | Controlled in job script |
| Thread | A stream of instructions within a task, sharing memory with other threads in the same task. Used by OpenMP and similar frameworks. | Controlled in job script |

## Resource requests for multiprocessor jobs

When running multi-processor jobs, specify:

1. The number of nodes required
2. The number of tasks per node
3. The number of threads per task (for threaded programs)

For pure MPI or task-farm jobs, specify items 1 and 2 only. For threaded jobs (OpenMP, Java), specify items 1 and 3.

The product of items 2 and 3 should not exceed 48 (the number of cores per node on COSMOS). For multi-node jobs, the product should usually equal 48 to fully utilise each node.

## Specifying the number of nodes

Request nodes with the `-N` option:

```bash
#SBATCH -N 4
```

!!! warning
    Using `-N` alone reserves only one core per node. You must also specify `--ntasks-per-node` or `--cpus-per-task` to use the full node.

## Specifying the number of tasks per node (MPI jobs)

Use `--ntasks-per-node` to set the number of MPI tasks per node. For a fully-packed MPI job on COSMOS, set this to 48:

```bash
#SBATCH -N 4
#SBATCH --ntasks-per-node=48
```

This allocates 192 MPI tasks across 4 nodes.

If you use fewer than 48 tasks per node and want to prevent other users' jobs from sharing your nodes, add `--exclusive`. Note that your project will be charged for all cores on the allocated nodes when using `--exclusive`.

## Specifying the number of threads per task (shared-memory jobs)

For threaded programs (e.g. OpenMP, Java) use `--cpus-per-task` to specify how many CPU cores each task may use. For a single-node job using all 48 cores:

```bash
#SBATCH -N 1
#SBATCH --ntasks=1
#SBATCH --cpus-per-task=48
```

For a 4-thread job:

```bash
#SBATCH -N 1
#SBATCH --ntasks=1
#SBATCH --cpus-per-task=4
```

!!! info "Setting the thread count for OpenMP"
    SLURM allocates the cores but does not automatically tell your program how many threads to use. Set `OMP_NUM_THREADS` explicitly in your job script to match `--cpus-per-task`:

    ```bash
    export OMP_NUM_THREADS=$SLURM_CPUS_PER_TASK
    ```

## Hybrid jobs (MPI + OpenMP)

Hybrid programs combining distributed and shared memory have become increasingly common. For a job using 48 MPI tasks where each task spawns 4 OpenMP threads, you need 4 nodes with 12 tasks per node, and 4 cores per task:

```bash
#SBATCH -N 4
#SBATCH --ntasks-per-node=12
#SBATCH --cpus-per-task=4
```

Remember to also set `OMP_NUM_THREADS=4` in the job script body.

## Specifying an exact number of cores

In unusual cases — for example when the required task count does not divide evenly across nodes — the `-n` option lets you specify the total number of tasks directly:

```bash
#SBATCH -N 2
#SBATCH --ntasks-per-node=48
#SBATCH -n 94
```

This reserves 94 cores across 2 nodes.

---

**Author:**
(LUNARC)

**Last Updated:**
2025-02-10
