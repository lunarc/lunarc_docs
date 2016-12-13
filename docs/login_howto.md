# Introduction

The main way of accessing the Lunarc systems are using a terminal and command line tools. To get access to a terminal the user has to login in to Lunarc using a Secure Shell (SSH) terminal client, for example:

    ssh aurora.lunarc.lu.se -l username

or

    ssh username@aurora.lunarc.lu.se

On Linux this client is built-in to the system and no installation is neccesary. Windows does not have a standard SSH terminal so an external application is needed such as PuTTY (http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) is needed.

To authenticate to the Lunarc system a two-factor authentication solution is used. Two-factor authentication uses two factors for authentication instead of one which is commonly found in normal username and password systems. In the Lunarc case the two factors are:

    Username and password.
    One-time password sent by SMS to a mobile phone.

When you apply for an account the mobile number is registered in our user database and will be used in the login process described in the following sections.

# Logging in using One Time Passwords (OTP)

Logging in to the Lunarc system with OTP passwords is not very different from a normal SSH login, except for the additional extra password prompt. A typical session is shown in the following example:

    login as: joeuser 
    Password: 
    Please enter your onetime password: 123456

If the otp and password are correct you will be logged in to the system.

# Linux

To be prompted for the OTP password the ssh client must be configured for so called "keyboard-interactive" login. These settings can be modified in either /etc/ssh/ssh_config (Redhat systems) or in the home-directory ~/.ssh/config. An example configuration is shown below:

    Host aurora.lunarc.lu.se 
    PreferredAuthentications keyboard-interactive 

    Host * 
    PreferredAuthentications hostbased,publickey,keyboard-interactive,password

In the above example Aurora is configured for keyboard-interactive login, but all other hosts are configured with default login options.

To reduce the number of logins to the system the ServerAlive option can also be added:

    Host aurora.lunarc.lu.se 
    PreferredAuthentications keyboard-interactive 
    ServerAliveInterval 10 

    Host * 
    PreferredAuthentications hostbased,publickey,keyboard-interactive,password

# Mac OS X

Mac OS X is already configured to handle the login to Platon with one time passwords (keyboard-interactive). 

To reduce the number of logins to the system the ServerAlive option can also be added:

    Host aurora.lunarc.lu.se 
    PreferredAuthentications keyboard-interactive 
    ServerAliceInterval 10 

    Host * 
        PreferredAuthentications hostbased,publickey,keyboard-interactive,password

## Mac OS X 10.7 Lion and 10.8 Mountain Lion

To login to a Lunarc system from a Mac system running Mac OS X 10.7 and 10.8, you need to unset the box "Set locale environment variables on startup" in the settings window of the terminal application, press "cmd ," to get there. 

English example:

Setting locale in lion (english)

Swedish example:

Setting locale in lion (english)

Note: This works on a per-theme basis.  In the above examples you will need to choose the theme "Homebrew" to connect to the Lunarc servers.

# Windows

To be prompted for the OTP password the PuTTY client must be configured for so called "keyboard-interactive" login. Open PuTTY from the start-menu. Load the session options for Platon. Open the Connection/SSH/Auth item in the tree-view. Make sure the "Attempt "keyboard-interactive" auth (SSH-2) is checked in the settings, see the following image:

To reduce the number of logins to the system the "Seconds between keepalives" can be changed to a value greater than 0. See the following figure:

# File transfers

To reduce the number of otp passwords needed when transferring files the SFTP protocoll should be used instead of SCP, as each SCP connection will require a new OTP password.

# File transfers with WinSCP

The default filetransfer method in WinSCP is SFTP, so no special settings is needed for this. Just make sure that the "File protocol" setting is set to SFTP as shown in the following figure:

To reduce the number of logins to the system the "Keepalive" options can be set as shown in the following figure:

Please note:

that the "Advanced options" checkbox must be checked to access these settings


