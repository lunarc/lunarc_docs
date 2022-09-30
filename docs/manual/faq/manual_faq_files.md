# Frequently asked questions - Files

![Node icon](../../images/node_icon.jpg "Node icon")

## I accidentally deleted or modified a file

For the home space on Aurora, we have **snapshots** enabled.  If you type `ls .snapshots` at the commandline in the current directory you get to see a number of diretories named like `@-2020.12.11-00.00.35`, which stands for the date and time of the snapshot.  Change into the directory specifying a point of time before the accident, e.g.:

```
cd .snapshots/@-2020.12.11-00.00.35
```
and you get presented with the state of the directory and it's sub-directories at that time-stamp.  You can copy the required contents out of the snapshot directory into your current directory.

---

**Author:**
Joachim Hein (LUNARC)

**Last Updated:**
2022-08-31