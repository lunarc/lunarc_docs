Sometimes it is desirable to have an interactive connection to the compute nodes of the cluster. Extensive code testing and debugging are typical use cases. Regular production runs, in particular calculations requiring multiple days, are expected to access the compute nodes via the [**sbatch**](../submitting_jobs/manual_basic_job) command.

## Two ways of interactive computing
LUNARC is commited to interactive HPC.  We currently support two ways of interactive resource access:

* The **LUNARC Desktop on Demand** offers an easy to use graphical interface, which provides interactive access to compute resources.  One can choose from a large number of application or just start a command line terminal on the choosen resources.  If you want a terminal on your choosen resource, seach for **Interactive Terminal** (CPU only) or **Accelerated Terminal** (GPU support).  Both terminals allow users to easily start graphical applications on the command line.  There is a dedicated [user document](../../getting_started/gfxlauncher) on the Desktop on Demand.
* The **interactive** command described in this document

We expect most users, in particular new users of our services to be better served by the [Desktop on Demand](../../getting_started/gfxlauncher).


## Starting an interactive session

To start an interactive session you need to use the **interactive** command. This will request the required resources from the resource pool for you and start the interactive session once the resources are available.

Use the following command to start an interactive session asking for 4 cores on 1 node lasting 60 minutes

    interactive -N 1 --ntasks-per-node=4 -t 60

The interactive session will last until either the requested time, 60 minutes in the above example, has expired or you manually exit the interactive shell. Your account gets charged with the wall time duration of your interactive session, independent of the amount of computation you do. In the above example, if your session lasts until it expires after 60 min, you get charged for 4 CPU hours. If you terminate your session after 1/2 hour, you would get charged 2 CPU hours.

The interactive command supports most command-line options of the **sbatch** command. Please refer to the man pages of **sbatch**.

## Modules and environment variables

Loaded modules and environment variables are not always exported properly to your interactive session. Once placed in the interactive session, we recommend users

 * To remove all currently loaded modules by using the **module purge** command
 * To reload **all** the modules they require

That is despite the **modules list** command claiming they are still loaded.

You also need to check whether environment variables still have the required values. If the software you are using has a set-up script, you might need to re-run that script.

---

**Author:**
(LUNARC)

**Last Updated:**
2024-08-08

