# Batch System Rules

!!! info

    These rules are applied by the scheduler for the batch queues.

A scheduler is used to start jobs, organise the order in the batch queue and put jobs aside that are not eligible to run or to queue at the moment; i.e., manage active, idle and blocked jobs, which are displayed by the **showq** command.

To be eligible to run, a job may not have a walltime that exceeds 168 hours (7 days). If a job is submitted with a longer walltime, it will be blocked and labeled BatchHold, which means that it will never start by itself. Furthermore, the sum of the walltime (multiplied by the number of processors for parallel jobs) remaining for the running jobs of the user and the walltime of the submitted job should not be larger than the value of **MAXPS** (see the table below). If it is, the submitted job will be blocked and labeled Idle. When the running jobs have spent enough walltime to lower the sum to MAXPS, the job will be put in the queue and started when its turn comes. In the same way, there are also limits to the number of allocated processors, **MAXPROC** (or the memory corresponding to a processor). These parameters have two levels, a higher "hard" value when there is no queue and a lower "soft" value when jobs are waiting (blocked jobs do not count as waiting). There are also limits on the eligible jobs in the queue: **MAXIPS**, **MAXIJOB**, and **MAXIPROC**.

| | MAXPS hard/soft | MAXPROC hard/soft | MAXIPS | MAXIJOB | MAXIPROC | 
|-----------------|-------------------|-------------|---------|----------|-----|
| Aurora | 30240 h/15120 h | 512/64-256* | 30240 h | 24 | 256 |
 
On Monday to Friday between 8.00 and 20.00, four nodes on Platon only accept jobs with walltimes of 2 hours and less, to make it possible to run short tests. It is not allowed to submit long series of two-hour jobs to bypass the queue.

The order of the jobs waiting in the queue is governed by two factors, waiting time and "fair share". These give priorities, which are constantly updated. The longer a job sits in the queue, the higher the priority, but a short job will gain priority faster than a job requesting more walltime and may overtake longer jobs. "Fair share" means that users who have not used the system much during the last 14 days will have a higher initial priority than users who have used the system to a large degree. "Fair share" is also a function of the allocated time; i.e., a small allocation will lose priority quicker per used absolute time than a large allocation.

The batch system rules are revised regularly to optimise system utilisation and fairness.

*) The soft MAXPROC depends on the project and is scaled according to the allocation. It is set so that a project running continuously the whole month would still get 80% more than the allocation, although the extra time may be harder to get due to a lower priority.

The reason for the quotas is that projects that submitted many jobs could run in large batches, keeping other projects in the queue. The purpose of the quota is, thus, to give projects their fair share all the time. Within a project, the same problem persists, i.e., some people can use up the quota and block others, but the soft quota is also soft in the sense that the queue system can choose to ignore it at times, for example, to let people in that are not running at all.

In addition, to prevent the queue from being swamped, the number of jobs from one user is limited to 500. That is a safety limit and more than 100-200 jobs should be avoided, for example, by collecting many short jobs in fewer longer ones.

---

**Author:**
(LUNARC)

**Last Updated:**
2022-10-06