# Controlling and monitoring jobs

## Submitting with sbatch

Use `sbatch` to submit a job script to the batch system. SLURM will reply with a job ID and hold the job in the queue until the requested resources become available:

```console
[fred@cosmos ~]$ sbatch runjob.sh
Submitted batch job 7197
```

## Starting executables with srun

`srun` starts executables in a way managed directly by SLURM. This is particularly useful for running many serial jobs within a single multicore submission script — see the [example job scripts](../example_job_scripts/manual_example_multiple_serial_jobs.md) section for a worked example.

## Monitoring jobs with jobinfo

The best overview of the queue is the `jobinfo` command. It sorts jobs into running and waiting, shows how long running jobs have left, and in many cases gives an estimated start time for waiting jobs.

Useful filters (these mirror `squeue` options):

| Option | Effect |
| --- | --- |
| `-u myid` | Show only jobs belonging to user `myid` |
| `-A lu2024-x-xx` | Show only jobs charged to a specific project |
| `-p lu48` | Show only jobs in the `lu48` partition |

## Monitoring jobs with squeue

`jobinfo` is a wrapper around `squeue`, which you can also use directly. Calling `squeue` without any options shows the full queue:

```console
JOBID PARTITION NAME     USER ST  TIME NODES NODELIST(REASON)
7303  lu48      hybrid_n fred PD  0:00    32 (Priority)
7302  lu48      hybrid_n fred PD  0:00    32 (Priority)
7301  lu48      hybrid_n fred PD  0:00    32 (Resources)
7304  lu48      preproce karl PD  0:00     6 (Priority)
7300  lu48      hybrid_n fred R   0:24    32 cn[001-032]
7305  lu48      preproce karl R   0:37     6 cn[081-086]
7306  lu48      hybrid_n fred R   0:37     6 cn[081-086]
7307  lu48      testsimu sven R   0:07     1 cn081
```

The columns are: job ID, partition, job name, user, state, time used, nodes, and node list or wait reason. The key job states are:

| Symbol | Meaning |
| --- | --- |
| `R` | Running |
| `PD` | Pending — awaiting resources |
| `CG` | Completing |

To see only your own jobs:

```bash
squeue -u myid
```

To get SLURM's current estimate of when a pending job will start:

```bash
squeue --start -j 7303
```

Note that start time estimates can shift in either direction as higher-priority jobs enter the queue or running jobs finish early.

## Terminating jobs with scancel

To cancel a job, use `scancel` with the job ID (obtainable from `jobinfo` or `squeue`):

```bash
scancel 7103
```

To cancel all your jobs at once:

```bash
scancel -u myid
```

---

**Author:**
(LUNARC)

**Last Updated:**
2022-10-05
