# Using software modules

On COSMOS, software is not available by default — it is provided through a *module system* that lets you load specific applications and versions on demand. This keeps the environment clean and avoids conflicts between software that requires different compiler versions or libraries.

This page covers the commands you need to find and load software. You will use these every time you run a job.

## Finding software

### Search by name

Use `module spider` to search for available software. The search is case-insensitive:

```bash
module spider python
```

This lists all installed versions of Python:

```text
----------------------------------------------------------------------------
  Python:
----------------------------------------------------------------------------
     Versions:
        Python/3.10.8-GCCcore-12.2.0
        Python/3.11.3-GCCcore-12.3.0
        Python/3.11.5-GCCcore-13.2.0
```

### Find out how to load a specific version

Running `module spider` with the full module name tells you which other modules must be loaded first:

```bash
module spider Python/3.11.5-GCCcore-13.2.0
```

```text
----------------------------------------------------------------------------
  Python: Python/3.11.5-GCCcore-13.2.0
----------------------------------------------------------------------------

    You will need to load all module(s) on any one of the lines below
    before the "Python/3.11.5-GCCcore-13.2.0" module is available to load.

      GCCcore/13.2.0
```

This two-step approach — `module spider <name>` then `module spider <name/version>` — is the standard way to find what you need on COSMOS.

!!! note "Why the two-step search?"
    COSMOS uses a hierarchical module system. Only a subset of modules is visible at login. Loading a compiler or toolchain unlocks further software built against it. `module spider` searches the entire tree, including modules not yet visible to you.

## Loading software

Load the prerequisite modules first, then the software you want:

```bash
module load GCCcore/13.2.0
module load Python/3.11.5-GCCcore-13.2.0
```

In many cases a toolchain module (such as `foss` or `intel`) will satisfy all prerequisites at once. `module spider` will tell you exactly what is required.

## Checking loaded modules

To see what you currently have loaded:

```bash
module list
```

## Unloading modules

To start fresh and remove all loaded modules:

```bash
module purge
```

This is good practice before loading a new set of modules, since switching between incompatible software environments can cause hard-to-diagnose errors.

## Using modules in job scripts

Modules loaded in your terminal are **not** automatically available on the compute node when a job runs. You must load them explicitly inside your job script:

```bash
#!/bin/bash
#SBATCH -t 01:00:00

module purge
module load GCCcore/13.2.0
module load Python/3.11.5-GCCcore-13.2.0

python my_analysis.py
```

Starting with `module purge` ensures your job always runs in a known, reproducible environment regardless of what you had loaded interactively.

## Browsing modules graphically

If you prefer a visual interface, there are two ways to browse available modules without using the command line.

**From the HPC Desktop menu:** Open **LUNARC Tools → LMOD Module Browser** in the application menu. This gives a point-and-click interface for searching and loading modules.

**From the terminal:** Run `ml-browse` to launch the same graphical browser directly from the command line:

```bash
ml-browse
```

Both options require the [LUNARC HPC Desktop](using_hpc_desktop.md) — they are not available over a plain SSH connection.

## Quick reference

| Command | What it does |
|---|---|
| `module spider <name>` | Search all available software by name |
| `module spider <name/version>` | Show prerequisites for a specific version |
| `module load <name/version>` | Load a module |
| `module list` | List currently loaded modules |
| `module purge` | Unload all modules |

## Software not available?

If `module spider` does not find what you need, you have several options: install it yourself with conda or pip, run it in a container, or request that LUNARC install it as a module. See [I cannot find my software — what are my options?](../manual/faq/manual_faq_software.md#i-cannot-find-my-software--what-are-my-options) in the FAQ for a full walkthrough.

## Further reading

The [Using installed software](../manual/manual_modules.md) page in the User Manual covers advanced topics including saving and restoring named module collections and browsing modules graphically with `ml-browse`.

---

**Author:**
(LUNARC)

**Last Updated:**
2026-03-30
