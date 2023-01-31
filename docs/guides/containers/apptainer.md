# Running containers on LUNARC using Singularity/Apptainer

A container is a self-contained environment for an application containing all required package dependencies. On LUNARC resources, we aim to provide most of our scientific software packages using the module system and EasyBuild. However, some applications can be difficult to build or have requirements that can't be satisfied with a standard LUNARC environment. One way of providing a solution for this is to use containers. On LUNARC resources, it is possible to run container-based applications using the Singularity / Apptainer environment. Using these tools, it is possible to run containers provided by docker/singularity repositories and custom containers. For more information on how to use containers, please see the following links:

[UPPMAX Singularity Workshop](https://pmitev.github.io/UPPMAX-Singularity-workshop)

[Apptainer](http://apptainer.org/)

[Singularity](https://sylabs.io/singularity/)

Singularity/Apptainer commands are available on front-ends and nodes.