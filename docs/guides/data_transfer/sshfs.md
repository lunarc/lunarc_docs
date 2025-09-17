# SSHFS

SSHFS is a network filesystem that relies on SSH service to transfer files.
Once a volume is connected, the files in the remote system are available on the user's computer as if they were local files.
There is a wide variety of software available for [Windows, MacOS or Linux](http://pig.made-it.com/sshfs.html) able to connect a network volume via SSHFS.
Most Graphical User Interfaces to connect SSHFS volumes, mainly on Windows or MacOS, fail to comply with LUNARC's mandatory [Second Factor authentication](../../getting_started/login_howto.md#logging-in-using-one-time-passwords-otp).
This guides describes a method to connect a remote SSHFS volume using a linux terminal, even with LUNARC's second factor authentication.

## Connecting a SSHFS volume

Make sure the SSHFS client is installed on your system (Debian: `apt-get install sshfs`, Fedora: `dnf install fuse-sshfs`).
Create a directory in your home directory on your local (non-LUNARC) computer, where your files hosted at LUNARC will be accessible, for example `~/media/cosmos-home`.

```bash
username@localhost:~$ mkdir -p ~/media/cosmos-home
```

Use SSHFS to connect the directory at LUNARC, that you want accessible on your local computer.
Here we assume lunarc user `lunarc_username` to connect its home directory on COSMOS:


```bash
username@localhost:~$ sshfs lunarc_username@cosmos.lunarc.lu.se:/home/lunarc_username ~/media/cosmos-home
(lunarc_username@cosmos.lunarc.lu.se) Password: 
(lunarc_username@cosmos.lunarc.lu.se) Enter your Pocket Pass OTP: 248921
username@localhost:~$
```

Your LUNARC files are now accessible on your local computer:
```bash
username@localhost:~$ ls ~/media/cosmos-home/
 create                      slurm-6411487.out   slurm-6890513.out
 Desktop                     slurm-6411488.out   slurm-6890522.out
 dev                         slurm-6411489.out   slurm-6890523.out
 Documents                   slurm-6411504.out   slurm-6890525.out
 ...
```

## Disconnecting a SSHFS volume

This example assumes the LUNARC directory connected in the example described in section "[Connecting a SSHFS volume](#connecting-a-sshfs-volume)".
Use `fusermount`'s option `-u` to disconnect the remote volume:

```bash
lunarc_username@localhost:~$ fusermount -u ~/media/cosmos-home
lunarc_username@localhost:~$ 
```

**Author:**
(LUNARC)

**Last Updated:**
2023-01-20
