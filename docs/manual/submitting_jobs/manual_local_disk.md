# Using local disk to improve I/O performance

Every compute node on COSMOS has a fast local disk. Because it is local to the node, it offers much higher bandwidth than the shared filesystems (`$HOME` or `/lunarc/nobackup/projects`). For jobs that read or write large amounts of data repeatedly, copying files to the local disk before execution — and copying results back afterwards — can significantly reduce runtime.

## How it works

1. At the start of your job script, copy input data from your submission directory to `$SNIC_TMP`
2. Run your program, reading from and writing to `$SNIC_TMP`
3. At the end of the script, copy results from `$SNIC_TMP` back to your submission directory

!!! warning "Local disk is deleted when the job ends"
    Everything in `$SNIC_TMP` is permanently deleted when the job finishes or is terminated. Always copy results back to `$HOME` or project storage before the job ends.

## Environment variables

| Variable | Description |
|---|---|
| `$SNIC_TMP` | Local scratch disk on the allocated node — use this for input and output during the job |
| `$TMPDIR` | Points to the same local disk. Many applications use this variable automatically for temporary files — if your application follows that convention, no extra configuration is needed |
| `$SLURM_SUBMIT_DIR` | The directory from which you ran `sbatch` — typically your `$HOME` or a subdirectory of it |

## Example script

```bash
#!/bin/bash
#SBATCH -t 02:00:00
#SBATCH -J io_intensive_job

# Copy input data to local scratch
cp $SLURM_SUBMIT_DIR/input.dat $SNIC_TMP/
cp $SLURM_SUBMIT_DIR/my_program $SNIC_TMP/

# Move to local scratch and run
cd $SNIC_TMP
./my_program input.dat output.dat

# Copy results back to submission directory
cp output.dat $SLURM_SUBMIT_DIR/
```

See the [example job scripts](../example_job_scripts/manual_example_mpi_local_discs.md) section for MPI examples using local disk.

## Recovering files if a job is terminated early

If a job is killed before it finishes (for example due to walltime being exceeded), files in `$SNIC_TMP` are normally lost. To protect specific files, list them in `$SNIC_TMP/slurm_save_files`:

```bash
# Inside your job script, before the program runs:
echo "output.dat partial_results/" > $SNIC_TMP/slurm_save_files
```

Paths in `slurm_save_files` should be relative to `$SNIC_TMP`, separated by spaces or newlines. Wildcards are allowed. If the job is terminated, SLURM will attempt to copy the listed files to `$SLURM_SUBMIT_DIR`.

!!! note
    The `slurm_save_files` mechanism is unique to LUNARC and is not available on other SLURM systems.

---

**Author:**
(LUNARC)

**Last Updated:**
2026-03-30
