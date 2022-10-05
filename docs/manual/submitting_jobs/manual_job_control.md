# Controlling and monitoring jobs

## Submitting with sbatch

One uses the command **sbatch** to submit a job script to the batch system
for execution. SLURM will reply with the jobid number. The job will then
be held in the queue until the requested resources become available. A
typical use case looks as follows:

```bash
[fred@aurora MPItest]$ sbatch runjob.sh
Submitted batch job 7197
```

User fred submitted the script runjob.sh to the job queue and got the
jobid 7197 assigned to it.

## Starting executables within SLURM with srun

The command **srun** allows starting executables in a way managed by SLURM. This is particularly effective if you want to process a large number of jobs within a single submission to the batch system. A use case of **srun** to start many serial jobs in a single multicore submission scipt is discussed in the [*example section*](#id.bdphbddpef0).

## Monitoring jobs

The best overview of the queue is obtained with the command **jobinfo**. It sorts jobs in the
queue into running and waiting jobs. It also shows additional information, such as how long running jobs have left and in some cases when waiting jobs are expected to start. It can take some of the options available to the command **squeue** to filter the output, such as -u myid to only show jobs of user myid, -A lu2022-x-xx to only show jobs belonging to project lu2022-x-xx, and -p gpu to only show jobs using or waiting for nodes in the gpu partition.

**jobinfo** is a script that uses the command **squeue**, which can, of course, also be used directly to show you the current state of the job queue. It is less convenient but more configurable. The standard output, created by calling **squeue** without any options looks as follows:

```bash
JOBID PARTITION NAME USER ST TIME NODES NODELIST(REASON)
7303 snic hybrid_n fred PD 0:00 32 (Priority)
7302 snic hybrid_n fred PD 0:00 32 (Priority)
7301 snic hybrid_n fred PD 0:00 32 (Resources)
7304 snic preproce karl PD 0:00 6 (Priority)
7300 snic hybrid_n fred R 0:24 32 an[001-032]
7305 snic preproce karl R 0:37 6 an[081-086]
7306 snic hybrid_n fred R 0:37 6 an[081-086]
7307 snic testsimu sven R 0:07 1 an081
```

The first column gives the jobid, the third the job names, followed by the userid. The column labeled “ST” gives the job state. The most important states are:

| Symbol | Meaning |
|--------|-----------------------------|
| R | running |
| PD | pending, awaiting resources |
| CG | completing |

The state column is followed by the time used by the job and the number of nodes utilised by the job. For running jobs the last column gives the names of the nodes utilised or if the job is waiting a reason why it is not executing.

The **squeue** command is highly configurable, as mentioned above. Useful options include -u
myid, which lists all jobs of the user myid and also the --start option. The latter gives the current estimate of when SLURM expects the job to start. Note, that this can shift in either direction, depending on e.g. jobs finishing earlier than specified or jobs with higher priority getting added to the job queue.

## Terminating jobs with scancel

It is frequently required to remove jobs from the queue. This might be that you discover a problem in your job specification or intermediate results of running a job indicating that something went wrong. Use **scancel** to remove a job from the job queue. To do so you need the jobid, which can be queried with the **jobinfo** or **squeue** command. To remove the job with the jobid 7103 from the job queue type

```bash
scancel 7103
```

---

**Author:**
(LUNARC)

**Last Updated:**
2022-10-05