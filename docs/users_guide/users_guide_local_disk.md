On Aurora all nodes have a local disk. This disk offers
superior bandwidth when compared to accessing your home space or the
/lunarc/nobackup centre storage. In particular when files are read or
written repeatedly during execution it is advisable to copy the input
data onto the local disk prior to job execution and copy the result
files back to the submission directory once your program has finished.
During its execution, your program would then read and write to local
disk.

In case of Aurora, the submission directory typically resides
on the /lunarc/nobackup centre storage. All data left on the node local
disks **will be deleted** when your job has finished. You need to copy
everything of interest to a more permanent storage space such as
/lunarc/nobackup. If a job is terminated prematurely, for example, if it
exceeds the requested walltime, the files on the local disk (in
$SNIC_TMP) will be lost.

Files that would still be useful can be listed in a special file
**$SNIC_TMP/slurm_save_files**. Filenames are assumed to be relative
to $SNIC_TMP and should be separated by spaces or listed on separate
lines. Wildcards are allowed. These files will be copied from the local
disk where $SNIC_TMP/slurm_save_files exists to the submission
directory regardless whether the job ends as planned or is deleted,
unless there is a problem with the disk or node itself. Note that the
slurm_save_files feature is unique to LUNARC.

For the required UNIX scripting you should use the following environment
variables. Example scripts using this technique are provided in the
example section of this document. Contact the help desk if you have
specific requirements and require consultation.

| Variable | Addressed Volume |
|------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| SNIC_TMP | node local disk, copy your input data here and start your program from here |
| TMPDIR | node local disk, many applications use this environment variable to locate a disk volume for temporary scratch space.  If your application follows that convention nothing needs to be done.  |
| SLURM_SUBMIT_DIR | submission directory where you ran `sbatch` |
