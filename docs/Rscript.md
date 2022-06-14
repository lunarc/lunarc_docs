# R @ LUNARC
Author: Joachim Hein

## Overview

R is a widely used software environment with a focus on statistical computing and graphics. 

## Finding available R versions

Information on available R versions installed can be obtained with the `module spider` command on the LINUX command line:

```
module spider R
```

which will get you an output similar to:

```
----------------------------------------------------------------------------
  R:
----------------------------------------------------------------------------
    Description:
      R is a free software environment for statistical computing and
      graphics.

     Versions:
        R/3.2.1-bare
        R/3.2.1
        R/3.2.3-libX11-1.6.3
        R/3.2.3
        R/3.3.1
        R/3.3.2-libX11-1.6.3
        R/3.4.0-X11-20170314
        R/3.4.3-X11-20171023
        R/3.5.0-X11-20180131
        R/3.5.1-Python-2.7.15
        R/3.5.1
        R/3.6.0
        R/3.6.2
        R/3.6.3
        R/4.0.0
        R/4.1.0
        R/4.1.2
```

Select the version that best fits your need and run `module spider` again to ask for detail on this specific version.  So if we want to run `R/4.1.2` we run (cut-n-paste is your friend):

```
module spider  R/4.1.2
```

This will give faily extensive output:

```
----------------------------------------------------------------------------
  R: R/4.1.2
----------------------------------------------------------------------------
    Description:
      R is a free software environment for statistical computing and
      graphics. 


    You will need to load all module(s) on any one of the lines below before the "R/4.1.2" module is available to load.

      GCC/11.2.0  OpenMPI/4.1.1
 
    Help:
      
      Description
      ===========
      R is a free software environment for statistical computing
       and graphics.
      
      
      More information
      ================
       - Homepage: https://www.r-project.org/
      
      
      Included extensions
      ===================
      abc-2.1, abc.data-1.0, abe-3.0.1, abind-1.4-5, acepack-1.4.1, adabag-4.2,
      ade4-1.7-18, ADGofTest-0.3, aggregation-1.0.1, AICcmodavg-2.3-1,
      akima-0.6-2.2, alabama-2015.3-1, AlgDesign-1.2.0, AnalyzeFMRI-1.1-24,
      animation-2.7, aod-1.3.1, ape-5.5, argparse-2.1.2, arm-1.12-2, askpass-1.1,
      asnipe-1.1.16, assertive-0.3-6, assertive.base-0.0-9, assertive.code-0.0-3,
      assertive.data-0.0-3, assertive.data.uk-0.0-2, assertive.data.us-0.0-2,
      assertive.datetimes-0.0-3, assertive.files-0.0-2, assertive.matrices-0.0-2,
      assertive.models-0.0-2, assertive.numbers-0.0-2, assertive.properties-0.0-4,
      assertive.reflection-0.0-5, assertive.sets-0.0-3, assertive.strings-0.0-3,
      assertive.types-0.0-3, assertthat-0.2.1, AUC-0.3.0, audio-0.1-8, aws-2.5-1,
      awsMethods-1.1-1, b-a, backports-1.3.0, bacr-1.0.1, bartMachine-1.2.6,
      ...
```

The key information here is a list of prerequisite modules and a list of included R extensions.  In this example you need to load the modules `GCC/11.2.0`and `OpenMPI/4.1.1` before being able to load R.

## Making R available and starting R
Once you have selected "your" version of R it is now time to load the R-module.   Staying with our example of R 4.1.2, we have found out that it requires `GCC/11.2.0` and `OpenMPI/4.1.1`.   We load these and then load the R-module from the command line:

```
module load GCC/11.2.0  OpenMPI/4.1.1
module load R/4.1.2
```

Now we can start R using the `R` command on the commandline.

## Installing and using your own R extensions



