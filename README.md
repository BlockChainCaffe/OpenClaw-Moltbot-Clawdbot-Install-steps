# OpenClaw-Moltbot-Clawdbot-Install-steps
A quich guide for get you started for real

![https___dev-to-uploads s3 amazonaws com_uploads_articles_1vcicqev4ids4xabxln7](https://github.com/user-attachments/assets/44d7e9d9-b028-4a22-a95b-e621fb1c0467)




## Naming convcention notice
> Just in the last few days `Clawdbot` changed name to `Moltbot` due to copyright claims, and then
to `OpenClaw`. Things are moving fast...
> As a result, at the end of the installation your program might have different names: `clawdbot`, `moltbot` or `openclaw` depending on the version you run/downloaded
> In this guide the name `openclaw` will be used.

----
## Installation options:

Openclaw can be installed on serveral different hardware configurations. While this guide defines the generic installation procedure, some more specific guides can be
found for specific hardware settings

- [Raspberry Pi](raspberry_pi.md)


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
``` bash
apt-get install -y sudo git vim net-tools build-essential procps curl file
```

### Sudo enablet user 'bot'

All the following procedure must be run by a **non-root user** that... can act as root 
We'll create the user **bot** for that purpouse

``` bash
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

### Install homebrew (Linuxbrew)

Homebrew/Linuxbrew is required to install skills etc. Although the installation scripts shows you how to install it later, it makes sense to install it before so to have more control on the installation issues etc.

As the **bot** user:

``` bash
## Run install script, press ENTER when prompted, wait for termination, it takes a while
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

## Bot Installation steps

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
- beware of possible installation errors for the skills
- next some more API keys could be asked
  - if you don't have them, don't want them feel free to reply No 
- (Optional) once skills are installed (it takes a while) run
``` bash
openclaw doctor
```
- at the end of the installation you'll be shown your access to **Control UI**
  - **note down	those access links and token**
```
◇  Control UI ─────────────────────────────────────────────────────────────────────╮
│                                                                                  │
│  Web UI: http://127.0.0.1:18789/                                                 │
│  Web UI (with token):                                                            │
│  http://127.0.0.1:18789/?token=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX  │
│  Gateway WS: ws://127.0.0.1:18789                                                │
│  Gateway: reachable                                                              │
│  Docs: https://docs.openclaw.ai/web/control-ui                                   │
│                                                                                  │
├──────────────────────────────────────────────────────────────────────────────────╯
```
- last question: Hatch in TUI

At this point **the installation is complete**. You can now chat with your Ai assistan already.
Next you'll probably want to interact via web interface and telegram

### 4) Connect to the gateway via web interface

Check if gateway is there

``` bash
netsta -tnap | grep 18789
```

The web UI listens on port 18789 on **localhost**. Unless you are installing Openclaw on your main computer **which is not recommended** you need a way to connect your browser to that port.
The best way to connect to the web UI is via **ssh port forwarding**
Run this SSL command **on your host**:

``` bash
ssh -N -L 18789:127.0.0.1:18789 bot@192.168.122.225
```
This will create a stable SSH connection between the port 18789 of your host to the same port on the bot's server.

Now point your browser to

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
You have two options now:

1) issue a command in the terminal as stated in the message
``` bash
openclaw pairing approve telegram XXXXXXXX
```

2) let the bot figure it out by itself (awesome!!): just chat with the bot, pass it the Telegram user id and pairing code, it'll do the rest. If does not work try option #1
done

## Troubleshooting

**openclaw command not found**

openclaw is installed via npm so...

``` bash
## get global npm folder
npm prefix -g
> /home/bot/.npm-global

## see openclaw command file is there
ls -al /home/conio/.npm-global/bin
> lrwxrwxrwx 1 bot bot   41 Feb  1 14:54 openclaw -> ../lib/node_modules/openclaw/openclaw.mjs

## add it to path
echo 'PATH="$(npm prefix -g)/bin:$PATH"' >> ~/.profile
source ~/.profile
echo $PATH

## Test
openclaw --version
```

## Maintenance

**Get access token**
``` bash
openclaw dashboard --no-open
```

**Redo from start**
Redo the configuration from the start
``` bash
openclaw onboard
```

**Security**:
https://docs.openclaw.ai/gateway/security
Run periodic security audit (set a cron-job?)
``` bash
openclaw security audit --deep
openclaw security audit --fix

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
