When submitting your job to SLURM using `sbatch`, your entire environment gets copied, including the currently loaded modules. On COSMOS, when hitting `sbatch`:

!!! info

    Make sure that the loaded modules and any environment variable you may have set will not be in conflict with the environment expected by the job script

## Compiler modules

On **COSMOS** software modules are arranged in a **hierarchical module naming scheme**.  Accessing software on COSMOS very different from earlier LUNARC systems and a [separate guide](http://lunarc-documentation.readthedocs.org/en/latest/aurora_modules/) is available. When compiling code using a [toolchain](http://lunarc-documentation.readthedocs.org/en/latest/aurora_modules/#compiling-code-and-using-toolchains) module is recommended.


## SLURM variables

*To come*

## SNIC variables

The SNIC meta-centres had agreed on a set of environment variables that should improve the portability of (parts of) job-scripts between SNIC sites. On COSMOS the following variables are set by the system:

| Environment variable | Explanation | Value on COSMOS |
|----------------------|-------------|-----------------|
| SNIC_TMP | Directory for best performance during a job.  At LUNARC: Local disk on nodes for storing temporary data during job execution. Transfer data with long-term value to permanent storage before job has finished |  jobid dependent |

---

**Author:**
(LUNARC)

**Last Updated:**
2022-07-27