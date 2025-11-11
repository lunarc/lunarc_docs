
# Interactive Computing on LUNARC

Interactive sessions are essential for code testing, debugging, visualization, and other tasks that require direct user interaction with compute nodes. LUNARC offers two main ways to access interactive resources:

---

## Overview: Interactive Access Methods

| Method                      | Best For                        | How to Start                | Notes                                  |
|-----------------------------|---------------------------------|-----------------------------|----------------------------------------|
| **Desktop on Demand**       | GUI applications, visualization | Web portal (browser)        | Easiest for most users; graphical      |
| **interactive command**     | Command-line, scripting, testing| Terminal (SSH)              | Flexible, scriptable, CLI only         |

---

### 1. Desktop on Demand

The **LUNARC Desktop on Demand** provides a user-friendly graphical interface for launching interactive applications and terminals on the cluster. You can:
- Start graphical applications (e.g., MATLAB, JupyterLab)
- Open interactive terminals (CPU or GPU nodes)
- Monitor and manage your sessions

For a step-by-step guide, see the [Desktop on Demand user document](../getting_started/gfxlauncher.md).

!!! tip

    New users are encouraged to start with Desktop on Demand for most interactive needs.

### 2. The `interactive` Command

For advanced users or those who prefer the command line, the `interactive` command allows you to request resources and start a shell session on a compute node.

**Example:**
```bash
interactive -N 1 --ntasks-per-node=4 -t 60 -A lu-test
```
- `-N 1`: Number of nodes
- `--ntasks-per-node=4`: Number of cores per node
- `-t 60`: Time in minutes
- `-A lu-test`: Project allocation (replace with your project ID)

> **Note:** Replace `lu-test` with your actual project ID. Project IDs are typically of the form `lu20YY-X-Z`. If you only have one project, you may omit the `-A` option.

Your session will last until the requested time expires or you exit the shell. You are charged for the full wall time and resources reserved.

The `interactive` command supports most options available to `sbatch`. See the SLURM documentation or `man sbatch` for details.

## Environment Setup in Interactive Sessions

When starting an interactive session, **modules and environment variables may not be preserved** from your login shell. To ensure a clean environment:
- Run `module purge` to unload all modules
- Reload all required modules for your application

If your software requires specific environment variables or setup scripts, re-run those after starting the session.

!!! tip

    - If you encounter issues with missing software or libraries, check your loaded modules.
    - For graphical applications, ensure you have a working X11 setup (or use Desktop on Demand).
    - For more help, see the [FAQ](../manual/faq/manual_faq.md) or contact LUNARC support.


**Author:**  
LUNARC

**Last Updated:**  
2025-09-08

