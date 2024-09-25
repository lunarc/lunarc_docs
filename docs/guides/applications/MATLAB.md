# MATLAB 

SNIC/NAISS provides a nationwide set of resources with the MATLAB Distributed Computing Server (MDCS) installed. At each respective NAISS centra the hosting university MATLAB licenses and toolboxes are available for use. 
 
The MATLAB versions available at LUNARC are:

```bash
Version        Module name

R2022a         matlab/2022a
R2023a         matlab/2023a
R2023b         matlab/2023b
```

## Load and Run MATLAB
### Running the GUI with Desktop On-Demand (Recommended)

The recommanded way to run MATLAB on cosmos is to log into the LUNARC HPC desktop with Thinlinc, go to the Applications menu, mouse over `Applications - MATLAB`, and click your preferred version. This will launch a GfxLauncher window where you can specify the resources you need to run the graphical user interface (GUI), like your choice of node(s), the number of tasks per node, the walltime, etc. Once you press `start`, the MATLAB GUI will automatically launch on a backend node with a graphical partition, and the GfxLauncher window in the background will show how much of your walltime has been used. With this method, there is no need to load any modules in a terminal.

Batch jobs and other parallelised scripts can be submitted to the slurm scheduler via the MATLAB GUI's Parallel Computing Toolbox once you have established a cluster profile (see the Configuration section). **The settings you enter for a cluster profile in the Parallel Computing Toolbox are distinct from those you set at the GfxLauncher for running the GUI.** Batch jobs are not bound by the parameters you set at the GfxLauncher. As long as you save your outputs to a directory with sufficient space, you are free to close the GUI and fetch your results later.

### Load and Run MATLAB in the terminal

If you want to use the command line for less interactive jobs or slurm scripts that call other programs besides MATLAB, you can choose to run MATLAB in a terminal instead of launching the GUI. **There are 2 options for starting a terminal session on the HPC desktop depending on which nodes you want to use:** the interactive terminal in `Applications` &rarr; `Applications - General`, which runs on the compute nodes, and the regular terminal that appears in `Favorites`, which runs on the login nodes. Regardless of which one you use, you will need to run these lines:

```bash
$ module load matlab/<version>
$ matlab -singleCompThread -nodisplay
```

and then you can submit scripts via the batch method of a cluster profile as discussed in the Configuration section below. The flag `-nodisplay` is required to keep the GUI from launching.

As MATLAB by default will use as many threads (cores) as it possibly can, any user who is to use MATLAB on the login-nodes **must start matlab with the option `-singleCompThread`** to prevent MATLAB from using more that one thread.
This will **not** prevent MATLAB from utilizing the MATLAB Distributed Computing Server (MDCS) with which any number of cores can be used for computations. You can also still send batch jobs on the local compute nodes this way if you correctly configure and instantiate a cluster profile, and call the batch method on that instance. 

#### Running MATLAB on login-node

MATLAB can also be used on the login nodes (either using LUNARC HPC Desktop, i.e. ThinLinc, or using SSH), and users can choose whether to open the GUI or stay within a terminal session. Please note that, as the login nodes are shared resources, excessive use of MATLAB on the login nodes will prevent other users from fully utilizing their requested resources. LUNARC administrators will kill any jobs associated with such misuse immediately upon identification and send a warning to the user afterward.

Users who want to run on the login nodes are advised to stick to the terminal interface (i.e. set `-nodisplay`) to help keep their resource usage low, and run most scripts via the cluster profile's batch method (discussed later in the next section) whenever possible. Scripts run outside of batch mode should be quick (<30 min) and strictly serial. Because it bears repeating, any user who plans to start MATLAB on the login nodes **MUST start matlab with the option `-singleCompThread`** to prevent MATLAB from using more than one thread on the login node's CPUs.

## Getting Started with Serial and Parallel MATLAB
### Cluster Configuration
There are two ways to configure MATLAB to run parallel jobs on your cluster depending on whether you want to run those jobs from the MATLAB GUI or from the command line. At the command line, users must call `configCluster` and set the parameters of the job with class methods of `ClusterInfo`. When running the GUI, users can go to a `Cluster Profile Manager` in `Parallel Settings` to set their job parameters and other constraints. Both ways are discussed in more detail below.

Either way, cluster profile configuration only needs to be done once per version of MATLAB you use, but you can save more than one profile for the same version if you want.

#### Configuration at the Command Line

Load and start MATLAB.

For each cluster, `configCluster` only needs to be called once per version of MATLAB. This cluster profile will be automatically saved and made available from then on every time you load that version of MATLAB. 

```matlab
configCluster
```

Jobs will now default to the cluster rather than submit to the local machine.

Prior to submitting the job, we can specify various parameters to pass to our jobs, such as queue, e-mail, etc.  
Specification is done with `ClusterInfo`.  The `ClusterInfo` class supports tab completion to ease recollection of method names.
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

Please note that as of version 2017a the syntax for setting the ClusterInfo parameters have changed. These are now properties of a `parcluster` object instead of methods in the `ClusterInfo` object.

```matlab
configCluster

% Set ProjectName and WallTime before submitting jobs
c = parcluster;
c.AdditionalProperties.ProjectName = 'project-name';
c.AdditionalProperties.WallTime = '01:00:00';
c.saveProfile
```

