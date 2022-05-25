On a modern HPC system efficient management of the compute resources is
absolutely crucial for the system to perform. LUNARC deploys 
[SLURM](http://slurm.schedmd.com/) (**S**imple **L**inux **U**tility
for **R**esource **M**anagement) as resource manager. For your program
to be executed you have to describe to SLURM the resources required by
your program, the name of your program and the command line arguments
your program may require. SLURM also allows monitoring and manipulation
of the progress of your programs execution.

This document contains two key parts. The [*first
part*](#job-submission) describes in-depth the job submission system
and its options. The [*second part*](#example-job-scripts) gives example
scripts for the most common use cases. They hopefully serve as a good
starting point when creating submission scripts fitting your needs and
requirements.
