# Using the Lunarc HPC Desktop #

*Providing users with a solution for hardware accelerated remote visualization*

The Lunarc HPC Desktop provides Lunarc and SNIC users with a solution for hardware accelerated remote visualization that will provide a new scalable approach to HPC and scientific data visualization. The desktop introduces a convenient way to work with our clusters using not only multiple terminal windows but also file browsers, editors, graphical tools such as queue status and debuggers, 2D and 3D applications, etc.

The same 2-factor authentication as for a normal ssh session is used and the desktop session can easily be disconnected for later reconnect. The whole desktop state (including the latest mouse position) is saved and the system works both from the campus network and from home or when traveling.

The 2D desktop (currently Gnome) runs on a scalable desktop infrastructure that supports multiple desktop agents and load balancing features to ensure a smooth experience. 3D OpenGL applications are executed on dedicated visualization nodes with hardware graphics and seamlessly displayed into the 2D desktop.

**The system is currently available only for Alarik and Erik users and is currently in BETA and we ask you kindly to report any issues you might experience and also send us your requests for improvement.**

The following applications are installed and accessible from the drop down menus (more will come):

 * Abacus CAE
 * Paraview
 * Allinea DDT debugger
 * Queue status
 * ARC storage tools (for accessing the national storage)
 * Editors
 * File browser
 * Misc graphics applications
 * Terminal
 * Firefox 

Brief instructions:

 1. Download the client for either Windows, Mac or Linux
 1. Install it on your workstation or laptop
 1. Launch the client
 1. Enter "alarik.lunarc.lu.se" in the server field.
 1. Enter your login credentials and click [Connect]
 1. Enter the one time password received on your mobile phone
 1. Done! 

Tips: Pressing F8 on the keyboard will bring up a menu where you, among other things, can toggle full screen mode on or off (on Mac it's F8). Depending on your system it might be convenient to *not run* in full screen mode. Play around and see what suits you best.

Mac OSX 10.9 (Mavericks): You need to press and hold the <ctrl>-key while opening the client for the first time to get past the "unindentified developer" notification.