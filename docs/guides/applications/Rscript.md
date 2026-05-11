# R Software environment

R is a widely used software environment with a focus on statistical computing and graphics.

## Finding available R versions

Information on available R versions can be obtained with the `module spider` command:

```bash
module spider R
```

which will produce output similar to:

```text
----------------------------------------------------------------------------
  R:
----------------------------------------------------------------------------
    Description:
      R is a free software environment for statistical computing and
      graphics.

     Versions:
        R/4.1.2
        R/4.2.1
        ...
```

!!! note
    The list of available versions changes over time. Run `module spider R` on COSMOS to see the currently installed versions.

Select the version that best fits your needs and run `module spider` again for details on that specific version. For example, for `R/4.1.2`:

```bash
module spider R/4.1.2
```

This gives fairly extensive output:

```text
----------------------------------------------------------------------------
  R: R/4.1.2
----------------------------------------------------------------------------
    Description:
      R is a free software environment for statistical computing and
      graphics.

    You will need to load all module(s) on any one of the lines below before
    the "R/4.1.2" module is available to load.

      GCC/11.2.0  OpenMPI/4.1.1

    Help:
      Homepage: https://www.r-project.org/
```

The key information is the list of prerequisite modules. In this example you need to load `GCC/11.2.0` and `OpenMPI/4.1.1` before being able to load R.

## Making R available and starting R

Once you have selected your version of R, load the prerequisites and then the R module. For R 4.1.2:

```bash
module load GCC/11.2.0 OpenMPI/4.1.1
module load R/4.1.2
```

Now start R using the `R` command on the command line or inside a **batch script**.

!!! note
    Resource intensive R jobs (compute time and/or memory consumption) have to use the compute nodes via the [batch scheduler](../../manual/submitting_jobs/manual_basic_job.md). Such jobs must not utilise login or frontend nodes.

## Installing and using R extensions in your home space

Inside R you can use `library()` to display available extensions. If a required extension is not available, you can install it in your own disk space. Create a directory in **your home space** — for example `~/MyRextensions`. This only needs to be done once:

```bash
mkdir ~/MyRextensions
```

Once the directory has been created, use `.libPaths()` inside your R session to make R functions such as `library()` and `install.packages()` use this directory:

```r
.libPaths(c('~/MyRextensions', .libPaths()))
```

It is important that `MyRextensions` is the first argument. After this, any call to `install.packages()` will install extensions into that directory. For example, to install the **geosphere** extension:

```r
install.packages('geosphere')
```

### Example R session to install an extension

A session to install **geosphere** into `~/MyRextensions` may look as follows:

```text
-bash-4.2$ R

R version 4.2.1 (2022-06-23) -- "Funny-Looking Kid"
Copyright (C) 2022 The R Foundation for Statistical Computing
Platform: x86_64-pc-linux-gnu (64-bit)

R is free software and comes with ABSOLUTELY NO WARRANTY.
You are welcome to redistribute it under certain conditions.
Type 'license()' or 'licence()' for distribution details.

Type 'demo()' for some demos, 'help()' for on-line help, or
'help.start()' for an HTML browser interface to help.
Type 'q()' to quit R.

> .libPaths(c('~/MyRextensions', .libPaths()))
> .libPaths()
[1] "/home/jhein/MyRextensions"
[2] "/sw/easybuild/software/R/4.2.1-foss-2022a/lib64/R/library"
> install.packages('geosphere')
...
> quit()
Save workspace image? [y/n/c]: n
```

The `install.packages` call will produce quite some output and ask you to select a CRAN mirror, which is omitted here. Note that the `.libPaths()` call is not necessary to the build; it just prints your library path so you can confirm that your personal extension directory has been added.

### Using a user installed package

To use a package installed in your own space you have to tell R where to look for it.

#### Modifying your R scripts

Add `.libPaths()` at the top of your R script:

```r
.libPaths(c('~/MyRextensions', .libPaths()))
library('geosphere')
```

#### Using the environment variable R_LIBS

An alternative is to set the `R_LIBS` environment variable inside your [SLURM submission script](../../manual/submitting_jobs/manual_basic_job.md) before starting R:

```bash
export R_LIBS=~/MyRextensions
```

---

**Author:**
Joachim Hein

**Last Updated:**
2023-01-20
