# User Manual

LUNARC uses [SLURM](https://slurm.schedmd.com/) (**S**imple **L**inux **U**tility for **R**esource **M**anagement) to manage compute resources on COSMOS. To run a program you describe its resource requirements to SLURM via a job script, and SLURM schedules and executes it on the cluster on your behalf.

## What this manual covers

### Submitting jobs

How to write job scripts and submit them with `sbatch`, from a minimal single-core job through to multi-node MPI jobs. Covers resource requests, the job execution environment, and how to monitor and control running jobs.

[Start here: A basic job](submitting_jobs/manual_basic_job.md){ .md-button }

### Example job scripts

Ready-to-use scripts for the most common use cases — serial jobs, MPI, OpenMP, and Python with MPI4Py. A good starting point when building your own submission scripts.

[Browse example scripts](example_job_scripts/manual_example_basic_serial.md){ .md-button }

### Interactive access

How to run interactive work on a compute node rather than the login node, using the Desktop on Demand.

[Interactive node access](manual_interactive.md){ .md-button }

### Software modules

How to find, load, and manage software using the modules system, including compiler toolchains for building your own code.

[Using installed software](manual_modules.md){ .md-button }

### Quick reference

A compact summary of the most commonly used SLURM directives, module commands, and filesystem paths.

[Quick reference](manual_quick_reference.md){ .md-button }

### FAQ

Answers to common questions about login, files, jobs, and software.

[FAQ](faq/manual_faq_login.md){ .md-button }

---

**Author:**
(LUNARC)

**Last Updated:**
2022-10-05
