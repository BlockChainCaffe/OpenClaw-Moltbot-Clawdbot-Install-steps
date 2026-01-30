# OpenClaw-Moltbot-Clawdbot-Install-steps
A quich guide for get you started for real





## Naming convcention notice
> Just in the last few days `Clawdbot` changed name to `Moltbot` due to copyright claims, and then
to `OpenClaw`. Things are moving fast...
> As a result, at the end of the installation your program might have different names: `clawdbot`, `moltbot` or `openclaw` depending on the version you run/downloaded
> In this guide the name `openclaw` will be used.

----

## Before we install : Requirements

## Minimum Hardware Requirements

These are the requirements to have it run without any local storage or computation

- **CPU:**  2–4 vCpu Cores.
- **RAM:** 2GB
- **Storage:** 10GB / 20GB – 50GB (To accommodate larger SQLite/PostgreSQL databases for conversation logs)

If you plan to run local models on OLLAma or locally store a lot of file the previous requirements are not enought.


### OS install

- OS Debian (minimal net-install)
- 4Gb ram (8 is better?)
- 25Gb ssd - 21 free Gb remains afther OS install

### Software Requirements

First lets add some packages that we'll need later on
```
apt-get install -y sudo git vim net-tools build-essential procps curl file
```

### Sudo enablet user 'bot'

All the following procedure must be run by a **non-root user** that... can act as root 
We'll create the user **bot** for that purpouse

```
## Become root if not already
sudo su -

## Create a new user (answer the prompts, provide a **strong** password)
adduser bot

## Add bot to sudo group
usermod -aG sudo bot

## Allow to run sudo commands without password request: create a dedicated sudo file for bot
visudo -f /etc/sudoers.d/bot

## In the file add this line, save and exit
bot ALL=(ALL) NOPASSWD: ALL
```

Now save and become the **bot** user

### Install homebrew (Linuxbrew))

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

You might want to repeat the .profile part as root as well

### Basic security settings (mostly for VPS):

openclaw is a security nightmare so:

- harden your ssh
	- move ssh to a different port
	- disable password login
	- allow public-key login only
- install and enabel fail-2-ban
- install and enable a firewall (ufw/iptables)
- install wireguard or other VPN software and allow access onlu via VPN

----

## Bot nstallation steps

### 1) Create Telegram bot
- chat with https://t.me/BotFather
- issue a new bot with /newbot
	- give it a name
	- give it a **username**
- Usernames are 5-32 characters long and are case insensitive, but may only include Latin characters, numbers, and underscores. Your bot's username **must end in "bot"**
- if you are stuck into finding a username for your bot as all names turn out to be invalid or taken **restart botfather** (right click and then `delete chat` or `clear history` and retry)

- get the bot API key, looks like : XXXXXXXXXX:XXX_XXXXXXXXXXXXXXXX_XXXXXX_XXX-XXX


- *Optional*: create a profile picture for your bot and add it:
	- in BotFather pick /mybots
	- edit --> edit botpic --> upload imge


### 2) Get the Google Gemini Api Key (or other AI OAuth provider)
- Go to  https://aistudio.google.com/api-keys
- create a new api key + new project
- be sure to pick the right plan (free or paid)
- write down your API key


### 3) ClawdBot Onboarding

> **NOTE** 
**Do not run the installation as root**. Use the unprivileged, sudo enabled user **bot** instead

Run the one-line : 

``` bash
curl -fsSL https://openclaw.ai/install.sh | bash
```

Quick answers for a basic install
- yes
- quickstart
- Google --> Google Gemini Api Key
- Pick model `google/gemini-2-5-flash-lite`
- define a channel: pick `Telegram`
	- when asked inser the Telegram Bot API key
- preferred node package manager for skills: `npm`
- pick the skills you like from the lost
	- himalaya
	- model-usage
	- openai-whisper
	- sag
	- summarize


### 4) Connect to the gateway

One of the last outptus will give you all the instructions you need:

```
◇  Dashboard ready
  Dashboard link (with token):
  http://127.0.0.1:18789/?token=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
  Copy/paste this URL in a browser on this machine to control Clawdbot.
  No GUI detected. Open from your computer:
  ssh -N -L 18789:127.0.0.1:18789 conio@192.168.122.225
  Then open:
  http://localhost:18789/
  http://localhost:18789/?token=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
  Docs:
  https://docs.clawd.bot/gateway/remote
  https://docs.clawd.bot/web/control-ui
```

Check if gateway is there

``` bash
netsta -tnap | grep 18789
```


run the SSL command **on your host**:

``` bash
ssh -N -L 18789:127.0.0.1:18789 conio@192.168.122.225
```

then point your browser to

```
http://localhost:18789/?token=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
```
### 5) Complete Installation

``` bash
openclaw doctor
```

Complete the parinig with the telegram bot:
- go to your bot in Telegram
- type "/start" then send a simple "Hello" message
- the bot will reply with
```
Clawdbot: access not configured.
Your Telegram user id: XXXXXXXX
Pairing code: XXXXXXXX
Ask the bot owner to approve with:
openclaw pairing approve telegram <code>
```

You can pass those values in the gateway chat and have openclaw set all for you.

## Installation tricks

``` bash
### Redo the configuration from the start
openclaw onboard
```


---- 

## Further info


- Free tier AWS install
	- https://www.youtube.com/watch?v=GLwTSlRn6-k
	- https://x.com/damianplayer/status/2015105669620269373
	- https://x.com/i/status/2014934471095812547


- Very good video
	- https://x.com/i/status/2015182480064893118

- VERY good article: step by step
	- https://x.com/nickspisak_/status/2015144613594415164

- 26 pages information
	- https://x.com/scobleizer/status/2015346738517942628


- Nice guide
	- https://x.com/heyshrutimishra/status/2015327280911073789


- Safety tips:
	- https://x.com/rahulsood/status/2015397582105969106
