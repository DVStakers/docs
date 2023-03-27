---
description: >-
  My Linux software installation guide, starting with a new machine through to
  the point of installing the validator clients.
---

# 💾 Installation

<figure><img src="https://raw.githubusercontent.com/DVStakers/docs/main/.gitbook/assets/Eridian.png" alt=""><figcaption><p>Eridian</p></figcaption></figure>

* [💾 Installing Linux](installation.md#installing-linux)
* [🚪 Change Default SSH Port](installation.md#change-default-ssh-port)
* [🔧 System Configuration](installation.md#system-configuration)
* [📏 Use all Available Disk Space](installation.md#use-all-available-disk-space)
* [🫣 Hide Welcome Message on Login](installation.md#hide-welcome-message-on-login)
* [⏱️ Increases Service Shutdown Timer](installation.md#increases-service-shutdown-timer)
* [🚧 Firewall Configuration](installation.md#firewall-configuration)
* [🛑 Brute-Force SSH Protection](installation.md#brute-force-ssh-protection)
* [🔒 Automatic Security Updates](installation.md#automatic-security-updates)
* [📱 SSH Security - 2FA](installation.md#ssh-security-2fa)
* [🚦 Git Configuration](installation.md#git-configuration)
* [📝 Systemd Journal Logs](installation.md#systemd-journal-logs)

### 💾 Installing Linux

To avoid duplication these details can be found on the EthStaker Knowledge Base.

{% content-ref url="http://localhost:5000/s/KnJhWg57YoZq2MPfatKE/tutorials/installing-linux" %}
[Installing Linux](http://localhost:5000/s/KnJhWg57YoZq2MPfatKE/tutorials/installing-linux)
{% endcontent-ref %}

### 🚪 Change Default SSH Port

Useful when you have multiple machines running on the same ip address.

```bash
sudo vim /etc/ssh/sshd_config
```

Uncomment `#Port 22` and change it to your modified ssh port.

Restart the `sshd` service.

```bash
systemctl restart sshd
```

### 🔧 System Configuration

Add useful `Bash` aliases.

```bash
echo "alias lsl='ls -la'" >> ~/.bashrc
echo "alias clc='clear'" >> ~/.bashrc
echo "alias myip='echo Response from https://ipinfo.io/ip:; curl https://ipinfo.io/ip; echo'" >> ~/.bashrc
echo "alias ports='sudo lsof -i -P -n | grep LISTEN'" >> ~/.bashrc
echo "alias update-system='sudo apt-get update -y; sudo apt-get upgrade -y; sudo apt-get dist-upgrade -y'" >> ~/.bashrc
echo "alias update-firmware='fwupdmgr refresh; fwupdmgr get-updates; fwupdmgr update'" >> ~/.bashrc
echo "alias daemon-reload='sudo systemctl daemon-reload'" >> ~/.bashrc
source ~/.bashrc
```

Update system packages.

{% tabs %}
{% tab title="Command Alias" %}
```bash
update-system    # Update all system packages
```
{% endtab %}

{% tab title="Full Commands" %}
```bash
sudo apt-get update -y; sudo apt-get upgrade -y; sudo apt-get dist-upgrade -y    # Update all system packages
```
{% endtab %}
{% endtabs %}

Install useful packages.

```sh
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

Update [firmware](https://github.com/fwupd/fwupd).

{% tabs %}
{% tab title="Command Alias" %}
```bash
update-firmware  # Update firmware
```
{% endtab %}

{% tab title="Full Commands" %}
```bash
fwupdmgr refresh; fwupdmgr get-updates; fwupdmgr update                          # Update firmware
```
{% endtab %}
{% endtabs %}

Set up `btop`.

```bash
btop
```

* Press `ESC` key to see menu and select `OPTIONS`.
* Change them to TTY.
* Change time interval to 1000ms.

Use `ntpd` instead of the default time synchronization `timedatectl`.

The firewall is set to allow NTP calls out, so that's why it needs to be used.

```bash
sudo timedatectl set-ntp no
```

Verify `timedatectl` is disabled - The result should say `NTP service: inactvie`.

```bash
timedatectl
```

Install `ntp`.

```bash
sudo apt-get install -y ntp
```

Check `ntp` is running - There should be some results shown.

```bash
ntpq -p
```

### 📏 Use all Available Disk Space

To avoid duplication these details can be found on the EthStaker Knowledge Base.

{% content-ref url="http://localhost:5000/s/KnJhWg57YoZq2MPfatKE/tutorials/use-all-available-disk-space" %}
[Use all available disk space](http://localhost:5000/s/KnJhWg57YoZq2MPfatKE/tutorials/use-all-available-disk-space)
{% endcontent-ref %}

### 🫣 Hide Welcome Message on Login

Some of the messages are useful so I don't want to hide everything, but some are annoying.

Hide parts of the message by making the template `non-executable`.

```bash
sudo chmod -x /etc/update-motd.d/10-help-text
sudo chmod -x /etc/update-motd.d/50-motd-news
```

### ⏱️ Increases Service Shutdown Timer

I was concerned that the default time of 180 seconds wouldn't be long enough for all the services running to gracefully shutdown.

The shutdown time can be individually set on each service, but this is a catch-all.

```bash
sudo vim /etc/systemd/system.conf
```

Uncomment out the line `#DefaultTimeoutStopSec=90s` and change it to `1200s`.

### 🚧 Firewall Configuration

Confirm UFW is disabled before blocking everything or you could be disconnected.

```bash
sudo ufw disable
sudo ufw status
```

Configure the firewall.

```bash
CUSTOM_SSH_PORT=                  # Default: 22
MOSH_STARTING_PORT=               # Default: 60000
MOSH_ENDING_PORT=                 # Default: 61000
GRAFANA_PORT=

sudo ufw default deny incoming comment 'Deny all incoming traffic'
sudo ufw default deny outgoing comment 'Deny all outgoing traffic'

sudo ufw allow ${CUSTOM_SSH_PORT} comment 'Allow custom ssh in'
sudo ufw allow ${MOSH_STARTING_PORT}:${MOSH_ENDING_PORT}/udp comment 'Allow Mosh in'
sudo ufw allow ${GRAFANA_PORT} comment 'Allow Grafana in'

sudo ufw allow out 53 comment 'Allow DNS calls out'
sudo ufw allow out 123 comment 'Allow NTP out'
sudo ufw allow out http comment 'Allow HTTP traffic out'
sudo ufw allow out https comment 'Allow HTTPS traffic out'
```

Edit the UFW settings to disable IPV6. This isn't for any particular security reason, it just makes the UFW status easier to read when it prints out on screen.

```bash
sudo vim /etc/default/ufw
```

Change `IPV6` to `no`.

```bash
IPV6=no
```

Enable UFW.

```bash
# <USE --FORCE TO STOP THE PROMPT ASKING IF YOU WANT TO ENABLE>
sudo ufw --force enable

sudo ufw status verbose
```

### 🛑 Brute-Force SSH Protection

To avoid duplication these details can be found on the EthStaker Knowledge Base.

{% content-ref url="http://localhost:5000/s/KnJhWg57YoZq2MPfatKE/networking/brute-force-ssh-protection" %}
[Brute-force SSH protection](http://localhost:5000/s/KnJhWg57YoZq2MPfatKE/networking/brute-force-ssh-protection)
{% endcontent-ref %}

### 🔒 Automatic Security Updates

To avoid duplication these details can be found on the EthStaker Knowledge Base.

{% content-ref url="http://localhost:5000/s/KnJhWg57YoZq2MPfatKE/tutorials/automatic-security-updates" %}
[Automatic security updates](http://localhost:5000/s/KnJhWg57YoZq2MPfatKE/tutorials/automatic-security-updates)
{% endcontent-ref %}

### 📱 SSH Security - 2FA

To avoid duplication these details can be found on the EthStaker Knowledge Base.

{% content-ref url="http://localhost:5000/s/KnJhWg57YoZq2MPfatKE/tutorials/ssh-security-2fa" %}
[SSH security - 2FA](http://localhost:5000/s/KnJhWg57YoZq2MPfatKE/tutorials/ssh-security-2fa)
{% endcontent-ref %}

### 🚦 Git Configuration

Edit the Git configuration.

```bash
vim ~/.gitconfig
```

Add this to the `[alias]` section for a pretty view of commit tree for current branch.

```
[alias] tree = log --color --graph --pretty=format:'%Cred%h%Creset -%C(bold magenta)%d%Creset %s %C(cyan)(%cr)%C(bold blue) <%an> %Creset' --date=relative --abbrev-commit --all
[alias] tree-current = log --color --graph --pretty=format:'%Cred%h%Creset -%C(bold magenta)%d%Creset %s %C(cyan)(%cr)%C(bold blue) <%an> %Creset' --date=relative --abbrev-commit --all
```

Then run these commands in a directory using Git to see a pretty view of the commit tree.

```bash
git tree
git tree-current
```

### 📝 Systemd Journal Logs

The systemd services create logs that grow over time. It is possible to clear the logs to free up disk space on your server. The following steps will delete existing log data.

Be careful if you require this data for debugging purposes.

Check the amount of disk space the logs are using.

```bash
sudo journalctl --disk-usage
```

To clear the logs use the following command.

* The `--flush` flag flushes the logs currently in memory onto the disk.
* The `--rotate` flag archives the existing logs so they can’t be written to anymore and starts new logs for each service.
* The `--vacuum-time` flag deletes log data that is older than `3 days`.

```bash
sudo journalctl --flush --rotate
sudo journalctl --vacuum-time=3days
```

It is recommended to check the logs are in a good state after the vacuum operation.

Each log should have a status of `PASS`.

```bash
sudo journalctl --verify
```

To have the system automatically keep log data to a specified max size complete the following additional steps.

Open the systemd journal service configuration file.

```bash
sudo vim /etc/systemd/journald.conf
```

Edit the file to set the maximum disk space that can be used by the journal in persistent storage.

Remove the `#` from the line `SystemMaxUse` and add a value in megabytes, say `1000M`.

Restart the `journald` after updating the file.

```bash
sudo systemctl restart systemd-journald
```

Journal logs will now be limited to 1000MB in size.



Success! The staking machine is now ready to [install the validator clients](../client-software/) 🥳
