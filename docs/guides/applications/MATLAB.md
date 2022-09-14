# MATLAB 

SNIC provides a nationwide set resources with the MATLAB Distributed Computing Server (MDCS) installed. At each respective SNIC centra the hosting university MATLAB licenses and toolboxes are available for use. 
 
The MATLAB versions available at LUNARC are:

```bash
Version        Module name

R2014a         matlab/8.3
R2014b         matlab/8.4
R2015a         matlab/8.5
R2015b         matlab/8.6
R2016a         matlab/8.7  (available on Aurora only)
R2017a         matlab/2017a
R2020a        matlab/2020a
R2020b        matlab/2020b
```

## Running MATLAB on login-node 

MATLAB can be used on the login-nodes (either using LUNARC HPC Desktop i.e. ThinLinc. Or using SSH) Please note that as these are shared resources excessive use of MATLAB on the login-nodes will prevent other users from using the resources. 
As MATLAB by default will use as many threads (cores) it possibly can, any user who is to use MATLAB on the login-nodes MUST start matlab with the option "-singleCompThread" thus preventing MATLAB from using more that one thread.
This will NOT prevent matlab from utilizing the MATLAB Distributed Computing Server (MDCS) with which any number of cores can be used for computations.

To start MATLAB on a login node use: 

```bash
matlab -singleCompThread
```

On the LUNARC HPC Desktop (Thinlinc) MATLAB R2016a as well as MATLAB R2017a are available via the Applications menu. Starting MATLAB from the menue automatically will set the -singleCompThread flag. This is now the recommended way to start MATLAB on the LUNARC HPC Desktop.

## Getting Started with Serial and Parallel MATLAB on Aurora

### Load the module
The MATLAB module is loaded with

```bash
$ module load matlab/<version>
```

### Configuration

Start MATLAB.  
Configure MATLAB to run parallel jobs on your cluster by calling configCluster.  
For each cluster, configCluster only needs to be called once per version of MATLAB.

```matlab
configCluster
```


Jobs will now default to the cluster rather than submit to the local machine.
Before submitting a job, you must specify the name of the charge account and the walltime, via ClusterInfo, which will be explained in more detail below.

### Configuring jobs

Prior to submitting the job, we can specify various parameters to pass to our jobs, such as queue, e-mail, etc.  
Specification is done with ClusterInfo.  The ClusterInfo class supports tab completion to ease recollection of method names.
NOTE:  Any parameters set with ClusterInfo will be persistent between MATLAB sessions.

```matlab
% Specify a charge account						[REQUIRED]
ClusterInfo.setProjectName(‘name-of-account’)
% Set walltime to 1 hour    						[REQUIRED]
ClusterInfo.setWallTime(’01:00:00’)
% Specify a partition to run on
ClusterInfo.setQueueName(‘partition-name’)
% Specify e-mail address to receive notifications about your job
ClusterInfo.setEmailAddress(‘user@company.com’)
```

Additional parameters that can be supplied are:

* MemUsage (in megabytes)
* ProcsPerNode
* RequireExclusiveNodes
* Reservation
* UseGpu

Please note that as of version 2017a the syntax for setting the ClusterInfo parameters have changed. These are now properties of a parcluster object instead of methods in the ClusterInfo object.

```matlab
configCluster

% Set ProjectName and WallTime before submitting jobs to AURORA
c = parcluster;
c.AdditionalProperties.ProjectName = 'project-name';
c.AdditionalProperties.WallTime = '01:00:00';
c.saveProfile
```

In the above example c is a parcluster object to which all properties are set accordingly.

To see the values of the current configuration options, call the state method.  To clear a value, assign the property an empty value (‘’, [], or false), or call the clear method to clear all values.

```matlab
% To view current configurations
ClusterInfo.state
% To clear a configuration that takes a string as an input argument
ClusterInfo.setEmailAddress(‘ ’)
% To clear all configurations
ClusterInfo.clear
```

#### Serial jobs

Use the batch command to submit asynchronous jobs to the cluster.  The batch command will return a job object which is used to access the output of the submitted job.  See the MATLAB documentation for more help on batch.

