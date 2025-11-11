
# Introduction to the LUNARC User Manual

Welcome to the LUNARC User Manual! This guide is designed to help new and experienced users make the most of LUNARC's High Performance Computing (HPC) resources.

Efficient management of compute resources is essential for successful HPC usage. LUNARC uses [SLURM](http://slurm.schedmd.com/) (**S**imple **L**inux **U**tility for **R**esource **M**anagement) as its job scheduler and resource manager. To run your programs, you will need to describe to SLURM:
- The resources your program requires (cores, memory, time, etc.)
- The program or script to execute
- Any command-line arguments your program needs

SLURM also provides tools for monitoring and managing your jobs during execution.

## How to Use This Manual

This manual is organized into several key sections:

1. **Job Submission**  
	Learn how to submit jobs to the cluster, including resource requests and job options.

2. **Example Scripts**  
	Find ready-to-use scripts for common use cases, such as serial and parallel jobs.

3. **Modules and Software**  
	Understand how to load and manage software environments using the module system.

4. **Interactive Computing**  
	Explore options for interactive sessions, including the Desktop on Demand and command-line access.

5. **Quick Reference & Glossary**  
	Access command summaries and definitions of common HPC terms.

!!! tip

    If you are new to LUNARC or HPC, start with the Job Submission and Example Scripts sections.


## Typical User Workflow

Below is a typical workflow for running a job on LUNARC (consider adding a diagram here):

1. **Connect** to the cluster (via SSH or Desktop on Demand)
2. **Prepare** your code and input data
3. **Load** required software modules
4. **Write** a job script describing your resource needs
5. **Submit** the job script to SLURM
6. **Monitor** your job's progress
7. **Collect** your results

---

**Author:**  
LUNARC

**Last Updated:**  
2022-10-05
