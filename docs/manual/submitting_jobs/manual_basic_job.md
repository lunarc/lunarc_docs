# A basic job

!!! info "Before you start"

    To run a job you will need:

    - Research data on the cluster
    - An application to run
    - Your login credentials
    - A text editor — `nano` is a good choice if you are new to the command line

    Make sure you are [logged in](../../getting_started/login_howto.md) before continuing. If you are new to SLURM, the [SLURM documentation](https://slurm.schedmd.com/) is a useful reference.

## The job script and sbatch

You submit a program to SLURM for execution using the `sbatch` command. The easiest way to do this is with a *job script* (also called a *job description file*).

A minimal job script looks like this:

```bash
#!/bin/bash
#SBATCH -t 00:05:00

echo "hello"
```

Write this to a file — in the examples below it is named `echo_script.sh`, but any name will do. Submit it with `sbatch`:

```bash
sbatch echo_script.sh
```

You should see output similar to:

```console
[fred@cosmos ~]$ sbatch echo_script.sh
Submitted batch job 7185
```

SLURM assigns the job a number (7185 here). Once the job has run, you will find a file named `slurm-7185.out` in your working directory containing the program's output and any error messages.

## The three parts of a job script

The `echo_script.sh` example shows the three sections every job script must have:

1. **Shell specification** — the `#!/bin/bash` line tells the system which shell to use
2. **Resource statement(s)** — lines starting with `#SBATCH` declare what resources the job needs; here `-t 00:05:00` requests 5 minutes of compute time. If the job has not finished by then, SLURM will terminate it.
3. **Body** — the UNIX commands to run; a job script is a normal shell script, so anything valid in bash is valid here

Real job scripts typically include several `#SBATCH` lines to specify things like the number of cores, memory, and a job name. These are covered in [Specifying job requirements](manual_specifying_requirements.md).

---

**Author:**
(LUNARC)

**Last Updated:**
2022-10-05
