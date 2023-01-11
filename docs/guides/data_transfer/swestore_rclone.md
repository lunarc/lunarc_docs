# Rclone and Swestore

A powerful complement to the ARC Clients is the **rclone** tool. This is a command line tool that support tranferring files from a multitude of storage resources and protocols. It is somewhat a "swiss army knife of cloud storage".

Rclone can help with the following tasks (from the manual page):

 * Backup (and encrypt) files to cloud storage
 * Restore (and decrypt) files from cloud storage
 * Mirror cloud data to other cloud services or locally

## Configure Rclone

To be able to use rclone with Swestore is has to be configured. A special command **rclone config** can be used to create this configuration for us:

```bash
$ rclone config1. n for New remote
2. swestore for name
3. webdav for Storage (or the corresponding number in the list)
4. https://webdav.swestore.se for url
5. other for vendor (or the corresponding number in the list)
6. Just press <Enter> for user (ie. leave blank)
7. n for No leave this optional password blank
8. Just press <Enter> for bearer_token
9. n for Edit advanced config?
10. y if you think the resulting config is correct, otherwise e to edit again.
11. q to Quit config
```

The recommended way of using **rclone** with swestore is using certificates and a proxy certificate. Using command line option the certificate locations can be specified in the call to **rclone**:

```bash
$ rclone --client-cert=/tmp/x509up_uXXX --client-key=/tmp/x509up_uXXX
```

This is a bit cumbersome everytime you want to access the **rclone**-command. To fix this a command line alias can be created:

```bash
$ echo 'alias rclone-cert="rclone --client-cert=/tmp/x509up_u${UID} --client-key=/tmp/x509up_u${UID}"' >> ~/.bashrc
```

Instead of the long command we can now use the shorter command **rclone-cert**, which we will use in the following examples.

## Listing directories and files

Listing directories can be done using the **rclone-cert lsd** command:

```bash
$ rclone-cert lsd swestore:/snic/storage-training
          -1 2022-02-14 16:44:22        -1 Course
          -1 2022-03-14 22:08:12        -1 newdir
```

Files can be listed using 

```bash
$ rclone-cert ls swestore:/snic/storage-training
   116876 Course/apply_storage1.png
   131456 Course/apply_storage2.png
   139240 Course/apply_storage3.png
   152858 Course/apply_storage4.png
    89300 Course/apply_storage5.png
```

## Copying files

Copying a single file can be done using the following command:

```bash
$ rclone-cert copyto IMG_E0868.JPG swestore:/snic/storage-training/IMG_E0868.JPG
```

Recursive copying:

```bash
$ rclone-cert copy /home/bmjl/Courses/storage-training/examples swestore:/snic/storage-training/examples
```

Recursive copying with progress status:

```bash
rclone-cert copy -P /home/bmjl/Courses/storage-training/examples swestore:/snic/storage-training/examples
Transferred:      338.249 MiB / 1.894 GiB, 17%, 52.625 MiB/s, ETA 30s
Transferred:           75 / 407, 18%
Elapsed time:         6.5s
Transferring:
 *                          runsten/DJI_0051.JPG: 33% /4.505Mi, 0/s, -
 *                          runsten/DJI_0052.JPG:  6% /4.658Mi, 0/s, -
 *                          runsten/DJI_0053.JPG: 17% /4.747Mi, 0/s, -
 *                          runsten/DJI_0054.JPG:  7% /4.808Mi, 0/s, -
```

## File operations

Creating a directory:

```bash
$ rclone-cert mkdir swestore:/snic/storage-training/examples/new_dir
```

Removing a file:

```bash
$ rclone-cert deletefile swestore:/snic/storage-training/examples/test.txt
```

Removing a directory:

```bash
$ rclone-cert rmdir swestore:/snic/storage-training/examples/new_dir/
```

Removing all files in a directory:

```bash
$ rclone-cert delete swestore:/snic/storage-training/examples/
```
Removing all empty directories:

```bash
$ rclone-cert delete swestore:/snic/storage-training/examples/
```

