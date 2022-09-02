# Wget and Curl

A common task when using HPC resources is to transfer data to the computing resource from external resources. It is of course possible to use a web browser to download files if you are using the LUNARC HPC Desktop, but this is often not possible if using a terminal or from a batch script. To accomplish this we can use the command line tools Wget or Curl instead. 

## Wget

Wget is an open-source tool for retrieving files from external sources supporting many different protocols. 

### Downloading a single file with Wget

In the following example the image from https://upload.wikimedia.org/wikipedia/commons/thumb/3/37/Grace_Hopper_and_UNIVAC.jpg/640px-Grace_Hopper_and_UNIVAC.jpg is downloaded to the current working directory:

```bash
$ wget https://upload.wikimedia.org/wikipedia/commons/3/37/Grace_Hopper_and_UNIVAC.jpg
```

Which will produce the following output:

```
--2022-06-01 12:00:36--  https://upload.wikimedia.org/wikipedia/commons/3/37/Grace_Hopper_and_UNIVAC.jpg
Slår upp upload.wikimedia.org (upload.wikimedia.org)... 91.198.174.208, 2620:0:862:ed1a::2:b
Ansluter till upload.wikimedia.org (upload.wikimedia.org)|91.198.174.208|:443... ansluten.
HTTP-begäran skickad, väntar på svar... 200 OK
Längd: 176587 (172K) [image/jpeg]
Sparar till: ‘Grace_Hopper_and_UNIVAC.jpg’

Grace_Hopper_and_UNIVAC.jp 100%[========================================>] 172,45K  --.-KB/s    om 0,09s

2022-06-01 12:00:36 (1,93 MB/s) - ‘Grace_Hopper_and_UNIVAC.jpg’ sparades [176587/176587]
```

### Downloading with a different filename - Wget

It is also possible to download the file using a different file name by using the **-O** option as shown in the following example:

```bash
$ wget https://upload.wikimedia.org/wikipedia/commons/3/37/Grace_Hopper_and_UNIVAC.jpg -O grace_hopper.jpg
```

Which will produce the following output:

```
--2022-06-01 12:01:15--  https://upload.wikimedia.org/wikipedia/commons/3/37/Grace_Hopper_and_UNIVAC.jpg
Slår upp upload.wikimedia.org (upload.wikimedia.org)... 91.198.174.208, 2620:0:862:ed1a::2:b
Ansluter till upload.wikimedia.org (upload.wikimedia.org)|91.198.174.208|:443... ansluten.
HTTP-begäran skickad, väntar på svar... 200 OK
Längd: 176587 (172K) [image/jpeg]
Sparar till: ‘grace_hopper.jpg’

grace_hopper.jpg           100%[========================================>] 172,45K  --.-KB/s    om 0,09s

2022-06-01 12:01:15 (1,87 MB/s) - ‘grace_hopper.jpg’ sparades [176587/176587]
```

### Downloading from a FTP server

Wget also supports downloading files from FTP-servers. Using this protocol it is also possible to download multiple files from a FTP-directory. In the following example we download all files in a remote FTP-directory:

```bash
$ mkdir space_images
$ cd space_images
$ wget ftp://ftp.sunet.se/mirror/archive/ftp.sunet.se/pub/pictures/space/*
```

Running these commands will produced the following output:

```
...

--2022-06-01 17:11:19--  ftp://ftp.sunet.se/mirror/archive/ftp.sunet.se/pub/pictures/space/space031.jpg
           => ‘space031.jpg’
==> CWD behövs inte.
==> PASV ... färdig.    ==> RETR space031.jpg ... färdig.
Längd: 139758 (136K)

space031.jpg               100%[========================================>] 136,48K  --.-KB/s    om 0,1s

2022-06-01 17:11:19 (1,40 MB/s) - ‘space031.jpg’ sparades [139758]

--2022-06-01 17:11:19--  ftp://ftp.sunet.se/mirror/archive/ftp.sunet.se/pub/pictures/space/surveyor.gif
           => ‘surveyor.gif’
==> CWD behövs inte.
==> PASV ... färdig.    ==> RETR surveyor.gif ... färdig.
Längd: 226324 (221K)

surveyor.gif               100%[========================================>] 221,02K  --.-KB/s    om 0,1s

2022-06-01 17:11:19 (1,85 MB/s) - ‘surveyor.gif’ sparades [226324]

...
```

All files have now been downloaded in the created directory.





