# MPI job using the node local discs

In many cases, you can use the node local discs also for your MPI jobs.  This can be beneficial if your job is very demanding concerning disc I/O and spends a lot of time doing it.

You need to transfer your executable onto the node local discs of all nodes.  Depending on the architecture of your application it is often sufficient to have the input files on the head node only.  Many MPI applications write result files only from the head node.  In these cases, the below script should work.

```bash
#!/bin/bash
# requesting the number of cores needed on exclusive nodes
#SBATCH -N 4
#SBATCH --tasks-per-node=20
#
# job time, change for what your job requires
#SBATCH -t 0:30:0
#
# job name
#SBATCH -J simula_n80
#
# filenames stdout and stderr - customise, include %j
#SBATCH -o simula_n80_%j.out
#SBATCH -e simula_n80_%j.out

# write this script to stdout-file - useful for scripting errors
cat $0

# Example assumes we need the intel runtime and OpenMPI library
# customise for the libraries your executable needs
module load iomkl/2015.03


# Copying the executable onto the local disks of the nodes
srun -n $SLURM_NNODES -N $SLURM_NNODES cp -p simula_mpi $SNIC_TMP

# Copy the input file onto the headnode - if your MPI program
# reads from all tasks, you need to do the above srun construct
# again
cp -p input.dat $SNIC_TMP

# change to local disk and start the mpi executable
cd $SNIC_TMP
mpirun -bind-to core simula_mpi

# Copy result files back - example assumes only task zero writes
# if in your application result files are written on all nodes
# you need to initiate a copy on each node via srun

cp -p result.dat $SLURM_SUBMIT_DIR
```

Again, applications using the Intel MPI library need to start with **srun**, see above.

## Modifications required for file I/O on all nodes

As discussed in the comments of the sample script, the script assumes that only MPI-task 0 on the head node reads the input file and writes to the output file. If for your MPI application every MPI task reads the input file(s), replace the line

    cp -p input.dat $SNIC_TMP

with

    srun -n $SLURM_NNODES -N $SLURM_NNODES cp -p input.dat $SNIC_TMP

and the file gets copied onto the local disk of each node. Matters are slightly more complex if your output is written from all tasks. We assume the output files can be wild-carded as result_*.dat. Copying these files back to the submission directory can be achieved creating a script, which is placed on all nodes and subsequently executed on all nodes. The following addition to the submission script will create the script and place it on all your nodes

```bash
cat <<EOF > copyfile.sh
#!/bin/sh
cp -p result*.dat $SLURM_SUBMIT_DIR
EOF

chmod u+x copyfile.sh
srun -n $SLURM_NNODES -N $SLURM_NNODES cp copyfile.sh $SNIC_TMP
```

This needs inserting into the script before the **cd $SNIC_TMP** statement. Once this is in place you can copy your result files by replacing the line

    cp -p result.dat $SLURM_SUBMIT_DIR

with the line

    srun -n $SLURM_NNODES -N $SLURM_NNODES copyfile.sh
