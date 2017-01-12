# Using the job submission system at Lunarc #

Authors: Joachim Hein, Jonas Lindemann, Anders Sjöström, Magnus Ullner

*Document is currently updated for Aurora - Check back frequently!*

A more in-depth guide to the job submission system.

# SLURM - the batch system on Aurora and Erik

On a modern HPC system efficient management of the compute resources is
absolutely crucial for the system to perform. Lunarc deploys 
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

# Job submission

## First example for a job submission

### The job script and sbatch

You register your program with SLURM for execution using the **sbatch**
command. This is done easiest by using a *job description file*. The job
description file is also know as a *job script*.

A very simple job script, looks as follows:
    
    #!/bin/sh
    #SBATCH -t 00:05:00

    echo "hello"

Write this into a file. In the following we assume the file is named
echo_script.sh, but in principle any name will do. You can now send the
script for execution using the sbatch command. This will execute the
“program” echo on the backend.

    sbatch echo_script.sh

This should deliver a screen output similar to

    [fred@alarik Serial]$ sbatch echo_script.sh
    Submitted batch job 7185

Where 7185 is the job number assigned by SLURM. Once your job has
executed you will find a file slurm-7185.out in your directory which
contains the output and error messages from your program.

### The three parts of a job script

The example echo_script.sh shows the three parts every job script
requires

 1.  Shell specification
 2.  Resource statement
 3.  Body containing a UNIX script

In our example each part consists of a single line. The first line of
our example contains the shell specification, in most cases the sh-shell
as used here is just fine. The second line starting with #SBATCH
specifies the resources needed. In our case it asks for 5 minutes of
computer time. If the jobs hasn’t finished after that time, SLURM will
terminate it. Job scripts typically contain more than one of these
statements, specifying e.g. more than one processor or more memory. The
most commonly used resource statements at Lunarc will be explained
below. The resource statements are followed by a list of programs and
UNIX commands to be executed on the system. This is actually a normal
UNIX script and everything you can do in a UNIX script can be done here
as well. In our example the script consists out of the UNIX echo
command.

## Resource statements for all jobs

We now describe a number of statements which are most commonly used to
specify resource requirements for all kind of jobs. Refer to “man
sbatch” for more information.

### Walltime

The walltime attribute specifies the time requested for completing the
job. The time is *not* cpu-time but the total time, as measured by a
normal clock. In the previous example the time requested was 0 hours 5
minutes and 0 seconds. Walltime is specified in seconds or using the
following notation:

    Hours:Minutes:Seconds

If your calculation hasn’t finished once the specified time has elapsed,
SLURM will terminate your job. It is therefore **good practise** to
specify a bit more time than you anticipate your job to take. This makes
sure that you still get your results, even the jobs is slowed by some
interference, e.g. waiting for a write to a shared file system to
finish. However don’t specify excessive extra time. Due to scheduling
constraints, jobs asking for less time will typically spend less time in
the queue, waiting for their execution. This also provides safety
against depletion of your allocation. If, e.g., your job hangs, SLURM
will terminate your job and the project will be charged less time if the
walltime margin is not excessive.

To specify your walltime requirements write a statement like

    #SBATCH -t 00:10:00

into your job script.

The maximum walltime for any job on Aurora is 168h, which is the same as
7 days. On Erik the maximum walltime for any job is 48h.

### Job naming

All jobs are given both a job identifier and a name, for easier
identification in the batch-system. The default name given to a job is
the file name of the submit script, which can make it difficult to
identify your job, if you use a standard name for your submit scripts.
You can give your job a name from inside the script by using the -J
option:

    #SBATCH -J parameterTest

This will name your job “parameterTest”.

### Specifying a project and partition for users with LU projects or multiple projects 

Most users are members of a single SNIC project. These users do not need to
specify a project in their submission script. The Lunarc set-up
will automatically use that project for accounting.

Users with membership in more than project and/or in an Lund
University (LU) project have to let the system know which project to
charge for the run. To do so, you need to
specify the project using the -A option:

    #SBATCH -A snic2016-x-xxx

for a SNIC project or

	#SBATCH -A lu2016-x-xx

for an LU project. Replace the **snic2016-x-xxx** or **lu2016-x-xx** with the string naming your project.
The correct name can be obtained by using the **projinfo** or
**projinfo -p lu** command, respectively. The information is also available in the
SUPR system, but notice the difference in the formatting of the names.

For LU-projects the partition also needs to be specified in the batch script:

	#SBATCH -p lu

In addition, those who access private nodes (financed by a research project) through an LU project, the corresponding reservation has to be stated:

    #SBATCH --reservation=lu2016-x-xx

### Specifying memory requirements

#### Aurora
The Aurora system has 64 GB of memory installed on a compute node.  Since each 
node has 20 cores, the defaul memory request per core is 3200 MB of memory.  
If more than 3200 MB per core is needed it has to be 
requested explicitly using the `--mem-per-cpu` option.  For example if you require 
5000 MB per core add the line

    #SBATCH --mem-per-cpu=5000

When requesting more then 3200 MB per processing core on Aurora, your 
jobs will be
charged at a higher rate. In this case some processing cores have to
remain idle since you are using more than your fair share of memory.

#### Erik
Erik has 64 GB of memory on the standard nodes. Each node has two CPUs
with eight cores each. The default memory request per core is therefore
4000 MB of memory. As in the case of Aurora, if more than 4000MB of
memory per core is needed it has to be described as above.

#### Alarik
Alarik has 32 GB of memory installed on the small memory nodes and 64 GB
of memory on the large memory nodes. The default memory request per core
on the system is 2000 MB (a sixteenth of 32GB). If more then 2000 MB per
core is needed it has to be requested explictly with the
**--mem-per-cpu** option of sbatch. In this case you also have to
request allocation on a large memory node using the **-C mem64GB**
option of sbatch. The following show an example how to request 4000 MB
of main memory per compute core used:

    #SBATCH -C mem64GB
    #SBATCH --mem-per-cpu=4000

