# Basic serial jobs

## Basic run script

The following is an example of a simple script running the program named `processor`, whose executable is located in the submission directory.  If you are using a LUNARC-provided executable, load the required module(s) and omit the dot before the program name. 

This example does not use the node local disc, which is ok for modest I/O requirements and regular, in particular streaming data access.  If your program is demanding with respect to its I/O requirements and is irregularly accessing data you should be utilising the node local disc as [described below](#basicrun-script-for-io-intensive-jobs).

This example executes a single serial program.  If you need to process a large number of serial jobs, your might want to bundle them into a single submission. Refer to the section [Running multiple serial jobs within a single job submission](#running-multiple-serial-jobs-within-a-single-job-submission) for a scripting example.


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

## Basic run script for I/O intensive jobs

As discussed the node local disk provides better I/O-bandwidth and I/O access times than the
other file systems available. The following script assumes the program processor reads the file **input.dat** and produces a file **result.dat**.

The script copies the input data and the program executable from the submission directory to the node local disk executes the program on the node local disk and copies the result file back to the submission directory for safekeeping. The individual steps are highlighted by comments starting with a “#”. These comment lines can be kept in the file.

You need to customise the file to suit your specific needs. The script is suitable for jobs consuming no more than 5300 MB of main memory on COSMOS.

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
module load foss/2023a

# copy the input data and program to node local disk
# customise for your input file(s) and program name
cp -p input.dat processor $NAISS_TMP

# change to the execution directory
cd $NAISS_TMP

# run the program
# customise for your program name and add arguments if required
./processor

# rescue the results to the submission directory
# customise for your result file(s)
cp -p result.dat $SLURM_SUBMIT_DIR
```

We recommend being selective about the files you copy between the
submission directory and the local node disk. If you have multiple input
and result files you need to modify the copy statements accordingly.

## Module loading

The above examples assume your program has been compiled with the foss/2023a tool-chain module. If it has been compiled with a different compiler or tool chain you need to load the compiler module by adding a line similar to, for example,

    module add imkl/2023.1.0

LUNARC-provided modules on COSMOS are only visible once the required compiler or combination of compiler and MPI library is loaded. If you are using software another person has compiled for you need to consult with that person on the required modules to load. 

On other services, the modules typically complain if the wrong compiler is loaded and are hence self-documenting.

## Example script for running a serial R job

The following is a basic example to run a serial R job on the cluster.  If you have many jobs to run, this can be combined with the techniques described below to run multiple serial jobs in a single submission.

```bash
#!/bin/bash
#
# job time, change for what your job requires
#SBATCH -t 00:10:00
#
# job name
#SBATCH -J R_data_process
#
# filenames stdout and stderr - customise, include %j
#SBATCH -o result_R_%j.out
#SBATCH -e result_R_%j.err

# write this script to stdout-file - useful for scripting errors
cat $0

# load the modules required for running R 
#    - customise for your program
#    - check for newer versions
module load foss/2021a
module load R/4.1.0

# run your R-script
# customise for your program name and add arguments if required
Rscript my_r_script.R
```

---

**Author:**
(LUNARC)

**Last Updated:**
2024-06-18
