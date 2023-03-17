---
description: >-
  My software installation guide, starting with a new machine through to the
  point of installing the validator clients.
---

# 💾 Installation

<figure><img src="../../.gitbook/assets/Spacesider.png" alt=""><figcaption><p>Spacesider</p></figcaption></figure>

* [💾 Installing Linux](installation.md#installing-linux)
* [🔧 System Configuration](installation.md#system-configuration)
* [📏 Confirm Available Disk Space](installation.md#confirm-available-disk-space)
* [🎨 Change Terminal Colors](installation.md#change-terminal-colors)
* [🫣 Hide Welcome Message on Login](installation.md#hide-welcome-message-on-login)
* [⏱️ Increases Service Shutdown Timer](installation.md#increases-service-shutdown-timer)
* [🚧 Firewall Configuration](installation.md#firewall-configuration)
* [🛑 Enable Brute-Force and DDoS Protection](installation.md#enable-brute-force-and-ddos-protection)
* [🔒 Enable Automatic Security Updates](installation.md#enable-automatic-security-updates)
* [📱 SSH Security - 2FA](installation.md#ssh-security-2fa)
* [🚦 Git Configuration](installation.md#git-configuration)

### 💾 Installing Linux

To avoid duplication these details can be found on the EthStaker Knowledge Base.

* [Installing Linux](https://ethstaker.gitbook.io/ethstaker-knowledge-base/tutorials/installing-linux)

### 🔧 System Configuration

Update system packages.

```bash
sudo apt-get update -y
sudo apt-get upgrade -y
sudo apt-get dist-upgrade -y
```

Check for Firmware Updates ([https://github.com/fwupd/fwupd](https://github.com/fwupd/fwupd)).

```bash
fwupdmgr refresh
fwupdmgr get-updates
fwupdmgr update
```

Install useful packages.

```bash
sudo apt-get install -y \
git \
build-essential \
software-properties-common \
pkg-config \
cmake \
clang \
wget \
curl \
ccze \
vim \
mlocate \
jq \
net-tools \
unzip \
screen \
mosh \
ufw \
fwupd

sudo snap install btop
```

Set up `btop`.

* Change them to TTY.
* Change time interval to 1000ms.

Add useful bash aliases.

```bash
echo "alias lsl='ls -la'" >> ~/.bashrc
echo "alias clc='clear'" >> ~/.bashrc
echo "alias myip='echo Response from https://ipinfo.io/ip:; curl https://ipinfo.io/ip; echo'" >> ~/.bashrc
echo "alias ports='sudo lsof -i -P -n | grep LISTEN'" >> ~/.bashrc
echo "alias update-all='sudo apt-get update -y; sudo apt-get upgrade -y; sudo apt-get dist-upgrade -y'" >> ~/.bashrc
echo "alias daemon-reload='sudo systemctl daemon-reload'" >> ~/.bashrc
source ~/.bashrc
```

Use `ntpd` instead of the default time synchronization `timedatectl`.

The firewall is set to allow NTP calls out, so that's why it needs to be used.

```
sudo timedatectl set-ntp no
```

Verify it's disabled.

```
timedatectl
```

Install `ntp`.

```
sudo apt-get install -y ntp
```

Check `ntp` is running.

```
ntpq -p
```

There should be some results shown.

### 📏 Confirm Available Disk Space

To avoid duplication these details can be found on the EthStaker Knowledge Base.

* [Confirm Available Disk Space](https://ethstaker.gitbook.io/ethstaker-knowledge-base/tutorials/confirm-available-disk-space)

### 🎨 Change Terminal Colors

Change the colors of the terminal prompt to make it easier to tell which server I'm currently using.

```
vim ~/.bashrc
```

Change the `PS1` value directly under `if [ "$color_prompt" = yes ]; then`

Comment out the existing `PS1` as it's useful to have a backup.

* `<LOCATION>`
  * E.g. Home or Cloud
* `<ENV>`
  * E.g. DEV or PROD

```
PS1='\[\033[01;34m\]<ENV>|<LOCATION>|Validator\[\033[00m\]:\[\033[01;34m\]\w\[\033[00m\]\$ '
```

Reload the terminal source to display the results.

```
source ~/.bashrc
```

### 🫣 Hide Welcome Message on Login

Some of the messages are useful so I don't want to hide everything, but some are annoying.

Hide parts of the message by making the template `non-executable`.

```
sudo chmod -x /etc/update-motd.d/10-help-text
sudo chmod -x /etc/update-motd.d/50-motd-news
```

### ⏱️ Increases Service Shutdown Timer

I was concerned that the default time of 180 seconds wouldn't be long enough for all the services running to gracefully shutdown.

The shutdown time can be individually set on each service, but this is a catch-all.

```
sudo vim /etc/systemd/system.conf
```

Uncomment out the line `#DefaultTimeoutStopSec=90s` and change it to `1200s`.

### 🚧 Firewall Configuration

Confirm UFW is disabled before blocking everything or you could be disconnected.

```
sudo ufw disable
sudo ufw status
```

Configure the firewall.

```
sudo ufw default deny incoming comment 'Deny all incoming traffic'
sudo ufw default deny outgoing comment 'Deny all outgoing traffic'


# <EXECUTION CLIENTS>
sudo ufw allow ***/tcp comment 'Allow Geth in'
sudo ufw allow ***/udp comment 'Allow Geth in'

sudo ufw allow out ***/tcp comment 'Allow Geth out'
sudo ufw allow out ***/udp comment 'Allow Geth out'


# <CONSENSUS CLIENTS>
sudo ufw allow ***/tcp comment 'Allow Lighthouse in'
sudo ufw allow ***/udp comment 'Allow Lighthouse in'

sudo ufw allow out ***/tcp comment 'Allow Lighthouse out'
sudo ufw allow out ***/udp comment 'Allow Lighthouse out'

sudo ufw allow from ***.***.*.** to any proto tcp port *** comment 'Allow Lighthouse http in from local IP'
sudo ufw allow out ***/tcp comment 'Allow Lighthouse VC out to local Lighthouse BN'


# <SPACESIDER CONNECTIONS>
sudo ufw allow from ***.***.***.*** to any proto tcp port *** comment 'Allow Lighthouse http in from Spacesider'

sudo ufw allow out ***/tcp comment 'Allow Lighthouse VC out to Spacesider Teku BN'
sudo ufw allow out ***/tcp comment 'Allow Lighthouse VC out to Spacesider Lighthouse BN'


# <MEV BOOST>
sudo ufw allow in ***/tcp comment 'Allow MEV Boost in'
sudo ufw allow in ***/udp comment 'Allow MEV Boost in'
sudo ufw allow out ***/tcp comment 'Allow MEV Boost out'
sudo ufw allow out ***/udp comment 'Allow MEV Boost out'


# <OTHER PORTS>
sudo ufw allow in *** comment 'Allow custom ssh in'
sudo ufw allow in ***:***/udp comment 'Allow Mosh in'
sudo ufw allow in *** comment 'Allow Grafana in'

sudo ufw allow out 53 comment 'Allow DNS calls out'
sudo ufw allow out 123 comment 'Allow NTP out'
sudo ufw allow out http comment 'Allow HTTP traffic out'
sudo ufw allow out https comment 'Allow HTTPS traffic out'
```

Edit the UFW settings to disable IPV6. This isn't for any particular security reason, it just makes the UFW status easier to read when it prints out on screen.

```
sudo vim /etc/default/ufw
```

Change `IPV6` to `no`.

```
IPV6=no
```

Enable UFW.

```
# <USE --FORCE TO STOP THE PROMPT ASKING IF YOU WANT TO ENABLE>
sudo ufw --force enable

sudo ufw status verbose
```

### 🛑 Enable Brute-Force and DDoS Protection

To avoid duplication these details can be found on the EthStaker Knowledge Base.

* [DDoS & Brute-Force Protection](https://ethstaker.gitbook.io/ethstaker-knowledge-base/networking/ddos-and-brute-force-protection)

### 🔒 Enable Automatic Security Updates

To avoid duplication these details can be found on the EthStaker Knowledge Base.

* [Automatic Security Updates](https://ethstaker.gitbook.io/ethstaker-knowledge-base/tutorials/automatic-security-updates)

### 📱 SSH Security - 2FA

To avoid duplication these details can be found on the EthStaker Knowledge Base.

* [SSH Security - 2FA](https://ethstaker.gitbook.io/ethstaker-knowledge-base/tutorials/ssh-security-2fa)

### 🚦 Git Configuration

Edit the Git configuration.

```
vim ~/.gitconfig
```

Add this to the `[alias]` section for a pretty view of commit tree for current branch.

```
[alias] tree = log --color --graph --pretty=format:'%Cred%h%Creset -%C(bold magenta)%d%Creset %s %C(cyan)(%cr)%C(bold blue) <%an> %Creset' --date=relative --abbrev-commit --all
[alias] tree-current = log --color --graph --pretty=format:'%Cred%h%Creset -%C(bold magenta)%d%Creset %s %C(cyan)(%cr)%C(bold blue) <%an> %Creset' --date=relative --abbrev-commit --all
```

Then run these commands in a directory using Git to see a pretty view of the commit tree.

```
git tree
git tree-current
```



Success! The staking machine is now ready to [install the validator clients](../../eridian/validator-clients/) 🥳
