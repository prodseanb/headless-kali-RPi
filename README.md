![8-Easy-Steps-of-Installing-Kali-Linux-In-Raspberry-Pi](https://user-images.githubusercontent.com/59718043/127959484-6963449f-d823-4a26-b04c-3e8fa6d6e6e8.jpg)
# Headless Kali Linux RPi Setup Guide
A "headless" setup means any host on the network can remotely connect to a 
headless client via SSH or VNC. This removes the need for peripherals to operate a server.  
In this walkthrough, we will be setting up a remote Kali Linux setup on ARM architecture.
(RPi 1, 2, 3, 4, 400, Zero, Zero W)
## Table of contents
- [Kali-ARM Installation](https://github.com/prodseanb/headless-kali-RPi#download-kali-arm-image)
- [Booting into Kali](https://github.com/prodseanb/headless-kali-RPi#boot-into-your-raspberry-pi)
- [OpenSSH Installation and Configuration](https://github.com/prodseanb/headless-kali-RPi#install-openssh-server)
- [Wireless Network Configuration](https://github.com/prodseanb/headless-kali-RPi#configure-wireless-network-availability-on-boot)
- [Auto-login Configuration](https://github.com/prodseanb/headless-kali-RPi#configure-auto-login)
- [References](https://github.com/prodseanb/headless-kali-RPi#references)
## Download Kali-ARM image
Initially, we need to download a Kali Linux image that is compatible with ARM architecture.<br/>
- [Download Link](https://www.kali.org/get-kali/#kali-arm)

## Load the image into any image flasher
For this step, we're going to use [Etcher](https://github.com/balena-io/etcher). Head over to [this
repo](https://github.com/balena-io/etcher) and follow the guide to install and configure Etcher on your OS.
<br/>
<br/>
![BalenaEtcher_v1 5 97](https://user-images.githubusercontent.com/59718043/127945937-b0aac1e8-49a0-4ab9-82c9-909d6e0b60d3.png)
- Make sure your chosen storage (SSD/Micro SD card) is inserted.
- Start Etcher
- Select the Kali-ARM image
- Select the storage
- Flash

## Boot into your Raspberry Pi
The default username is 'kali' and the password is 'kali'.<br/>
Remove unnecessary packages and apt cache to allow for upgrade:
```
sudo apt autoremove && sudo apt autoclean
```
Make sure you are connected to the network, then run the updates:
```
sudo apt-get update
sudo apt-get upgrade
sudo apt-get dist-upgrade
```
Update kali's password (for obvious reasons):
```
passwd kali
```

## Install OpenSSH Server
Run the following to install OpenSSH and update the runlevels to allow SSH on boot:
```
sudo apt-get install openssh-server
update-rc.d -f ssh remove
update-rc.d -f ssh defaults
```
Change the default keys:
```
cd /etc/ssh
mkdir insecure_old
mv ssh_host* insecure_old
dpkg-reconfigure openssh-server
```
Restart the SSH service:
```
sudo service ssh restart
update-rc.d -f ssh enable 2 3 4 5
```
Verify SSH functionality:
```
sudo service ssh status
```
The output should look similar to this:
```
● ssh.service - OpenBSD Secure Shell server
     Loaded: loaded (/lib/systemd/system/ssh.service; enabled; vendor preset: disabled)
     Active: active (running) since Sun 2021-08-01 19:18:18 UTC; 1 day 8h ago
```
If the SSH service is NOT running:
```
sudo service ssh start
```

## Configure wireless network availability on boot
Navigate into this directory:
```
cd /etc/NetworkManager/system-connections
```
Verify that a `.nmconnection` file exists, containing the connected wireless network's information.
You can copy the `template.nmconnection` from this repo into this directory and replace 
the `template` and
`password` fields if the file does NOT exist.<br/>
Change the permissions of this file: 
```
sudo chmod 600 yournetwork.nmconnection
```
Next, we're going to edit `NetworkManager.conf`:
```
sudo nano /etc/NetworkManager/NetworkManager.conf
```
Change the contents of the file to this (managed=true):
```
[main]
plugins=ifupdown,keyfile

[ifupdown]
managed=true
```
Next, we're going to edit `/etc/network/interfaces`. 
<br/>
Add these lines:
```
auto wlan0
iface wlan0 inet dhcp
```
## Configure auto-login
To make our Kali completely headless, we need to edit `/etc/lightdm/lightdm.conf`:
```
sudo nano /etc/lightdm/lightdm.conf
```
Find these lines:
```
#autologin-user =
#autologin-user-timeout =
```
There will be 4 lines (2 sets of occurences) of these same lines in this file. 
Change all occurences to this:
```
autologin-user=kali
autologin-user-timeout=0
```
Find this line and uncomment:
```
#pam-autologin-service=lightdm-autologin
```
You can also take a look at `lightdm.conf` in this repo, copy and paste everything to your
own lightdm.conf file.
<br/><br/>
Done! Run `ifconfig` to find Kali's IP. We're going to need this to SSH into it.
You can now boot up your Pi without a monitor or keyboard and connect via SSH:
```
ssh kali@<ip-address>
```
## References
- [Defsecone](https://www.youtube.com/watch?v=VXxb_F1Vb7Y)
- [null-byte](https://null-byte.wonderhowto.com/how-to/set-up-headless-raspberry-pi-hacking-platform-running-kali-linux-0176182/)

