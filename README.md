<!-- ![FunKey-OS Build](https://github.com/FunKey-Project/FunKey-OS/workflows/FunKey-OS%20Build/badge.svg) -->
# FunKey-OS

## Intro
This repository contains all the sources required to build FunKey-OS, the Open-Source firmware at the heart of the [FunKey S retro-gaming console](https://www.funkey-project.com/).

As the FunKey-S console is based on a sophisticated [Allwinner V3s ARM Cortex-A7 1.2GHz CPU](http://www.allwinnertech.com/index.php?c=product&a=index&id=38), an Operating System is mandatory in order to access all the hardware resources without re-inventing the wheel.

FunKey-OS is based on Linux, and is built from scratch using the [buildroot](http://nightly.buildroot.org/) tool that simplifies and automates the process of building a complete Linux system for an embedded system like this.

Technically, Funkey-OS is a [buildroot (v2) based external tree](https://buildroot.org/downloads/manual/manual.html#outside-br-custom) for building the bootloader, the Linux kernel and user utilities, as well as the optimized retro-game launcher and console emulators.

## Build host requirements
Even if the resulting disk image and firmware update files are relatively small (202 MB and 55MB, respectively), the size of the corresponding sources and the compilation by-products tend to be rather large, such that an available disk space of at least 12GB is required during the build.

And even if the resulting FunKey-OS boots in less than 5s, it still requires a considerable amount of time to compile: please account for 1 1/2 hour on a modern multi-core CPU with SSD drives and a decent Internet bandwidth.

As the target CPU is probably different from the one running on your build host machine, a process known as [_cross-compilation_](https://en.wikipedia.org/wiki/Cross_compiler) is required for the build, and as the target system will eventually be Linux, this is much better handled on hosts running a Linux-based operating system too.

As a matter of fact, the FunKey-OS is meant to be built on a native Ubuntu or Debian Linux host machine (Ubuntu 20.04 LTS in our case, but this should also work with other versions, too). And with only a few changes to the prerequisites, it can certainly be adapted to build on other common Linux distros.

However, if your development machine does not match this setup, there are still several available solutions:
 -  use a lightweight container system such as [Docker](https://www.docker.com/) and run an Ubuntu or Debian Linux container in it
 - use a VM (Virtual Machine) , such as provided by [VirtualBox](https://www.virtualbox.org/) and run an Ubuntu or Debian Linux in it
 - for Windows 10 users, use the [WSL2](https://docs.microsoft.com/en-us/windows/wsl/install-win10) (Windows System for Linux 2) subsystem and run an Ubuntu Linux distro in it

In order to install one of these virtualized environments on your machine, please refer to the corresponding documentation.

## Build on fresh Ubuntu 20.04.6 (step by step)
This is the original environment for the build process and therefore the only one really tested.
Is has recently (2023) been tested on WSL2, however a native or virtual Ubuntu 20.04.06 LTS machine should behave identically.

<ins>Warning</ins>: Do not try 22.04; Building on 22.04 currently fails with Error 2 because the buildroot version used is not compatible.

- If you're on Windows, install WSL Ubuntu 20.04.6 LTS from the Windows Store. Otherwise use your prefered install method and environment.
- Set it up and start a shell
- Enter the following commands:
```bash
#We'll set this up in the current user's home directory. But you can use any path
cd ~

#This procedure has only be tested as root, so enter your root password
sudo su

#Update the apt repositories
apt-get update

#Install prerequesites. Note that which is missing because it is not a separate package in 20.04. Answer with yes to install
apt install bash bc binutils build-essential bzip2 ca-certificates cpio cvs expect file g++ gcc git gzip liblscp-dev libncurses5-dev locales make mercurial openssh-client patch perl procps python python-dev python3 python3-dev python3-distutils python3-setuptools rsync rsync sed subversion sudo tar unzip wget xxd zip

#This would clone the RG branch of Bnazari's fork of DrUm78's fork of FunKey-OS if the # was removed.
#It is listed as an example on how to clone a branch:
# git clone --branch rg_nano_bn https://github.com/bnazari/FunKey-OS.git FunKey-OS
#The actual command shown below clones the master branch of DrUm78's fork.
#Please refer to each fork's documentation to select the one which is best for you
git clone https://github.com/DrUm78/FunKey-OS.git FunKey-OS

#This gets the previous SDK which is a prerequisite for building
wget "https://github.com/FunKey-Project/FunKey-OS/releases/download/FunKey-OS-2.3.0/FunKey-sdk-2.3.0.tar.gz"

#This extracts the SDK to the FunKey-sdk-2.3.0 directory
tar -xf FunKey-sdk-2.3.0.tar.gz

#And this sets it up
source $(pwd)/FunKey-sdk-2.3.0/environment-setup

#There seems to be a python in the current version which has to fixed manually.
#The line below will open the editor. Look for this line:
# lib = cdll.LoadLibrary(f'lib{name}.so')
#and change it to this:
# lib = cdll.LoadLibrary('lib%s.so' % name)
nano ./FunKey/output/target/usr/lib/python2.7/site-packages/pyudev/_ctyp
eslib/utils.py

#Ready, so we're switching to the build directory
cd FunKey-OS

#And start building
make sdk all

```
The build process will take a while. It took about 2 hours for me, so please be patient.

## Build on other Physical/Virtual Machine

### Prerequisites
While Buildroot itself will build most host packages it needs for the compilation, some standard Linux utilities are expected to be already installed on the host system. If not already present, you will need to install the following packages beforehand:
 - bash
 - bc
 - binutils
 - build-essential
 - bzip2
 - ca-certificates
 - cpio
 - cvs
 - expect
 - file
 - g++
 - gcc
 - git
 - gzip
 - liblscp-dev
 - libncurses5-dev
 - locales
 - make
 - mercurial
 - openssh-client
 - patch
 - perl
 - procps
 - python
 - python-dev
 - python3
 - python3-dev
 - python3-distutils
 - python3-setuptools
 - rsync
 - rsync
 - sed
 - subversion
 - sudo
 - tar
 - unzip
 - wget
 - which
 - xxd

On Ubuntu/Debian Linux, this is achieved by running the following command:
```bash
$ sudo apt install bash bc binutils build-essential bzip2 ca-certificates cpio cvs expect file g++ gcc git gzip liblscp-dev libncurses5-dev locales make mercurial openssh-client patch perl procps python python-dev python3 python3-dev python3-distutils python3-setuptools rsync rsync sed subversion sudo tar unzip wget which xxd
```

You also need a previous version of the SDK installed:
```bash
#This gets the previous SDK which is a prerequisite for building
wget "https://github.com/FunKey-Project/FunKey-OS/releases/download/FunKey-OS-2.3.0/FunKey-sdk-2.3.0.tar.gz"

#This extracts the SDK to the FunKey-sdk-2.3.0 directory
tar -xf FunKey-sdk-2.3.0.tar.gz

#And this sets it up
source $(pwd)/FunKey-sdk-2.3.0/environment-setup
```

### How to get the sources
When using either physical or virtual Linux machines, you must clone the FunKey OS repository from Github (here we place it into a `FunKey-OS` directory):

```bash
$ git clone https://github.com/FunKey-Project/FunKey-OS.git FunKey-OS
```

Then enter into the created directory:

```bash
$ cd FunKey-OS
```

### Build the disk image & firmware update files
You may now build your FunKey with:

```bash
$ make sdk all
```
This may take a while (~1h30), so consider getting yourself a cup, a glass or a bottle of your favorite beverage ;-)

<ins>Note</ins>: you will need to have access to the network, since buildroot will download the package sources.

### Result of the build
After building, you should obtain the SD Card image `FunKey-sdcard-X.Y.Z.img` and the firmware update file `FunKey-rootfs-X.Y.fwu` in the `images` directory.

## Build in a container

### Prerequisites
When using a Docker container, all the prerequisites are automatically installed.

### How to get the sources
When using a Docker container, you must first create a new directory (here we create a `FunKey-OS` directory) and get the FunKey-OS [Dockerfile](https://github.com/FunKey-Project/FunKey-OS/blob/master/docker/Dockerfile):
```bash
$ mkdir FunKey-OS
$ cd FunKey-OS
$ wget https://raw.githubusercontent.com/FunKey-Project/FunKey-OS/master/docker/Dockerfile -o Dockerfile
```

You must then build the docker image (don't forget the final dot!):
```bash
$ docker build -t funkeyproject/funkey-os .
```

### Build the disk image & firmware update files
You may now build your FunKey with:
```bash
$ docker run --name funkey-os funkeyproject/funkey-os
```

Or alternatively, you can run it in the background with:
```bash
$ docker run -d --name funkey-os funkeyproject/funkey-os
```

If you launch it in the background, you can still follow what is going on with either:
```bash
$ docker top funkey-os
```
Or:
```bash
$ docker logs funkey-os
```

This may take a while (~1h30), so consider getting yourself a cup, a glass or a bottle of your favorite beverage ;-)

<ins>Note</ins>: you will need to have access to the network, since buildroot will download the package sources.

### Result of the build
After building, you can copy the SD Card image `sdcard.img` and the firmware update file `FunKey-rootfs-X.Y.fwu` from the container into the host current directory:
```bash
$ mkdir images
$ docker cp funkey-os:/home/funkey/FunKey-OS/images/FunKey-sdcard-X.Y.Z.img images/
$ docker cp funkey-os:/home/funkey/FunKey-OS/images/FunKey-rootfs-X.Y.Z.fwu images/
```

## How to write to the SD card
You can copy the bootable `images/sdcard.img` onto an SD card using "dd":

```bash
$ sudo dd if=images/FunKey-sdcard-X.Y.Z.img of=/dev/sdX
```
<ins>Warning</ins>: Please make sure that */dev/sdX* device corresponds to your SD Card, otherwise you may wipe out one of your hard drive partitions!

Alternatively, you can use the Balena-Etcher graphical tool to burn the image
to the SD card safely and on any platform:

https://www.balena.io/etcher/

Once the SD card is burnt, insert it into your FunKey S console slot, and
power it up. Your new system should come up now and start a console on
the UART0 serial port and display the retro game launcher on the graphical screen.

## How to update the FunKey S firmware
It is possible to update a FunKey-S over USB:
 - Connect the FunKey S console to your host machine using the USB cable
 - From the retro-game launcher, press the **ON/OFF** button to access the menu
 - Using the **Up/Down** keys, select the "**MOUNT USB**" screen ad press the "**A**" key twice to mount the FunKey S on your machine as an USB mass storage drive
 - Drag and drop the images/FunKey-rootfs-X.Y.fwu file into it
 - When finished, eject the USB mass storage from your host machine
 - Back on the FunKey S console, press the "**A**" key twice to eject the USB mass storage drive
 - The FunKey S console will automatically detect the firmware update file and proceed with the update before returning to the retro game launcher screen once finished
