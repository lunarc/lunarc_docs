# Rclone and Swestore

A powerful complement to the ARC clients is the **rclone** tool — a command-line tool supporting file transfers to and from a multitude of storage resources and protocols. It is something of a "Swiss Army knife of cloud storage".

Rclone can help with the following tasks:

- Backup (and encrypt) files to cloud storage
- Restore (and decrypt) files from cloud storage
- Mirror cloud data to other cloud services or locally

## Configure Rclone

To use rclone with Swestore it must first be configured. Run `rclone config` and follow the numbered steps:

```bash
rclone config
```

1. `n` for New remote
2. `swestore` for name
3. `webdav` for Storage (or the corresponding number in the list)
4. `https://webdav.swestore.se` for url
5. `other` for vendor (or the corresponding number in the list)
6. Press `Enter` for user (leave blank)
7. `n` for "No, leave this optional password blank"
8. Press `Enter` for bearer_token
9. `n` for "Edit advanced config?"
10. `y` if the resulting config looks correct, otherwise `e` to edit again
11. `q` to Quit config

The recommended way of using **rclone** with Swestore is with certificates and a proxy certificate. Specify the certificate locations on the command line:

```bash
rclone --client-cert=/tmp/x509up_uXXX --client-key=/tmp/x509up_uXXX
```

To avoid typing this every time, create a command alias:

```bash
echo 'alias rclone-cert="rclone --client-cert=/tmp/x509up_u${UID} --client-key=/tmp/x509up_u${UID}"' >> ~/.bashrc
```

The following examples use `rclone-cert` as the shorter alias.

## Listing directories and files

```bash
rclone-cert lsd swestore:/snic/storage-training
          -1 2022-02-14 16:44:22        -1 Course
          -1 2022-03-14 22:08:12        -1 newdir
```

```bash
rclone-cert ls swestore:/snic/storage-training
   116876 Course/apply_storage1.png
   131456 Course/apply_storage2.png
   139240 Course/apply_storage3.png
```

## Copying files

Copy a single file:

```bash
rclone-cert copyto IMG_E0868.JPG swestore:/snic/storage-training/IMG_E0868.JPG
```

Copy a directory recursively:

```bash
rclone-cert copy /home/bmjl/Courses/storage-training/examples swestore:/snic/storage-training/examples
```

Copy recursively with progress display:

```bash
rclone-cert copy -P /home/bmjl/Courses/storage-training/examples swestore:/snic/storage-training/examples
Transferred:      338.249 MiB / 1.894 GiB, 17%, 52.625 MiB/s, ETA 30s
Transferred:           75 / 407, 18%
Elapsed time:         6.5s
```

## File operations

Create a directory:

```bash
rclone-cert mkdir swestore:/snic/storage-training/examples/new_dir
```

Remove a file:

```bash
rclone-cert deletefile swestore:/snic/storage-training/examples/test.txt
```

Remove a directory:

```bash
rclone-cert rmdir swestore:/snic/storage-training/examples/new_dir/
```

Remove all files in a directory:

```bash
rclone-cert delete swestore:/snic/storage-training/examples/
```

---

**Author:**
(LUNARC)

**Last Updated:**
2022-10-05