When requesting more than 2000 MB of memory, your jobs may spend a
longer time in the queue, waiting for execution, since it needs to wait
for run-slot(s) on the large memory nodes to become available. When
requesting more then 4000 MB per processing core, your jobs will be
charged at a higher rate. In this case some processing cores have to
remain idle since you are using more than your fair share of memory.


### Controlling job output

By default, the output which your job writes to stdout and stderr is
written to a file named

    slurm_%j.out

The %j in the file name will be replaced by the jobnumber SLURM assigns
to your job. This ensures that the output file from your job is unique
and different jobs do not interfere with each other's output file.

In many cases the default file name is not convenient. You might want to
have a file name which is more descriptive of the job that is actually
running - you might even want to include important meta-data, such as
physical parameters, into the output filename(s). This can be achieved
by using the -o and -e options of sbatch. The -o option specifies the
file containing the stdout and the -e option the file containing the
stderr. It is good practise to include the %j string into the filenames.
That will prevent jobs from overwriting each other's output files. The
following gives an example:

    #SBATCH -o calcflow_m1_%j.out
    #SBATCH -e calcflow_m1_%j.err

You can give the same filename for both options to get stdout and stderr
written to the same file.

### Notification

SLURM on the systems can send you email if the status of your job
changes as it progresses through the job queue. To use this feature you
need to specify the email address using the --mail-user option and
specify the event you want to get notified about using the --mail-type
option. The following

    #SBATCH --mail-user=fred@institute.se
    #SBATCH --mail-type=END

Will send an email to the address fred@institute.se once the job has
ended. Valid type values, selecting the event you can get notified
about, are BEGIN, END, FAIL, REQUEUE, and ALL (any state change).

### Job dependencies

To describe job dependencies, use the -d option of sbatch. This is
particularly useful for job dependencies, in workflows.

To illustrate this consider the following example. You require a serial
job to create a mesh for your simulation. Once this has finished, you
want to start a parallel job, which uses the mesh. You first submit the
mesh creation job using sbatch

    [fred@alarik Simcode]$ sbatch run_mesh.sh
    Submitted batch job 8042

As discussed, sbatch returns you a jobid, 8042 in this example. You use
this to declare your dependency when submitting the simulation job to
the queue

    [fred@alarik Simcode]$ sbatch -d afterok:8042 run_sim.sh
    Submitted batch job 8043

When using squeue to monitor job 8043, this should now be in status
pending (PD) with the reason of dependency. Another common use case for
this functionality is a simulation requiring many days of computer times
being split into a number of submissions.

### Test queue

To run short tests, it is possible to request extra high priority on
Alarik with the help of

    #SBATCH --qos=test

For one such job, the maximum walltime is 1 h and the maximum number of
nodes is two and a user is only allowed to run two such jobs
simultaneously. A system of floating reservations is used to free two
nodes every second hour between 8.00 and 20.00 to reduce the queue time
for test jobs. The way it works also means that the shorter the test
job, the more likely it is to start sooner rather than later. It is not
allowed to use qos=test for series of production runs.

On Erik there is one two-GPU node reserved for tests in a partition of
its own, which is specified with

    #SBATCH -p test

Like on Alarik, the maximum walltime is 1 h.

	
### Fat, extra fat and MIC nodes on Erik

Erik has 7 nodes with 4 GPUs (and 96 GB of memory). To access them, the
partition fat has to be specified

    #SBATCH -p fat

One node is equipped with 8 GPUs (and 96 GB of memory), which is in
partition extra

    #SBATCH -p extra

There is also one node with two Xeon Phi (MIC) cards in the partition
mic

    #SBATCH -p mic

There is also one node with two Nvidia K80 cards in the partition new

    #SBATCH -p new

If no -p option is specified, normal nodes with two Nvidia K20 cards
will be allocated to the job.

### Controlling requeueing/restarting of jobs

By default the scheduler will requeue (aka. restart) jobs that
suffered from node failure.  This is not always desirable.  Adding a line

    #SBATCH --no-requeue

to the header portion of your job script prevents this behaviour.


## Resource statements for multiprocessor

In HPC it is very common to have many processing elements working on a
job. The extra processing power can be utilised to process large
problems beyond the capabilities of a single processing element. It can
also be used to swiftly perform a number of calculations within a single
job submission.

### Terminology around nodes, processors, cores, tasks

There is a a lot of structure within modern HPC equipment. For the
purposes of this user guide we will stick to the following terminology:

| Term | Explanation | Number on Aurora | Number  on Erik |
|-----------|---------------------------------|-----------------------------|-----------------------------|
| Node | A physical computer | Standard: 180 for SNIC use | Standard: 16 |
|  |  | | Fat: 7 |
|  |  |  | Extra: 2 |
|  |  |  | Mic: 1 |
|  |  |  | New: 1 |
| Processor | This denotes a the multi-core processor, housing many processing elements | 2 per node | 2 per node |
| GPU | This denotes a nvidia co-processor | 0 | Standard: 2 per node |
|  |  |  | Fat: 4 per node|
|  |  |  | Extra: 8 per node|
|  |  |  | Mic:  0 per node|
|  |  |  | New: 2 cards per node, 4 logical per node |
| Socket | This is the “plug” the processor gets plugged into.  Used as a synonym for the processor | 2 per node | 2 per node |
| Core | Individual processing element | 20 per node | 16 per node |
| Task | This is a software concept.  It denotes a process, which is an instance of a running program.  It has its own data and instruction stream(s).  It can fork multiple threads to increase the computational speed.  Serial programs and pure MPI programs do not spawn threads. | User controls in job script | User controls in job script |
| Thread | This is also a software concept.  A thread is a stream of instructions executed on the hardware.  It is part of a task and shares resources such as  data with other threads within the same task. | User controls in job script | User controls in job script |

