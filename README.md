# Headless Kali Linux RPi Setup Guide
A "headless" setup means any host on the network can remotely connect to a 
client via SSH or VNC. This removes the need for peripherals to operate a server.  
In this walkthrough, we will be setting up a remote Kali Linux setup on ARM architecture.
(RPi 1, 2, 3, 4, 400, Zero, Zero W)
## Download Kali-ARM images
Initially, we need to download a Kali Linux image that is compatible with ARM architecture.<br/>
- [Download Link](https://www.kali.org/get-kali/#kali-arm)

## Load the image into any image flasher
For this step, we're going to use [Etcher](https://github.com/balena-io/etcher). Head over to [this
repo] and follow the guide to install and configure Etcher on your OS.
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
