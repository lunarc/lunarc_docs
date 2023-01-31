Most MPI jobs achieve the best cost efficiency when deploying 20 tasks per node, that is one task per core.  The sample uses core binding as offered by the OpenMPI library.

The resource request is straightforward in this case. Ask for several nodes and place 20 tasks on each node.  The product should match the number of tasks you want to run. We recommend using the **--exclusive** option.  The following is an example submission script to run the MPI application simula_mpi with 80 tasks on 4 nodes. Notice you do not need to specify the node count.

```bash
#!/bin/bash
# requesting the number of cores needed on exclusive nodes
#SBATCH -N 4
#SBATCH --ntasks-per-node=20
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

# start the mpi executable - use mpirun in case of OpenMPI
mpirun -bind-to core simula_mpi
```

In case the executable was compiled with the **Intel MPI library** (using a module named `impi`) the last line should read

```bash
# start the mpi executable 
srun simula_mpi
```

---

**Author:**
(LUNARC)

**Last Updated:**
2023-01-31

