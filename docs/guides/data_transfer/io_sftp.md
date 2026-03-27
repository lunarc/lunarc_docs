# SFTP

SFTP is a secure data transfer protocol provided by the SSH software. The protocol is very similar to FTP and uses many of the same commands.

## Logging into an SFTP server

Login is accomplished using the `sftp` command-line client, providing a username and hostname:

```bash
sftp bmjl@cosmos.lunarc.lu.se
Password:
Enter your Pocket Pass OTP: 207151
Connected to bmjl@cosmos.lunarc.lu.se.
sftp>
```

`sftp>` is the prompt where commands can be entered to initiate data transfers, list files, or create directories.

Use the [Pocket Pass app](../../getting_started/authenticator_howto.md) on your smartphone to obtain the **Pocket Pass OTP**.

## Listing remote files

Files on the remote server can be listed using the `ls` command:

```bash
sftp> ls
imb                              imb_test
mpibench-1.1.tar.gz              mpibench-1.2
skampi                           skampi-5.0.4-r0355
skampi-5.0.4-r0355.tar.gz
```

## Listing files on the local computer

Listing files on the local computer is done using the `lls` command:

```bash
sftp> lls
Desktop         Pictures
Development     Public
Documents       Downloads
Dropbox         Library
Movies          Music
```

## Changing remote directory

Change the working directory on the remote server using the `cd` command:

```bash
sftp> cd imb
sftp> ls
ReadMe_first    WINDOWS         doc             license
src             versions_news
```

## Changing working directory locally

Changing the working directory on the local computer is done using the `lcd` command:

```bash
sftp> lcd Documentation
sftp> lls
qmake.qch    qt.qch    qtcreator.qch    sdk.qch
```

## Transferring a local file to the remote server

A single file is transferred to the remote server using the `put` command:

```bash
sftp> put PyQt-mac-gpl-4.8.5.tar.gz
Uploading PyQt-mac-gpl-4.8.5.tar.gz to /home/bmjl/openmpi/PyQt-mac-gpl-4.8.5.tar.gz
PyQt-mac-gpl-4.8.5.tar.gz   27% 2592KB 918.4KB/s   00:07 ET

sftp> ls
PyQt-mac-gpl-4.8.5.tar.gz        imb
imb_test                         mpibench-1.1.tar.gz
mpibench-1.2                     skampi
skampi-5.0.4-r0355               skampi-5.0.4-r0355.tar.gz
```

## Transferring a remote file to the local computer

A file on the remote server is retrieved using the `get` command:

```bash
sftp> get mpibench-1.1.tar.gz
Fetching /home/bmjl/openmpi/mpibench-1.1.tar.gz to mpibench-1.1.tar.gz
/home/bmjl/openmpi/mpibench-1.1 100%  235KB 235.0KB/s   00:01
sftp> lls
Desktop         Pictures
...
Library         mpibench-1.1.tar.gz
```

## Transferring multiple local files

To transfer multiple local files use the `mput` command with a wildcard pattern:

```bash
sftp> mput *.txt
Uploading Changelog.txt to /home/bmjl/openmpi/Changelog.txt
Changelog.txt              100%   15KB  15.4KB/s   00:00
Uploading InstallationLog.txt to /home/bmjl/openmpi/InstallationLog.txt
InstallationLog.txt        100%  452KB  90.5KB/s   00:05
```

## Transferring multiple remote files

To retrieve multiple remote files use the `mget` command with a wildcard pattern:

```bash
sftp> mget *.tar.gz
Fetching /home/bmjl/openmpi/mpibench-1.1.tar.gz to mpibench-1.1.tar.gz
/home/bmjl/openmpi/mpibench-1.1 100%  235KB 235.0KB/s   00:01
Fetching /home/bmjl/openmpi/skampi-5.0.4-r0355.tar.gz to skampi-5.0.4-r0355.tar.gz
/home/bmjl/openmpi/skampi-5.0.4 100%  377KB 377.1KB/s   00:00
```

!!! info
    Available commands in the SFTP client can be listed by typing `help` at the `sftp>` prompt.

---

**Author:**
(LUNARC)

**Last Updated:**
2022-10-05
