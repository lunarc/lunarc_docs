# Running containers on LUNARC using Singularity/Apptainer

A container is a self-contained environment for an application containing all required package dependencies. On LUNARC resources, we aim to provide most of our scientific software packages using the module system and EasyBuild. However, some applications can be difficult to build or have requirements that can't be satisfied with a standard LUNARC environment, such as software that requires a specific operating system other than Rocky Linux 9, the OS that COSMOS uses. One solution for this is to use containers. On LUNARC resources, it is possible to run applications in containers provided by Docker / Singularity repositories or build custom containers using the Singularity / Apptainer environment. (Apptainer is the new name for the non-commercial branch of Singularity, but commands that use the word "singularity" will still work for now.)

Apptainer/Singularity containers are stored in Singularity Image Format (.sif) files where the "image" refers to the computing environment contained, which could be any set of software and associated configuration settings from a single software package to a full virtual operating system with hundreds of packages. These .sif files should be run in your `/home/` directory, which will automatically `/home/` and all its contents inside the container. 

Singularity/Apptainer commands are available on front-ends and nodes. When you launch the container, it is possible to work as root within it using the `--fakeroot` runtime option, but this does not allow you to escalate your user privileges on data that you do not already have read+write access to outside the container.

## Running premade containers
Singularity / Apptainer can download premade containers for certain programs from online repositories, like DockerHub, Singularity Hub (shub), or cloud.sylabs.io (default library). For example, let's say you want to download the latest Docker container built around TensorFlow for a GPU. This is the command you would use to download that container:

`$ apptainer pull tensorflow.sif docker://tensorflow/tensorflow:latest-gpu`

The documentation for the `apptainer pull` command with all avilable repository options can be found [here](https://apptainer.org/docs/user/latest/cli/apptainer_pull.html#apptainer-pull)

If you want to run a premade container immediately without modification and know the repository link, you can do so with the command `apptainer run <repository://link/to/container>`. A popular test example is the `lolcow` container that prints a colorful ASCII character rendering of cow with a randomly-generated funny or witty joke. Here is the command that builds and runs the container locally:

`$ apptainer run docker://godlovedc/lolcow`

The output during the build process will look like this:
```
INFO:    Converting OCI blobs to SIF format
INFO:    Starting build...
Getting image source signatures
Copying blob 8e860504ff1e done   | 
Copying blob 9fb6c798fa41 done   | 
Copying blob 7fac07fb303e done   | 
Copying blob d010c8cf75d7 done   | 
Copying blob 9d99b9777eb0 done   | 
Copying blob 3b61febd4aef done   | 
Copying config 38dc06177c done   | 
Writing manifest to image destination
INFO:    Creating SIF file...
INFO:    squashfuse not found, will not be able to mount SIF or other squashfs files
INFO:    fuse2fs not found, will not be able to mount EXT3 filesystems
INFO:    gocryptfs not found, will not be able to use gocryptfs
INFO:    Converting SIF file to temporary sandbox...
```

And the output will looks like this:
![lolcow](lolcow-output.png "lolcow")

## Building your own containers



For more information on how to use containers, please see the following links:

[UPPMAX Singularity Workshop](https://pmitev.github.io/UPPMAX-Singularity-workshop)

[Apptainer Documentation](http://apptainer.org/)

[Singularity Documentation](https://sylabs.io/singularity/)

Singularity/Apptainer commands are available on front-ends and nodes.
