# An introduction to Linux shells and commands

It is possible to assign values to parameters within a shell. Certain parameters, environment  or shell variables, have to be defined for the proper function of the shell and are usually set automatically. Some shell variables are modified when modules are loaded (see the User's Guide). The user can also define his/her own parameters.

Shell variables set by the system have names in capital letters. Note that Linux shells are case sensitive; i.e., parameters or commands with names like PARAMETER, PARAmeter, and parameter are all different. The value of a parameter is represented by the name of the parameter preceded by a dollar sign ($) and the value can be checked with the command echo. For example,

```bash
echo $PWD
```

gives the path of the current directory and

```bash
echo $HOME 
```

produces the path of the home directory. Consequently, the file xfile in the examples above can also be addressed as $HOME/xfile. The interpretation of a parameter name ends at a special character, which is why the last substitution works, but if the parameter is to be used for substitution in a string of characters, the name can be protected by brackets,

```bash
Program=myprog
JobNr=1
OutputFile=${Program}_job$JobNr.out
```

Note that an underscore (_) counts as ordinary text and is not a special character, while a period (.) is the latter.

A very important shell variable is PATH. It contains a list of paths where the shell will look for commands, which are actually files containing programs. A selected part of the default PATH on Milleotto is

```bash
/usr/kerberos/bin:/usr/lpp/mmfs/bin/:/usr/local/bin:/bin:/usr/bin 
```

There are two things to note here. First, the order is important. If a command or program with the same name exists in more than one of the directories, the shell will pick the one that comes first in the list. Second, a standard name for a directory is bin. A user can customise the environment by defining and modifying shell variables in the file .bash_profile, which resides in the home directory. On MIlleotto, the default file contains the lines

```bash
PATH=$PATH:$HOME/bin 
export PATH
```

This adds a bin directory in the home directory of the user to the search path. The second line is needed to make the change propagate to all subshells. Without the export command the modification of PATH would only have an effect in the shell where the file is executed as a script. This is general. If a parameter is supposed to be defined anywhere else besides the shell where it is initated, it has to be exported. It can be defined and exported in one go, for example,

```bash
export WorkDir=/disk/global/xxxx/workdir
```

To list all the defined and exported parameters the command set can be used.

The result of a command can also be put into a parameter by using the grave accent for "reverse quotation", for example,

```bash
ThisMachine=`hostname`
```

or by the substitution $(command)

```bash
ThisMachine=$(hostname) 
```

To put the results of integer arithmetics into a parameter, the command  expr can be used

```bash
Number=4 Sum=`expr $Number + 1` 
```

Note that it is necessary to separate the elements of the expression by spaces. An alternative is the arithmetic substitution $((expression))

```bash
Sum=$(($Number+1))
```

No spaces necessary. In both cases, echo $Sum gives 5.