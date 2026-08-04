# Connection of LU Research Data folders to COSMOS

Connecting your LU Research Data folders to COSMOS is now possible.

> Please contact [LUNARC support](https://supr.naiss.se/support/?centre_resource=c5) if you need this functionality activated for your account.

Connection can be initiated via menus if you're using the COSMOS desktop, or from the commandline in your COSMOS session.
Once connected, your LU Research Data folders will be seen in your $HOME (`/home/$USER/lu_research_data`), and you'll be able move or copy data back and forth between that network-mounted folder and your storage in COSMOS with the same tools/commands you use for moving and copying files within your COSMOS storage.

## A few important points on efficient usage:

- This functionality will only be available on COSMOS frontends, and ***not*** on COSMOS nodes. The main reason for this is that we want to maximise performance in COSMOS, which requires that jobs in the cluster should only be accessing the cluster local storage for reading/writing data.
- This means that using data from your LU Research Data folders in COSMOS jobs, will require you to copy the relevant data into your COSMOS storage before running your jobs.
- Moving results from COSMOS to your LU Research Data folders is as easy as copying data into COSMOS.
- Deleting unneeded copies of data as well as temporary/intermediate files from your COSMOS storage after your jobs have reached completion is always a good idea.
- Disconnecting your LU Research Data folders manually isn't mandatory, but advisable.

## For GUI use

In your COSMOS desktop, please open the 'Applications' menu, and choose the entry 'LUNARC - Wizards/Connect LU Research Data'. A small form will open and ask for your LUCAT ID and password, and using this information it will try and connect your LU Research Data folders into your $HOME.

If started with the folders connected, the same tool will allow you (without filling in LUCAT ID and password) to disconnect your LU Research Data folders from your $HOME.

## For CLI use

If you're accessing COSMOS over ssh, there are two commands available:

- `lurd_mount`, which asks for your LUCAT ID and password, and then tries to connect your LU Research Data folders into your $HOME.
- `lurd_umount`, which disconnects your LU Research Data folders from your $HOME.

## Using GUI and CLI

The tools for GUI and CLI are built to play nice together. You can connect with the CLI commands and disconnect using the GUI menu entry, or vice-versa.

---

**Author:**
(LUNARC)

**Last Updated:**
2026-08-04

