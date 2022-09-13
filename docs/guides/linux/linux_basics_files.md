## Organisation

Files and directories are organised in a tree structure, with the top directory, known as the root directory, simply represented by /, which is also the separator for different directory levels. For example, the home directory of user xxxx is /home/xxxx, where home is a directory under / and xxxx a directory under home. A file in the home directory would be represented as /home/xxxx/xfile. This representation is known as a path and if it starts from the root directory, it is a full path.

If the user is in the home directory, the file can simply be specified as xfile, i.e., using a path relative to the current directory. A re relative path can be made more explicit by using the special symbols . (a period), which represents the current directory, and .. (two periods), which denotes the directory one level up. For example, when the user is in the home directory, xfile and ./xfile are equivalent. If the user is in a subdirectory /home/xxxx/subdir1, the file would be represented by ../xfile. Relative levels can be added; i.e., ../../ means two levels up and from /home/xxxx/subdir1/subsubdir1, /home/xxxx/subdir2/subsubdir2/subfile can be specified as ../../subdir2/subsubdir2/subfile.

Another path symbol is ~, which succeeded by a / denotes the home directory of the user. Thus, ~/xfile points to the file in the example in the same way as if the full path /home/xxxx/xfile had been given.

## Shortcuts

Instead of typeing a long file name, it is possible to write the first few characters and then press the tab key. If the rest of the file name is unique, it will be completed. If more than one file have the same beginning, the name will be completed up to the first point of difference. A second pressing of the tab key will give the possible alternatives. This works for files and directories in the current directory as well as for commands.

Typeing can also be reduced by the use of the wildcard *. It matches anything in the current directory, which means that the wildcard on its own gives a list of all files and directories in the current directory. If it is combined with characters, names that contain those characters in the corresponding place will be matched. For example, *file will match file, xfile, and morefile, but not xfile2. The latter will be matched by *file*, *fi*2, and x*2.

## Permissions

Using the command ls -l to list files in adirectory can give something like

```bash
-rw-r--r-- 1 xxxx xgroup 38 Feb 22 17:08 file1 
-rw-rw-r-- 1 xxxx xgroup 50 Feb 22 17:08 file2 
lrwxrwxrwx 1 xxxx xgroup 5 Feb 22 17:14 link1 -> file2 
-rwxr-xr-x 1 xxxx xgroup 91 Feb 22 17:08 script1 
drwx------ 2 xxxx xgroup 4096 Feb 22 17:07 subdir1 
```

The very first character of each line indicate if the file is of a special kind, such as a link (l) or a directory (d). The next nine characters are the permissions in groups of three for the user (xxxx), group (xgroup), and others. The three types of permissions are read (r), write (w), and execute (x). Thus, in the example, anyone can read file1, but only the user can modify the file, provided the permissions of the directory allow anyone to go there. file2 can also be modfied by members of group xgroup

To run a shell script or a program, it must be executable, at least for the user. script1 can be run by anyone. Similarly, to enter a directory it has to be executable. Only the user xxxx may enter the directory subdir1 in the example.