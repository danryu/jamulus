## Points to note

- Jamulus can be compiled for Linux, Windows and macOS. However, the preferred method of supporting these platforms is to use the binaries generated by the autobuild process in the Jamulus repository.
- For unattended installs, see the contributed [installation scripts](https://github.com/jamulussoftware/installscripts)
- There are reports from people who successfully compile and run Jamulus on BSDs.
- Android and iOS are not officially supported.

---


## Download sources

For .tar.gz [use this link](https://github.com/jamulussoftware/jamulus/archive/latest.tar.gz) to download the latest release

For .zip [use this link](https://github.com/jamulussoftware/jamulus/archive/master.zip)

## Linux

#### Install dependencies

On Ubuntu-based distributions 18.04+, Debian 9+, and Raspberry Pi OS:

* build-essential
* qt5-qmake
* qtdeclarative5-dev
* qt5-default
* qttools5-dev-tools
* libjack-jackd2-dev

#### On Fedora 33+

* qt5-qtdeclarative-devel
* jack-audio-connection-kit-dbus
* qt5-qtbase
* jack-audio-connection-kit-devel
* qt5-linguist

#### For all desktop distributions

[QjackCtl](https://qjackctl.sourceforge.io/) is optional, but recommended to configure JACK.


### Standard desktop build

```
make distclean
qmake # qmake-qt5 on Fedora 33
make
sudo make install
```

`make distclean` is optional but ensures a clean build environment. `make install` is optional and puts the Jamulus binary into `/usr/local/bin`.

### “Headless” server build

Although not strictly necessary, we recommend using the headless flag to avoid having to install some of the dependent packages, save some disk space and/or speed up your build time. 

Note that you don’t need to install the JACK package(s) for a headless build. If you plan to run headless on Gentoo, or are compiling under Ubuntu for use on another Ubuntu machine, the only packages you should need for a headless build are `qtcore`, `qtnetwork`, `qtconcurrent` and `qtxml` (both for building and running the server).

Compile the sources to ignore the JACK sound library:

```
make distclean # recommended
qmake "CONFIG+=nosound headless"
make
sudo make install # optional
```

To control the server with systemd, see this [unit file example](https://github.com/jamulussoftware/jamulus/blob/master/distributions/jamulus-server.service). See also runtime [configuration options](/wiki/Command-Line-Options), and [this information](/wiki/Tips-Tricks-More#controlling-recording-on-linux-headless-servers) on controlling recordings on headless servers.

---

## Windows


You will need [Qt](https://www.qt.io/download)

* Use the free GPLv2 license for Open Source development
* To determine the Qt version you need, check [qt-installer-windows.qs](https://github.com/jamulussoftware/jamulus/blob/master/windows/qt-installer-windows.qs): under INSTALL_COMPONENTS you will see `qt.qt5.[version]`, e.g., 5123 means version 5.12.3.
* Select Components during installation: Expand the Qt section, find the matching version, e.g., **Qt 5.12.3**, and add the compiler components for your compiler, e.g., `MSVC 2017 32-bit/64-bit` for Visual Studio 2019
* [ASIO development files](https://www.steinberg.net/en/company/developer.html)

### Compiling and building installer

Most users will probably want to use this method:

1. Open PowerShell
1. Navigate to the `jamulus` directory
1. To allow unsigned scripts, right-click on the `windows\deploy_windows.ps1` script, select properties and allow the execution of this script. You can also run `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser`. (You can revert this after having run this script. For more information see the [Microsoft PowerShell documentation page](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.security/set-executionpolicy)).
1. Edit the $QtCompile32 and $QtCompile64 variables.
1. Run the Jamulus compilation and installer script in PowerShell: `.\windows\deploy_windows.ps1`.
1. You can now find the Jamulus installer in the `.\deploy` directory.

### Compiling only

1. Create a folder under `\windows` called ASIOSDK2
1. Download the [ASIOSDK](https://www.steinberg.net/asiosdk), open the top level folder in the .zip file and copy the contents into `[\path\to\jamulus\source]\windows\ASIOSDK2` so that, e.g., the folder `[\path\to\jamulus\source]\windows\ASIOSDK2\common` exists
1. Open Jamulus.pro in Qt Creator, configure the project with a default kit, then compile & run

---

## macOS
You will need XCode and Qt as follows:

```
brew install qt5
brew link qt5 --force
```

### Generate XCode Project file

`qmake -spec macx-xcode Jamulus.pro`

### Print build targets and configuration in console

`xcodebuild -list -project Jamulus.xcodeproj`

will prompt

```
Targets:
    Jamulus
    Qt Preprocess

Build Configurations:
    Debug
    Release
```

If no build configuration is specified and `-scheme` is not passed then "Release" is used.

```
Schemes:
    Jamulus
```

### Build the project

`xcodebuild build`

Will build the file and make it available in `./Release/Jamulus.app`

## iOS
* Make sure to install qt5 with the Qt Installer (not homebrew), and explicitly select iOS when choosing the Qt version
* Run `/path/to/qt/5.15.2/ios/bin/qmake -spec macx-xcode Jamulus.pro`
* Open the generated .xcodeproject in XCode, check the Signing & Capabilities and configure a team
* To run on a iOS device, build and run on the device, you'll have first trust your developer profile in device's Settings.

## Android
* Install Qt, including the Android support from the Qt installer
* Follow Qt's [Getting Started with Qt for Android](https://doc.qt.io/qt-5/android-getting-started.html) instructions 
* Make sure Jamulus submodules are present, notably oboe:
`git submodule update --init`
* Open Jamulus.pro in Qt Creator
* Now you should be able to Build & Run for Android.