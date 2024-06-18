To run a shared memory code using OpenMP on COSMOS, you specify the number of cores you require using **--ntasks-per-node** option of **sbatch**. In this case you have to request placement on a single node with the **-N 1** option. In this example, we call the executable “processor_omp” to emphasis that this need to be compiled with OpenMP support. Unless you are doing something special, you are not required to specify the environment variable **OMP_NUM_THREADS**. The example script uses the techniques described for the [*basic run script*](#id.oyajyndi4e55) to engage the node local disk.

```bash
#!/bin/bash
#
# Specify the number of threads - request all on 1 node
#SBATCH -N 1
#SBATCH --ntasks-per-node=48
#
# job time, change for what your job requires
#SBATCH -t 00:10:00
#
# job name
#SBATCH -J data_process
#
# filenames stdout and stderr - customise, include %j
#SBATCH -o process_omp_%j.out
#SBATCH -e process_omp_%j.err

# write this script to stdout-file - useful for scripting errors
cat $0

# run the program
# customise for your program name and add arguments if required
./processor_omp
```

This script allows using 5300 MB of main memory per requested core. Then asking for 20 cores on COSMOS you can use all the memory available to users on the node.

The modification required to utilise the node local discs is the same as required [for serial](#basicrun-script-for-io-intensive-jobs) jobs](#basicrun-script-for-io-intensive-jobs).

## Thread binding for OpenMP codes

The COSMOS nodes deploy a cache-coherent non-uniform-memory access architecture (cc-numa). Many scientific simulation codes gain significant performance benefits on a cc-numa architecture when the user binds the threads to the physical cores of the hardware. This inhibits thread migration and improves memory locality. In the latest OpenMP standards thread binding has been standardised. In addition, many compilers offer their binding syntax.

### Thread binding with the GNU compilers

By default, the GNU compiler suite (gcc/gfortran) does not bind threads to cores. To engage thread binding, you need to set the environment variable **GOMP_CPU_AFFINITY** and provide this with a binding list. When setting

    export GOMP_CPU_AFFINITY=”0-47”

in your submission script, before starting your OpenMP application, this will bind the threads to the 20 cores in the node. The above will bind thread 0 to core 0, thread 1 to core 1 and so on.


!!! info

    If you want to utilise only 24 cores from a node and ask for exclusive node access (#SBATCH --exclusive), it might be a good idea to place threads on every second core only. This will give you more memory bandwidth and make sure you are utilising all FPUs of the Interlagos architecture. This can be achieved by setting:

        export GOMP_CPU_AFFINITY=”0-23:2”

    or

        export GOMP_CPU_AFFINITY=”0 2 4 6 8 10 12 14 16 18 20 22”

    It depends on the details of your application, and whether or not this helps performance. Also note, when asking for exclusive access to a node, you will be charged for the full node, whether or not you use all cores.

!!! important

    If you set **GOMP_CPU_AFFINITY=0** this will bind all threads to core 0. You will see extremely poor performance in this case.


### Thread binding with the Intel compiler

Obviously all versions of the Intel compiler support thread binding on the Intel processors deployed on Aurora.

For version 13.1 of the Intel-compiler thread is controlled by setting the environment variable **KMP_AFFINITY**. The value

    export KMP_AFFINITY=granularity=fine,compact

might be a good starting point for your experimentation.

---

**Author:**
(LUNARC)

**Last Updated:**
2023-01-31

