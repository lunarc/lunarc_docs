# Quick reference

Useful hints and short information on COSMOS resources and the SLURM batch system.

## Installed software

To list all software available through the module system:

```bash
module spider
```

To check prerequisites for a specific package:

```bash
module spider <module_name/version>
```

To see currently loaded modules:

```bash
module list
```

To load a module:

```bash
module add <module_name>
```

To unload a module:

```bash
module del <module_name>
```

## Resource allocation

### Number of cores

Specify nodes and tasks per node in your job script:

```bash
#SBATCH -N <number_of_nodes>
#SBATCH --ntasks-per-node=<number_of_cores_per_node>
```

COSMOS has 48 cores per node. To allocate 192 cores across 4 nodes:

```bash
#SBATCH -N 4
#SBATCH --ntasks-per-node=48
```

### Memory per core

```bash
#SBATCH --mem-per-cpu=<amount_in_MB>
```

COSMOS nodes have 256 GB of memory. The default allocation is **5300 MB per core**, leaving headroom for the operating system. Requesting more than 5300 MB per core will leave some cores idle — your account is charged for those idle cores too.

## File systems

### Home directory

Your home directory on COSMOS is:

```bash
/home/<username>
```

Also accessible as `$HOME`.

### Global working directory

Use your home directory for job submission. The old `/lunarc/nobackup/users` directory has been decommissioned.

### Local working directory

Each compute node has a local scratch disk available during a job at `$SNIC_TMP` (also `$TMPDIR`). It is deleted when the job ends.

If a job is terminated early (e.g. walltime exceeded), files on the local disk are lost. To preserve specific files, list them in `$SNIC_TMP/slurm_save_files` — paths should be relative to `$SNIC_TMP`, separated by spaces or newlines. These files will be copied to the submission directory if at all possible.

### Quotas

Disk quotas are set per user and filesystem. Your quota status is shown at login, or run:

```bash
snicquota
```

Quota increases are available on request.

## Test queue

On COSMOS, short tests (maximum 1 hour, at most 2 nodes) can be given elevated priority:

```bash
#SBATCH --qos=test
```

Do not submit long series of jobs to the test queue.

---

**Author:**
(LUNARC)

**Last Updated:**
2024-06-18
