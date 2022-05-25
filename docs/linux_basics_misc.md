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