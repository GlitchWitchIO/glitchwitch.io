---
layout: post
title:  "Windows 10 On DigitalOcean"
tagline: "Build a Windows Droplet From Scratch"
description: "A guide to running Windows 10 on DigitalOcean using your own disk image."
tags: [notes, digitalocean, virtualization, qemu, windows]
date:   2018-09-19
comments: true
image:
  feature: blog/6/feature.jpg
  hero: blog/6/header.jpg
  credit: Ilkka Koivula
  creditlink: https://pixabay.com/en/water-flowing-spatter-flowing-water-2130047/
---
Recently I wanted to run a resource intensive Windows program but didn't really have the hardware or bandwidth to support it.

I started doing some digging into installing Windows on DigitalOcean as it seemed cheaper than putting credits into a proper Windows VPS host.

Search results for "Windows on Digital Ocean" primarily show a [paywalled blog post on whatuptime.com](https://www.whatuptime.com/installing-microsoft-windows-onto-digitalocean-droplet/), a [leaked copy of that guide](https://milankragujevic.com/how-to-install-windows-10-on-digitalocean) and [similar](https://joodle.nl/install-windows-10-creators-update-on-kimsufi-soyoustart-ovh-and-online-net/) [guides](http://windowstemplate.com/2017/03/22/windows-10-pro/) with pre-compiled images of dubious origin and trustworthiness.

While at least one of these guides seemed to work, I wanted to be able to compile the latest version of Windows myself using images downloaded directly from Microsoft and forgo the potential security (and legal) nightmares that could come from running Windows from an unofficial source.

Initially I tried to download the official Windows [Virtual Machines](https://developer.microsoft.com/en-us/microsoft-edge/tools/vms/), convert them to raw format using `qemu-img`, and dd them to the droplet. This however did not work for various reasons, most notably these images do not include the required drivers.

After some research and trial-and-error I was able to successfully create a VM image that worked on any-size droplet using the Windows ISO, VirtIO Drivers, and QEMU emulation. The following guide will walk you through the process I used so that you can make your own image and flash it to a DigitalOcean Droplet.

---
## Table of Contents
- 1 [Summary](/blog/2018-09/windows-10-on-digitalocean#summary)
- 2 [Preparation](/blog/2018-09/windows-10-on-digitalocean#preparation)
  - 2.1 [Build environment](/blog/2018-09/windows-10-on-digitalocean#build-environment)
  - 2.2 [Windows ISO](/blog/2018-09/windows-10-on-digitalocean#windows-iso)
- 3 [Windows Installation](/blog/2018-09/windows-10-on-digitalocean#windows-installation)
  - 3.1 [Drivers](/blog/2018-09/windows-10-on-digitalocean#drivers)
  - 3.2 [Disk Transfer](/blog/2018-09/windows-10-on-digitalocean#disk-transfer)
  - 3.3 [The Basics](/blog/2018-09/windows-10-on-digitalocean#the-basics)
  - 3.4 [Network Settings](/blog/2018-09/windows-10-on-digitalocean#network-settings)
  - 3.5 [Remote Desktop](/blog/2018-09/windows-10-on-digitalocean#remote-desktop)

## Summary

In this guide we will learn how to install Windows 10 1803 on Digital Ocean using the official Windows ISO. This process will take between 1-2 hours and requires some level of linux and virtualization knowledge.


## Preparation

If you don't already have a DigitalOcean account, you can use my referral code and [get $10 off your new account](https://m.do.co/c/feeb77af6f8f).

First create two droplets, in this example I created two 2GB instance named `imageserver` and `windows` respectively.

We will use `imageserver` temporarily to build and host the Windows installation files. As such it will be deleted at the end.

`windows` will be where our Windows installation will eventually live. This droplet can be any size.


For this guide to work `imageserver` must have at least 2GB of ram, however we can select a more powerful droplet as it will only be used temporarily and thus only costs a fraction of a cent for a faster installation process.

![](/assets/img/blog/6/digitalocean-droplet-names.png)


## Build environment

Once they have been spun up, we will ssh into `imageserver` and setup the required dependencies.

```shell
# Install qemu
apt-get update && apt-get install qemu -y

# Create disk image
qemu-img create -f raw windows10.img 16G

# Get virtio drivers
wget https://fedorapeople.org/groups/virt/virtio-win/direct-downloads/stable-virtio/virtio-win.iso

```

## Windows ISO
---

We will also need a copy of the Windows 10 ISO. To get this you will need to visit Microsoft's [ISO download page](https://www.microsoft.com/en-us/software-download/windows10ISO) to generate a unique link.


![](/assets/img/blog/6/microsoft-iso-select.png)


Select `Windows 10` and `English`, then right click on `64-bit Download` and select copy link location.


![](/assets/img/blog/6/microsoft-download-link.png)


Replace the link within the single quotes in the next section with your own. Make sure you keep the quotes and `-O Win10_1803_English_x64.iso` line.

```shell
# Download Windows 10 ISO
wget -O Win10_1803_English_x64.iso 'https://software-download.microsoft.com/pr/Win10_1803_English_x64.iso?t=72da8fbf-7dff-49e0-9fbe-7a61e523c2ac&e=1537466952&h=6662b277f04183b1a08d25fbc1256aec'
```

## Windows installation

Now that we have all the required dependencies, we can go ahead and start the installation. Using the following command we will start emulating the Windows ISO and a VNC server so we can connect to it.

```shell
# Start Windows 10 VM with VNC
 qemu-system-x86_64 \
  -m 1G \
  -cpu host \
  -enable-kvm \
  -boot order=d \
  -drive file=Win10_1803_English_x64.iso,media=cdrom \
  -drive file=windows10.img,format=raw,if=virtio \
  -drive file=virtio-win.iso,media=cdrom \
  -vnc :0 \
  # Press CTRL + C to stop virtualization

```

_Replace `-m 1G` with `-m 2G` or `-m 6G` if your `imageserver` droplet is 4GB or 8GB_


---


Now on your local machine you will need to use a VNC Viewer to access it. In this example I use `xtightvncviewer` on my local debian system. Replace `imageserver` with your servers IP.

```shell
# Install vncviewer
sudo apt-get install xtightvncviewer

# Connect to imageserver
vncviewer imageserver
```

After connecting to the VNC server we should see the Windows language selection screen.

![](/assets/img/blog/6/vnc-1.png)

If we hit `next` then `install now` we should be presented with a screen asking for our license key. Either enter your key or select `I don't have a product key` and continue to the selection screen. I choose Windows 10 Pro for this demo and grudgingly ignored the EULA.

_Hot tip: you can use the tab, enter and arrow keys to navigate the entire installation process._


## Drivers
*Now for the important bits!*

First select `Custom: Install Windows only (advanced)`.

![](/assets/img/blog/6/vnc-2.png)

---

Then select `Load driver` then click `browse`.

![](/assets/img/blog/6/vnc-4.png)

---

You'll want to scroll down to `CD Drive (E:) virtio-win-0.1.1`.

Now we can select the Network driver, which can be found in `E:\NetKvm\w10\amd64`.

We will also need to uncheck `Hide drivers that aren't compatible with this computer's hardware` then select the first option `Red Hat VirtIO Ethernet Adapter` and `next`.

![](/assets/img/blog/6/vnc-network-adapter.png)


Now we need to repeat this process again selecting `Load driver`, `browse` and scrolling down to `CD Drive (E:) virtio-win-0.1.1`.

This time we need to select `E:\viostor\w10\amd64` and install the `Red Hat VirtIO SCSI controller`.

---

We should now see our drive. Go ahead and highlight it then click `next` one last time.

![](/assets/img/blog/6/vnc-disk.png)

---

Now we wait for the installation to complete. This will take about 5 minutes.


Once we see the `Windows needs to restart to continue` screen we can close vncviewer, go back to our terminal, and hit CTRL+C to stop virtualization.

![](/assets/img/blog/6/vnc-restart.png)


## Disk transfer
Now that we have a Windows 10 image with the correct drivers we will need to compress it and transfer it to our Windows Droplet.

To do this first we compress the image using the following command.

```shell
# Compress image
dd if=windows10.img | gzip -c > windows10.gz
```

_This will take a a little while, so now is probably a good time to grab some water or stretch a bit._

The following will show up when it's done.

```shell_session
33554432+0 records in
33554432+0 records out
17179869184 bytes (17 GB, 16 GiB) copied, 723.762 s, 23.7 MB/s
```

Once the image has been compressed we can go ahead and host it using the following.

```shell
python3 -m http.server

```
---

Now we need to jump back to the DigitalOcean dashboard and open the `windows` droplet we created earlier.

First click `Recovery` on the bottom left, then select `Boot from Recovery ISO` and turn off the droplet using the switch in the top right.

![](/assets/img/blog/6/digitalocean-recovery.png)

As soon as the droplet is powered off we can hit the power switch again and bring it online.

Once it's powered on with the recovery ISO we can SSH into it. SSH is needed as the DigitalOcean console doesn't support the `|` `pipe` character.

```shell_session
--------------------------------------------------------------------

DigitalOcean Recovery Environment 18.04.1 (Zesty Zona)

This image has been mounted by the DigitalOcean Support Team.
When you have completed your work in the recovery environment
update your support ticket to request that your droplet be booted
to it's disk.

This rescue environment is based on Ubuntu 18.04.

--------------------------------------------------------------------
Last login: Wed Sep 19 19:32:54 2018
root@windows:~#
```

Now we need to type the following, replacing `imageserver` with the IP of your `imageserver` droplet.

```shell
wget -O- http://imageserver:8000/windows10.gz | gunzip | dd of=/dev/vda
```

This should take about 7 minutes. Near the end it may appear to hang, but it is just copying the file to the disk.

```shell_session
--2018-09-19 19:44:19--  http://imageserver:8000/windows10.gz
Connecting to imageserver:8000... connected.
HTTP request sent, awaiting response... 200 OK
Length: 3901220354 (3.6G) [application/gzip]
Saving to: ‘STDOUT’

-                              100%[=================================================>]   3.63G  39.2KB/s    in 7m 14s  

2018-09-19 19:51:34 (8.57 MB/s) - written to stdout [3901220354/3901220354]

33554432+0 records in
33554432+0 records out
17179869184 bytes (17 GB, 16 GiB) copied, 436.588 s, 39.4 MB/s
```

Once the copy is complete we can run `shutdown 0` and go back to the recovery page on the digital ocean dashboard. We then select `Boot from Hard Drive` and power the droplet back on.

We should now be able to press the `console` button in the top right to complete our Windows installation.


## The Basics
![](/assets/img/blog/6/windows-region-totalsuccess.png)

At this point we a have a mostly functional Windows install. We just need to complete the initial setup, configure the network settings and enable remote desktop.

Go ahead and fill in the basics, choosing `do this later` when you get to network settings, and put special care (or better yet random generation) into choosing a secure password.

Once the basics are done, we need to configure the network.


## Network Settings
We can do this by clicking on the network icon in the bottom right, then selecting `Network & Internet Settings`

Now we can click `Change adapter options` at the bottom of this window.


![](/assets/img/blog/6/windows-network-1.png)

---

From here we can select our `Ethernet` adapter and click the little drop down in the top right, then selecting `Change settings of this connection`

![](/assets/img/blog/6/windows-network-2.png)

---

Now double click on `Internet Protocol Version 4 (TCP/IPv4)` to bring up the IPv4 settings.

![](/assets/img/blog/6/windows-network-3.png)

---

You can use the information at the bottom of the Droplet Console to complete the next section. You will also need to enter DNS servers. In this example I used `1.1.1.1` provided by CloudFlare and `8.8.8.8` provided by Google.

![](/assets/img/blog/6/windows-network-4.png)


## Remote Desktop
Now that our droplet is connected to the network we will need to setup a better way to manage it. For this guide we will set up Remote Desktop Protocol (aka RDP), however you could also setup VNC or another remote desktop application if you prefer.

First we goto the start menu and search `Remote Desktop settings`

![](/assets/img/blog/6/windows-rdesktop-1.png)

---

From here we toggle the `Enable Remote Desktop` button, then click `Advanced settings`

![](/assets/img/blog/6/windows-rdesktop-2.png)

---

Lastly we need to uncheck `Require computers use Network Level Authentication` otherwise we will receive a CredSSP error when trying to connect.

![](/assets/img/blog/6/windows-rdesktop-3.png)

---

That's it! Go ahead and connect using one of Microsoft's [official remote desktop clients](https://docs.microsoft.com/en-us/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients) or [rdesktop](https://github.com/rdesktop/rdesktop/releases) if you're on Linux.

If you found this helpful and don't already have a DigitalOcean account you can use my referral code to [get $10 off your new account](https://m.do.co/c/feeb77af6f8f).
