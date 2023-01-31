A cluster with multicore nodes such as Alarik is a natural environment to execute parallel codes deploying both MPI and OpenMP threads. When running such applications the optimal number of MPI-tasks and OpenMP threads to place on a node can depend highly on the details of the application. In particular, for applications that make many references to main memory and the programmer has not implemented a proper “first touch data allocation” it is typically **best to have 2 or 4 threads** per MPI task on an Alarik node. Together with a proper binding of your MPI tasks to the “numa-islands”, this will ensure memory locality for your code. For the below syntax you have to use **version 1.8.3 or newer** of the OpenMPI library.

In the following, we give a simple example script to run a MPI-OpenMP hybrid named simul_hyb on 2 nodes using 10 tasks and 4 threads per task. The tasks and their threads will be bound to the *numa-islands*, minimising cc-numa effects.

```bash
#!/bin/bash
# requesting number of nodes (-N option)
# number of mpi-tasks per node
# and number of threads per task exclusive nodes
#SBATCH -N 2
#SBATCH --ntasks-per-node=5
#SBATCH --cpus-per-task=4
# time required
#SBATCH -t 01:00:00
#SBATCH -J hybrid_run
# filenames stdout and stderr - customise, include %j
#SBATCH -o simula_N2t4c4_%j.out
#SBATCH -e simula_N2t4c4_%j.out

cat $0

# Example assumes we need the intel runtime and OpenMPI library
# customise for the libraries your executable needs
module add intel/15.0
module add openmpi/1.8.3/intel/15.0

# Copying the executable onto the local disks of the nodes
srun -n $SLURM_NNODES -N $SLURM_NNODES cp -p simul_hyb $SNIC_TMP

# Copy the input file onto the headnode - if your MPI program
# reads from all tasks, you need to do the above srun construct
# again
cp -p input.dat $SNIC_TMP
cd $SNIC_TMP

# setting number of OpenMP threads and ask for thread binding
export OMP_NUM_THREADS=$SLURM_CPUS_PER_TASK
export OMP_PROC_BIND=true

mpiexec --map-by ppr:$SLURM_NTASKS_PER_NODE:node:PE=$SLURM_CPUS_PER_TASK simul_hyb

# Copy result files back - example assumes only task zero writes
# if in your application result files are written on all nodes
# you need to initiate a copy on each node via srun
cp -p result.dat $SLURM_SUBMIT_DIR
```

The example assumes that MPI task 0 is the only task reading and writing input files. If your application reads and writes data on all nodes, you need to study the [*modifications*](#id.hpgejkt8dzry) described in the MPI section.

As discussed, the above binds the tasks and their threads to the numa-islands of the Alarik architecture. Alariks numa-islands have four cores, therefore the script is best used with 2 or four threads per MPI task. This results in one or two MPI tasks per numa islands.

---

**Author:**
(LUNARC)

**Last Updated:**
2023-01-31

