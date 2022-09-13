When you need to run many serial jobs, similar to the ones [described
above](#basic-run-script), these should be bundled together and
submitted to the job queue in a small number of submissions or even a
single submission. With SLURM is perfectly reasonable to run several
hundred individual jobs in a single submission. To speed up the
processing of your jobs, you can ask for the cores from a number of
nodes. The concept is known as a **task-farm**. The individual job are
known as **job-steps**.

The following is an example processing 200 such jobs using 20 cores from
one node. The scripting use two scripts, the master script and the
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

**Remarks:**

* Keep the number of jobs-steps at a reasonable level. Recent testing by
the LUNARC support team has shown that, when including a sleep statement
inside the do loop the setup can be used to processes 800 jobs.
* Make sure to spawn worker tasks from a unique master script running within a unique `sbatch` job submission.
Please refrain from using `sbatch` or `sbatch --array` to spawn each worker tasks as an individual job.

## The master script

The master script describes the resources required and registers, once
running the worker tasks with SLURM. In most cases modifying the number
of nodes needed, the total job time and the number of jobs to be
processed should be all that is required.

The following example will be
using 20 cores on 1 node to process 200 jobs.

```bash
#!/bin/sh
# requesting the number of nodes needed
#SBATCH -N 1
#SBATCH --tasks-per-node=20
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
    srun -Q --exclusive --overlap -n 1 -N 1 workScript.sh $i &> worker_${SLURM_JOB_ID}_${i} &
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
cores of
our earlier
Alarik system.
For reasons of job reliability, we therefore recommend not to process
more than 800 jobs in a single script. However it is possible to process
significantly larger job numbers than 800 by carefully tuning sleep-time
and core count in relation to the average job-time.

**Remarks:** When using srun inside a batch script many srun-options act
differently compared to using srun within a different environment.
Note also that even the order of the options `--exclusive` and `--overlap` is crucial for the correct behaviour.
Consult the man-page of srun for documentation and contact the LUNARC
help desk if your require further consultancy.

If you need more than the default 3100 MB memory per core, you have to specify both `--tasks-per-node` and `--mem-per-cpu`. Please match the core count and the memory per core so as to best utilise the resources of a node, which has a total of 62000 MB memory available for jobs.

## The worker script

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
#!/bin/bash
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

## Monitoring the progress of your multi-job submission

Using the -s option of sbatch you can monitor the progression of the
individual job-steps of your multi-job submission. Please keep in mind,
that the step number SLURM assigns to your job and the one you assign
typically differs from the loop index used in the master script.

The below is an output from squeue when running a script processing 500
jobs on 32 cores. The jobid of the job is 8070. The output shows the
job-steps the script is presently processing

    [fred@aurora MultiSerialTest]$ squeue -j 8070 -s
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


