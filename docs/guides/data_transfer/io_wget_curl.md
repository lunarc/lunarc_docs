# Wget and Curl

A common task when using HPC resources is to transfer data from external sources to the computing resource. While a web browser can be used if you are on the LUNARC HPC Desktop, this is not possible from a terminal or inside a batch script. The command-line tools `wget` and `curl` serve this purpose.

## Wget

Wget is an open-source tool for retrieving files from external sources, supporting HTTP, HTTPS, and FTP protocols.

### Downloading a single file

The following example downloads an image to the current working directory:

```bash
wget https://upload.wikimedia.org/wikipedia/commons/3/37/Grace_Hopper_and_UNIVAC.jpg
```

Output will look similar to:

```text
--2022-06-01 12:00:36--  https://upload.wikimedia.org/wikipedia/commons/3/37/Grace_Hopper_and_UNIVAC.jpg
Resolving upload.wikimedia.org... 91.198.174.208
Connecting to upload.wikimedia.org|91.198.174.208|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 176587 (172K) [image/jpeg]
Saving to: 'Grace_Hopper_and_UNIVAC.jpg'

Grace_Hopper_and_UNIVAC.jpg  100%[========================================>] 172.45K  1.93 MB/s  in 0.09s

2022-06-01 12:00:36 (1.93 MB/s) - 'Grace_Hopper_and_UNIVAC.jpg' saved [176587/176587]
```

### Downloading with a different filename

Use the `-O` option to save under a different name:

```bash
wget https://upload.wikimedia.org/wikipedia/commons/3/37/Grace_Hopper_and_UNIVAC.jpg -O grace_hopper.jpg
```

### Downloading from an FTP server

Wget also supports FTP, including downloading all files in a directory:

```bash
mkdir space_images
cd space_images
wget ftp://ftp.example.se/pub/pictures/space/*
```

## Curl

Curl is another widely used tool for transferring data supporting many protocols including HTTP, HTTPS, FTP, and SCP.

### Downloading a single file with curl

```bash
curl -O https://upload.wikimedia.org/wikipedia/commons/3/37/Grace_Hopper_and_UNIVAC.jpg
```

The `-O` flag saves the file using the remote filename. Output looks similar to:

```text
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100  172K  100  172K    0     0  1823k      0 --:--:-- --:--:-- --:--:-- 1840k
```

### Saving to a custom filename with curl

Use the `-o` (lowercase) option to specify a local filename:

```bash
curl -o grace_hopper.jpg https://upload.wikimedia.org/wikipedia/commons/3/37/Grace_Hopper_and_UNIVAC.jpg
```

### Following redirects

Many URLs redirect to the actual download location. Use `-L` to follow redirects:

```bash
curl -L -O https://example.com/latest-release.tar.gz
```

### Downloading from an FTP server with curl

```bash
curl -O ftp://ftp.example.se/pub/pictures/space/space031.jpg
```

### Useful curl options

| Option | Effect |
| --- | --- |
| `-O` | Save using the remote filename |
| `-o filename` | Save to a specific local filename |
| `-L` | Follow HTTP redirects |
| `-C -` | Resume an interrupted download |
| `--progress-bar` | Show a simple progress bar instead of statistics |

---

**Author:**
(LUNARC)

**Last Updated:**
2022-10-05
