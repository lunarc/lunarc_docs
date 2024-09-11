Standard COSMOS CPU nodes offer 48 cores.  While it is typically recommended to have as many MPI tasks per node as there are cores, there are situations when using 48 tasks per node is not advisable or possible

* The program is not capable of utilising that many tasks or problem is to small to be distributed on that many tasks
* The tasks needs more than than the default 5300 MB of main memory

## Sample script to run fewer than 48 tasks

 There are many examples for program executions which do not improve their time to solution when utilising 48 MPI tasks instead of a lower number of tasks, in some cases it might actually take longer on 48 tasks than on a smaller task count.  In many cases, when you need many similar computations, experimenting with the task count is worthwhile.  

 When utilising fewer than 48 tasks in total, we recommend having all tasks on a single node, such that the MPI library can utiilise the memory sub-system to exchange data instead of the infiniband network.

!!! info

    If you do not use the **--exclusive** option of slurm your allocation will be charged for the number of cores requested, which is 12 in the example below.   Remaining cores, not assigned to your job may be assigned and charged to other jobs or left idle, depending on the job-queue.

```bash
#!/bin/bash

# requesting a single node and the cores needed, non-exclusive node
#SBATCH -N 1
#
# task count, change to meet your job requirements
#SBATCH --ntasks-per-node=12
#
# job time, change to meet your job requires
#SBATCH -t 0:30:0
#
# job name
#SBATCH -J simula_n12
#
# filenames stdout and stderr - customise, include %j
#SBATCH -o simula_n12_%j.out
#SBATCH -e simula_n12_%j.out

# write this script to stdout-file - useful for scripting errors
cat $0

# Example assumes we need the gcc runtime and OpenMPI library
# customise for the libraries your executable needs
module load foss/2023b

# start your program with core binding
mpirun --bind-to core simula_mpi
```

## Utilising fewer than 48 tasks per node to increase the resources per task

If you want to use fewer than 48 tasks per node to e.g. give more resources to the individual task, you can use the **-N** and **--task-per-node** options of **sbatch**. We recommend not to use the -n option in this case. This example is for 4 nodes with 24 tasks each, a total of 96 tasks. In our experience, in this case, and when using **--exclusive** it is typically advantageous to not use binding. Though we encourage experimenting with your own application.  The below example shows how to utilise the node local disks, which could be removed if your I/O-demands are modest.

!!! info 

    Despite the example using only 96 cores, it will block 192 cores, since it uses all the memory of the 4 nodes. Your allocation will be charged for all 192 cores.

```bash
#!/bin/bash
# requesting the number of nodes and cores needed, exclusive nodes
#SBATCH -N 4
#SBATCH --ntasks-per-node=24
#SBATCH --mem-per-cpu=10600
#
# job time, change for what your job requires
#SBATCH -t 0:30:0
#
# job name
#SBATCH -J simula_n96
#
# filenames stdout and stderr - customise, include %j
#SBATCH -o simula_n96_%j.out
#SBATCH -e simula_n96_%j.out

# write this script to stdout-file - useful for scripting errors
cat $0

# Example assumes we need the gcc runtime and OpenMPI library
# customise for the libraries your executable needs
module load foss/2023b

# Copying the executable onto the local disks of the nodes
srun -n $SLURM_NNODES -N $SLURM_NNODES cp -p simula_mpi $SNIC_TMP

# Copy the input file onto the headnode - if your MPI program
# reads from all tasks, you need to do the above srun construct
# again
cp -p input.dat $SNIC_TMP

# change to local disk and start the mpi executable
cd $SNIC_TMP
mpirun simula_mpi

# Copy result files back - example assumes only task zero writes
# if in your application result files are written on all nodes
# you need to initiate a copy on each node via srun

cp -p result.dat $SLURM_SUBMIT_DIR
```

---

**Author:**
(LUNARC)

**Last Updated:**
2024-09-11

