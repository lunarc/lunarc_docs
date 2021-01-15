
# Linux Basics

An introduction to Linux shells and commands

# Shells

Before discussing Linux commands, it is important to understand the general Linux environment. A LUNARC user who has just logged into one of the LUNARC systems gets a terminal window with a prompt. At the prompt, a text-based command can be given. The user has actually entered a shell that interprets the commands given. Each command starts a subshell where the command is executed. If the command is a script that contains other commands, they will be executed in a subshell to the subshell. After execution, the prompt returns and represents the level of the original shell.

Normally, the user does not have to consider the levels of shells, but it is good to be aware of them, because some funny effects do not make sense otherwise. For example, a new interactive (sub)shell can be started by the command sh. If this is followed by the command exit, the user just exits the new shell, instead of being logged out of the system, which would be the normal result. The shell levels are also important for shell variables (see next section).

There are different kinds of shells with some differences in commands and features. The default shell obtained by a LUNARC user is known as Bourne-Again shell (bash), combining features of Bourne shell (sh, a very early shell and the origin of the pun intended by the bash name), Korn shell (ksh), and C shell (csh). In the following, bash will be assumed, since users who have asked for ksh or csh are supposed to be familiar with their shell of choice.

# Files

## Organisation

Files and directories are organised in a tree structure, with the top directory, known as the root directory, simply represented by /, which is also the separator for different directory levels. For example, the home directory of user xxxx is /home/xxxx, where home is a directory under / and xxxx a directory under home. A file in the home directory would be represented as /home/xxxx/xfile. This representation is known as a path and if it starts from the root directory, it is a full path.

If the user is in the home directory, the file can simply be specified as xfile, i.e., using a path relative to the current directory. A re relative path can be made more explicit by using the special symbols . (a period), which represents the current directory, and .. (two periods), which denotes the directory one level up. For example, when the user is in the home directory, xfile and ./xfile are equivalent. If the user is in a subdirectory /home/xxxx/subdir1, the file would be represented by ../xfile. Relative levels can be added; i.e., ../../ means two levels up and from /home/xxxx/subdir1/subsubdir1, /home/xxxx/subdir2/subsubdir2/subfile can be specified as ../../subdir2/subsubdir2/subfile.

Another path symbol is ~, which succeeded by a / denotes the home directory of the user. Thus, ~/xfile points to the file in the example in the same way as if the full path /home/xxxx/xfile had been given.

## Shortcuts

Instead of typeing a long file name, it is possible to write the first few characters and then press the tab key. If the rest of the file name is unique, it will be completed. If more than one file have the same beginning, the name will be completed up to the first point of difference. A second pressing of the tab key will give the possible alternatives. This works for files and directories in the current directory as well as for commands.

Typeing can also be reduced by the use of the wildcard *. It matches anything in the current directory, which means that the wildcard on its own gives a list of all files and directories in the current directory. If it is combined with characters, names that contain those characters in the corresponding place will be matched. For example, *file will match file, xfile, and morefile, but not xfile2. The latter will be matched by *file*, *fi*2, and x*2.

## Permissions

Using the command ls -l to list files in adirectory can give something like

    -rw-r--r-- 1 xxxx xgroup 38 Feb 22 17:08 file1 
    -rw-rw-r-- 1 xxxx xgroup 50 Feb 22 17:08 file2 
    lrwxrwxrwx 1 xxxx xgroup 5 Feb 22 17:14 link1 -> file2 
    -rwxr-xr-x 1 xxxx xgroup 91 Feb 22 17:08 script1 
    drwx------ 2 xxxx xgroup 4096 Feb 22 17:07 subdir1 

The very first character of each line indicate if the file is of a special kind, such as a link (l) or a directory (d). The next nine characters are the permissions in groups of three for the user (xxxx), group (xgroup), and others. The three types of permissions are read (r), write (w), and execute (x). Thus, in the example, anyone can read file1, but only the user can modify the file, provided the permissions of the directory allow anyone to go there. file2 can also be modfied by members of group xgroup

To run a shell script or a program, it must be executable, at least for the user. script1 can be run by anyone. Similarly, to enter a directory it has to be executable. Only the user xxxx may enter the directory subdir1 in the example.

# Shell Parameters

