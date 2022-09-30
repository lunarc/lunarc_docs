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

## File operations