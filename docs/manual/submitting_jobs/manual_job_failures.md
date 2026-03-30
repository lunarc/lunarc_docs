# When your job fails

Jobs fail for many reasons. This page walks through a systematic approach to diagnosing what went wrong, covering the most common failure modes new users encounter.

## Step 1: Read the output file

The first place to look is always the output file SLURM wrote for your job. By default this is named `slurm-<jobid>.out` in the directory where you ran `sbatch`:

```bash
cat slurm-12345.out
```

This file contains everything your job printed to standard output and standard error. Error messages, Python tracebacks, and missing file warnings all appear here.

!!! tip "Separate output and error streams"
    When an error message is buried among pages of normal output, it can be hard to spot. Add these lines to your job script to write stdout and stderr to separate files:

    ```bash
    #SBATCH -o job_%j.out
    #SBATCH -e job_%j.err
    ```

    After the job runs, check `job_12345.err` first — if it is empty, the job script itself ran without errors.

## Step 2: Check what SLURM recorded

Once a job has finished, `sacct` shows you what SLURM recorded about it — including its final state, exit code, how long it ran, and how much memory it used:

```bash
sacct -j 12345 --format=JobID,State,ExitCode,MaxRSS,Elapsed
```

Example output:

```
       JobID      State ExitCode     MaxRSS    Elapsed
------------ ---------- -------- ---------- ----------
       12345     FAILED      1:0             00:02:13
 12345.batch     FAILED      1:0    512000K  00:02:13
```

The key columns are:

| Column | What it tells you |
|---|---|
| `State` | How the job ended — see the table below |
| `ExitCode` | Two numbers: `exitcode:signal`. `0:0` means clean exit. |
| `MaxRSS` | Peak memory used — compare to your `--mem-per-cpu` request |
| `Elapsed` | How long the job actually ran |

### Job states

| State | Meaning |
|---|---|
| `COMPLETED` | Job finished normally (exit code `0:0`) |
| `FAILED` | Job exited with a non-zero exit code |
| `TIMEOUT` | Job was terminated because it exceeded the requested walltime |
| `OUT_OF_MEMORY` | Job was killed because it exceeded its memory allocation |
| `CANCELLED` | Job was cancelled by the user or an administrator |

## Common failure patterns

### Job timed out (`TIMEOUT`)

**Symptom:** `sacct` shows state `TIMEOUT`. The output file may be cut off mid-run.

**Cause:** The job needed more time than requested with `-t`.

**Fix:** Estimate how long your job takes by running a small test case first:

```bash
#SBATCH -t 00:30:00
#SBATCH --qos=test
```

Time a representative small input, then scale up your walltime estimate. Add 20–30% margin to account for variability, but avoid requesting far more than you need — shorter walltime requests queue faster.

---

### Job ran out of memory (`OUT_OF_MEMORY`)

**Symptom:** `sacct` shows state `OUT_OF_MEMORY`, or the output file ends abruptly with a message like `Killed`.

**Cause:** The job used more memory than the default allocation (5300 MB per core).

**Fix:** Check how much memory the job actually used with `sacct`:

```bash
sacct -j 12345 --format=JobID,MaxRSS,ReqMem
```

`MaxRSS` is the peak memory in kilobytes. Convert to MB and request slightly more than that:

```bash
#SBATCH --mem-per-cpu=10600
```

---

### Command not found or module errors

**Symptom:** Output file contains lines like:

```
bash: python: command not found
```
or:
```
module: command not found
```

**Cause:** The software was not loaded inside the job script. Modules loaded in your terminal session are not automatically available on the compute node.

**Fix:** Add explicit `module load` lines to your job script, after a `module purge`:

```bash
module purge
module load Python/3.11.5-GCCcore-13.2.0
```

Do not rely on modules you have loaded interactively — they will not be present when the job runs.

---

### File or directory not found

**Symptom:** Output file contains:

```
FileNotFoundError: [Errno 2] No such file or directory: 'input.dat'
```

**Cause:** The job script uses a relative path that is not valid from the compute node's working directory, or the file was not transferred to COSMOS before submission.

**Fix:** Use absolute paths, or build paths using the `$SLURM_SUBMIT_DIR` variable, which always points to the directory where you ran `sbatch`:

```bash
python my_script.py --input $SLURM_SUBMIT_DIR/input.dat
```

---

### Job exits with code 1 but no obvious error

**Symptom:** `sacct` shows `ExitCode 1:0` and `State FAILED`, but the output file looks normal.

**Cause:** An application or script returned a non-zero exit code without printing a helpful message, or the error was written to stderr while stdout looks fine.

**Fix:** Make sure you are checking both the `.out` and `.err` files (or combine them if you have not already). You can also add `set -e` at the top of your job script to make bash exit immediately on any error and print which line caused it:

```bash
#!/bin/bash
#SBATCH ...

set -e   # exit on first error

module purge
module load ...
python my_script.py
```

---

## Test before scaling up

The most effective way to avoid wasted queue time is to validate your job at small scale before committing to a large run:

1. **Use a small input** — run with a reduced dataset that finishes in minutes
2. **Use the test queue** — add `#SBATCH --qos=test` for elevated priority (max 1 hour, max 2 nodes)
3. **Check resource usage** — once the small run completes, use `sacct` to see actual memory and time usage, then scale your requests accordingly before submitting the full job

## Getting further help

If you cannot identify the cause of a failure, contact LUNARC support via [SUPR](https://supr.naiss.se/support/?centre_resource=c5). Include:

- The job ID
- The job script
- The contents of the output (and error) file

---

**Author:**
(LUNARC)

**Last Updated:**
2026-03-30