It is possible to assign values to parameters within a shell. Certain parameters, environment  or shell variables, have to be defined for the proper function of the shell and are usually set automatically. Some shell variables are modified when modules are loaded (see the User's Guide). The user can also define his/her own parameters.

Shell variables set by the system have names in capital letters. Note that Linux shells are case sensitive; i.e., parameters or commands with names like PARAMETER, PARAmeter, and parameter are all different. The value of a parameter is represented by the name of the parameter preceded by a dollar sign ($) and the value can be checked with the command echo. For example,

    echo $PWD

gives the path of the current directory and

    echo $HOME 

produces the path of the home directory. Consequently, the file xfile in the examples above can also be addressed as $HOME/xfile. The interpretation of a parameter name ends at a special character, which is why the last substitution works, but if the parameter is to be used for substitution in a string of characters, the name can be protected by brackets,

    Program=myprog
    JobNr=1
    OutputFile=${Program}_job$JobNr.out

Note that an underscore (_) counts as ordinary text and is not a special character, while a period (.) is the latter.

A very important shell variable is PATH. It contains a list of paths where the shell will look for commands, which are actually files containing programs. A selected part of the default PATH on Milleotto is

    /usr/kerberos/bin:/usr/lpp/mmfs/bin/:/usr/local/bin:/bin:/usr/bin 

There are two things to note here. First, the order is important. If a command or program with the same name exists in more than one of the directories, the shell will pick the one that comes first in the list. Second, a standard name for a directory is bin. A user can customise the environment by defining and modifying shell variables in the file .bash_profile, which resides in the home directory. On MIlleotto, the default file contains the lines

    PATH=$PATH:$HOME/bin 
    export PATH

This adds a bin directory in the home directory of the user to the search path. The second line is needed to make the change propagate to all subshells. Without the export command the modification of PATH would only have an effect in the shell where the file is executed as a script. This is general. If a parameter is supposed to be defined anywhere else besides the shell where it is initated, it has to be exported. It can be defined and exported in one go, for example,

    export WorkDir=/disk/global/xxxx/workdir

To list all the defined and exported parameters the command set can be used.

The result of a command can also be put into a parameter by using the grave accent for "reverse quotation", for example,

    ThisMachine=`hostname`

or by the substitution $(command)

    ThisMachine=$(hostname) 

To put the results of integer arithmetics into a parameter, the command  expr can be used

    Number=4 Sum=`expr $Number + 1` 

Note that it is necessary to separate the elements of the expression by spaces. An alternative is the arithmetic substitution $((expression))

    Sum=$(($Number+1))

No spaces necessary. In both cases, echo $Sum gives 5.

# Special Command Symbols

## Redirection

    < file 
    
Reads input from the file file.

    > file 

Writes standard output to the file file; i.e., an old file with the same name will be overwritten.

    >> file 

Appends standard output to the file file; i.e., the information will be added at the end of the file file, if it already exists.

    2> file 
    
Writes standard error to the file file.

    &> file 
    
Writes standard output and error to the file file.

Example:

    myprog <inputfile >outfile 2>errfile

The program `myprog` will read from the file `inputfile` and write its standard output to `outfile` and write error message to `errorfile`.
## Command execution

; Separates multiple commands on the command line; i.e., the semi-colon corresponds to pressing ENTER between commands.

    command1 ; command2 

is the same as

    command1 command2

| Pipe symbol. Uses output from one command as input for the next command; i.e.,

    command1 | command2

is the same as

```
    command1 > outfile; command2 < outfile
```
Typing

    !string 
    
repeats a previous command line that starts `string`.

    echo $PATH ... !echo 

The last line corresponds to executing echo $PATH again.

## Quotation

' ' (Single quotes) Quotes the enclosed string exactly.

    Program=myprog
    JobNr=1
    OutputFile='${Program}_job$JobNr.out'
    echo $OutputFile 

gives ${Program}_job$JobNr.out as output.

" " (Double quotes) Quotes the enclosed string after variable substitution.

    Program=myprog
    JobNr=1
    OutputFile="${Program}_job$JobNr.out"
    echo $OutputFile

gives myprog_job1.out as output

# Useful Linux Commands

## About Commands

### man

To get more information about a command type

    man command

### which

Check where the command that will be used resides

    which command

This gives the path of the first occurence of command in the directories listed in the envrionment variable PATH. Note that a few commands are picked up directly by the shell, for example, the time command, and the program in the search path will not be executed. If a command behaves differently than expected from the man page, a solution may be to give the full path of the command found by which.

## Manage Shell Variables and Parameters

### echo

Give the value of a parameter

    echo $PATH

### set

List the values of all defined environment variables alphabetically

    set

### export

Export a parameter to all future subshells

    export parameter

The value can be set and exported at the same time, for example,

    export MyParameter=myvalue

## Manage Files and Directories

### mkdir

Make a directory

    mkdir dirname

-p: Make a directory and also any parent directories, if they do not exist

    mkdir -p parentdir1/parentdir2/dirname

### rmdir

Remove an empty directory

    rmdir dirname

### cd

Change the current directory

    cd dirname

Some special behaviour:

    cd

Without a name the new directory is the home directory.

    cd -

returns the user to the previous directory.

    cd ..

moves up one level in the file tree.

### rm

Remove a file

    rm filename

-r: Remove a file or a directory with all files and directories in it

    rm -r dirname

-i: Get a question before taking action (removing files, removing directories, descending in directories)

    rm -ir dirname

### mv

Change the name of a file or directory

    mv oldname newname

### cp

Copy a file to a new file

    cp oldname newname

or to another directory

    cp file1 file2 file3 newlocation

Copy all files in the current directory to another directory

    cp * newlocation

-p: Copy with preserved file settings (modification date, permissions, ownership):

    cp -p oldname newname

-r: Copy a directory and its subdirectories

    cp -r oldname newname

-u: Copy only files that do not exist or have an earlier modification date in another directory

    cp -u * newlocation

### ls

List files in the current directory

    ls
    
List the files in another directory

    ls dirname

-l: List in long format

-s: List with size (in blocks by default)

-S: List sorted by size

-t: List sorted by time

-r: Reverse the order of a sorted listing

-h: Print size in human-readable form (with units adjusted to size)

List files in long format with most recent file last

    ls -ltr

### find

Find a file/directory with a given name and print its path, starting from a given directory

    find dirname -name filename

Find all files that contain a specified string in their name, starting from the current directory

    find . -name "*string*"

Find files newer than a specified file, starting from the current directory

    find . -newer filename

-exec ... {} \;: Execute a command with the found file represented by {}

Remove files and directories with a specified string in their name

    find . -name "*string*" -exec rm -r {} \;

-not: Negate an expression

-type d: Specify the file type as a directory

Get a long-format listing of files with a specified string in their name, as long as they are not directories

    find . -name "*string*" -not -type d -exec ls -l {} \;

find is a powerful command with many options as a look at the man page will reveal,

    man find

### du

Print the disk usage of the current and all subdirectories

    du

Print the disk usage of a specifed directory and all its subdirectories

    du dirname

-a: Print the size of individual files in addition to diectories

Print the size of a file

    du -a filename

-s: Just print the sum of the disk usage

--max-depth=level: Print usage for directories level levels down, where level is a number

-k: Print usage in kB

-m: Print usage in MB

-h: Print usage in human-readable form (with units adjusted after the size)

Print the summed disk usage of the current directory in human-readable form

    du -sh

Print the disk usage for the current directory and its immediate subdirectories in human-readable form

    du -h --max-depth=1

### chmod

Set the file permission.

Make a file executable for anyone

    chmod +x filename 

To be more precise it is possible to specify the changeas a string in the format who-add/remove-permission, where who is one of more of the characters u (user), g (group), and o (others); + means add and - means remove; and permission is one or more of the characters r (read), w (write), and x (execute). For example, to remove read and write permissions for the group and others

    chmod go-rw filename 

-R: Make the change recursively, i.e., for all files in all subdirectories

    chmod -R g+w dirname

## Text Handling
### cat

List the contents of a file

    cat filename

List the contents of several files and put the result in a new file

    cat file1 file2 file3 > newfile

Append a file to another file

    cat file1 >> file2

### more

List the contents of a file without scrolling through everything at once

    more filename

Display the output of a command without scrolling through everything at once

    command | more

Pressing return gives a new line. Pressing the space bar gives a new page. b gives the previous page if possible (not for the output stream of a command). /string searches for a string. q exists more.

### less

less is similar to more. One of the more important differences is that it also works backwards. For example, pressing b gives the previous page (works also for more on files - first more example, but not pipes - second more example) and ?string searches backwards for a string.

### grep

Print lines that contain a specified string in a file

    grep string filename

Search for and print lines containing a specified string in several files

    grep string file1 file2 file3

Search for and print lines containing a specified string in all files in the current directory

    grep string *

Print the lines in the output of a command containing a specified string

    command | grep string

-i: Ignore the case of the search string (print both upper and lower case matches)

-v: Print lines that do not contain the string

    command | grep -v string 

### diff

Compare the contents of two files and print differences

    diff file1 file2 

--side-by-side: Display the file contents side-by-side with differences marked

    diff --side-by-side file1 file2 