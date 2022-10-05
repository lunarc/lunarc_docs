# Special Command Symbols

## Redirection

```bash
< file 
```
    
Reads input from the file file.

```bash
> file 
```

Writes standard output to the file file; i.e., an old file with the same name will be overwritten.

```bash
>> file 
```

Appends standard output to the file file; i.e., the information will be added at the end of the file file, if it already exists.

```bash
2> file 
```
    
Writes standard error to the file file.

```bash
&> file 
```
    
Writes standard output and error to the file file.

Example:

```bash
myprog <inputfile >outfile 2>errfile
```

The program `myprog` will read from the file `inputfile` and write its standard output to `outfile` and write error message to `errorfile`.

## Command execution

; Separates multiple commands on the command line; i.e., the semi-colon corresponds to pressing ENTER between commands.

```bash
command1 ; command2 
```

is the same as

```bash
command1 command2
```

| Pipe symbol. Uses output from one command as input for the next command; i.e.,

```bash
command1 | command2
```

is the same as

```bash
command1 > outfile; command2 < outfile
```
Typing

```bash
!string 
```
    
repeats a previous command line that starts `string`.

```bash
echo $PATH ... !echo 
```

The last line corresponds to executing echo $PATH again.

## Quotation

' ' (Single quotes) Quotes the enclosed string exactly.

```bash
Program=myprog
JobNr=1
OutputFile='${Program}_job$JobNr.out'
echo $OutputFile 
```

gives ${Program}_job$JobNr.out as output.

" " (Double quotes) Quotes the enclosed string after variable substitution.

```bash
Program=myprog
JobNr=1
OutputFile="${Program}_job$JobNr.out"
echo $OutputFile
```

gives myprog_job1.out as output

---

**Author:**
(LUNARC)

**Last Updated:**
2022-10-05