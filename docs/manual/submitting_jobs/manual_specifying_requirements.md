We now describe several statements that are most commonly used to specify resource requirements for all kinds of jobs. Refer to **“man sbatch”** for more information.

## Walltime (reqeuested job time)

The walltime attribute specifies the time requested for completing the job. The time is _not_ CPU-time but the total time measured by an ordinary clock. In the previous example, the time requested was 0 hours 5 minutes and 0 seconds. Walltime is specified in seconds or using the following notation:

```
Hours:Minutes:Seconds
```

If your calculation hasn’t finished once the specified time has elapsed, SLURM will terminate your job. It is, therefore **good practise**** to specify a bit more time than you anticipate your job to take. This ensures that you still get your results, even if the job is slowed by some interference, e.g. waiting for a write to a shared file system to finish. However, don’t specify excessive amounts of extra time. Due to scheduling constraints, jobs asking for less time will typically spend less time in the queue, waiting for their execution. This also provides safety against the depletion of your allocation. If e.g., your job hangs, SLURM will terminate your job and the project will be charged less time if the walltime margin is not excessive.

To specify your walltime requirements write a statement like

```bash
#SBATCH -t 00:10:00
```

into your job script.

The maximum walltime for any job on COSMOS is 168h (7 days).

## Naming jobs

All jobs are given both a job identifier and a name for easier identification in the batch system. The default name given to a job is the file name of the submit script, making it difficult to identify your job if you use a common name for your submit scripts. You can give your job a name from inside the script by using the -J
option:

```bash
#SBATCH -J parameterTest
```

This will name your job “parameterTest”.

## Specifying memory requirements

The COSMOS system has 256 GB of memory installed on a normal compute node. To allow memory for the operating system, only 254000 MB are available for jobs and the default memory request per core is 5300 MB of memory (48 cores per node). If more than 5300 MB per core is needed, it has to be requested explicitly using the **--mem-per-cpu** option.  For example, if you require  10000 MB per core, add the line:

```bash
#SBATCH --mem-per-cpu=10000
```

When requesting more than 5300 MB per processing core on a normal COSMOS node, your jobs will be charged at a higher rate. If you do this, some processing cores must remain idle since you use more than your fair share of memory.


## Controlling job output

By default, the output which your job writes to standard output (stdout) and standard error (stderr) is written to a file named

```bash
slurm_%j.out
```

The %j in the file name will be replaced by the jobnumber SLURM assigns to your job. This ensures that the output file from your job is unique and that different jobs do not interfere with each other's output files.

In many cases, the default file name is not convenient. You might want to have a file name which is more descriptive of the job that is actually running - you might even want to include important meta-data, such as physical parameters, into the output filename(s). This can be achieved by using the **-o** and **-e** options of **sbatch**. The **-o** option specifies the file containing the stdout and the **-e** option the file containing the stderr. It is good practise to include the **%j** string into the filenames. That will prevent jobs from overwriting each other's output files. The following gives an example:

```bash
#SBATCH -o calcflow_m1_%j.out
#SBATCH -e calcflow_m1_%j.err
```

!!! info

    You can give the same filename for both options to get **stdout** and **stderr** written to the same file.

## Job notification emails

SLURM on the systems can send you an email if the status of your job changes as it progresses through the job queue. To use this feature, you need to specify the email address using the **--mail-user** option and specify the event you want to get notified about using the **--mail-type** option. The following

```bash
#SBATCH --mail-user=fred@institute.se
#SBATCH --mail-type=END
```

will send an email to the address fred@institute.se once the job has ended. Valid type values, selecting the event you can get notified about, are BEGIN, END, FAIL, REQUEUE, and ALL (any state change).

!!! note
    If messages do not arrive, please monitor your junkmail or spam folder.

## Job dependencies

To describe job dependencies, use the **-d** option of **sbatch**. This is particularly useful for job dependencies, in workflows.

To illustrate this consider the following example. You require a serial job to create a mesh for your simulation. Once this has finished, you want to start a parallel job using the mesh. You first submit the mesh creation job using **sbatch**

```bash
[fred@cosmos-dt Simcode]$ sbatch run_mesh.sh
Submitted batch job 8042
```

As discussed, **sbatch** returns you a jobid, 8042 in this example. You use this to declare your dependency when submitting the simulation job to the queue

```bash
[fred@cosmos-dt Simcode]$ sbatch -d afterok:8042 run_sim.sh
Submitted batch job 8043
```

When using **jobinfo** or **squeue** to monitor job 8043, this should now be in status pending (PD) with the reason of dependency. Another common use case for this functionality is a simulation requiring many days of computer times being split into several submissions.

## Testing jobs (test queue)

To run short tests, it is possible to request extra high priority on COSMOS with the help of

```bash
#SBATCH --qos=test
```

For one such job, the maximum walltime is 1 h and the maximum number of nodes is two and a user is only allowed to run two such jobs simultaneously. A system of floating reservations is used to free two nodes every second hour between 8.00 and 20.00 to reduce the queue time for test jobs. The way it works also means that the shorter the test job, the more likely it is to start sooner rather than later. It is not allowed to use **qos=test** for a series of production runs.

## Controlling requeueing/restarting of jobs

By default, the scheduler will requeue (aka. restart) jobs that suffered from node failure.  This is not always desirable.  Adding a line

```bash
#SBATCH --no-requeue
```

to the header portion of your job script prevents this behavior.

## Specifying a project allocation and partition 

Most users are members of a single LU project. These users do not need to specify a project in their submission script. The LUNARC set-up will automatically use that project for accounting.

Users with membership in more than one project have to let the system know which project to charge for the run. To do so, you need to specify the project using the **-A** option:

```bash
#SBATCH -A lu2022-x-xxx
```

Replace the **lu2022-x-xx** with the string naming your project. The correct name can be obtained by using the **projinfo** command. The information is also available in the SUPR system, but notice the difference in the formatting of the names.

In addition, for those who access private nodes (financed by a research project) through an LU project, the corresponding reservation has to be stated:

```bash
#SBATCH --reservation=lu2022-x-xx
```

## Accessing GPUs

### NVIDIA A100 GPUs with AMD processors 

A number of compute nodes on COSMOS are equipped with GPUs. At the time of this revision, there are six nodes with A100 NVIDIA GPUs and AMD CPUs available.  These nodes feature two AMD EPYC 7413 24-Core processors, a total of 48 cores.  These nodes have 512 GB of RAM are placed in the **gpua100** partition. To access these nodes, you must include the line

```bash
#SBATCH -p gpua100
```
in your batch scipt.  These nodes are configured as exclusive access and will not be shared between users.   User projects will be charged for the entire node (48 cores).

To gain access to all the memory of the nodes, users need to override the default memory request of 5300 MB per core. Your batch script should include the line:

```bash
#SBATCH --mem-per-cpu=10600
```
to use all allowable memory.  These nodes in the gpua100 partition must not be used for CPU only applications, which are not utilising the GPUs.

### NVIDIA A40 GPUs with AMD processors

These nodes placed in the **gpua40** are reserved for interactive graphic work in the on-demand system.  Please do not submit compute jobs via slurm to these nodes.  Compute jobs submitted to the gpua40 partition may be terminated without prior warning.   

---

**Author:**
(LUNARC)

**Last Updated:**
2024-08-06
