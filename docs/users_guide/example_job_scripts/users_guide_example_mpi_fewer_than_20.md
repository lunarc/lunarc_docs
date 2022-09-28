If you want to use fewer than 20 tasks per node to e.g. give more resources to the individual task, you can use the **-N** and **--task-per-node** options of **sbatch**. We recommend not to use the -n option in this case. This example is for 4 nodes with 10 tasks each, a total of 40 tasks. In our experience, in this case, and when using **--exclusive** it is typically advantageous to not use binding. Though we encourage experimenting with your own application.

!!! note 

    Despite the example using only 40 cores, it will be block 80 cores, since it uses all the memory of the 4 nodes. Your allocation will be charged for all 80 cores.

```bash
#!/bin/bash
# requesting the number of nodes and cores needed, exclusive nodes
#SBATCH -N 4
#SBATCH --tasks-per-node=10
#SBATCH --mem-per-cpu=6200
#
# job time, change for what your job requires
#SBATCH -t 0:30:0
#
# job name
#SBATCH -J simula_n40
#
# filenames stdout and stderr - customise, include %j
#SBATCH -o simula_n40_%j.out
#SBATCH -e simula_n40_%j.out

# write this script to stdout-file - useful for scripting errors
cat $0

# Example assumes we need the intel runtime and OpenMPI library
# customise for the libraries your executable needs
module add iomkl/2015.03

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

