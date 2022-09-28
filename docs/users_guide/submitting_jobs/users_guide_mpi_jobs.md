# Running MPI jobs

## Launching MPI jobs in OpenMPI

To execute message passing parallel jobs these should be built against one of the MPI libraries provided by the support team as a module. To execute an MPI job for an executable build with an OpenMPI library, your job script should do the following:

 * Load the compiler module you have been using
 * Load the OpenMPI module relevant to the compiler you are using
 * Start the program with **mpirun**
 * Using the correct binding can help code performance. 
     *   When using 20 tasks per node on Aurora, we recommend using the **-bind-to core** option of mpirun 
     *   When using fewer than 16 tasks we recommend experimenting with whether not using binding helps or hinders performance.

## Launching MPI jobs compiled with the Intel MPI library

When the Intel MPI library was used to build your executable, your jobsscript should do as follows:

 * Load the compiler module you have been using
 * Load the Intel MPI module relevant to your compiler
 * Start your program with **srun**

When using LUNARC provided software, and you are loading an
**impi** module to see your package, you need to use **srun** to start MPI
executables of this package.

!!! note
     
     Task binding for the Intel MPI library is still under investigation