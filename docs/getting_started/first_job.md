# Your first job

This page walks you through submitting a job to COSMOS for the first time — writing a script, submitting it, checking its progress, and reading the output. The whole process takes about five minutes.

## 1. Open a terminal on COSMOS

Log in via SSH or the HPC Desktop if you have not already. All commands below are run on COSMOS, not your local machine.

## 2. Write a job script

A job script is a plain text file that tells SLURM what resources your job needs and what commands to run. Create one with a text editor — `nano` is a good choice if you are new to the command line:

```bash
nano first_job.sh
```

Paste in the following:

```bash
#!/bin/bash
#SBATCH -J my_first_job
#SBATCH -t 00:05:00
#SBATCH --qos=test

echo "Job started at: $(date)"
echo "Running on node: $(hostname)"
echo "Hello from COSMOS!"
echo "Job finished at: $(date)"
```

Save and close the file. In nano: `Ctrl+O` then `Enter` to save, `Ctrl+X` to exit.

### What each line does

| Line | Meaning |
|---|---|
| `#!/bin/bash` | Use bash to run this script |
| `#SBATCH -J my_first_job` | Give the job a name (visible in the queue) |
| `#SBATCH -t 00:05:00` | Request up to 5 minutes of compute time |
| `#SBATCH --qos=test` | Use the test queue — jobs get elevated priority (max 1 hour, max 2 nodes) |

!!! tip "Always use `--qos=test` while testing"
    The test queue gives your job elevated priority so you get results quickly. Remove it when you move on to production runs.

## 3. Submit the job

```bash
sbatch first_job.sh
```

SLURM will confirm with a job ID:

```console
Submitted batch job 12345
```

Note the number — you will use it to find the output file.

## 4. Monitor the job

Check the status of your job with `jobinfo`:

```bash
jobinfo -u $USER
```

You will see something like this while the job is pending:

```
------------------------ Running jobs ------------------------
(none)

------------------------ Waiting jobs ------------------------
  JOBID PARTITION       NAME     USER  ST       TIME  NODES NODELIST(REASON)
  12345      lu48 my_first_  yourid  PD       0:00      1 (None)
```

And like this while it is running:

```
------------------------ Running jobs ------------------------
  JOBID PARTITION       NAME     USER  ST       TIME  NODES NODELIST(REASON)
  12345      lu48 my_first_  yourid   R       0:02      1 cn001
```

Once your job no longer appears in the list, it has finished. The key status codes are:

| Code | Meaning |
|---|---|
| `PD` | Pending — waiting for resources to become available |
| `R` | Running |
| `CG` | Completing — cleaning up after the job |

## 5. Read the output

SLURM writes all output to a file named `slurm-<jobid>.out` in the directory where you ran `sbatch`. For job 12345:

```bash
cat slurm-12345.out
```

You should see:

```
Job started at: Mon Mar 30 10:42:01 CET 2026
Running on node: cn001
Hello from COSMOS!
Job finished at: Mon Mar 30 10:42:01 CET 2026
```

The node name (`cn001`) confirms the job ran on a compute node, not the login node.

!!! note "If the output file is empty or missing"
    Wait a few seconds and try again — SLURM may not have flushed the file yet. If the file contains an error message, see [When your job fails](../manual/submitting_jobs/manual_job_failures.md) for a guide to diagnosing common problems.

## 6. Using software in your job

Most research workflows require specific software. COSMOS provides applications through a module system — if you have not used it yet, read [Using software modules](using_modules.md) first.

The key rule: modules loaded in your terminal are **not** available on the compute node. Load them explicitly in your job script:

```bash
#!/bin/bash
#SBATCH -J my_python_job
#SBATCH -t 00:05:00
#SBATCH --qos=test

module purge
module load GCCcore/13.2.0
module load Python/3.11.5-GCCcore-13.2.0

python my_script.py
```

## Where to go next

Your next steps depend on what you want to run:

- **Specify more resources** (cores, memory, GPU) — [Specifying job requirements](../manual/submitting_jobs/manual_specifying_requirements.md)
- **Browse ready-made job script templates** — [Example job scripts](../manual/example_job_scripts/manual_example_basic_serial.md)
- **Understand the module system** — [Using installed software](../manual/manual_modules.md)
- **Run interactive work** — [Interactive node access](../manual/manual_interactive.md)

---

**Author:**
(LUNARC)

**Last Updated:**
2026-03-30
