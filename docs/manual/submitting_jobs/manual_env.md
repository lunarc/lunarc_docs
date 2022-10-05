When submitting your job to SLURM using **sbatch**, your entire environment including the currently loaded modules gets copied. On Aurora, when hitting **sbatch**:

!!! info

    Make sure that the loaded modules and any environment variable you may have set will not be in conflict with the environment expected by the job script

## Compiler modules

On **Aurora** software modules are arranged in a **hierarchical module naming scheme**.  Accessing software on Aurora very different from earlier LUNARC systems and a [separate guide](http://lunarc-documentation.readthedocs.org/en/latest/aurora_modules/) is available. When compiling code using a [toolchain](http://lunarc-documentation.readthedocs.org/en/latest/aurora_modules/#compiling-code-and-using-toolchains) module is recommended.


## SLURM variables

*To come*

## SNIC variables

The SNIC meta-centres have agreed on a set of environment variables that should improve the portability of (parts of) job-scripts between SNIC sites. On Aurora the following variables are set by the system:

| Environment variable | Explanation | Value on Aurora |
|----------------------|-------------|-----------------|
| SNIC_SITE | Identifying the SNIC site you are using | lunarc |
| SNIC_RESOURCE | Identifying the compute resource you are using | aurora |
| SNIC_BACKUP | User directory which is: Regularly backed up against accidental deletion, typically extremely limited space, used for e.g. precious source code | `/home/<user>` |
| SNIC_NOBACKUP | User directory which is: Accessible on all LUNARC systems, for storing larger amounts of data, not backed up against accidental deletion   | `/lunarc/nobackup/users/<user>` |
| SNIC_TMP | Directory for best performance during a job.  At LUNARC: Local disk on nodes for storing temporary data during job execution. Transfer data with long-term value to SNIC_NOBACKUP before job has finished |  jobid dependent |

---

**Author:**
(LUNARC)

**Last Updated:**
2022-10-05