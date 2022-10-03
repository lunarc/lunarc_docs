# Using ARC to access Swestore

All LUNARC resources have the ARC clients installed for accessing Swestore resources. Typically these tools can be used to automate storage workflows in jobs. As they authenticate using proxy certificates they can be used in batch scripts to automatically upload and download data from Swestore resources. In the following sections some of the ARC commands are illustrated by simple usage examples:

## Uploading single files

The **arccp** command is used to copy file to and from storage resources. Uploading single files are done by specifying a local file and a destination URL as in the following example:

```bash
$ arccp DJI_0001.JPG gsiftp://gsiftp.swestore.se/snic/storage-training/
$ arcls gsiftp://gsiftp.swestore.se/snic/storage-training/
Course
DJI_0001.JPG
```

## Uploading multiple files

Multiple files in directories can also be uploaded. The following commands uploads the file in the local **many_files/** directory to the remote directory **storage-training/**.

```bash
$ arccp --recursive=999 many_files/    gsiftp://gsiftp.swestore.se/snic/storage-training/
```

!!! info 

    The directory **many_files/** is not copied over.

To copy the **many_files/** directory the following command can be used instead:

```bash
$ arccp --recursive=999 --indicate many_files/ gsiftp://gsiftp.swestore.se/snic/storage-training/many_files/
|===============================================================||
    ...
|===============================================================-|
```

The **--indicate** command line option is used to display a progress indicator during long running transfers.

## Listing files

Listing files in remote location is done using the **arcls** command.

```bash
$ arcls gsiftp://gsiftp.swestore.se/snic/storage-training/Course
apply_storage1.png
apply_storage2.png
apply_storage3.png
apply_storage4.png
apply_storage5.png
```

Additional information can be displayed using the **--long** command line option.

```bash
$ arcls --long gsiftp://gsiftp.swestore.se/snic/storage-training/Course
<Name>            <Type>  <Size>     <Modified>      <CheckSum>        <Latency>
apply_storage1.png  file  116876 2022-02-14 15:44:21 adler32:e58f06bb      (n/a)
apply_storage2.png  file  131456 2022-02-14 15:44:21 adler32:06a7362d      (n/a)
apply_storage3.png  file  139240 2022-02-14 15:44:21 adler32:3f3b33ee      (n/a)
apply_storage4.png  file  152858 2022-02-14 15:44:22 adler32:fbc001dd      (n/a)
apply_storage5.png  file   89300 2022-02-14 15:44:22 adler32:474e75d8      (n/a)
```

## Downloading single files

Single files can be downloaded by specifying the direct URL to the file on Swestore as shown in the following example:

```bash
$ arccp gsiftp://gsiftp.swestore.se/snic/storage-training/DJI_0001.JPG myjpeg.jpg
```

## Downloading multiple files

Downloading multiple files is done in a similar way when uploading multiple files:

```bash
$ arccp --recursive=999 --indicate gsiftp://gsiftp.swestore.se/snic/storage-training/many_files/ downloaded_files/
10610 kB                    
33127 kB                    
4 kB                    
664 kB                    
15410 kB                    
8 kB                    
17216 kB                    
...
```

## Creating directories

Directories on remote resources can be created by the **arcmkdir** command as shown in the following example:

```bash
$ arcmkdir gsiftp://gsiftp.swestore.se/snic/storage-training/newdir
$ arcls gsiftp://gsiftp.swestore.se/snic/storage-training
Course
newdir
```

## Removing files and directories

Files can be removed from remote resources using the **arcrm**-command. A single file can be removed using the following command:

```bash
$ arcrm gsiftp://gsiftp.swestore.se/snic/storage-training/newdir/dummyfile
```

Directories can be removed using the following command (note the trailing slash):

```bash
$ arcrm gsiftp://gsiftp.swestore.se/snic/storage-training/newdir/
```

## Long running operations

Note that copying large directory trees can take quite some time, and might fail if you're not aware of the following:

 * Your login session created with the arcproxy command has a limited lifetime. Use arcproxy -I to show the remaining time. Use arcproxy -c validityPeriod=xxH to initiate a session with longer lifetime.

 * The command will abort if you lose your network connection with the computer where you are running arccp. A utility such as screen or tmux can be used to create a terminal session you can reattach to.

 * Transfer rates are largely dependent on the average file size, if you have a lot of small files the transfer will be slower than if you have large files.

 * We recommend to limit your transfer sessions (ie. the directory tree copied with each arccp command) to 1TB if you have mostly large (100+MB) files and to 100GB if you have smaller files.


