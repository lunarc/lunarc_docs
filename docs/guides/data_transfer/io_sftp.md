SFTP is a secure data transfer protocol provided by the SSH software. The protocol is very similar to FTP and uses many of the commands provided by an FTP server.

## Logging into an SFTP server

Login is accomplished by using the **sftp** command-line client, providing a username and a hostname as shown in the following example.

```bash
sftp bmjl@aurora.lunarc.lu.se
Password: 
Please enter your onetime password :207151
Connected to aurora.lunarc.lu.se.
sftp> 
```

**sftp>** is a prompt where commands can be entered initiating data transfers, listing files or creating directories.

## Listing remote files

Files on the remote server can be listed using the **ls** command as shown in the following example:

```bash
sftp> ls
imb                              imb_test                         
mpibench-1.1.tar.gz              mpibench-1.2                     
skampi                           skampi-5.0.4-r0355               
skampi-5.0.4-r0355.tar.gz 
```

## Listing files on the local computer

Listing files on the local computer is done using the **lls** command as shown in the following example:

```bash
sftp> lls 
Desktop				Pictures
Development			Public
Documents			PyQt-mac-gpl-4.8.5
Downloads			PyQt-mac-gpl-4.8.5.tar.gz
Dropbox				QtSDK
Library				sw_local_sbin.tar.gz
Movies				usercert.p12
Music
```

## Changing remote directory

Change working directory on the remote server is done using the **cd** command:

```bash
sftp> cd imb
sftp> ls
ReadMe_first    WINDOWS         doc             license         
src             versions_news 
```

## Changing working directory locally

Changing the working directory on the local computer is done using the **lcd** command:

```bash
sftp> lcd Documentation
sftp> lls
qmake.qch	qt.qch		qtcreator.qch	sdk.qch
```

## Transferring local file to remote server

A transfer to the remote server of a single file is done using the **put** commmand. **put** takes a single argument with a file in the current working directory to transfer (put) to the remote server as shown in the following example:

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

## Transferring a remote file to local computer

A file on the remote server is retrieved using the **get** command with the filename of the remote file. If no path is given the file is retrieved from the current working directory on the remote server. A remote path can also be prefixed on the filename.

```bash
sftp> get mpibench-1.1.tar.gz
Fetching /home/bmjl/openmpi/mpibench-1.1.tar.gz to mpibench-1.1.tar.gz
/home/bmjl/openmpi/mpibench-1.1 100%  235KB 235.0KB/s   00:01 
sftp> lls
Desktop				Pictures
...
Dropbox				QtSDK
Library				mpibench-1.1.tar.gz
Movies				sw_local_sbin.tar.gz
Music				usercert.p1
```

## Transferring multiple local files

To transfer multiple local files the **mput** command is used. This commmand takes a wild card pattern for selecting the files to transfer as shown in the following example:

```bash
sftp> mput *.txt
Uploading Changelog.txt to /home/bmjl/openmpi/Changelog.txt
Changelog.txt              100%   15KB  15.4KB/s   00:00    
Uploading InstallationLog.txt to /home/bmjl/openmpi/InstallationLog.txt
InstallationLog.txt        100%  452KB  90.5KB/s   00:05 
```

## Transferring multiple remote files

To retrieve multiple remote files the **mget** command is used. The files transferred is selected by given a wildcard pattern as shown in the example below:

```bash
sftp> mget *.tar.gz
Fetching /home/bmjl/openmpi/mpibench-1.1.tar.gz to mpibench-1.1.tar.gz
/home/bmjl/openmpi/mpibench-1.1 100%  235KB 235.0KB/s   00:01    
Fetching /home/bmjl/openmpi/skampi-5.0.4-r0355.tar.gz to skampi-5.0.4-r0355.tar.gz
/home/bmjl/openmpi/skampi-5.0.4 100%  377KB 377.1KB/s   00:00 
```

!!! Note

    Available commands in the SFTP client can be listed by using the help command on the **sftp>** prompt.