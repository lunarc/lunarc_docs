# Interactive node access

Sometimes it is desirable to have a direct interactive connection to a compute node — for example when testing or debugging code. Regular production runs, especially multi-day calculations, should use [`sbatch`](submitting_jobs/manual_basic_job.md) instead.

## Two ways of interactive computing

LUNARC is committed to interactive HPC and supports two approaches:

- The **LUNARC Desktop on Demand** provides a graphical interface with interactive access to compute resources. You can launch applications directly or open a command-line terminal on your chosen resource. Search for **Interactive Terminal** (CPU only) or **Accelerated Terminal** (GPU support) in the application menu. See the dedicated [Desktop on Demand guide](../getting_started/gfxlauncher.md).
- The **`interactive` command** — described on this page

We recommend the Desktop on Demand for most users, especially those new to LUNARC.

## Starting an interactive session

Use the `interactive` command to request resources and start a session. The following example requests 4 cores on 1 node for 60 minutes, charged to project `lu-test`:

```bash
interactive -N 1 --ntasks-per-node=4 -t 60 -A lu-test
```

!!! note "Replace `lu-test` with your project ID"
    `lu-test` is an administrator testing account. Use the ID of your own project allocation. Project IDs follow the pattern `lu20YY-X-Z` (year awarded, project type code, sequence number). If you belong to exactly one project you can omit `-A` entirely.

The session ends when the requested time expires or you type `exit`. Your account is charged for the full wall-clock duration regardless of how much computation you perform — 4 cores for 60 minutes costs 4 CPU-hours; 4 cores for 30 minutes costs 2 CPU-hours.

The `interactive` command accepts most `sbatch` options. See `man sbatch` for the full list.

## Modules and environment variables

Loaded modules and environment variables are not always exported correctly to the interactive session. Once inside the session, run:

```bash
module purge
```

Then reload all the modules you need from scratch — even if `module list` shows them as still loaded, they may not function correctly.

Also check that any required environment variables still hold the expected values. If your software uses a setup script, re-run it inside the session.

---

**Author:**
(LUNARC)

**Last Updated:**
2025-09-08
