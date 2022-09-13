# Introduction to Linux

Before discussing Linux commands, it is important to understand the general Linux environment. A LUNARC user who has just logged into one of the LUNARC systems gets a terminal window with a prompt. At the prompt, a text-based command can be given. The user has actually entered a shell that interprets the commands given. Each command starts a subshell where the command is executed. If the command is a script that contains other commands, they will be executed in a subshell to the subshell. After execution, the prompt returns and represents the level of the original shell.

Normally, the user does not have to consider the levels of shells, but it is good to be aware of them, because some funny effects do not make sense otherwise. For example, a new interactive (sub)shell can be started by the command sh. If this is followed by the command exit, the user just exits the new shell, instead of being logged out of the system, which would be the normal result. The shell levels are also important for shell variables (see next section).

There are different kinds of shells with some differences in commands and features. The default shell obtained by a LUNARC user is known as Bourne-Again shell (bash), combining features of Bourne shell (sh, a very early shell and the origin of the pun intended by the bash name), Korn shell (ksh), and C shell (csh). In the following, bash will be assumed, since users who have asked for ksh or csh are supposed to be familiar with their shell of choice.

