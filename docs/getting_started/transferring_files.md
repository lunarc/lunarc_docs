# Transferring files

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
