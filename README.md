# Gentoo package for Windows Subsystem for Linux
## Introduction 
This is a slightly modified version of the reference WSL package found at [Microsoft/WSL-DistroLauncher](https://github.com/Microsoft/WSL-DistroLauncher), customized for Gentoo.

This is intended for people who are already at least somewhat experienced with Gentoo.
  
## WSL Limitations
Filesystem access is slow. This has a significant impact on the amount of time it takes to emerge anything.

32-bit programs will not run, so multilib profiles cannot be used.

GUIs are officially unsupported under WSL. VNC and X over TCP seem to work fine in practice, but your mileage may vary.

WSL provides its own init. It sets up a reasonable environment for a user session, but it won't automatically start any services.

At least for now, systemd doesn't work at all.
  
## Requirements for building
This project can be built using Visual Studio Community 2017. The "Universal Windows Platform development" feature is required, along with the following subfeatures:

* C++ Universal Windows Platform tools
* Windows 10 SDK (10.0.16299.0) for UWP: C#, VB, JS

## Requirements for installing and running 
The system must be in Developer mode (found in Settings -> Update & Security -> For developers)

The Windows Subsystem for Linux is not enabled by default, and is required to run the app. You can add the feature to your Windows installation by running `C:\Windows\System32\OptionalFeatures.exe` and selecting it from the list.

Windows 10 version 1803 (the April 2018 update) or later is strongly recommended.

## Building
1. Open Gentoo.sln in Visual Studio.
1. Generate a test certificate:
    1. In the Solution Explorer, open `Gentoo-Appx\Gentoo.appxmanifest`
    1. Select the Packaging tab
    1. Select "Choose Certificate"
    1. Click the Configure Certificate drop down and select Create test certificate.
1. Set up the stage3 tarball:
    1. Go to your favorite Gentoo mirror and get the latest stage3-amd64-nomultilib tarball (WSL doesn't support 32-bit binaries, so a multilib profile is likely to cause problems)
    1. Extract the .xz file and recompress it as a .gz file, using 7-zip or the software of your choice
    1. Rename the resulting file to install.tar.gz and place it in the x64 folder
1. Build the package:
    1. Open a `Developer Command Prompt for VS 2017` and change directory to the project root.
    1. Run `build.bat`
       * (Theoretically you should be able to build the project from within Visual Studio as well, but it hangs every time I try. You may have better luck with this than me.)
1. If everything's gone right, you should find your app package in a subfolder under the `AppPackages\Gentoo-Appx` folder.
    1. First, install the certificate:
	    1. Double-click on the security certificate file.
		1. Click the Install Certificate button.
		1. Select Local Machine for the store location.
		1. Select "Place all certificates in the following store", then click the Browse button.
		1. Select "Trusted Root Certification Authorities" from the list, then click OK.
		1. Proceed through the rest of the wizard.
	1. Once that's done, you should be able to double-click on the package file and install it.

## Usage
The launcher provides the following functionality:

* `gentoo.exe`
  - Launches the user's default shell in the user's home directory.

* `gentoo.exe install [--root]`
  - Install the distribution and do not launch the shell when complete.
    - `--root`: Do not create a user account and leave the default user set to root.

* `gentoo.exe run <command line>`
  - Run the provided command line in the current working directory. If no command line is provided, the default shell is launched.
  - Everything after `run` is passed to WslLaunchInteractive.

* `gentoo.exe config [setting [value]]`
  - Configure settings for this distribution.
  - Settings:
    - `--default-user <username>`: Sets the default user to <username>. This must be an existing user.

* `gentoo.exe help`
  - Print usage information.