```matlab
% Get a handle to the cluster
c = parcluster;
% Submit a job to query where MATLAB is running on the cluster
j = c.batch(@pwd, 1, {});
% Query the job for the state
j.State
% If the state of the job is finished, fetch the results
j.fetchOutputs{:}
% Delete the job after the results are no longer needed
j.delete
```

To retrieve a list of running or completed jobs, call parcluster to retrieve the cluster object.  The cluster object stores an array of jobs that were run, are running, or are queued to run.  This allows us to fetch the results of completed jobs.  Retrieve and view the list of jobs as shown below.

```matlab
c = parcluster;
% Retrieve the results of past jobs from the cluster
jobs = c.Jobs
```

Once we’ve identified the job we want, we can retrieve the results as we’ve done previously. 

```matlab
% Retrieve the results of the 3rd job
j3 = jobs(3);
j3.fetchOutputs{:}
```

fetchOutputs is used to retrieve function output arguments; if calling batch with a script, use load instead.   Data that has been written to files on the cluster needs be retrieved directly from the file system.

#### Parallel jobs

Users can also submit parallel workflows with batch.  Let’s use the following example for our parallel job.

![Code example](../../images/Code_example.png "Start window")    

We’ll use the batch command again, but since we’re running a parallel job, we’ll also specify a MATLAB Pool.     

```matlab
% Get a handle to the cluster
c = parcluster;
% Submit a batch pool job using 4 workers for 16 simulations
j = c.batch(@parallel_example, 1, {}, ‘Pool’, 4);
% View the current job status
j.State
% Fetch the results after the job is in the finished state
j.fetchOutputs{:}
ans = 
    8.8872
```

The job ran in 8.89 seconds using 4 workers.  Note that these jobs will always request N+1 CPU cores, since one worker is required to manage the batch job and pool of workers.   For example, a job that needs eight workers will consume nine CPU cores.  	
We’ll run the same simulation, but increase the Pool size.  This time, to retrieve the results at a later time, we’ll keep track of the job ID.
NOTE: For some applications, there will be a diminishing return when allocating too many workers, as the overhead may exceed computation time.    

```matlab
% Get a handle to the cluster
c = parcluster;
% Submit a batch pool job using 8 workers for 16 simulations
j = c.batch(@parallel_example, 1, {}, ‘Pool’, 8);
% Get the job ID
id = j.ID
id =
    4
% Clear the job variable, as though we quit MATLAB
clear j
```

Once we have a handle to the cluster, we’ll call the findJob method to search for the job with the specified job ID.   

```matlab
% Get a handle to the cluster
c = parcluster;
% Find the old job
j = c.findJob(‘ID’, 4);
% Retrieve the state of the job
j.State
ans
    finished
% Fetch the results
j.fetchOutputs{:}
ans = 
    4.7270
```

The job now runs in 4.73 seconds using 8 workers.  Run the code with different number of workers to determine the ideal number to use.
Alternatively, to retrieve job results via a graphical user interface, use the Job Monitor (Parallel > Monitor Jobs).

![Code example](../../images/Parallel_interface.png "Start window") 

### Debugging

If a serial job produces an error, we can call the getDebugLog method to view the error log file.

```matlab
j.Parent.getDebugLog(j.Tasks(1))
```

When submitting independent jobs, with multiple tasks, you will have to specify the task number.  For Pool jobs, do not deference into the job object.

```matlab
j.Parent.getDebugLog(j)
```

The scheduler ID can be derived by calling schedID

```matlab
schedID(j)
ans
    25539
```

This is how the scheduler identifies the job.

## To learn more
To learn more about the MATLAB Parallel Computing Toolbox, check out these resources:

*	[Parallel Computing Coding Examples](http://www.mathworks.com/products/parallel-computing/code-examples.html)
*	[Parallel Computing Documentation](http://www.mathworks.com/help/distcomp/index.html)
*	[Parallel Computing Overview](http://www.mathworks.com/products/parallel-computing/index.html)
*	[Parallel Computing Tutorials](http://www.mathworks.com/products/parallel-computing/tutorials.html)
*	[Parallel Computing Videos](http://www.mathworks.com/products/parallel-computing/videos.html)
*	[Parallel Computing Webinars](http://www.mathworks.com/products/parallel-computing/webinars.html)