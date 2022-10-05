# DDT debugger on Aurora

A basic description on how to start a debugging session with DDT, part of [ARM Forge](http://www.allinea.com/products/develop-allinea-forge), on the Aurora systems.  Job will be submitted through the Slurm batch system to the back-end nodes.

## About this document

This document gives basic instruction on how to start a debugging session using the DDT debugger on the Aurora system at LUNARC.  This document is based on DDT version 6.0.2 currently installed on Aurora.  There is currently a centralised SNIC license hosted by NSC.  The licenses are shared between the users of all the SNIC systems.  So please be considerate to other users fellow regarding for how many licenses your use (time and number of cores).
    
DDT is a powerful debugger for serial and parallel programs.  The tool is developed and maintained by ARM.  It is part of the ARM's Forge suite.  A number of parallel programming models are supported.  This includes MPI, OpenMP and a number of GPU languages.  This document is not a DDT userguide, we refer our users to the documentation available from the [ARM website](https://www.arm.com/products/development-tools/server-and-hpc/forge), in particular their [user guide](https://developer.arm.com/documentation/101136/2113/?lang=en).

## Getting started with DDT on Aurora

### Connect to Aurora via the LUNARC HPC desktop

To use DDT you need to be able to access its graphical user interface (GUI).  
The recommended way to connect to the system is via the [LUNARC HPC desktop](using_hpc_desktop).  

<!--
### Using x-forwarding under Linux or on a Mac
Linux systems are typically setup to support x-windows.  On the latest releases of Mac OSX you have to install [XQuartz](http://www.xquartz.org/) to display x-windows.  Once this is available connect to Aurora:

    ssh -X aurora.lunarc.lu.se -l <username>
    
You will be prompted for password and one-time-password as usual.

### Connecting from a windows system
To connect using x-forwarding from a windows it is recommended to install [Cygwin](https://www.cygwin.com/). 
-->

### Starting the DDT GUI on Aurora

LUNARC currently recommends using *reverse connect* to start DDT.  Load the relevant module.  On Aurora the module name is *arm_forge*.  Load it: 

```
    module load arm_forge
``` 
   
You can now start the GUI by typing

```
    ddt &
```    

at the command prompt.  This will bring up the following GUI window

![Start window](../../images/allineaForgeStartWindow.png "Start window")    

In the bottom left hand corner you get confirmation whether you managed to reach the license server at NSC.

#Preparing and running your executable
We have seen issues when sources and/or executables are placed on the /lunarc file system (nobackup space).  Copying sources and executables into your home space typically solves the issues.

You need to prepare your executable for debugging.  Please **recompile** and **relink** everything with debugging support and without optimisation.  To do so, for most compilers you need to add the flags

```
   -g -O0
```

Once your created an executable with debugging support, run it using either a batch script or an interactive session.  

Make sure the `arm_forge` module is loaded by your script or manually inside your session, before starting the executable.  This is in addition to the modules normally required to execute your code.

To start your program, prefix the execution statement with `ddt --connect`.  For example an MPI code compiled against an OpenMPI-library should be started as follows

```
   ddt --connect mpirun program_g
```

with the executable being named *program_g*.  In case of the Intel MPI-library the code gets started using *srun*

```
   ddt --connect srun program_g
```

Once your job starts running, you get a request to allow your job connecting to the DDT GUI

![Reverse connect request](../../images/ddtReverseConnectRequest.png "reverse connect request")     

Accept this to get to the next window.

![Code feature window](../../images/ddtcodeFeatureWindow.png "code feature window")

In this window you can select the features of ddt which you require.  We would like to point out the **Memory Debugging**, which can be extremely useful when trying to resolve segmentation faults and memory leaks.  Please consult Allinea's user guide for more details and side effects (e.g. increased memory consumption) of using this feature.

Hit the *run* button to start the debugger window

![DDT gui](../../images/ddtGui.png "DDT gui")

In the GUI you can run your code (parallel or serial), set breakpoints, examine values of variables and data structures.  

During a debugging job it is often required to restart the program execution from the beginning.  We recommend not to choose the *Restart Session* option from the *File* pull down menu to restart the programs execution from the beginning:

![DDT restart pull down](../../images/ddtstartRestartPulldown.png "DDT restart pull down menue") 

In particular when using DDT from a batch script, using this option will keep your script active and **you do not need to re-queue**.
  
If you want to start over for e.g. changing the level of memory debugging, we recommend using the *End Session* option from the File pull down menue:

![DDT end pull down menue](../../images/ddtstartEndSessionPulldown.png "DDT end pull down menue")

Using this option will terminate the ddt execution, but keep the GUI alive, which is often advantageous when using *ssh -X* to connect to the cluster.  If working from a batch script, its execution will then continue to the next line(s) which typically leads to the script finishing and requires you to re-queue.  An interactive session will keep running, if the time limit has not been reached.

 
<!-- # Debugging MPI code on Alarik #

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
# Debugging of GPU code on Erik

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

-->

---

**Author:**
(LUNARC)

**Last Updated:**
2022-10-05