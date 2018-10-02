# Python

Python is available on the cluster. Depending on the users experience and needs, different versions are available. Below is an non-exhaustive list of installed versions:

        Anaconda2 
        Anaconda3
        Python
        Biopython
        
Information on these versions can be had using **module spider version**



## Recommended use

Depending on the needs of the user, the recommended versions differ.

For regular users with **no special requirements**, the recommended distribution is the Anaconda python. Anaconda is provided for both Python versions 2 and 3. This is also the recommended distribution for users who whish to use the interactive environment **spyder** 

Users who are planning to do **larger scale calculations** potentially using MPI4PY the recommended version is the Python distribution. Please note that this is the version requiring the foss-toolchain. i.e. do:
        
         module load foss/2017a
         module load Python

to get the default version of Python (typically 3.x). 
Please note that there are several versions of the foss and intel toolchains each with multiple versions of Python, please choose the one appropriate for you. Note that after having chosen your toolchain use **module avail Python** to see versions available.

For more information on available toolchains see [compiling-code-and-using-toolchains](http://lunarc-documentation.readthedocs.io/en/latest/aurora_modules/#compiling-code-and-using-toolchains)

For users working in **bioinformatics** the Biopython package is most likely the desired one.

Please note that there are several versions of the installed Python environments and that depending on your needs different versions may be suitable. 

## Packages

The installations have large set of packages installed. Users are encouraged to list the set of installed packages to verify that all needed packages are in place.

Anaconda users can check the installed packages with:

        conda list

All other installations are using the *pip* framework:

        pip list

## LUNARC installed packages

LUNARC staff will install packages in the various distributions (mainly in the non-anaconda based ones). If you have a packages that may be of use for other users, or if you are having trouble installing a package, please send a support request for the installation of the package and LUNARC staff will consider it for inclusion in the distribution. 

## User installed packages

Users are also able to install their own packages by creating their own local repository.

###Anaconda Python distributions
In **Anaconda** (available as a loadable module) the users are free to create their own environment. The user cen either use the pre-installed environment or create an empty environment and populate it with packages that the user chooses using *conda create* and *conda install*. Please review: <a target="_blank" href="https://conda.io/docs/user-guide/tasks/manage-environments.html"> Manage environments (new window)</a> for information on how to use environments in conda. 

**Please note:** The .conda directory where the installed-files are located may become large which may become a problem as the default location for this directory is in ***/home/< username >***. If space is a problem, users should consider using ***/lunarc/nobackup*** instead.

###Python installations
In all other installations (default or loadable module) users can install their own packages using *pip*

        pip install --install-option="--prefix=$HOME/local" package_name

This will install the package *package_name* in the users home-directory. If the package is large, consider using ***/lunarc/nobackup*** instead.  Make sure the installation location of your packages gets added to your **PYTHONPATH** environment variable (e.g. private module file, sourceing a script, ...)

Again, *please* check using *pip list* or *conda list* if the package is already installed in the selected distribution. 

