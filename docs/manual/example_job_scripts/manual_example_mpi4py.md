Python scripts that have been parallelised utiling the MPI4PY need ob started slightly differently from standard MPI-executables which have been compiled from C or Fortran sources.  Below we give an example for a submission script utilising 12 cores on a single COSMOS node.   If you need a different task count, you need to modify the header in line with what is shown in the above examples for standard MPI-executables.

In the example we run a Python script named **hello_mpi.py**.

```bash
#!/bin/bash
#SBATCH -N 1                   # number of nodes
#SBATCH --tasks-per-node=12    # number of processes per node
#SBATCH -t 00:05:00            # job-time – here 5 min

# job name
#SBATCH -J mpi4py_n12

# filenames stdout and stderr - customise, include %j
#SBATCH -o result_mpihello_n12_%j.out
#SBATCH -e result_mpihello_n12_%j.out


# write this script to stdout-file - useful for scripting errors
cat $0

# loading a Python module, NumPy/SciPy and MPI4PY - modify for your needs
ml purge
ml foss/2022b
ml Python/3.10.8
ml SciPy-bundle/2023.02
ml mpi4py/3.1.4

# run your script
mpirun --bind-to core python3 hello_mpi.py
```

---

**Author:**
(LUNARC)

**Last Updated:**
2024-09-11