### Outline: Resource requests for multiprocessor jobs

When running multi processor jobs on the Lunarc clusters, one should
specify:

 1.  The number of nodes required by the jobs
 2.  The number of computational tasks per node
 3.  The number of threads spawned by each task

For a pure MPI job or when processing a large number of serial jobs in a
so called task farm, one will typically only specify the items 1 and 2,
while for a threaded job, using e.g. OpenMP or Java, one will typically
only specify items 1 and 3.

It is typically not advisable to have the product of items 2 and 3
exceeding the number of cores per node, which is 16 for standard Alarik
and Erik compute nodes. On the Alarik extra compute nodes this number is
48. In most cases users requesting multiple nodes will want the product
to equal the number of cores per node. The syntax how to control nodes,
tasks per node and threads per task is explaned below.

### Exclusive node access

For parallel codes using MPI or OpenMP it is typically best to keep
interference on the nodes at a minimum, that is to have exclusive access
to the nodes you are using. This also applies to specialist and
experimental work, which would interfere very badly with other user’s
codes on the nodes. Adding

    #SBATCH --exclusive

to your job script will ensure that SLURM will allocate dedicated nodes
to your job. Obviously your project gets charged for the full costs of
the nodes you are using, that is in case of Alarik and Erik 16 cores per
node.

### Specifying the number of nodes required for the job

In SLURM one requests the number of nodes for a job with the **-N**
option. The following statement requests four nodes for your job:

    #SBATCH -N 4

**Important:** without using either the --tasks-per-node or
the --cpus-per-task options of sbatch, this will reserve a single core
per node, so four in total, which is most likely not what you want.

### Specifying the number of tasks per node

Use the --tasks-per-node of sbatch to specify the number of tasks you
require per node. Most multinode job will set this equal to the number
of cores availble per node. The following example asks for 20 task per
node:

    #SBATCH --tasks-per-node=20

This should be used together with the -N option, specifying the number
of nodes to be used. The default value for the number of tasks per node
is 1. For example to specify the requirements for an MPI job with 80
tasks or multiprocessor job using 80 processors to process a larger
number of serial jobs one would specify

    #SBATCH -N 4
    #SBATCH --tasks-per-node=20

When using fewer than 20 tasks per node and you want to prevent other
user’s jobs sharing your node, you need to consider using
the --exclusive option. If --exclusive is not specified, SLURM might
place other tasks onto your node.

### Specifying the number of threads for a shared-memory job

If you want to run shared-memory applications using threads, e.g. OpenMP
parallised code or Java applications, you need to specify the number of
threads you require per task. This can be done with the --tasks-per-node
option of sbatch.

For a standard shared-memory program, which doesn’t also use distributed
memory programming models such as MPI, one is restricted to a single
node. On that node, one can request as many threads as there are cores
on the node. On the standard Aurora compute nodes one can efficiently
use up to 20 threads. Use the following resource statement:

    #SBATCH -N 1
    #SBATCH --tasks-per-node=20

If your program is only efficient at a lower thread count, you may want
to use e.g.:

    #SBATCH -N 1
    #SBATCH --tasks-per-node=4

if you only want to use four threads. 

### Resource statements for hybrid programs using distributed and shared memory 

So-called hybrid programs, using both distributed and shared-memory
techniques have recently become popular. For example: for a program
using 40 MPI tasks, each task spawning 2 OpenMP threads one would
require 4 nodes and place eight tasks on each node. The number of
threads per task is given by --cpus-per-task. The resource statement
would look as follows:

    #SBATCH -N 4
    #SBATCH --tasks-per-node=10
    #SBATCH --cpus-per-task=2

### Specifying the number of cores to be required by the job

In special cases, such as using very unusal numbers of tasks, the **-n**
option of sbatch to specify the number of cores might become useful.
When running a pure MPI program this option corresponds to the **number
of tasks** required for your program. The following statement in a job
script would reserve 78 cores for your job

    #SBATCH -N 4
    #SBATCH --tasks-per-node=20
    #SBATCH -n 78

Please consider using the --exclusive option of sbatch to avoid SLURM
spreading your job on more nodes than necessary and placing other user’s
jobs on nodes utilising fewer than 20 cores for your job. Other user’s
jobs could via shared node resources (memory bus, cache, …)
interfere with your job and introduce undue operational noise. Such
noise is something parallel program execution can be extremely sensitive
to.

## Program execution environment

### Job execution environment

When submitting your job to SLURM using sbatch, your entire environment
including the currently loaded modules gets copied.  On Aurora,
when hitting sbatch:

 * Make sure that the loaded modules and any environment variable you may have set will not be in conflict with the environment expected by the job script

### Compiler modules

