## The job script and sbatch

You register your program with SLURM for execution using the **sbatch** command. This is easiest accomplished using a *job description file*. The job description file is also known as a *job script*.

A very simple job script looks as follows:
    
```bash
#!/bin/bash
#SBATCH -t 00:05:00

echo "hello"
```

Write this into a file. In the following, we assume the file is named **echo_script.sh**, but in principle, any name will do. You can now send the script for execution using the **sbatch** command. This will execute the “program” echo on the backend.

```bash
sbatch echo_script.sh
```

This should deliver a screen output similar to

```bash
[fred@aurora Serial]$ sbatch echo_script.sh
Submitted batch job 7185
```

Where 7185 is the job number assigned by SLURM. Once your job has been executed you will find a file **slurm-7185.out** in your directory which contains the output and error messages from your program.

## The three parts of a job script

The example echo_script.sh shows the three parts every job script requires

 1. Shell specification
 2. Resource statement
 3. Body containing a UNIX script
 
In our example, each part consists of a single line. The first line of our example contains the shell specification, in most cases the sh-shell as used here is just fine. The second line starting with #SBATCH specifies the resources needed. In our case, it asks for 5 minutes of computer time. If the job hasn’t finished after that time, SLURM will terminate it. Job scripts typically contain more than one of these statements, specifying e.g. more than one processor or more memory. 
 
The most commonly used resource statements at LUNARC will be explained below. The resource statements are followed by a list of programs and UNIX commands to be executed on the system. This is actually a normal UNIX script and everything you can do in a UNIX script can be done here as well. In our example, the script consists of the UNIX **echo** command.