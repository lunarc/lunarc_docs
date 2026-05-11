# Transferring files

## Where to put your files

Before transferring anything, it helps to know what storage is available and what each location is for.

| Location | Path | Purpose | Persists after job? |
|---|---|---|---|
| Home directory | `$HOME` | Code, scripts, job scripts, results | Yes |
| Project storage | `/lunarc/nobackup/projects/<project>` | Shared storage for larger datasets, shared within a project group | Yes |
| Local scratch | `$SNIC_TMP` | Fast temporary storage on the compute node during a job | No — deleted when job ends |
| Swestore | — | Long-term archival of research data | Yes (separate service) |

**Home directory (`$HOME`)** is your main working area. It is backed up via snapshots — if you accidentally delete a file, run `ls .snapshots` in the affected directory to browse restore points. Your quota is displayed at login; run `snicquota` at any time to check current usage. Quota increases are available on request.

**Project storage (`/lunarc/nobackup/projects`)** is a larger shared area suitable for datasets and other files that need to be accessible to all members of a project group. Storage here is allocated per project and must be applied for separately through [SUPR](https://supr.naiss.se/). If your project has been granted storage, the path will be `/lunarc/nobackup/projects/<project-name>`. Note that this area is not snapshotted — deleted files cannot be recovered.

**Local scratch (`$SNIC_TMP`)** is a fast per-node disk available only while a job is running. Use it for heavy read/write workloads within a job to avoid putting load on the shared filesystem. Do not transfer files from your laptop here — it is not accessible from the login node. See [Using local disk to improve I/O performance](../manual/submitting_jobs/manual_local_disk.md) for details.

**Swestore** is NAISS's long-term storage service, separate from COSMOS. Use it to archive data that does not need to be on the cluster permanently. See the [Swestore guides](../guides/data_transfer/swestore_mounting.md) for access options.

!!! note
    The old `/lunarc/nobackup/users` personal storage area has been decommissioned. If you see references to it in older guides or scripts, use `$HOME` instead.

---

## Transferring files to and from COSMOS

There are several ways to move files between your local machine and COSMOS. The right choice depends on your operating system and workflow.

## Quick guide by use case

| I want to... | Recommended tool |
| --- | --- |
| Transfer files from Windows | [WinSCP](../guides/data_transfer/io_winscp.md) |
| Transfer files from Linux or macOS | [SFTP](../guides/data_transfer/io_sftp.md) |
| Mount COSMOS as a remote drive | [SSHFS](../guides/data_transfer/sshfs.md) |
| Download files from the web directly to COSMOS | [wget / curl](../guides/data_transfer/io_wget_curl.md) |
| Archive data to long-term storage (Swestore) | [Swestore guides](../guides/data_transfer/swestore_mounting.md) |

## SFTP (Linux and macOS)

SFTP is the standard secure file transfer method on Linux and macOS. It runs over SSH, so no extra setup is needed beyond your normal login credentials.

[SFTP guide](../guides/data_transfer/io_sftp.md){ .md-button }

## WinSCP (Windows)

WinSCP provides a graphical drag-and-drop interface for Windows users. It uses SFTP by default and works with LUNARC's two-factor authentication.

[WinSCP guide](../guides/data_transfer/io_winscp.md){ .md-button }

## SSHFS (mount as a drive)

SSHFS mounts your COSMOS home directory as a local filesystem, letting you work with remote files directly in your file manager or editor without manually copying them.

[SSHFS guide](../guides/data_transfer/sshfs.md){ .md-button }

## wget and curl (download directly to COSMOS)

If you need to fetch data from the internet directly to the cluster — for example a dataset or software archive — `wget` and `curl` let you do this from a COSMOS terminal without routing the file through your laptop.

[wget and curl guide](../guides/data_transfer/io_wget_curl.md){ .md-button }

## Swestore (long-term storage)

Swestore is NAISS's long-term data storage service. Use it to archive research data that does not need to be on COSMOS permanently.

[Swestore guides](../guides/data_transfer/swestore_mounting.md){ .md-button }

---

**Last Updated:**
2022-10-05
