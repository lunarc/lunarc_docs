Most MPI jobs which give good performance on a larger numbers of tasks 
achieve the best cost efficiency when deploying 48 tasks per node.  On COSMOS 48 tasks relates to one task per core.  The sample uses core binding as offered by the OpenMPI library.

The resource request is straightforward in this case. Ask one or more nodes and place 48 taisks on each node.  The product should match the number of tasks you want to run. The following is an example submission script to run the MPI application simula_mpi with 192 tasks on 4 nodes. Notice you do not need to specify the total core count.

```bash
#!/bin/bash
# requesting the number of cores needed on exclusive nodes
#SBATCH -N 4
#SBATCH --ntasks-per-node=48
#
# job time, change for what your job requires
#SBATCH -t 0:30:0
#
# job name
#SBATCH -J simula_n192
#
# filenames stdout and stderr - customise, include %j
#SBATCH -o simula_n80_%j.out
#SBATCH -e simula_n80_%j.out

# write this script to stdout-file - useful for scripting errors
cat $0

# Example assumes we need the intel runtime and OpenMPI library
# customise for the libraries your executable needs
module load foss/2023b

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
2024-09-10

