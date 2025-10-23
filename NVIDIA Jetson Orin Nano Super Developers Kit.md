---
title: "NVIDIA Jetson Orin Nano Super Developers Kit - Getting Started"
source: "https://dronebotworkshop.com/jetson-orin-nano/"
author:
  - "[[DroneBot Workshop]]"
published: 2025-01-17
created: 2025-09-09
description: "NVIDIAs Jetson Orin Nano development board, a staple of the AI development community since 2022, has received a significant update that nearly doubles its…"
tags:
  - "clippings"
---
Table of Contents \[[show](https://dronebotworkshop.com/jetson-orin-nano/#)\]

![Download PDF](https://i0.wp.com/dronebotworkshop.com/wp-content/uploads/2023/04/dbws-pdfdown.png?resize=64%2C64&ssl=1) [![Parts List](https://i0.wp.com/dronebotworkshop.com/wp-content/uploads/2023/04/dbws-parts.png?resize=64%2C64&ssl=1 "Parts List")](https://dronebotworkshop.com/jetson-orin-nano/#Parts_List) [![View on YouTube](https://i0.wp.com/dronebotworkshop.com/wp-content/uploads/2023/04/dbws-youtube.png?resize=64%2C64&ssl=1 "View on YouTube")](https://youtu.be/BaRdpSXU6EM)

NVIDIAs Jetson Orin Nano development board, a staple of the AI development community since 2022, has received a significant update that nearly doubles its performance. If that’s not enough, NVIDIA has slashed the price of this “AI Brain” in half, bringing it into the reach of makers and experimenters for use in their Artificial Intelligence projects.

![](https://i.ytimg.com/vi/BaRdpSXU6EM/hqdefault.jpg)

Today, we’ll examine this little wonder, which has been repackaged as the [**NVIDIA Jetson Orin** **Nano Super Developer Kit**](https://www.nvidia.com/en-us/autonomous-machines/embedded-systems/jetson-orin/nano-super-developer-kit/). At a new list price of **$249.00 USD**, it’s an ideal board for that big robotics or computer vision project you’ve been dreaming up.

## Introduction – NVIDIA’s Jetson Platform

![](https://i0.wp.com/dronebotworkshop.com/wp-content/uploads/2025/01/Jetson-Orin-Nano_small.jpg?resize=750%2C422&ssl=1)

NVIDIA’s Jetson line of development boards, introduced in 2014, was designed to bring the company’s GPU and AI expertise to embedded systems, robotics, and edge computing. The first release, the Jetson TK1, featured a Tegra K1 processor.

The line expanded with the TX1 in 2015, bringing improved performance and lower power consumption. The TX2, released in 2017, marked a significant step forward in AI processing capabilities. These early boards helped establish NVIDIA’s presence in the embedded AI and robotics space.

The real breakthrough came with the Xavier generation, launched in 2018. This generation dramatically increased AI performance and introduced the AGX form factor for industrial applications.

Of course, the affordable [Jetson Nano](https://dronebotworkshop.com/nvidia-jetson-developer-kit/), released in 2019, became particularly popular with makers and educators. We’ve used it in several projects, and for many experimenters, this was their introduction to working with machine learning and artificial intelligence.

The current Orin generation, introduced in 2022, represents NVIDIA’s most powerful embedded AI platform. The Orin family significantly improved AI processing power, featuring NVIDIA’s Ampere GPU architecture and advanced AI accelerators while maintaining backward compatibility with previous Jetson platforms.

The Jetson platform has become popular with developers looking to deploy AI solutions in real-world scenarios. Its compact size, high performance, and comprehensive software ecosystem have made it ideal for this purpose.

## Jetson Orin Nano Super Developers Kit

The repriced and improved Jetson Orin Nano Super Developers Kit allows makers and experimenters to work with cutting-edge Artificial intelligence projects.

The kit is packaged with the Jetson Orin Nano development board, a 19-volt DC power supply, and power cords for different localities. You’ll need to supply a keyboard, mouse, and a video monitor along with a DisplayPort cable or adapter.

You’ll also require some media to hold the operating system, applications, and utilities. This can be a microSD card or SSD; they both have special requirements, which we will discuss in a moment. You can also use both if you like.

In addition to the above, you’ll also need the following:

- [**A NVIDIA account**](https://www.nvidia.com/en-gb/account/). It’s free!
- **A workstation for setup**. If you plan on booting your Orion Nano from a microSD, you’ll need this to burn the microSD. However, if you plan on setting up an SSD boot instead, having an Ubuntu 22.04 workstation is helpful.

Once you have all that, it’s time to start working with this fantastic little board.

### NVIDIA Jetson Orin Nano

The heart of the Super Developer Kit is, of course, the NVIDIA Jetson Orin Nano.

![](https://i0.wp.com/dronebotworkshop.com/wp-content/uploads/2025/01/jetson-orin-nano-side-view_small.jpg?resize=750%2C422&ssl=1)

The NVIDIA Jetson Orin Nano is a powerful single-board computer perfect for AI and robotics projects. It boasts an NVIDIA Ampere architecture GPU with 1024 CUDA cores, allowing it to process massive amounts of data in parallel. The Orin Nano also includes a 6-core Arm Cortex-A78AE CPU and 8GB of LPDDR5 memory.

This AI horsepower is packaged on a small (69.9mm x 45mm) module mounted to a carrier board using a 260-pin SO-DIMM connector. The module fits onto a carrier board with a 40-pin GPIO, four USB 3.1 Type A sockets, two CSI Camera connectors, a DisplayPort 1.2 video output, a Gigabit Ethernet port, and a USB C connector.

The carrier board is powered by a DC supply of 7 to 20 volts; a 19-volt power adapter is included in the Super Developers Kit.

Note that this board only provides one video output, a DisplayPort connector. You’ll probably need a DisplayPort to HDMI Adapter or cable to connect it to a video monitor.

![](https://i0.wp.com/dronebotworkshop.com/wp-content/uploads/2025/01/Jetson-Orin-Nano-Connectors-1_small.jpg?resize=750%2C422&ssl=1)

Another thing to note is that the USB C port is data-only. You can’t use it to connect a second video monitor or power the board. I’ll show you what it is used for when we install an SSD into our Nano.

![](https://i0.wp.com/dronebotworkshop.com/wp-content/uploads/2025/01/Jetson-Orin-Nano-Connectors-2_small.jpg?resize=750%2C422&ssl=1)

Flipping the board over reveals three M.2 sockets. One of them, a Key-E TYpe 2230, houses a wireless networking card. The other two can be used for Solid-State Drives.

![](https://i0.wp.com/dronebotworkshop.com/wp-content/uploads/2025/01/Jetson-Orin-Nano-Connectors-3_small.jpg?resize=750%2C422&ssl=1)

If you look on the side of the board, at the top of the Nano module, you’ll see a socket for a microSD card. Below that, on the carrier board, is a 14-pin connector, which NVIDIA calls a “button header.” You can use this connector to connect external power or reset switches. It can also be used to place the Jetson Orin nano into “Recovery Mode,” a step we will perform when working with SSDs.

![](https://i0.wp.com/dronebotworkshop.com/wp-content/uploads/2025/01/Jetson-Orin-Nano-Connectors-4_small.jpg?resize=750%2C422&ssl=1)

### Jetson Orin Super Power Boost

In December 2024, NVIDIA released an update for all Jetson Orins. This “Super” update added a new 25-watt power mode to the Jetson Orin Nano, resulting in performance improvements of 70 percent or more.

![](https://i0.wp.com/dronebotworkshop.com/wp-content/uploads/2025/01/Jetson-Orin-Nano-Super-Improvements_small.jpg?resize=750%2C422&ssl=1)

The chart above shows the improvements resulting from the “Super” update. Note that the update is available for all Jetson Orin Nano boards, including ones purchased before the price decrease.

Here is how the Jetson Orin Nano improved in benchmarks for three key AI applications.

#### LLM (Large Language Model)

A Large Language Model is an application that understands text and language. It can generate human-like responses to randomly formatted questions. Examples of LLMs are ChatGPT, Gemini, and CoPilot.

Here are the increases in LLM performance achieved with the Super update:

![](https://i0.wp.com/dronebotworkshop.com/wp-content/uploads/2025/01/Jetson-Orin-Nano-LLM_small.jpg?resize=750%2C422&ssl=1)

#### VLM (Vision Language Model)

A Vision Language Model goes one step further and accepts input from both images and text. These can then be used to produce an output, which can be in the form of text or an image. Typical applications for VLMs are Image Captioning and Text-to-image Generation.

The Super Update provides significant performance improvements with several VLM benchmarks:

![](https://i0.wp.com/dronebotworkshop.com/wp-content/uploads/2025/01/Jetson-Orin-Nano-VLM_small.jpg?resize=750%2C422&ssl=1)

#### ViTs (Vision Transformers)

A Vision Transformer is a Neural Network used for image recognition. ViTs are used for Image Classification and detection. They are found in medical imaging devices and self-driving automobiles.

The Super boost gives the Jetson Orin Nano a considerable performance improvement when running ViTs.

![](https://i0.wp.com/dronebotworkshop.com/wp-content/uploads/2025/01/Jetson-Orin-Nano-ViTs_small.jpg?resize=750%2C422&ssl=1)

## JetPack 6.1

If you want to work with the Jetson Orin Nano, or any other Jetson, you must be familiar with the [NVIDIA JetPack SDK](https://developer.nvidia.com/embedded/jetpack).

JetPack is a comprehensive software suite that provides everything you need to develop, build, and deploy AI applications on your device. In addition to the Linux operating system, JetPack provides a comprehensive set of tools, libraries, and APIs that enable developers to create innovative AI-powered projects.

![](https://i0.wp.com/dronebotworkshop.com/wp-content/uploads/2025/01/Jetpack_small.jpg?resize=750%2C422&ssl=1)

JetPack 6.1 is the latest edition of JetPack. It includes tools for interfacing with GPIO, I2C, SPI, and UART pins. Its libraries also work with the Jetson Orin Nano’s MIPI CSI camera connectors. This enables users to build projects like home automation systems, AI-enhanced IoT devices, and robotic platforms with minimal effort.

Here is a breakdown of the core system components of JetPack 6.1:

**Jetson Linux 36.4**

- Based on Linux Kernel 5.15.
- Ubuntu 22.04-based root file system.

**Jetson AI Stack**

- CUDA 12.6 for GPU acceleration.
- TensorRT 10.3 for high-performance inference.
- cuDNN 9.3 for deep learning operations.
- VPI 3.2 (Vision Programming Interface) for image and video processing.
- DLA 3.1 (Deep Learning Accelerator) for AI workloads.
- DLFW 24.0 (Deep Learning Frameworks) support.

The first step in getting your new Jetson Orin Nano up and running is to install JetPack 6.1. Let’s do that right now!

## Getting Started with the Jetson Orin Nano – microSD Boot

The easiest method of getting started with the NVIDIA Jetson Orin Nano is to flash JetPack 6.1 onto a microSD card.

If you’ve worked with a Raspberry Pi microcomputer, then the process will seem familiar. The microSD is your “disc drive”; it holds your operating system and the entire file system. You can add an SSD or external USB storage to expand the file system.

While most “power users” will prefer to run the Orin Nano with an SSD, a microSD actually has a couple of advantages:

- It’s easy to set up.
- You can create multiple operating environments for different projects or different users.

Even if you intend to boot from an SSD, you might want to “experiment” using a microSD before making your final SSD-based installation.

### Selecting an SDXC Card

The microSD card holds both your operating system and data, so selecting a high-quality card with decent specifications is essential. I would strongly suggest that you restrict yourself to well-known brands purchased from a reputable source, as several counterfeit and low-quality “bargains” exist.

![](https://i0.wp.com/dronebotworkshop.com/wp-content/uploads/2025/01/MicroSD-Specs_small.jpg?resize=750%2C422&ssl=1)

You can purchase quality microSD cards from a local photography or electronics store. You can also get them at the “big box” stores or on Amazon. If you go the Amazon route, try to select cards sold by Amazon themselves instead of third-party sellers. You CAN buy from third-party sellers if you read their reviews and determine they are a good vendor – most are, but a few fly-by-night outfits see Amazon as a method of making a quick dollar. Buyer beware!

Here is a guide to selecting an SDXC card for your NVIDIA Jetson Orin Nano:

- Size – 64GB or higher. I suggest at least 128 GB, as you’ll quickly fill a smaller card.
- Specs – Class 10, UHS-1, V30 or better.
- Read Speed – 90 MB/s or netter.
- Write Speed – 50 MB/s or better.

As a reference, I have been using SanDisk Ultra 256 GB cards with good results.

### Flashing JetPack 6.1

Now that your microSD card is ready, you’ll need to get JetPack 6.1 flashed onto it. The first step is as simple as visiting [NVIDIA’s JetPack Download page](https://developer.nvidia.com/embedded/jetpack) and scrolling down to the *“Installing JetPack* ” section. You will find two selections – *SD Card Image Method* and *NVIDIA SDK Manager Method*.

![](https://i0.wp.com/dronebotworkshop.com/wp-content/uploads/2025/01/nvidia-jetpack-install-page_small.jpg?resize=750%2C422&ssl=1)

Click on the JETSON ORIN NANA DEVELOPER KIT text in the *SD Card Image Method* section. This will expand the section, and you’ll see two buttons for downloading. Click the “ *Download for Jetson Orin Nano Super Developer Kit”* box to download the JetPack 6.1 image.

The image file is pretty big, so downloading may take a while.

Once you have the image, you’ll need a method of flashing it onto a microSD card. Several applications can do this, and you may already have a favorite. Otherwise, a good choice is [Balena Etcher](https://etcher.balena.io/). It’s free, available for Linux, MacOS, and Windows, and easy to use.

![](https://i0.wp.com/dronebotworkshop.com/wp-content/uploads/2025/01/balena-etcher-page_small.jpg?resize=750%2C422&ssl=1)

Use Etcher (or another application) to select the file you downloaded as a source and your microSD card as the destination. Then, flash the file onto the microSD card.

![](https://i0.wp.com/dronebotworkshop.com/wp-content/uploads/2025/01/etcher-image-flash_small.jpg?resize=750%2C422&ssl=1)

### Installation and Setup

Remove the microSD card from your workstation. Connect a keyboard, mouse, video monitor, and (optionally but preferably) an ethernet cable to the Jetson Orin Nano, but don’t connect the power supply.

Now insert the microSD card into the socket, which is hidden on the underside of the Nano module (above the carrier board). Make sure to insert the card with the correct orientation; the SDXC card contacts should face the underside of the Nano module.

Now connect the power. Wait a few agonizing seconds while nothing is displayed on the screen. Eventually, an NVIDIA logo will appear, followed by a lot of text—a typical Linux boot sequence.

You’ll soon arrive at the first Ubuntu configuration screen, a license agreement. Accept it and continue. You must also confirm your language, location (for your timezone), and keyboard type. You’ll also need to determine a partition size; just accept the default, which is the entire available file space.

You’ll also need to choose a username and password, as well as a name for the Jetson Orin Nano.

At one point, you will be asked if you wish to install the Chromium web browser, which is not usually part of an Ubuntu 22.04 installation. You should do that, as many NVIDIA applications will work best on Chromium. Chromium will install in its own small terminal window, which you will need to close for the Ubuntu installation to continue.

The rest of the installation proceeds on its own. When finished, it will restart and bring you to an Ubuntu login screen. Login and take a look at your new desktop.

### First Boot

Your first boot will bring you to a standard Ubuntu 22.04 desktop with a splashy green NVIDIA background. The desktop has been prepopulated with shortcuts to some helpful NVIDIA resources:

- [**Jetson Developer Zone**](https://developer.nvidia.com/embedded-computing) – A great place to begin your Jetson journey. Links to a variety of developer resources for the NVIDIA Jetson platform.
- [**Jetson Zoo**](https://elinux.org/Jetson_Zoo) – A goldmine of instructions for installing popular open-source applications on the Jetson.
- [**Jetson Community Projects**](https://developer.nvidia.com/embedded/community/jetson-projects) – A repository for projects for every Jetson model. There’s not much for the Orin Nano yet, but with the release of the Super Developers Kit, that situation should change very soon.
- [**Jetson Support Forums**](https://forums.developer.nvidia.com/c/agx-autonomous-machines/jetson-embedded-systems/70) – NVIDIA’s Jetson support forums. You’ll need an NVIDIA account to participate, which is something you should have anyway.

You will see a small NVIDIA logo on the left side of the taskbar (on the top of the screen), followed by some text. This displays the power mode you are currently operating with. Click on the logo to display a menu.

![](https://i0.wp.com/dronebotworkshop.com/wp-content/uploads/2025/01/jetson-orin-nano-desktop_small.jpg?resize=750%2C422&ssl=1)

The *Power mode* menu item allows you to select between three modes:

- 0: 15W – 15 Watt mode
- 1: 7W – 7 Watt mode
- 2: MAXN – 25 Watt mode

The last option is part of the Super upgrade.

The *Run Jetson Power GUI* option launches an application that displays dozens of statistics on power consumption and temperature for each component in the Jetson system.

Otherwise, this is a standard Ubuntu desktop, and you can use it as such. But, of course, you’ll want to use it to develop amazing AI applications!

## Booting the Jetson Orin Nano from SSD

Booting from a Solid State Drive (SSD) has several advantages:

- SSDs are much faster than microSD cards.
- SSDs are available with larger capacities than microSD cards.
- On a per-byte basis, SSDs are less expensive.
- SSDs are more reliable than microSD cards.

The NVIDIA Jetson Orin Nano has space for two SSDs. You can use either one as a boot drive.

### Selecting an SSD

When selecting an SSD for your Jetson Orin Nano, you must consider more than just the form factor.

![](https://i0.wp.com/dronebotworkshop.com/wp-content/uploads/2025/01/SSD-NVME-Only_small.jpg?resize=750%2C422&ssl=1)

The first and most important consideration is that the Jetson Orin Nano only supports NVMe PCIe drives. **You cannot use SATA SSD drives in this machine**. While they have the same form factor, SATA drives can usually be identified by the 2nd notch on their data connector.

The second consideration is that the **Jetson Orin Nano supports Generation 3 drives**, which are not the latest generation of SSD. You can use a Generation 4 drive, and it will work, but it will operate in a downgraded mode, so you won’t be able to take advantage of its performance improvements. Generation 4 drives are typically more expensive than their older cousins, so you would be wasting money using one.

### Installing an SSD

Installation of a Solid State Drive in the NVIDIA Jetson Orin Nano is straightforward.

The Orin Nano has two vacant M.2 slots, one for a 2230 form factor SSD and the other for the more popular 2280 form factor. Each slot has a mounting screw, which must be removed with a small cross-head (Phillips) screwdriver before installing the SSD.

![](https://i0.wp.com/dronebotworkshop.com/wp-content/uploads/2025/01/jetson-orin-nano-ssd-recovery-jumper_small.jpg?resize=750%2C422&ssl=1)

SSDs are somewhat fragile, so handle yours with care. An anti-static strap is highly recommended when working with SSDs.

After removing the mounting screw, insert the SSD into the M.2 socket; it will only fit in one way as the socket is keyed. Once it is in, it will hold itself in place at about a 45-degree angle above the carrier board. You’ll need to push it down and secure it with the mounting screw.

### NVIDIA SDK Manager

[NVIDIA SDK Manager 2.2.0](https://developer.nvidia.com/sdk-manager) is a graphical tool that simplifies the setup and management of NVIDIA software development kits (SDKs) for Jetson devices and other NVIDIA platforms.

We will be using SDK Manager to flash the JetPack image onto our SSD.

SDK manager runs on its own workstation, and you’ll connect to the Jetson Nano using a cable connected to the Nano’s USB-C port. You can use one of two methods to run SDK Manager on the workstation:

- Use an Ubuntu 20.04 or 22.04 workstation.
- Use a workstation running any operating system and use Docker.

I’ll be providing instructions for using Ubuntu Linux 22.04.

### Ubuntu 22.04 Workstation

I should point out this note from NVIDIAs SDK Manager page:

*“Ubuntu 24.04: add support to SDK Manager client application. Specific SDK support will be added later”*

After seeing that, I tried using SDK Manager with Ubuntu 22.04, as it is Ubuntu’s most recent LTS version. However, it failed on the flash process with a syntax error (if I’m reading the log files correctly). So, I would stick with Ubuntu 22.04.

You’ll need an Ubuntu 22.04 (or 20.04) workstation with the following requirements:

- At least 8GB of RAM. SDK Manager will refuse to run with anything less.
- At least 100 GB of free storage space.
- An available USB 3 port.
- A wired Ethernet connection is preferable; Wi-Fi will likely be problematic.

Since 22.04 is not the current Ubuntu release, you’ll probably have to assemble a workstation. You can do that in a few ways:

- Use an old or “retired” computer – That is the method I used.
- Dual Boot – If you can provide a partition of at least 128GB, you can install Ubuntu 22.04 on that partition.
- External SSD (or large memory stick) – Install Ubuntu 22.04 onto an external SSD. Set your workstation to boot from that SSD. Use it for SDK Manager; when you are done, unplug the SSD, and your workstation should return to normal.

You can [download the Ubuntu 22.04 installation files](https://releases.ubuntu.com/jammy/) and use [Etcher](https://etcher.balena.io/) to burn them to a USB stick.

If you install Ubuntu, you can install the minimal system, as you won’t need all the OpenOffice products and other bloatware. This will save both space and time. When you finish the installation and start for the first time, you’ll be asked if you want to both update and upgrade Ubuntu. Choose “YES” to update and “NO” to upgrade – **don’t upgrade this workstation, or you’ll defeat the purpose of building it!** Updating, however, is always a good thing.

**You should also turn off the screen blanking in the Power section of the Ubuntu setup.** The flashing procedure takes a long time, and some computers turn off their USB ports when the screen blanks. This will interrupt the flashing process.

### Install SDK Manager on Ubuntu Workstation

Now that your Ubuntu 22.04 workstation is ready, it’s time to install the SDK Manager.

You can use the Firefox web browser installed with Ubuntu 22.04 to visit [NVIDIA’s SDK Manager web page](https://developer.nvidia.com/sdk-manager). You’ll see a link to download the.deb file, which you need for Ubuntu 22.04.

To download the file, you’ll need a free NVIDIA account. One thing to be aware of is that NVIDIA does account verification via email every time you log in, so you will need access to your email account as well. If you have browser-based email, you can access it via Firefox.

After downloading the file, leave your browser open; you can minimize it or work on a new desktop if you wish. You’ll need to log in to NVIDIA again during the SDK Manager setup, so having the window open and logged in makes things a bit easier by skipping a second verification step.

![](https://i0.wp.com/dronebotworkshop.com/wp-content/uploads/2025/01/sdk-manager-ubuntu-install_small.jpg?resize=750%2C422&ssl=1)

Open the *Files* application and navigate to the *Downloads* folder, where you should find your SDK Manager installation file. Right-click on it, and select “ *Open with other application* ”. From the list of software applications, select “ *Software Install*.”

The Software Installer will open. You can use it to install the application, giving permissions and accepting the risk of installing a 3rd party application. You’ll need to provide a password.

Once the software is installed, you can open it. You may wish to place a shortcut in your Favorites bar at the left of the screen.

The SDK Manager’s initial screen summarizes the source and target device information. Your target is likely unknown right now, but we will change that.

### Put the Jetson Nano into Recovery Mode

To flash files to a blank SSD, your Jetson Orin Nano needs to be placed into “Recovery Mode.”

When you put the board in Recovery Mode, it bypasses the normal boot process and enters a state where it can receive new system files through its USB port. When in Recovery Mode, the Jetson Orin Nano:

- Bypasses the normal boot process and loads a minimalistic environment.
- Provides access to the device’s flash memory and other system components.
- Allows for low-level system access and maintenance.
- Enables firmware updates, flashing new operating systems, and other system modifications.

You can put the Jetson Orin Nano into recovery mode by placing a jumper between pins 2 and 3 on the Button Header (the 14-pin DuPont connector on the top of the carrier board).

Install a jumper or strap, connect the Jetson Nano to the Ubuntu workstation via the USB-C connector, and power up the Jetson. SDK Manager should immediately see the device and will pop up a dialog box asking what type of Orin Nano you have. Select the *Development Board* option and continue.

![](https://i0.wp.com/dronebotworkshop.com/wp-content/uploads/2025/01/sdk-manager-select-device_small.jpg?resize=750%2C422&ssl=1)

You can now proceed to the second step by clicking the *CONTINUE* button in the bottom right of the SDK Manager screen.

### Select Files and Download

On the second screen, you will be presented with a list of components (files and images) you can install on your Jetson Orin Nano. Many have already been selected; you can choose additional ones if you wish.

These files must be downloaded and assembled into a package that can be flashed to your Jetson Orin Nano. This will take some drive space; depending upon which components you selected, you could require up to 100 GB of free space.

![](https://i0.wp.com/dronebotworkshop.com/wp-content/uploads/2025/01/sdk-manager-step-2_small.jpg?resize=750%2C422&ssl=1)

At the bottom of the screen are two checkboxes. Read them carefully before selecting them:

- The first checkbox is the license agreement. You must click this to continue, so select it.
- The second checkbox is an option to download the component files now and install them later. You should leave this unchecked, as you likely want to proceed with the installation immediately.

After selecting the license agreement, the *CONTINUE* button will be enabled. Click it, and a dialog box will pop up asking permission to create two directories to hold the download files and the new image for upload. After accepting the file creation, you must click *CONTINUE* again to proceed.

After clicking *CONTINUE,* another dialog box will appear, requesting your Linux password. Provide it, and you will move on to the third page.

![](https://i0.wp.com/dronebotworkshop.com/wp-content/uploads/2025/01/sdk-manager-downloading_small.jpg?resize=750%2C422&ssl=1)

The third page illustrates the download progress. This can take a fair bit of time and occasionally fail. If it does, you’ll be given an opportunity to retry. It helps if you keep the Internet traffic down on your network while this is happening to provide SDK with Manager as much bandwidth as possible.

### Flash to the SSD

When the downloads have been successfully completed, you will get a dialog box indicating that SDK Manager is about to flash the files to the Jetson Orin Nano. You need to make a few selections here.

![](https://i0.wp.com/dronebotworkshop.com/wp-content/uploads/2025/01/sdk-manager-flash-dialog_small.jpg?resize=750%2C422&ssl=1)

The first thing you need to do is select the OEM Configuration. You have two choices here:

- **Pre-Config** – If you select this, you will need to provide a username and password for your new Jetson Nano admin account. SDK Mager will configure the Ubuntu Linux installation with this information. You won’t have to run the Ubuntu setup if you select this.
- **Runtime** – If you select this, you will not need to provide a username and password. When the Jetson first boots up, you must go through the configuration and create a new user account, just as we did with the microSD card.

Either selection is suitable; if you choose Pre-Config you must enter a username and password.

The second selection is the target device. You have several choices, including a microSD card and an external USB drive. However, we are installing to an SSD, so select the *NVME* option.

Once you are done, click the *Flash* button to proceed. You will move to the next screen, and the image will begin flashing. You can monitor its progress if you like.

### SDK File Installation

After the image has flashed, the SDK Mager will display another dialog box. This box controls how the SDK Components are installed on the board.

![](https://i0.wp.com/dronebotworkshop.com/wp-content/uploads/2025/01/sdk-manager-sdk-components-install_small.jpg?resize=750%2C422&ssl=1)

At this point, the Ubuntu Linux installation on the Jetson Orin Nano has been completed; however, the SDK components still need to be installed. This is done with a file transfer.

The transfer is done using TCP/IP and can be done by Ethernet or USB. To initiate it, you need to get the IP address of the Jetson Orin Nano.

![](https://i0.wp.com/dronebotworkshop.com/wp-content/uploads/2025/01/jetson-orin-nano-network-address_small.jpg?resize=750%2C422&ssl=1)

At this point, your Nano is powered up with the Recovery Mode jumper in place. Do the following:

- Leave the Jetson Orin Nano powered on.
- Connect a video monitor, mouse, keyboard, and ethernet cable to the Jetson Nano.
- Pull out the Recovery Jumper
- You should now get video on your Nano monitor. If you did a Pre-Select installation, you will be at the Ubuntu login screen. If you chose a Runtime installation then you will be at the Ubuntu setup menu.
- Login to Ubuntu (complete the setup if necessary).
- Click on the networking icon in the top right corner of the taskbar.
- Click on PCI Ethernet Connected or on any of the USB connections (they all have the same IP address)
- On the network display, click on the Setting icon (gear).
- Make a note of the IP address.

Now, back on the SDK Manager, fill in the IP address you just obtained. You can leave the connection at USB if you wish. Provide your Nano username and password (if you chose Pre-Select, they have already been entered for you).

Now click Install. The SDK files will be transferred to your Jetson Orin Nano board. Once they have completed your Nano is ready to go!

### Install Chromium Browser

You will need to install the Chromium web browser on your new Jetson Orin Nano, as it is needed for many operations. Go back to the Jetson Nano and search for the Software application.

![](https://i0.wp.com/dronebotworkshop.com/wp-content/uploads/2025/01/jetson-orin-nano-chromium-install_small.jpg?resize=750%2C422&ssl=1)

Search the Software application for “chromium.” Select it from the results list and install it.

Once the installation is finished you can test it using one of the NVIDIA-supplied icons on the desktop.

## Building an AI Chatbot

Now that your Jetson Orin Nano is all set up with JetPack, we should run a quick project to see it in action. How about building an AI chatbot that runs locally on the Nano and doing it without writing any code?!

We can accomplish such a miracle by using Ollama.

### Ollama

Ollama is an open-source software platform that allows users to run large language models (LLMs) locally on their computers rather than relying on cloud services.

![](https://i0.wp.com/dronebotworkshop.com/wp-content/uploads/2025/01/Ollama_small.jpg?resize=750%2C422&ssl=1)

It’s designed to be user-friendly while providing powerful features for running, managing, and customizing various open-source language models, including Llama 2, Mistral, and CodeLlama.

Ollama is particularly notable for its efficient resource management and ability to optimize models for different hardware configurations, making it possible to run sophisticated AI models even on relatively modest hardware.

As it is optimized for edge devices, Ollama is particularly suited to platforms like the Nvidia Jetson Orin Nano.

We will use Ollama to run the LLaMAa 3.2 collection of Large Language Models as a basic chatbot.

### Installing Ollama

The installation of Ollama is very simple. Start by opening a web browser and visiting [Ollama’s download page](https://ollama.com/download).

Open a Terminal, NVIDIA has placed a terminal link on the desktop. Copy the text from the “install with one command” section:

| 1 | curl \- fsSL https://ollama.com/install.sh \| sh |
| --- | --- | --- |

Paste this into the terminal. This will start the Ollama installation. You will be asked for your administrator password at the start of the procedure.

![](https://i0.wp.com/dronebotworkshop.com/wp-content/uploads/2025/01/ollama-commands_small.jpg?resize=750%2C422&ssl=1)

It takes several minutes to download and install Olama. Once it is done, you can check it out by just typing in “ollama” at the terminal command prompt. If all is working properly, you’ll see a list of Ollama commands.

### Running LLaMA 3.2

LLaMA 3.2 (Large Language Model Meta AI) is an iteration in Meta’s series of advanced language models. LLaMA 3.2 excels in natural language understanding and generation tasks, making it ideal for applications such as question answering and code generation.

Ollama simplifies running LLaMA 3.2, all you need to do to install this collection of Large Language Models is to type the following in the Terminal:

| 1 | ollama llama3.2 |
| --- | --- |

And that’s it! LLaMA 3.2 will load; give it a few minutes as there are several modules. Once it’s finished, you can start conversing with it at the command prompt.

### Testing the AI Chatbot

With Ollama running LLaMA 3.2, you can enter your questions at the command prompt in the terminal. The cursor will become animated while it is “thinking.”

The first time it runs, it takes a very long time, but after that, the responses are quick. Test it out as you would test ChatGPT, ask it questions, and marvel at its answers.

Remember, LLaMA 3.2 runs locally on the Nano, and it will work just as well without an Internet connection. Just think of the fantastic projects you can build by harnessing a power like that!

## Conclusion

As soon as I got my hands on the NVIDIA Jetson Nano, I thought of an excellent use for it. So, keep your eyes on the DroneBot Workshop, as I’m planning a robotics project that uses the Jetson Orin Nano. I think it’s the perfect combination of size, power consumption, and AI computing power.

The NVIDIA Jetson Orin Nano Super Developer Kit provides experimenters and makers with a powerful AI engine at a very reasonable price. As with the original Jetson Nano board, this powerful microcomputer is sure to inspire many innovative AI projects.

So, what would you design with a tiny electronic brain? Let me know!

### Parts List

*Here are some components you might need to complete the experiments in this article. Please note that some of these links may be affiliate links, and the DroneBot Workshop may receive a commission on your purchases. This does not increase the cost to you and is a method of supporting this ad-free website.*

  
Jetson Orin Nano Super Developer Kit [NVIDI A](https://developer.nvidia.com/buy-jetson) [Amazon](https://amzn.to/42omotA)

Crucial 1TB PCIe 3 NVME SSD [Amazon](https://amzn.to/40yJRHp)

SanDisk Ultra 256 GB microSD [Amazon](https://amzn.to/40CQSXu)

### Resources

Article PDF – A PDF version of this article in a ZIP file.

[NVIDIA Jetson Orin Nano Super Developer Kit](https://www.nvidia.com/en-us/autonomous-machines/embedded-systems/jetson-orin/nano-super-developer-kit/) – Learn more about the kit and where to buy one.

[NVIDIA JetPack SDK](https://developer.nvidia.com/embedded/jetpack) – Learn about JetPack 6.1 and download a microSD image.

[NVIDIA SDK Manager](https://developer.nvidia.com/sdk-manager) – The SDK manager tool for Ubuntu and Docker.

[Ubuntu 22.04 Download](https://releases.ubuntu.com/jammy/) – Download the image for Ubuntu 22.04 (Jammy).

[Olama Download Page](https://ollama.com/download) – Downloads for Ollama clients for every operating system.

[Etcher Download Page](https://etcher.balena.io/#download-etcher) – Download Etcher.

- [Two Raspberry Pi AI Cameras →](https://dronebotworkshop.com/2-pi-ai-cameras/)