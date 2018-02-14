# Using the Pocket Pass authenticator

Download the Pocket Pass app:

## Download for Apple iOS

<a href="https://itunes.apple.com/se/app/phenixid-pocket-pass/id1071318323?mt=8" target="_blank"><img class="ios" src="https://www.phenixid.se/wp-content/themes/phenixid/images/app-store.svg" width=150></a>

## Download for Android OS

<a href="https://play.google.com/store/apps/details?id=com.phenixidentity.pocketpass" target="_blank"><img src="https://www.phenixid.se/wp-content/themes/phenixid/images/google-play.png" width=160></a>

## Login into the LUNARC HPC desktop

Activation of the Pocket Pass app must be done from the LUNARC HPC Desktop. Please see the following guide on how to login to the desktop:

[Using the LUNARC HPC Desktop](http://lunarc-documentation.readthedocs.io/en/latest/using_hpc_desktop "Using the LUNARC HPC Desktop")

## Starting the Self-service portal

When logged into the LUNARC HPC Desktop click on the **Applications** menu on the top left on the desktop. Select **Lunarc Tools/Security/Register OTP Authenticator**

![sample screen](images/authenticator_menu.png "Desktop sample screen")

This will show the login screen to the self-service portal:

![sample screen](images/selfservice_login.png "Desktop sample screen")

Enter your normal LUNARC credentials. Make sure to select the **I am a human** checkbox. If all is OK you will receive a SMS with a OTP token and the next screen will display:

![sample screen](images/selfservice_otp.png "Desktop sample screen")

The next screen will display a general tab with user information and a token tab. Click on the token tab. This will display a button **Activate PhenixID Pocket Pass**. 

![sample screen](images/add_token1.png "Desktop sample screen")

Click on the button to begin the process of adding a authenticator token. In the shown dialog enter a descriptive name for your token that will be displayed in your device.

![sample screen](images/add_token2.png "Desktop sample screen")

Click next to continue. This will display a QR-code that will be scanned by your app to add the token to your device.

![sample screen](images/add_token3.png "Desktop sample screen")

Open your Pocket Pass app and click **Install key**

![sample screen](images/pp_ss2.png "Desktop sample screen")

Click on the **Scan barcode**

![sample screen](images/pp_ss3.png "Desktop sample screen")

Point your camera on the QR-code shown on the screen. The app should detect the code and install the key automatically. In the next step the app asks you for a pin-code to protect your key. Enter your pin-code. 

![sample screen](images/pp_ss4.png "Desktop sample screen")

When this is done the following screen should be shown:

![sample screen](images/pp_ss5.png "Desktop sample screen")

Click continue to display the current valid otp-code.

![sample screen](images/pp_ss6.png "Desktop sample screen")

You can now login to the Aurora services using the authenticator app instead of with SMS. 












