# Frequently asked questions - Files

## I accidentally deleted or modified a file

Snapshots are enabled for home directories on COSMOS. Run `ls .snapshots` in any directory to see available snapshots, named by date and time — for example `@-2020.12.11-00.00.35`. Change into the snapshot directory for a point in time before the accident:

```bash
cd .snapshots/@-2020.12.11-00.00.35
```

You will see the state of the directory and its subdirectories at that timestamp. Copy what you need back to your current directory.

## I can't access my MAX-IV files on LUNARC resources

If your MAX-IV project files are not accessible under `/projects/`, send a ticket to LUNARC via [SUPR](https://supr.naiss.se/support/?centre_resource=c5&summary=Access+to+MAXIV+folder) to request access. Include the project name and, if possible, the UNIX group name and GID.

!!! warning "MAX-IV files are not available on compute nodes"
    MAX-IV project files under `/projects/` are only accessible from the frontend (login) nodes. Before submitting a job, copy the required files to your home directory and update your job script to use those copies.

---

**Author:**
Joachim Hein (LUNARC)
Nicolas Melot (LUNARC)

**Last Updated:**
2024-11-14
