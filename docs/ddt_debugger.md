
# Starting the DDT debugger on Alarik and Erik #

A basic description on how to start a debugging session with DDT on the Alarik and Erik system.  Job will be submitted through the Slurm batch system to the back-end nodes.

# About this document #

This document gives basic instruction on how to start a debugging session using the DDT debugger on the Alarik and Erik system at Lunarc.  This document is based on DDT version 5.0.1.  The current license allows for debug up to 88 processes.  These are shared between the users of all systems at Lunarc.  So please be considerate to your fellow Lunarc users.
    
DDT is a powerful debugger for serial and parallel programs.  The tool is developed and maintained by Allinea Software.  It is part of the Allineas Forge suite.  A number of parallel programming models are supported.  This includes MPI, OpenMP and a number of GPU languages.  This document is not a DDT userguide, we refer our users to the documentation available from the Allinea website, in particular their user guide.

# Getting started with DDT on Alarik and Erik # 

## Connecting to Alarik and Erik using the Lunarc HPC desktop ##

To use DDT on Alarik and Erik we recommend to connect to the system using the Lunarc HPC desktop. 
Starting DDT on Alarik and Erik

Before starting DDT, make sure you have the modules for your code loaded.  This includes the compilers and libraries, in particular the MPI library if your are debugging MPI code.  When compiling your application, you should add the -g option to the compiler flags.  This will enable DDT to display source lines while executing your program.  Please note that optimisation often breaks the link between an instruction and a source line.  Disabling optimisation is in many cases a good idea.

It is now time to start DDT, for this you need to load the relevant DDT module, e.g.:

    module load ddt

Now you can start DDT:

    ddt &

which should get you the following window:

Start window in DDT version 5.0.1

# Debugging MPI code on Alarik #

If you now select the "Run and Debug a Program" option you get to the following screen:

DDT_run_window_5.0.1 **PLEASE FIX***

In this window you can select your executable, tell DDT any command line arguments, select input file and working directories. Make sure the "MPI" box is ticked.  Inside the MPI box, you can select the number of processors needed - the examples shows 4 processes.  DDT will not allow you to ask for more processes than the number of licenses currently available.  We highly recommend to use as small a number of processors as possible to reduce the complexity of the debugging task.

Check the box "Submit to Queue".  The default is for a debugging session of up to 30 minutes.  You can change that by selecting the "Parameters" button associated with "Submit to Queue".  You get the following dialog:

DDT_run_window_5.0.1 **PLEASE FIX***

This also allows to change the wall clock time and the memory per task setting.  We have set initial values for the memory, matching on the systems configuration to minimise waiting time and cost allocated to your account.  On Alarik, if you increase your memory beyond 2000, please also check the box requiring 64 GB nodes. Uncheck that box if your memory per task is 2000 MB or less.  If you increase this setting, your debugging jobs may spent more time before they become active and might be more expensive with regard to your allocation.  DDT will remember changes made.  You must manually reset this to the recommended value if you don't need this any longer.  The recommended values are:

| System | recommended |
|-----------------|-------------|
| memory per task |  |
| Alarik | 2000 |
| Erik | 4000 |
 
In the box "Queue options" you can specify any sbatch option you like.  The example specifies an account (required only if your user-id is linked to multiple projects) and placement in the test queue.

Once everything is set, hit "Submit". 

DDT will then write a Slurm script for you and submit it to the batch queue.  While waiting in the queue, DDT will display the job queue.  Once your job starts running you get the DDT working window:

DDT_working_window_5.0.1 **PLEASE FIX***

You can now start your debugging session.  The DDT User Guide describes the options for running and debugging the program. 

Once your debugging session is finished you will find an output file in your directory.  This is named: ddt_jobid.out with jobid denoting the job-id number.  This file contains the output of your program to stdout and stderr.  You might want to clean your directory after the debugging has finished.
Debugging of GPU code on Erik

Here we describe the changes needed from the above to debug code on the GPU.  To facilitate GPU debugging for CUDA code one has to add the flags

    -g -G

to the options of the nvcc compiler. If you select "Run and debug a program" you get the following dialog:

DDT_cudarun_5.0.1

You have to check the "CUDA" box and the "Submit to Queue" boxes.  You can change the queue parameters by clicking on the "Parameters" button associated with "Submit to Queue".  You get the following dialog:

Erik_queue_parameters_5.0.1

Here we have selected 30 minutes of time, the default memory and the test partition.  After submission, you get into the job queue and once starting, you get to the working window.  When working on a source line executed on the GPU, the working window looks similar to:

DDT_working_window_gpu_5.0.1
Workflow

To improve the workflow and reduce time spent in the job queue waiting for processors becoming available, it is recommendable to ask for considerable amount of time, 30 min or more.  During this time you can restart your application as many times as needed (Tab: File -> Restart Session).  To release the resources use the Tab: File -> New Session -> Run.  This will release the cpus held by your debugging job and get you back to the window where you can change one or more of: selected executable, number of processes, wall time etc.  Your account gets charged for the resources consumed from when your debugging session became active until you released the cpus.
Resetting DDT

DDT remembers settings from your previous debugging sessions and also failed attempts.  Removing all DDT history can be achieved by deleting the directories .ddt and/or .allinea in your home space. 