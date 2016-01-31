Useful hints and short information on issues that may vary between the different systems

# Installed softwared software

To see the installed software available through the modules system, issue the command

    module avail

To see the currently loaded modules

    module list

To load a module

    module add <module_name>

To unload a module

    module del <module_name>

# Resource allocation

## Number of cores

The number of cores for a job is specified in the batch script in the format

    #SBATCH -N <number_of_nodes>
    #SBATCH --tasks-per-node=<number_of_cores_per_node>

Alarik has 16 cores per node. On this system, 64 cores would be allocated through

    # 64 cores on Alarik
    #SBATCH -N 4 #SBACTH --tasks-per-node=16

## Memory per core

The amount of memory per core is specified in the format

    #SBATCH --mem-per-cpu=<amount_of_memory_per_core_in_MB>

Alarik has nodes with 32 GB and 64 GB memory. The default allocation per core is therefore 2000 MB to match the smaller memory. To fully utilise the memory on the 64 GB nodes, the nodes have to requested specifically with **-C mem64GB** and the memory per core should be set to 4000 MB .

    # Twice the default amount of memory per core on Alarik nodes with 64 GB memory 
    #SBATCH -C mem64GB #SBATCH --mem-per-cpu=4000

# File systems

## Home directory

Currently all Lunarc systems have a home directory that is different for each system, i.e., the login directory for user xxxx is

    /home/xxxx

This directory can be referenced as **$HOME**.

As a rule, the home directory should not be used for job submission. It is intended for storing important files, such as the source code of user programs, and, of course, environment files, such as .bashrc.

## Global working directory

For job submission, there is a centre file system common to all Lunarc systems:

    /lunarc/nobackup/users/xxxx

Here the xxxx has to be replaced with your userid.

## Local working directory

When a job is running, it has access to a temporary directory on the local disk of each allocated node. The directory can be referenced as **$SNIC_TMP** (or **$TMPDIR**). It will be deleted when the job finishes.

If a job is terminated prematurely, for example, if it exceeds the requested walltime, the files on the local disk will be lost. Files that would still be useful can be listed in a special file **$SNIC_TMP/slurm_save_files**. Filenames are assumed to be relative to **$SNIC_TMP** and should be separated by spaces or listed on separate lines. These files will be copied to the submission directory regardless whether the job ends as planned or is deleted, unless there is a problem with the disk or node itself.

## Quotas

To limit the disk usage, quotas are set for each user and filesystem. The status can be seen at login. A quota report can also be obtained by issuing the command

    snicquota

The quota can be increased on request.

# Test queues

On Alarik, it is possible to request extra high priority to run short tests (maximum 1h) using at most 2 nodes using

    #SBATCH --qos=test

Floating reservations are used to free two nodes every second hour between 8.00 and 20.00 to reduce the queue time for test jobs, which means that a shorter walltime increases likelihood of an earlier start. Only two such test jobs are allowed to run at the same time.

On Erik there is one two-GPU node reserved for tests (maximum 1 h) in a partition of its own, which is specified with

    #SBATCH -p test

It is not allowed to submit long series of jobs to a test queue. 