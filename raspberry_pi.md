# Openclaw on Raspberry PI


## OS install

### First boot
- Downdload the SD-card image [from this page](https://www.raspberrypi.com/software/operating-systems/)
  - the **Raspberry Pi OS Lite** with no desktop environment is probably the best
- Extract the *.img.xz file to get the *.img file, note it's not an ISO file
- Write the iamge file to the SD-card using, you can use
  - [Raspberry Pi Imager](https://www.raspberrypi.com/software/),, or
  - **Disks** application in Gnome/Debian/Ubuntu, or
  - using the **dd** command as root in a shell
 
``` bash
## find the SD card device
lsblk

## Write the image
sudo dd if=/path/to/your/image.img of=/dev/sdX bs=4M status=progress conv=fsync

## Close and eject
sync
sudo eject /dev/sdX
```

- Insert the SD card in the raspberry, connect keyboard and monitor
- boot the raspberry
- when asked provide
  - locale info for OS and keyboard
  - main user: **we'll assume you use bot as user**
  - password for the bot user

### Configuration

- Right afther the first boot you'll be promped to login.
- provide the credentials for user **bot**
- become root
``` bash
sudo su -
```
- **run the raspi-config configuration tool**

> **Note:**
> **raspi-config** is the recomended way to configure you raspberry, all other manual methods are clumsy and might fail

-  in raspi-config make sure you configure:
  - System Options:
    - Wireless LAN
    - Audio: use the 3.5mm jack
    - Hostname: openclaw or something
  - Interface options:
    - SSH: enable
  - Update: now that networking is enabled via WiFi run un update
  - Advanced Options:
    - Expand Filesystem
    - Network interfaces names: enable predictable names
  - **Rebbot**

### Checks and update

``` bash
## Get the local IP and write it down
ip a

## Check SSH is listening
netstat -tnap | grep sshd

## Check networking and DNS resolution
ping google.com

## Update the OS
apt-get update
apt-get -y upgrade
```

## Connect via SSH using public key

Perform the following steps steps **in another host in the same network**.

If you **DON'T** already have an SSH key pair ofr you user:
``` bash
ssh-keygen -t ed25519 -N ""
```
Use the key-pair to connect to the rasbperry as the user **bot**

``` bash
## Transfer your public key to the raspberry
ssh-copy-id -o PreferredAuthentications=password -o PubkeyAuthentication=no -i ~/.ssh/id_ed25519.pub bot@<raspberry_IP_address>

## then connect
ssh bot@<raspberry_IP_address>
```


## Software dependencies

First lets add some packages that we'll need later on
``` bash
apt-get install -y git vim 
```