In the above example `c` is a `parcluster` object to which all properties are set accordingly.

To see the values of the current configuration options, call the state method.  To clear a value, assign the property an empty value (‘’, [], or false), or call the clear method to clear all values.

```matlab
% To view current configurations
ClusterInfo.state
% To clear a configuration that takes a string as an input argument
ClusterInfo.setEmailAddress(‘ ’)
% To clear all configurations
ClusterInfo.clear
```

#### Cluster Profiles in the GUI
The MATLAB GUI provides a way to set common batch job parameters and save them as one or more Cluster Profiles (`parcluster` objects) to load each time you load that version of MATLAB. These are distinct from the settings input into the GfxLauncher, which only apply to the MATLAB GUI itself, not to batch jobs submitted to SLURM within MATLAB.

From the main menu ribbon along the top, you can can either go to `Preferences` &rarr; `Parallel Computing Toolbox`, or `Parallel` &rarr; `Parallel Preferences`. Either way, the `Parallel Computing Toolbox Preferences` section of the pop-up will have a box where you can select a default profile if any profiles exist; otherwise, click `Cluster Profile Manager` to create one.

![Code example](../../images/Matlab-ParallelToolboxPrefs.png "Preferences") 

In the `Cluster Profile Manager`, the editor will let you set:
* the name of the profile
* the default number of workers (the total number of processes **minus 1**)
* the default working directory for the job
* your account name
* the required walltime
* the number of tasks per node
* the memory per node
* the number of GPUs per node (optional)
* the range of numbers of workers to allow
* whether to require an exclusive node (default is false)

Most familiar SBATCH parameters are set in a box near the bottom titled "Scheduler Plugin". At the very bottom, you can also pass important environmental variables to the workers. To check that these settings work, after saving your edits, you will need to run the `Validate` command in the Cluster Profile Manager's top toolbar. It is recommended that you leave the boxes checked that shut down and delete parallel pools that have been idle for 30 minutes.

![Code example](../../images/Matlab-ClusterMgr0.png "Cluster Profile Manager") 

In the `Parallel` drop-down menu, hovering over `Select Parallel Environment` will reveal a second drop-down menu where previously-saved profiles will appear under the `Cluster Tab`.

![Code example](../../images/Matlab-ParallelMenu.png "Parallel Menu") 

Now whenever you start a `parpool` instance (i.e. the template for a group of tasks to be run in parallel) or use the `'pool'` option with a cluster's `batch` method, it will be generated with your default cluster profile settings, or the settings for whichever cluster profile you select as the default for the current session, if you don't pass any arguments or let another function start it anonymously.

In the GUI, if you want to generate one or more `parpool` instances with different settings but without changing which cluster profile is the default, you pass override arguments to `parpool` to change the number of workers and/or select a different cluster for only that instance of `parpool`. Alternative clusters can be passed by handle or by the name (string) of the cluster as defined in the cluster profile manager. But don't forget that only 1 `parpool` instance can be active at a time, and the settings can't be changed once the pool instantiated. An existing pool must be deleted and a new one instantiated to get a pool with different settings. Below is an example of how you might start a parallel pool for a small interactive parallel job. Batch mode is discussed in the next section for larger jobs and is still generally preferred.

```matlab
% start a default pool and get handle
poolobj = parpool;

% Oops, that has N workers but we need 2N.
% Delete the old one
delete(poolobj)
% Start a new pool with 2N workers (let's say N was 7)
pool2n = parpool(15);
% ...do stuff...

% clean up
delete(pool2n)

% Now we want to use a different cluster
% get handle for some other cluster profile
c = parcluster;
% start a pool with this profile and 31 workers
pool4n = parpool(c, 31);
% ...continue doing stuff...
```

It is also important to remember that **the number of workers is 1 less than the total number of processes that will be spawned**, because MATLAB implicitly spawns one additional process to manage overhead for the others. Therefore, if you want to run a parallel job using 1 task per CPU on a node with 32 CPUs, you must specify 31 workers to stay within that node.

### Running Jobs
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

We’ll use the batch command again, but since we’re running a parallel job, we’ll also specify a MATLAB Parallel Pool.

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

Alternatively, to retrieve job results via the GUI, use the Job Monitor (`Parallel` &rarr; `Monitor Jobs`).

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

## Learn more
To learn more about the MATLAB Parallel Computing Toolbox, check out these resources:

*	[Parallel Computing Coding Examples](http://www.mathworks.com/products/parallel-computing/code-examples.html)
*	[Parallel Computing Documentation](http://www.mathworks.com/help/distcomp/index.html)
*	[Parallel Computing Overview](http://www.mathworks.com/products/parallel-computing/index.html)
*	[Parallel Computing Tutorials](http://www.mathworks.com/products/parallel-computing/tutorials.html)
*	[Parallel Computing Videos](http://www.mathworks.com/products/parallel-computing/videos.html)
*	[Parallel Computing Webinars](http://www.mathworks.com/products/parallel-computing/webinars.html)

---

**Author:**
(LUNARC)

**Last Updated:**
2024-09-23
