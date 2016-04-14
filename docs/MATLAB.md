# MATLAB 
SNIC provides a nationwide set of licenses for the MATLAB Distributed Computing Server (MDCS). There are 500 licenses in total available from the six SNIC centra. Each user of MATLAB will be using their home university base-license for MATLAB and any toolboxes therein, specifically the parallel computing toolbox (PCT). 

The MATLAB versions available at Lunarc are:

    Version        Module name
    
    R2014a         matlab/8.3
    R2014b         matlab/8.4
    R2015a         matlab/8.5
    R2015b         matlab/8.6
    R2016a         matlab/8.7  (available on Aurora only)
    
At Lunarc the MATLAB installation R2014a (also known as 8.3) is the **only** version available for use with MDCS. If any of the newer versions are used, submission to the batch queue from within MATLAB is not possible .

Versions newer than R2014a can use PCT at Lunarc but ONLY within a single node and ONLY by writing a batch script where MATLAB is run with a MATLAB script. Note that MATLAB will be able to use the PCT on the cores of this single node using the "local" profile.

## Non-MDCS submissions

Using MATLAB with versions newer than R2014a requires the user to write a batch script and a MATLAB script. An example SBATCH-script to start MATLAB using the batch system follow below. 

    #!/bin/bash
    #
    # job time, change for what your job requires   
    #SBATCH -t 00:10:00
    #
    # project ID if needed
    #SBATCH -A <your-project-ID>
    #
    # job name
    #SBATCH -J MATLAB_job
    #
    # filenames stdout and stderr - customise, include %j
    #SBATCH -o process_%j.out
    #SBATCH -e process_%j.err
    # Can only use one node
    #SBATCH -N 1
    # Make sure that you are the only one using the node. If not used MATLAB will only see 1 core 
    #SBATCH --exclusive
    
    # write this script to stdout-file - useful for scripting errors
    cat $0

    # load the modules required for you program
    # customise for your program replacing X with desired version
    module load matlab/8.X

    # copy the input data and program to node local disk
    # customise for your input file(s) and program name
    cp -p input.dat program.m $SNIC_TMP

    # change to the execution directory
    cd $SNIC_TMP

    # run the program
    # customise for your program name and add arguments if required
    matlab < program.m

    # rescue the results to the submission directory
    # customise for your result file(s)
    cp -p result.dat $SLURM_SUBMIT_DIR
    
Keep in mind that you as the user must make sure that any data produced is saved explicitly within the script as they otherwise will be lost.


## Using PCT

In R2014a of MATLAB the queueing system is accessible from within MATLAB. To set up use of the system the user must first run 

    configCluster

This will prepare the users profile for use of the MATLAB MDCS. This ONLY needs to be done once per user and MATLAB version. 
Having prepared the profile the user then as a minimum need to give information on the name of the users project and requested walltime for the users programs to run.

    ClusterInfo.setProjectName('SNIC2016-X-Y')
    ClusterInfo.setWallTime('XX:YY:ZZ')
    
If a particular queue is requested (for instance to request nodes with larger memory footprint) this can be set as shown below:

    ClusterInfo.setQueueName('name-of-queue')
    
The ClusterInfo command have a number of parameters that can be set

    Arch
    ClusterHost
    Constraint
    DataParallelism
    DebugMessagesTurnedOn
    DiskSpace
    EmailAddress
    GpusPerNode
    MemUsage
    PrivateKeyFile
    PrivateKeyFileHasPassPhrase
    ProcsPerNode
    ProjectName
    QueueName
    RequireExclusiveNode
    Reservation
    SshPort
    UseGpu
    UserDefinedOptions
    UserNameOnCluster
    WallTime

Most of the parameters do not need to be set but can be needed in some instances depending on the type of problem investigated or resource needed e.g. if a GPU or larger memory is needed.

A short example of using the scheduler from within MATLAB is given below:

Given the function createMatrix.m

    function retval=createMatrix(x,y)
        parfor i=1:5
              retval(i)=mean(mean(randn(x,y)));
        end
    end    
    
Run the following in the matlab command window    
    
    c=parcluster                            # set up the connection to the queue
    j=batch(c,@createMatrix,1,{100,100})    # send the job using the batch function
    j.State                                 # check the state of the job
    j.fetchOutputs                          # get the results when finished
    ans{1}                                  # show the results
    
    
       

