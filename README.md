## Semi-automatic installer of macOS on VirtualBox

### Supports macOS Catalina (10.15), Mojave (10.14), and High Sierra (10.13)

The script is semi-automatic and requires a little user interaction. Most of the time the user simply has to press enter. There is one step where the user has to choose between [C]atalina, [M]ojave, or [H]igh Sierra, and a couple of instances where the user has to choose whether to delete or keep temporary files and previous installations.

The goal of the script is to allow for a very easy installation without any closed-source additions or extra bootloaders.

Tested on Cygwin and WSL, should work on most Linux distros and macOS.

## iCloud and iMessage connectivity

iCloud, iMessage, and other connected Apple services require a valid device name and serial number, board ID and serial number, and other genuine (or genuine-like) Apple parameters. These parameters may be set in the script before installation, or set after installation and applied with `./macos-guest-virtualbox.sh configure_vm`

## Storage size

The script by default assigns the minimum required storage size for the installation of Catalina. After the installation is complete, the storage size may be increased. First increase the virtual disk image size through VirtualBox Manager or `VBoxManage`, then in Terminal in the virtual machine run `sudo diskutil repairDisk disk0`, and then `sudo diskutil apfs resizeContainer disk1 0` or from Disk Utility, after repairing the disk from Terminal, delete the "Free space" partition so it allows the system APFS container to take up the available space.

## Performance and unsupported features

Developing and maintaining VirtualBox or macOS features is beyond the scope of this script. Some features may behave unexpectedly, such as USB device support, [audio support](https://github.com/chris1111/VoodooHDA-2.9.2-Clover-V13/releases), and other features.

After successfully creating a working macOS virtual machine, consider importing it into QEMU/KVM so it can run with hardware passthrough at near-native performance.

## Dependencies

* [VirtualBox](https://www.virtualbox.org/wiki/Downloads)≥6.0 **with Extension Pack**
* `Bash`≥3 (GNU variant; run on Windows through [Cygwin](https://cygwin.com/install.html) or WSL)
* `coreutils` (GNU variant; install through package manager)
* `unzip` (install through package manager)
* `wget` (install through package manager)
* `dmg2img` (install through package manager on Linux, macOS, or WSL; let the script download it automatically on Cygwin)

## Dependencies with brew

Most of the dependencies can be installed with HomeBrew

```shell
brew install wget
brew install coreutils
brew install unzip
brew install dmg2img
```

## Dependencies Troubleshooting

When the installation of Virtual Box fails, then refer to this guide to solve it:

* Open [Apple Icon] >> System Preferences
* Open Security & Privacy
* Open "General" tab
* Click on "Allow" that is presented next to the 'System software from developer "Oracle America, Inc"' was blocked
  from loading. 

## Installation

Run the following in terminal

```shell
bash macos-guest-virtualbox.sh
```

Follow the guide presented by the script by referring to what is happening on the screen of the VM.

HINT: When the installation/setup is and you've set up everything in a way that you just see the normal MacOS desktop
      and are able to interact with it, consider creating 'clean' snapshot of the VM that you could return to when you 
      mess it up or just want to test something from the starting point of clean installation.     

## Remote Login

If you want the terminal of the remote machine to be available from the HOST machine, some additional manual steps have
to be taken.

This guide assumes that the admin user in the system was named 'mojave'. If you used other user name, use yours instead.   

### Allow Remote Login

This can be done within the VM by doing the following: 

* [Apple] > System Preferences > Sharing > Remote Login

### Update VM configuration

1. Open VirtualBox
2. select the VM that has MacOS installed on it
3. choose to edit its Settings
4. choose Network tab
5. open Advanced section on that tab
6. open Port Forwarding
7. Configure the following:

```txt
Name: SSH
Protocol: TCP
Host IP: 127.0.0.1
Host Port: 2200
Guest IP: 10.0.2.15
Guest Port: 22
```

You might also consider configuring port-forwarding for web:  

```txt
Name: HTTP
Protocol: TCP
Host IP: 127.0.0.1
Host Port: 8000
Guest IP: 10.0.2.15
Guest Port: 80
```