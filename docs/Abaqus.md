# ABAQUS

Abaqus is installed on the cluster. 
Using Abaqus is divided into separate tasks. 
   
To create models, set up simulation jobs and view results Abaqus CAE is used. This is started trough the menue on the LUNARC HPC Desktop as shown below.

![Start Abaqus](images/start_abaqus.png "Start ABAQUS") 


To run the simulation as defined in the .inp file produced by the Abaqus CAE, a sbatch script detailing the resources needed for the simulation is required. The sbatch script is the description telling the queueing system which project is to be used, what resources are needed and for how long and also what program is to be run. The queueing system used at LUNARC is SLURM and a good tutorial on how to use it is found here: [SLURM tutorial](http://lunarc-documentation.readthedocs.io/en/latest/batch_system/)

Below an example SBATCH-script to run an Abaqus-job called sample.inp is shown, adjust as needed.


    #!/bin/bash
    #
    # job time, change for what your job requires In this example 1h (max is 170h)
    #SBATCH -t 1:00:00
    #
    # Number of nodes used. for most Abaqus jobs one node is sufficient
    #SBATCH -N 1
    #SBATCH --exclusive
    #
    # How many cores do you need to use? Remember that a large number here WILL consume 
    # a lot of tokens i.e. limit the number left for other abaqus users: Max per node is 20
    #SBATCH --tasks-per-node=10
    # 
    # What is the name of your project i.e. snic20xy-z-n or lu20xy-z-n
    #SBATCH -A <your-project-name>
    # 
    # A job name making it easy for you to remember what job this is
    #SBATCH -J <whatever_name_you_want>
    #
    # For information on how the job went error etc, include %j to get uniqe identifier
    #SBATCH -o process_%j.out
    #SBATCH -e process_%j.err
    #
    # Its nice to get an email when the job has started (i.e. finished queueing) and is finished
    #SBATCH --mail-user=you@your_place.se
    #SBATCH --mail-type=START,END

    # write this script to stdout-file - useful for scripting errors
    cat $0

    # copy the input data and program to node local disk
    # customise for your input file(s) and program name
    cp -p sample.inp $SNIC_TMP
    
    # change to the execution directory
    cd $SNIC_TMP

    # Add modules, please check which module you want to use, in this example we use v6R2017x
    # Please note that this MUST correspond to the version used to create the .inp file
    module add abaqus/V6R2017x

    # run the program 
    # customise for your program name and add arguments if required
    # The number of cpus MUST be equal or lower than the number of nodes times the number of 
    # tasks-per-node in this case 10. 
    # The memory is depending on the size of the node and if you are the only one on it 
    # i.e. if you have set -N 1 or  --exclusive see the SLURM tutorial for info. 
    # The scratch space should be set to . otherwise the execution will be slow and might crash. 
    # interactive is a required statement otherwise the simulation will NOT run 

    abaqus job=sample memory=60000MB cpus=10 scratch=. interactive

    # When the run is finished, copy all the data from the execution directory to the directory 
    # you started the run from.
    cp -p *.* $SLURM_SUBMIT_DIR

In your SBATCH-script you adjust the parts unique for your job using a text-editor and save it in the directory where your .inp file is. then in the terminal, send the job desription (the SBATCH-script) to the queue using the following command:

        sbatch my_SBATCH_script.sub

This sends the job to the queue and the job manager will start the simulation when a slot on the cluster fitting the description in the script is available.

