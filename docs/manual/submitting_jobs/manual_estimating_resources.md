# Estimating job resources

Requesting accurate resources for a job matters in two directions: request too little and the job is killed before it finishes; request too much and the job sits longer in the queue and your allocation is depleted faster. This page describes a practical workflow for arriving at good estimates before committing to a production run.

## The workflow

1. **Run a small test** using a reduced input or a short simulation segment with `--qos=test`
2. **Measure actual usage** with `sacct` once the test completes
3. **Scale up** your estimates based on the test results
4. **Add a margin** to account for variability, then submit the full job

---

## Estimating walltime

### Step 1 — time a small test run

Wrap your program in `time` to measure how long it takes on a representative small input:

```bash
#!/bin/bash
#SBATCH -t 00:30:00
#SBATCH --qos=test

time ./myprogram small_input.dat
```

The `time` output appears in your job's output file:

```
real    3m42.4s
user    3m41.8s
sys     0m0.6s
```

`real` is the walltime — the elapsed clock time.

### Step 2 — scale to your full input

Walltime often scales roughly linearly with input size. If your test input is 1/10th the size of the full dataset and took 3m 42s, the full run will take approximately 37 minutes.

For iterative methods (simulations, optimisers), the number of steps is usually the scaling factor rather than input size.

### Step 3 — check elapsed time with sacct

After the test job, confirm the actual elapsed time:

```bash
sacct -j <jobid> --format=JobID,Elapsed,State
```

### Step 4 — add a margin

Add 20–30% to your scaled estimate. This absorbs variability from filesystem load, memory pressure, and other cluster conditions. Avoid adding excessive margin — jobs requesting less walltime queue faster because SLURM can fit them into gaps in the schedule.

```bash
# Test took ~37 minutes scaled; add 30% margin → request 50 minutes
#SBATCH -t 00:50:00
```

---

## Estimating memory

### Default allocation

The default memory allocation on COSMOS is **5300 MB per core**. If your job does not request more, this is what it gets. For many jobs this is sufficient.

### Step 1 — run a test and check peak memory

After any job completes, `sacct` reports the peak memory used (`MaxRSS`):

```bash
sacct -j <jobid> --format=JobID,MaxRSS,ReqMem,Elapsed
```

Example output:

```
       JobID     MaxRSS     ReqMem    Elapsed
------------ ---------- ---------- ----------
       12345            5300Mc     00:03:44
 12345.batch    3276800K  5300Mc   00:03:44
```

`MaxRSS` is reported in kilobytes on the `.batch` step line. Convert to MB:

```
3 276 800 KB ÷ 1024 = 3200 MB total
```

### Step 2 — calculate per-core memory

Divide total peak memory by the number of cores your job used, then add a 20% margin:

```
3200 MB total ÷ 1 core × 1.2 = 3840 MB/core  →  round up to 4000 MB/core
```

If the result is below 5300 MB/core the default is sufficient and no `--mem-per-cpu` directive is needed. If it exceeds 5300 MB/core, request it explicitly:

```bash
#SBATCH --mem-per-cpu=8000
```

!!! note "Memory and billing"
    Requesting more than 5300 MB per core leaves some cores idle on the node. Your allocation is charged for those idle cores too. Only request what you actually need.

---

## Choosing a core count

### Serial jobs

If your program is single-threaded, request 1 core. Requesting more does not speed it up and wastes allocation.

### OpenMP (shared memory)

OpenMP jobs scale within a single node. Performance typically improves up to 8–16 cores for most codes, with diminishing returns beyond that. Test with 4, 8, and 16 cores before committing to a full 48-core request:

```bash
#SBATCH -N 1
#SBATCH --ntasks-per-node=8
```

### MPI (distributed memory)

MPI jobs can span multiple nodes. Before a large run, test scaling efficiency by comparing runtimes at 1, 2, and 4 nodes. If doubling the cores does not roughly halve the runtime, the job does not scale well and requesting more nodes wastes allocation.

---

## Putting it together

A typical resource estimation workflow:

```bash
#!/bin/bash
# Step 1: small test run
#SBATCH -t 00:30:00
#SBATCH -J resource_test
#SBATCH --qos=test
#SBATCH -N 1
#SBATCH --ntasks-per-node=8

module purge
module load <your-modules>

time ./myprogram small_input.dat
```

After the test completes:

```bash
# Check elapsed time and peak memory
sacct -j <jobid> --format=JobID,Elapsed,MaxRSS,State
```

Use the results to set your production job's walltime and memory, then remove `--qos=test` before submitting.

---

**Author:**
(LUNARC)

**Last Updated:**
2026-03-30
