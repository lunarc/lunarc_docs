# Before you run

Naive use of HPC resources can easily lead to mistakes that disrupt other users. Before running anything, it is worth familiarising yourself with the [commonly used phrases and terms](../manual/manual_common_phrases.md) in HPC — the glossary explains concepts like nodes, cores, walltime, partitions, and SLURM in plain language. [The HPC Wiki](https://hpc-wiki.info/hpc/Category:Basics) is also a good general reference.

## The login node and compute nodes

When you connect to COSMOS via SSH or the HPC Desktop, you land on the **login node** (also called the frontend). This is a single shared machine used by every user on the cluster simultaneously — for editing files, writing job scripts, transferring data, and managing jobs.

The **compute nodes** are the machines that do the actual work. COSMOS has hundreds of them, each with 48 or 32 CPU cores and up to 256 GB of memory. You do not log into compute nodes directly — instead, you describe what your job needs in a job script and SLURM allocates dedicated compute nodes for it.

| | Login node | Compute nodes |
|---|---|---|
| How you access it | SSH / HPC Desktop | Via SLURM (`sbatch`, `interactive`) |
| Who else is using it | All users at once | Only your job |
| Resources | Limited, shared | Dedicated to your job |
| What it is for | Editing, submitting jobs, small tasks | Running your actual computations |

## Don't clog the login node

The most disruptive mistake a new HPC user can make is to run resource-intensive code on the login node — directly at the command line or through a user interface such as JupyterLab. Because the login node is shared, a demanding program running there will slow down or stall every other user on the system.

For any computation worth running on an HPC cluster, **you must run it on a compute node through SLURM**. You have two options:

* Create a job script and submit it with **sbatch**. See [Your first job](../first_job) for a step-by-step walkthrough.
* For interactive work, use the Desktop on Demand. See [On-demand applications](../gfxlauncher) for details.

Remember that repeated disruptive misuse of the login node is a violation of the terms and conditions of your NAISS user agreement, and may result in the suspension or termination of your account.

## Training through NAISS

LUNARC and NAISS offer a variety of training and development courses, including several recurring workshops targetting users new to high performance computing. We highly recommend that all new users check on SUPR that they are signed up for the NAISS Training Newsletter. To check your subscription status, log into SUPR and click the Groups tab. If you are signed up for the newletter (which typically happens automatically upon creating an account), it will appear under the heading "Group You Manage or Belong To". Otherwise, you should be able to find "NAISS-training-subscribe" under the "Groups You Can Join" heading.

You can also find the list of upcoming events on the [LUNARC website under "Learning More".](https://www.lunarc.lu.se/learning-more/training-courses/)

---

**Author:**
(LUNARC)

**Last Updated:**
2023-12-08
