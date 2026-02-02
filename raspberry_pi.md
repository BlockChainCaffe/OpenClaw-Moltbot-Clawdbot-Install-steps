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
----

## System configuration

### Enable conncetions via SSH using public key

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


### Basic software dependencies

First lets add some packages that we'll need later on
``` bash
apt-get install -y git vim 
```

### Install homebrew (Linuxbrew)
Homebrew/Linuxbrew is required to install skills etc. Although the installation scripts shows you how to install it later, it makes sense to install it before so to have more control on the installation issues etc.
As the **bot** user:

``` bash
## Run install script
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

## permanently add brew to path
echo 'eval "$(/home/linuxbrew/.linuxbrew/bin/brew shellenv)"' >> ~/.profile
source .profile

## Check brew is in path and works
echo $PATH
brew --version
brew doctor
```

----

## System security

Since we are using a Raspberry PI to host Openclaw, the hardware is likely to be sitting on your desk or not far from that.
At the end of the installation the bot will allow connection only on 127.0.0.1 and via SSH. Unless you are planning to open
those ports to the outside world, **which is NOT advisable**, you'll interact remotely with your bot only via Telegram and use 
the browser interface only when you are in the same network of the bot (i.e. home or office).
This does not mean your installation is secure per-se, but that it's less exposed than, say, a remote VPS.

----

## External services

### 1) Create Telegram bot

While there are several chat/channels available, Telegram is recognized as the simplest and more convenient.
Creating the Telegram bot account now allow for a cleaner approach later.

- chat with https://t.me/BotFather
- issue a new bot with /newbot
	- give it a name
	- give it a **username**
- Usernames are 5-32 characters long and are case insensitive, but may only include Latin characters, numbers, and underscores. Your bot's username **must end in "bot"**
- if you are stuck into finding a username for your bot as all names turn out to be invalid or taken **restart botfather** (right click and then `delete chat` or `clear history` and retry)

- get the bot API key, looks like : XXXXXXXXXX:XXX_XXXXXXXXXXXXXXXX_XXXXXX_XXX-XXX

- *Optional*: create a profile picture for your bot and add it:
	- in BotFather pick /mybots
	- edit --> edit botpic --> upload image


### 2) Get the Google Gemini Api Key 

In this example we'll be using Gemini as a "backend" LLM. Other LLMs can be used, in that case refer to the specific LLM on how to get the API key that will be provided to Openclaw.
Beware of considering API usage costs too.

- Go to  https://aistudio.google.com/api-keys
- create a new api key + new project
- be sure to pick the right plan (free or paid)
- write down your API key

----

## Bot installation

### 1) Bot onboarding script

> **NOTE** 
> **Do not run the installation as root**. Use the unprivileged, sudo enabled user **bot** instead

Run the one-line : 

``` bash
curl -fsSL https://openclaw.ai/install.sh | bash
```

Quick answers for a basic install
- Yes
- Quickstart
- Google --> Google Gemini Api Key
- Pick model `google/gemini-2-5-flash-lite`
- define a channel: pick `Telegram`
	- when asked inser the Telegram Bot API key
- Configure Skills now: yes
- preferred node package manager for skills: `npm`
- pick the skills you like from the lost (space to select, return to confirm). Example:
	- `himalaya`
	- `model-usage`
	- `openai-whisper`
	- `sag`
	- `summarize`

One of the last outptus will give you all the instructions you need:

```
◇  Dashboard ready
  Dashboard link (with token):
  http://127.0.0.1:18789/?token=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
  Copy/paste this URL in a browser on this machine to control Clawdbot.
  No GUI detected. Open from your computer:
  ssh -N -L 18789:127.0.0.1:18789 bot@192.168.122.225
  Then open:
  http://localhost:18789/
  http://localhost:18789/?token=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
  Docs:
  https://docs.clawd.bot/gateway/remote
  https://docs.clawd.bot/web/control-ui
```

Screenshot or copy+paste those lines as we'll need them soon


### 2) Get to the web gateway

Check if gateway is there

``` bash
netsta -tnap | grep 18789
```


run the SSL command **on your host**:

``` bash
ssh -N -L 18789:127.0.0.1:18789 bot@192.168.122.225
```

then point your browser to

``` bash
http://localhost:18789/?token=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
```

The web-page of the gateway should be there

----


