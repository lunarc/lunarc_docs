In HPC it is very common to have many processing elements working on a
job. The extra processing power can be utilised to process large
problems beyond the capabilities of a single processing element. It can
also be used to swiftly perform a number of calculations within a single
job submission.

## Terminology around nodes, processors, cores, tasks

There is a a lot of structure within modern HPC equipment. For the
purposes of this user guide we will stick to the following terminology:

| Term | Explanation | Number on Aurora |
|-----------|---------------------------------|-----------------------------|
| Node | A physical computer | Standard: 180 for SNIC use |
| Processor | This denotes a the multi-core processor, housing many processing elements | 2 per node |
| GPU | This denotes a nvidia co-processor | 0 for SNIC use | 
| Socket | This is the “plug” the processor gets plugged into.  Used as a synonym for the processor | 2 per node |
| Core | Individual processing element | 20 per node |
| Task | This is a software concept.  It denotes a process, which is an instance of a running program.  It has its own data and instruction stream(s).  It can fork multiple threads to increase the computational speed.  Serial programs and pure MPI programs do not spawn threads. | User controls in job script |
| Thread | This is also a software concept.  A thread is a stream of instructions executed on the hardware.  It is part of a task and shares resources such as  data with other threads within the same task. | User controls in job script |

## Outline: Resource requests for multiprocessor jobs

When running multi processor jobs on the LUNARC clusters, one should
specify:

 1.  The number of nodes required by the jobs
 2.  The number of computational tasks per node
 3.  The number of threads spawned by each task

For a pure MPI job or when processing a large number of serial jobs in a
so called task farm, one will typically only specify the items 1 and 2,
while for a threaded job, using e.g. OpenMP or Java, one will typically
only specify items 1 and 3.

It is typically not advisable to have the product of items 2 and 3
exceeding the number of cores per node, which is 20 for Aurora compute
nodes.
In most cases users requesting multiple nodes will want the product
to equal the number of cores per node. The syntax how to control nodes,
tasks per node and threads per task is explaned below.

## Specifying the number of nodes required for the job

In SLURM one requests the number of nodes for a job with the **-N**
option. The following statement requests four nodes for your job:

```bash
#SBATCH -N 4
```

**Important:** without using either the --tasks-per-node or
the --cpus-per-task options of sbatch, this will reserve a single core
per node, so four in total, which is most likely not what you want.

## Specifying the number of tasks per node

Use the --tasks-per-node of sbatch to specify the number of tasks you
require per node. Most multinode job will set this equal to the number
of cores availble per node. The following example asks for 20 task per
node:

```bash
#SBATCH --tasks-per-node=20
```

This should be used together with the -N option, specifying the number
of nodes to be used. The default value for the number of tasks per node
is 1. For example to specify the requirements for an MPI job with 80
tasks or multiprocessor job using 80 processors to process a larger
number of serial jobs one would specify

```bash
#SBATCH -N 4
#SBATCH --tasks-per-node=20
```

When using fewer than 20 tasks per node and you want to prevent other
user’s jobs sharing your node, you need to consider using
the --exclusive option. If --exclusive is not specified, SLURM might
place other tasks onto your node.

## Specifying the number of threads for a shared-memory job

If you want to run shared-memory applications using threads, e.g. OpenMP
parallised code or Java applications, you need to specify the number of
threads you require per task. This can be done with the --tasks-per-node
option of sbatch.

For a standard shared-memory program, which doesn’t also use distributed
memory programming models such as MPI, one is restricted to a single
node. On that node, one can request as many threads as there are cores
on the node. On the standard Aurora compute nodes one can efficiently
use up to 20 threads. Use the following resource statement:

```bash
#SBATCH -N 1
#SBATCH --tasks-per-node=20
```

If your program is only efficient at a lower thread count, you may want
to use e.g.:

```bash
#SBATCH -N 1
#SBATCH --tasks-per-node=4
```

if you only want to use four threads. 

## Resource statements for hybrid programs using distributed and shared memory 

So-called hybrid programs, using both distributed and shared-memory
techniques have recently become popular. For example: for a program
using 40 MPI tasks, each task spawning 2 OpenMP threads one would
require 4 nodes and place eight tasks on each node. The number of
threads per task is given by --cpus-per-task. The resource statement
would look as follows:

```bash
#SBATCH -N 4
#SBATCH --tasks-per-node=10
#SBATCH --cpus-per-task=2
```

## Specifying the number of cores to be required by the job

In special cases, such as using very unusal numbers of tasks, the **-n**
option of sbatch to specify the number of cores might become useful.
When running a pure MPI program this option corresponds to the **number
of tasks** required for your program. The following statement in a job
script would reserve 78 cores for your job

```bash
#SBATCH -N 4
#SBATCH --tasks-per-node=20
#SBATCH -n 78
```

Please consider using the --exclusive option of sbatch to avoid SLURM
spreading your job on more nodes than necessary and placing other user’s
jobs on nodes utilising fewer than 20 cores for your job. Other user’s
jobs could via shared node resources (memory bus, cache, …)
interfere with your job and introduce undue operational noise. Such
noise is something parallel program execution can be extremely sensitive
to.
