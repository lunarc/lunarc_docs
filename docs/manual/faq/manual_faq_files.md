# Frequently asked questions - Files

## I accidentally deleted or modified a file

For the home space on COSMOS, we have **snapshots** enabled.  If you type `ls .snapshots` at the commandline in the current directory you get to see a number of diretories named like `@-2020.12.11-00.00.35`, which stands for the date and time of the snapshot.  Change into the directory specifying a point of time before the accident, e.g.:

```
cd .snapshots/@-2020.12.11-00.00.35
```
and you get presented with the state of the directory and it's sub-directories at that time-stamp.  You can copy the required contents out of the snapshot directory into your current directory.

## I can't access my MAX-IV files on LUNARC resources

If your MAX-IV project (i.e., "proposal" in MAX-IV terms) files are not already accessible under /projects/, then send a ticket to LUNARC via [SUPR](https://supr.snic.se/support/) to request it. Mention the project name and, if possible, the UNIX Group name and GID.

Note that when MAX-IV project files are made accessible on LUNARC resources, access is only possible from frontends, i.e., they are not accessible from compute nodes. Before starting a job, please copy the files from /projects/<group name> to your home directory, then modify the job to use the copies in your home directory before submitting it.

---

**Author:**
Joachim Hein (LUNARC)  
Nicolas Melot (LUNARC)

**Last Updated:**
2022-11-01