On **Aurora** software modules are arranged in a **hierarchical module naming scheme**.  Accessing software on Aurora very different from 
earlier Lunarc systems and a [separate guide](http://lunarc-documentation.readthedocs.org/en/latest/aurora_modules/) is available.  
When compiling code using a [toolchain](http://lunarc-documentation.readthedocs.org/en/latest/aurora_modules/#compiling-code-and-using-toolchains) module is recommended.

On Alarik we automatically load a modern version of the GCC compiler,
which supports the deployed AMD Opteron processors. At the time of
writing this is version 4.6.2 of GCC. If you prefer using a different
compiler, you can add the desired module, e.g., version 12.1 of the
Intel compiler

    module add intel/12.1

If different modules have files with the same names in the search path,
those of the module added last will be picked. Generally this is not a
problem, but the compiler wrappers in the openmpi modules have the same
names and it safest to only have one loaded at a time.

On Erik the same compilers as on Alarik are present. Note that the
processors on Erik are of the Intel Xeon type and thus utilize the mkl
as supplied.

### SLURM variables

*To come*

### SNIC variables

The SNIC meta-centres have agreed on a set of environment variables
which should improve the portability of (parts of) job-scripts between
SNIC sites. On Aurora and Erik the following variables are set by the system:

| Environment variable | Explanation | Value on Aurora | Value on Erik |
|----------------------|-------------|-----------------|-----------------|
| SNIC_SITE | Identifying the SNIC site you are using | lunarc | lunarc |
| SNIC_RESOURCE | Identifying the compute resource you are using | aurora | erik |
| SNIC_BACKUP | User directory which is: Regularly backed up against accidental deletion, typically extremely limited space, used for e.g. precious source code | `/home/<user>` | `/home/<user>` |
| SNIC_NOBACKUP | User directory which is: Accessible on all Lunarc systems, acessible on all Lunarc systems, for storing larger amounts of data, not backed up against accidental deletion   | `/lunarc/nobackup/users/<user>` | `/lunarc/nobackup/users/<user>` |
| SNIC_TMP | Directory for best performance during a job.  At Lunarc: Local disk on nodes for storing temporary data during job execution. Transfer data with long-term value to SNIC_NOBACKUP before job has finished |  jobid dependent | jobid dependent |


## Using the node local disks to improve I/O performance

On Aurora and Erik, all nodes have a local disk. This disk offers
superior bandwidth when compared to accessing your home space or the
/lunarc/nobackup centre storage. In particular when files are read or
written repeatedly during execution it is advisable to copy the input
data onto the local disk prior to job execution and copy the result
files back to the submission directory once your program has finished.
During its execution, your program would then read and write to local
disk.

In case of Aurora and Erik, the submission directory typically resides
on the /lunarc/nobackup centre storage. All data left on the node local
disks **will be deleted** when your job has finished. You need to copy
everything of interest to a more permanent storage space such as
/lunarc/nobackup. If a job is terminated prematurely, for example, if it
exceeds the requested walltime, the files on the local disk (in
$SNIC_TMP) will be lost.

Files that would still be useful can be listed in a special file
**$SNIC_TMP/slurm_save_files**. Filenames are assumed to be relative
to $SNIC_TMP and should be separated by spaces or listed on separate
lines. Wildcards are allowed. These files will be copied from the local
disk where $SNIC_TMP/slurm_save_files exists to the submission
directory regardless whether the job ends as planned or is deleted,
unless there is a problem with the disk or node itself. Note that the
slurm_save_files feature is unique to Lunarc.

For the required UNIX scripting you should use the following environment
variables. Example scripts using this technique are provided in the
example section of this document. Contact the help desk if you have
specific requirements and require consultation.

| Variable | Addressed Volume |
|------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| SNIC_TMP | node local disk, copy your input data here and start your program from here |
| TMPDIR | node local disk, many applications use this environment variable to locate a disk volume for temporary scratch space.  If your application follows that convention nothing needs to be done.  |
| SLURM_SUBMIT_DIR | submission directory where you ran `sbatch` |

## Launching MPI jobs in OpenMPI

To execute message passing parallel jobs these should be built against
one of the MPI libraries provided by the support team as a module. To
execute an MPI job for an executable build with an OpenMPI library, 
your job script should do the following:

*   Load the compiler module you have been using
*   Load the OpenMPI module relevant for the compiler you are using
*   Start the program with `mpirun`
*   Using the correct binding can help code performance. 
     *   When using 20 task per node on Aurora, we recommend using the `-bind-to core` option of mpirun 
     *   When using fewer than 16 tasks we recommend experimenting whether not using binding helps or hinders performance.

## Launching MPI jobs compiled with the Intel MPI library

When the Intel MPI library was used to build your executable, your jobsscript should do as follows:

* Load the compiler module you have been using
* Load the Intel MPI module relevant for your compiler
* Start your program with `srun`

When using Lunarc provided software, and you are loading an
`impi` module to see your package, you need to use `srun` to start MPI
executables of this package.

**Remark**: Task binding for the Intel MPI library is still under investigation

## Submitting, monitoring and manipulating jobs in SLURM

### Submitting with sbatch

One uses the command sbatch to submit a job script to the batch system
for execution. SLURM will reply with the jobid number. The job will then
be held in the queue until the requested resources become available. A
typical use case looks as follows:

    [fred@alarik MPItest]$ sbatch runjob.sh
    Submitted batch job 7197

User fred submitted the script runjob.sh to the job queue and got the
jobid 7197 assigned to it.

### Starting executables within SLURM with srun

The command srun allows to start executables in a way managed by SLURM.
This is particularly effective if you want to process a large number of
jobs within a single submission to the batch system. A use case of srun
to start many serial jobs in a single multicore submission scipt is
discussed in the [*example section*](#id.bdphbddpef0).

### Monitoring with squeue

The command squeue will show you the current state of the job queue. The
standard output, created by calling squeue without any options looks as
follows:

    JOBID PARTITION NAME USER ST TIME NODES NODELIST(REASON)
    7303 snic hybrid_n fred PD 0:00 32 (Priority)
    7302 snic hybrid_n fred PD 0:00 32 (Priority)
    7301 snic hybrid_n fred PD 0:00 32 (Resources)
    7304 snic preproce karl PD 0:00 6 (Priority)
    7300 snic hybrid_n fred R 0:24 32 an[001-032]
    7305 snic preproce karl R 0:37 6 an[081-086]
    7306 snic hybrid_n fred R 0:37 6 an[081-086]
    7307 snic testsimu sven R 0:07 1 an081

The first column gives the jobid, the third the job names, followed by
the userid. The column labeled “ST” gives the job state. The most
important states are:

| Symbol | Meaning |
|--------|-----------------------------|
| R | running |
| PD | pending, awaiting resources |
| CG | completing |

The state column is followed by the time used by the job and number of
nodes utilised by the job. For running jobs the last column gives the
names of the nodes utilised or if the job is waiting a reason why it is
not executing.

The squeue command is highly configurable. Useful options include -u
myid, which lists all jobs of the user myid and also the --start option.
The latter gives the current estimate of when SLURM expects the job to
start. Note, that this can shift in either direction, depending on e.g.
jobs finishing earlier than specified or jobs with higher priority
getting added to the job queue.

The command jobinfo is a script that sorts the output of squeue into
running and waiting jobs. It also shows additional information, such as
how long running jobs have left and in some cases when waiting jobs are
expected to start.

### Terminating jobs with scancel

It is frequently required to remove jobs from the queue. This might be
that you discover a problem in your job specification or intermediate
results of running job indicating that something went wrong. Use scancel
to remove a job from the job queue. To do so you need the jobid, which
is best queried with the squeue command. To remove the job with the
jobid 7103 from the job queue type

scancel 7103

# Example job scripts

***The examples are still in the process of being updated for Aurora***


In this section we provide sample scripts for typical use cases.

## Job scripts for serial jobs

### Basic run script
The following is an example for a simple script running the program
named `processor`, whose executable is located in the submission
directory.  If you are using a Lunarc provided executable, load the
required module(s) and omit the dot before the program name. 

This example does not use the node local disc, which is ok
for modest I/O requirements and regular, in particular streaming data
access.  If you program is demanding with respect to its I/O
requirements and is accessing data in an irregular fashion you should be
utilising the node local disc as
[described below](#basicrun-script-for-io-intensive-jobs).

This example executes a single serial program.  If you need to process
a large number of serial
jobs, your might want to bundle them into a single submission. Refer to the
section [Running multiple serial jobs within a single job submission](#running-multiple-serial-jobs-within-a-single-job-submission) for a scripting example.

Unless you have project disc, jobs should be started from a
subdirectory of your
`/lunarc/nobackup/users/<user>` disc space.
```bash
#!/bin/bash
#
# job time, change for what your job requires
#SBATCH -t 00:10:00
#
# job name
#SBATCH -J data_process
#
# filenames stdout and stderr - customise, include %j
#SBATCH -o process_%j.out
#SBATCH -e process_%j.err

# write this script to stdout-file - useful for scripting errors
cat $0

# load the modules required for you program - customise for your program
module load foss/2016a

# run the program
# customise for your program name and add arguments if required
./processor

```

### Basic run script for I/O intensive jobs
As discussed the node local disk provides better I/O-bandwidth and I/O
access times than the
other file systems available. The following script assumes the
program processor reads the file **input.dat** and produces a file
**result.dat**.

The script copies the input data and the program executable from the
submission directory to the node local disk, executes the program on the
node local disk and copies the result file back to the submission
directory for safe keeping. The individual steps are highlighted by
comments starting with a “#”. These comment lines can be kept in the
file.

You need to customise the file to
suit your specific needs. The script is suitable for jobs consuming no
more than 3200 MB of main memory on Aurora.

```bash
#!/bin/bash
#
# job time, change for what your job requires
#SBATCH -t 00:10:00
#
# job name
#SBATCH -J data_process
#
# filenames stdout and stderr - customise, include %j
#SBATCH -o process_%j.out
#SBATCH -e process_%j.err

# write this script to stdout-file - useful for scripting errors
cat $0

# load the modules required for you program - customise for your program
module load foss/2016a

# copy the input data and program to node local disk
# customise for your input file(s) and program name
cp -p input.dat processor $SNIC_TMP

# change to the execution directory
cd $SNIC_TMP

# run the program
# customise for your program name and add arguments if required
./processor

# rescue the results to the submission directory
# customise for your result file(s)
cp -p result.dat $SLURM_SUBMIT_DIR
```

We recommend to be selective about the files you copy between the
submission directory and the local node disk. If you have multiple input
and result files you need to modify the copy statements accordingly.

### Module loading
The
above examples assumes your program has been compiled with the
foss/2016a tool-chain module.
If it has been compiled with a different
compiler or tool-chain you need to load the compiler module by adding a line similar
to

    module add intel/12.1

If you are running on Erik, it is necessary to add the support for the
GPU with the line

    module add cuda

prior to the line ./processor. You need to consult with the person who
build the executable for you.  Lunarc provided modules on Aurora are only
visible once the required compiler or combination of compiler and MPI library is loaded.

On other services the modules typically complain
if the wrong compiler is loaded and are hence self-documenting.

## Running multiple serial jobs within a single job submission

When you need to run many serial jobs, similar to the ones [described
above](#basic-run-script), these should be bundled together and
submitted to the job queue in a small number of submissions or even a
single submission. With SLURM is perfectly reasonable to run several
hundred individual jobs in a single submission. To speed up the
processing of your jobs, you can ask for the cores from a number of
nodes. The concept is known as a **task-farm**. The individual job are
known as **job-steps**.

The following is an example processing 200 such jobs using 40 cores from
two nodes. The scripting use two scripts, the master script and the
worker script. The master script requests the resources (number of
cores, job time, ...) and then registers 200 copies of the worker script
with SLURM using the command srun. The worker script is a modification
of the [basic script for I/O intensive jobs](#basic-run-script-for-io-intensive-jobs) described above.

In our example this will then start fourty jobs on the fourty cores
you requested. Once a job has finished, it will take an unprocessed job
and place it on the now idle core for processing. This will continue until
all jobs are processed. The ordering of the jobs can not be relied on.

For our example the entire setup assumes the submission directory has
200 sub-directories, named job_0, job_1, job_2, …, job_199. Each of
the directories contains the input data and the program executable to be
run.

Keep the number of jobs-steps at a reasonable level. Recent testing by
the Lunarc support team has shown that, when including a sleep statement
inside the do loop the setup can be used to processes 800 jobs.

### The master script

The master script describes the resources required and registers, once
running the worker tasks with SLURM. In most cases modifying the number
of nodes needed, the total job time and the number of jobs to be
processed should be all that is required.

With this script you only specify the number of nodes you require
using the `-N`option and specify the `--exclusive`.  The task farm
will use all the cores available on the nodes, which is 20 in case of
Aurora.   You **must not** specify `--tasks-per-node`.  If you did only one
core per node would be used on Aurora.

The following example will be
using 40 cores on 2 nodes to process 200 jobs.

```bash
#!/bin/sh
# requesting the number of nodes needed
#SBATCH -N 2
#SBATCH --exclusive
#
# job time, change for what your job farm requires
#SBATCH -t 20:00:00
#
# job name and output file names
#SBATCH -J jobFarm
#SBATCH -o res_jobFarm_%j.out
#SBATCH -e res_jobFarm_%j.out
cat $0

# set the number of jobs - change for your requirements
export NB_of_jobs=200

# Loop over the job number

for ((i=0; i<$NB_of_jobs; i++))
do
    srun -Q --exclusive -n 1 -N 1 \
        workScript.sh $i &> worker_${SLURM_JOB_ID}_${i} &
    sleep 1
done

# keep the wait statement, it is important!

wait
```

The script assumes that the job is described in a script file
“workScript.sh”, which takes a single number identifying the job
directory to be accessed as a command line argument. Please note the
“sleep 1” command inside the do loop. In our testing this greatly
enhances the stability by submitting the actual jobs over a longer
period of time. With this statement included the script was able to
successfully handle up to about 800 outstanding jobs on 16 and 32
Alarik cores.
For reasons of job reliability, we therefore recommend not to process
more than 800 jobs in a single script. However it is possible to process
significantly larger job numbers than 800 by carefully tuning sleep-time
and core count in relation to the average job-time.

**Remarks:** When using srun inside a batch script many srun-options act
differently compared to using srun within a different environment.
Consult the man-page of srun for documentation and contact the Lunarc
help desk if your require further consultancy.

### The worker script

This outlines the worker script. Compared to the script describing a
[basic script for I/O intensive jobs](#basic-run-script-for-io-intensive-jobs), a few modifications are
required:

-   To avoid access conflicts between the individual jobs, each job creates a job private sub-directory on the node local disk.

-   The input file(s) are expected in the sub_directories job_0,
    job_1, job_2, … of the submission directory. The result file(s) will also be placed in these directories.

-   The example assumes a single input file and single result file. If
     you have multiple input and/or result files modifications are
     needed, as are modifications for that actual names of your file

-   The present set up allows for different executables for each
     job-stop. The script assumes to find an executable named
     “processor” in the same location as the input file(s). If you all
     job steps use the same executable the scripts can be simplified.

-   Once a job-step has finished and the result file has been copied
     back, the job private sub-directory on the node local disk is
     removed to prevent the disc from overflow.

If you are using the above master script, the script should be named
“workScript.sh”.

```bash
#!/bin/sh
# document this script to stdout (assumes redirection from caller)
cat $0

# receive my worker number
export WRK_NB=$1

# create worker-private subdirectory in $SNIC_TMP
export WRK_DIR=$SNIC_TMP/WRK_${WRK_NB}
mkdir $WRK_DIR

# create a variable to address the "job directory"
export JOB_DIR=$SLURM_SUBMIT_DIR/job_${WRK_NB}

# now copy the input data and program from there

cd $JOB_DIR

cp -p input.dat processor $WRK_DIR

# change to the execution directory

cd $WRK_DIR

# run the program

./processor

# rescue the results back to job directory

cp -p result.dat ${JOB_DIR}

# clean up the local disk and remove the worker-private directory

cd $SNIC_TMP

rm -rf WRK_${WRK_NB}
```

### Monitoring the progress of your multi-job submission

Using the -s option of sbatch you can monitor the progression of the
individual job-steps of your multi-job submission. Please keep in mind,
that the step number SLURM assigns to your job and the one you assign
typically differs from the loop index used in the master script.

The below is an output from squeue when running a script processing 500
jobs on 32 cores. The jobid of the job is 8070. The output shows the
job-steps the script is presently processing

    [fred@alarik MultiSerialTest]$ squeue -j 8070 -s
    STEPID NAME PARTITION USER TIME NODELIST
    8070.130 small_ex snic fred 2:09 an074
    8070.133 small_ex snic fred 2:02 an073
    8070.135 small_ex snic fred 1:55 an074
    8070.136 small_ex snic fred 1:41 an073
    8070.139 small_ex snic fred 1:41 an073
    8070.140 small_ex snic fred 1:41 an073
    8070.143 small_ex snic fred 1:41 an073
    8070.144 small_ex snic fred 1:41 an074
    8070.147 small_ex snic fred 1:41 an074
    8070.148 small_ex snic fred 1:41 an074
    8070.151 small_ex snic fred 1:41 an074
    8070.155 small_ex snic fred 1:38 an074
    8070.156 small_ex snic fred 1:35 an074
    8070.157 small_ex snic fred 1:34 an073
    8070.158 small_ex snic fred 1:34 an073
    8070.159 small_ex snic fred 1:34 an073
    8070.161 small_ex snic fred 1:34 an073
    8070.164 small_ex snic fred 1:33 an074
    8070.165 small_ex snic fred 1:33 an074
    8070.168 small_ex snic fred 1:32 an073
    8070.170 small_ex snic fred 1:26 an073
    8070.171 small_ex snic fred 1:12 an073
    8070.172 small_ex snic fred 1:12 an073
    8070.175 small_ex snic fred 1:11 an074
    8070.176 small_ex snic fred 1:11 an074
    8070.179 small_ex snic fred 1:11 an074
    8070.184 small_ex snic fred 1:04 an074
    8070.185 small_ex snic fred 0:42 an073
    8070.190 small_ex snic fred 0:35 an073
    8070.193 small_ex snic fred 0:35 an074
    8070.194 small_ex snic fred 0:13 an073
    8070.195 small_ex snic fred 0:13 an074

## MPI job using 20 tasks per node

Most MPI jobs achieve best cost efficiency when deploying 20 tasks per
node, that is one task per core.  The sample uses core binding as
offered by the OpenMPI library.

The resource request is straight forward in this case.  Ask for a number of
nodes and place 20 tasks on each node.  The product should match
the number of tasks you want to run. We recommend
using the `--exclusive` option.  The following is an example submission
script to run the MPI application simula_mpi with 80 tasks on 4 nodes.
Notice you do not need to specify the node count.

```bash
#!/bin/sh
# requesting the number of cores needed on exclusive nodes
#SBATCH -N 4
#SBATCH --tasks-per-node=20
#SBATCH --exclusive
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

In case the executable was compiled with the **Intel MPI library** (using
a module named `impi`) the last line should read
```bash
# start the mpi executable 
srun simula_mpi
```

## MPI job using the node local discs

In many cases you can use the node local discs also for your MPI
jobs.  This can be beneficial if your job is very demanding with
respect to disc I/O and spends a lot of time doing it.

You need to transfer your executable onto the node local discs of all
nodes.  Depending on the architecture of your application it is often
sufficient to have the input files on the head node only.  Many MPI
applications write result files only from the head node.  In these
cases the below script should work.

```bash
#!/bin/sh
# requesting the number of cores needed on exclusive nodes
#SBATCH -N 4
#SBATCH --tasks-per-node=20
#SBATCH --exclusive
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
Again, applications using the Intel MPI library need starting with
`srun`, see above.

### Modifications required for file I/O on all nodes

As discussed in the comments of the sample script, the script assumes
that only MPI-task 0 on the head node reads the input file and writes to
the output file. If for your MPI application every MPI task reads the
input file(s), replace the line

    cp -p input.dat $SNIC_TMP

with

    srun -n $SLURM_NNODES -N $SLURM_NNODES cp -p input.dat $SNIC_TMP

and the file gets copied onto the local disk of each node. Matters are
slightly more complex, if your output is written from all tasks. We
assume the output files can be wild-carded as result_*.dat. Copying
these files back to the submission directory can be achieved creating a
script, which is placed on all nodes and subsequently executed on all
nodes. The following addition to the submission script will create the
script and place it on all your nodes

```bash
cat <<EOF > copyfile.sh
#!/bin/sh
cp -p result*.dat $SLURM_SUBMIT_DIR
EOF

chmod u+x copyfile.sh
srun -n $SLURM_NNODES -N $SLURM_NNODES cp copyfile.sh $SNIC_TMP
```

This needs inserting into the script before the “cd $SNIC_TMP”
statement. Once this is in place you can copy your result files by
replacing the line

    cp -p result.dat $SLURM_SUBMIT_DIR

with the line

    srun -n $SLURM_NNODES -N $SLURM_NNODES copyfile.sh

## MPI jobs using fewer than 20 tasks per node

If you want to use fewer than 20 task per nodes to e.g. give more
resources to the individual task, you can use the -N and --task-per-node
options of sbatch. We recommend not to use the -n option in this case.
This example is for 4 nodes with 10 tasks each, a total of 40 tasks. In
our experience, in this case and when using --exclusive it is typically
advantageous to not use binding. Though we encourage experimenting with
your own application.

**Note:** Despite the example using only 40 cores, it will be block 80
  cores, since it uses all the memory of the 4 nodes.   Your
  allocation will be charged for all 80 cores.

```bash
#!/bin/sh
# requesting the number of nodes and cores needed, exclusive nodes
#SBATCH -N 4
#SBATCH --tasks-per-node=10
#SBATCH --mem-per-cpu=6400
#SBATCH --exclusive
#
# job time, change for what your job requires
#SBATCH -t 0:30:0
#
# job name
#SBATCH -J simula_n40
#
# filenames stdout and stderr - customise, include %j
#SBATCH -o simula_n40_%j.out
#SBATCH -e simula_n40_%j.out

# write this script to stdout-file - useful for scripting errors
cat $0

# Example assumes we need the intel runtime and OpenMPI library
# customise for the libraries your executable needs
module add iomkl/2015.03

# Copying the executable onto the local disks of the nodes
srun -n $SLURM_NNODES -N $SLURM_NNODES cp -p simula_mpi $SNIC_TMP

# Copy the input file onto the headnode - if your MPI program
# reads from all tasks, you need to do the above srun construct
# again
cp -p input.dat $SNIC_TMP

# change to local disk and start the mpi executable
cd $SNIC_TMP
mpirun simula_mpi

# Copy result files back - example assumes only task zero writes
# if in your application result files are written on all nodes
# you need to initiate a copy on each node via srun

cp -p result.dat $SLURM_SUBMIT_DIR
```

OpenMP jobs using shared memory
-------------------------------

To run a shared memory code using OpenMP on Aurora, you specify the
number of cores you require using --tasks-per-node option of sbatch. In
this case you have to request placement on a single node with the “-N 1”
option. In this example we call the executable “processor_omp” to
emphasis that this need to be compiled with OpenMP support. Unless you
are doing something special, you are not required to specify the
environment variable OMP_NUM_THREADS. The example script uses the
techniques described for the [*basic run script*](#id.oyajyndi4e55) to
engage the node local disk.

```bash
#!/bin/bash
#
# Specify the number of threads - request all on 1 node
#SBATCH -N 1
#SBATCH --tasks-per-node=20
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

This script allows to use 3200 MB of main memory per requested core.
When asking for 20 cores on Aurora you can use all the memory
available on the node.

The modification required to utilise the node local discs are exactly
the same as require [for serial jobs](#basicrun-script-for-io-intensive-jobs).

### Thread binding for OpenMP codes

The Alarik nodes deploy a cache-coherent non-uniform-memory access
architecture (cc-numa). Many scientific simulation codes gain
significant performance benefits on a cc-numa architecture when the user
binds the threads to physical cores of the hardware. This inhibits
thread migration and improves memory locality. Unfortunately invoking
thread binding is not standartised. Depending on the OpenMP runtime
library the user needs to modify different environment variables to bind
his threads.

#### Thread binding with the GNU compilers

By default the GNU compiler suite (gcc/gfortran) does not bind threads
to cores. To engage thread binding, you need to set the environment
variable GOMP_CPU_AFFINITY and provide this with a binding list. When
setting

    export GOMP_CPU_AFFINITY=”0-15”

in your submission script, prior to starting your OpenMP application,
this will bind the threads to the 16 cores in the node. The above will
bind thread 0 to core 0, thread 1 to core 1 and so on.

**More advanced remark:** If you want to utilise only 8 cores from a
node and asking for exclusive node access (#SBATCH --exclusive), it
might be a good idea to place threads on every second core only. This
will give you more memory bandwidth and make sure you are utilising all
FPUs of the Interlagos architecture. This can be achieved by setting:

    export GOMP_CPU_AFFINITY=”0-14:2”

or

    export GOMP_CPU_AFFINITY=”0 2 4 6 8 10 12 14”

It depend on details of your application, whether or not this helps
performance. Also note, when asking for a exclusive access to a note,
you will be charged for the full node, whether or not you use all cores.

**Important pitfall:** If you set GOMP_CPU_AFFINITY=0 this will bind
all threads to core 0. You will see extremely poor performance in this
case.


#### Thread binding with the Intel compiler

Versions 12.1 and 13.0 of the **Intel** compiler do not support thread
binding when used on the AMD processors deployed on Alarik. Starting
from version 13.1 the Intel compile does support thread binding on the
AMD processors deployed on Alarik. Obviously all versions of the Intel
compiler support thread binding on the Intel processors deployed on
Erik.

For version 13.1 of the Intel compiler thread is controlled by setting
the environment variable KMP_AFFINITY. The value

    export KMP_AFFINITY=granularity=fine,compact

might be a good starting point for your experimentation.

## Hybrid-jobs using threads within an MPI framework

*This needs to be revisited for Aurora - we keep the Alarik
 documentation for the time being*

A cluster with multicore nodes such as Alarik is a natural environment
to execute parallel codes deploying both MPI and OpenMP threads. When
running such applications the optimal number of MPI-tasks and OpenMP
threads to place on a node can depend highly on the details of the
application. In particular for application which make many references to
main memory and the programmer has not implemented a proper “first touch
data allocation” it is typically **best to have 2 or 4 threads** per MPI
task on an Alarik node. Together with a proper binding of your MPI tasks
to the “numa-islands”, this will ensure memory locality for your code.
For the below syntax you have to use **version 1.8.3 or newer** of the
OpenMPI library.

In the following we give a simple example script to run a MPI-OpenMP
hybrid named simul_hyb on 2 nodes using 8 tasks and 4 threads per task.
The tasks and their threads will be bound to the *numa-islands*,
minimising cc-numa effects.

```bash
#!/bin/sh
# requesting number of nodes (-N option)
# number of mpi-tasks per node
# and number of threads per task exclusive nodes
#SBATCH -N 2
#SBATCH --tasks-per-node=4
#SBATCH --cpus-per-task=4
#SBATCH --exclusive
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

The example assumes that MPI task 0 is the only task reading and writing
input files. If your application reads and writes data on all nodes, you
need to study the [*modifications*](#id.hpgejkt8dzry) described in the
MPI section.

As discussed, the above binds the tasks and their threads to the
numa-islands of the Alarik architecture. Alariks numa-islands have four
cores, therefore the script is best used with 2 or four threads per MPI
task. This results in one or two MPI tasks per numa islands.

### Things to try for MPI-OpenMP hybrids with 16 threads per task

While using more than 4 threads per MPI task on the Alarik system can
result in reduced performance due to cc-numa effects, there are
situations when using 16 threads per task can be required (e.g. special
algorithms or extreme memory requirements per MPI task).

When running 16 threads per MPI task, that is a single MPI task per
Alarik node, you might want to experiment with starting your job without
specifying binding on mpiexec, that is remove the -bind-to-core, but
utilise the [*OpenMP thread binding*](#id.zfbd8w955ujk) techniques
described in the OpenMP sample section.

# Interactive access to compute nodes

Sometimes it is desirable to have an interactive connection to the compute
nodes of the cluster. Extensive code testing and debugging is a typical use case.
Regular production runs, in particular calculations requiring multiple days, are expected to
access the compute nodes via the **sbatch** command.

## Starting an interactive session

To start an interactive session you need to use the **interactive**
command. This will request the required resources from the resource pool
for you and start the interactive session once the resources are
available.

Use the following command to start an interactive session asking for 32
cores lasting 60 minutes

    interactive -n 32 -t 60

On Aurora and Erik this will be allocated on multiple nodes, since the
nodes have only 20 and 16 cores available, respectively. The interactive session will last
until either the requested time, 60 minutes in the above example, has
expired or you manually exit the interactive shell. Your account gets
charged with the wall time duration of your interactive session,
independent of the amount of computation you do. In the above example,
if your session lasts until it expires after 60 min, you get charged for
32 cpu hours. If you terminate your session after 1/2 hour, you would
get charged 16 cpu hours.

The interactive command supports most command line options of the **sbatch**
command. Please refer to the man pages of sbatch.

## Modules and environment variables

Loaded modules and environment variables are not always exported properly to your
interactive session. Once placed in the interactive session, we
recommend users

* To remove all currently loaded modules by using the **module purge** command
* To reload **all** the modules they require

That is despite the **modules list** command claiming they are still loaded.

You also need to check whether environment variables still have the
required values. If the software you are using has a set-up script, you
might need to re-run that script.

## Known issues with the interactive command

None at the time of writing.
