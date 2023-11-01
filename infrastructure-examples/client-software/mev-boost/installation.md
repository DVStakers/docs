---
description: MEV Boost client installation guide.
---

# 💾 Installation

* [Create Aliases](installation.md#create-aliases)
* [Firewall Configuration](installation.md#firewall-configuration)
* [MEV Boost - Install](installation.md#mev-boost-install)
* [MEV Boost - Configure Service](installation.md#mev-boost-configure-service)
* [MEV Boost - Update Scripts](installation.md#mev-boost-update-scripts)

### Create Aliases

```bash
echo "alias mev-log='sudo journalctl -f -u mevboost.service -o cat | ccze -A'" >> ~/.bashrc
echo "alias mev-start='sudo systemctl start mevboost.service'" >> ~/.bashrc
echo "alias mev-stop='sudo systemctl stop mevboost.service'" >> ~/.bashrc
echo "alias mev-restart='sudo systemctl restart mevboost.service'" >> ~/.bashrc
echo "alias mev-status='sudo systemctl status mevboost.service'" >> ~/.bashrc
echo "alias mev-config='sudo vim /etc/systemd/system/mevboost.service'" >> ~/.bashrc
echo "alias mev-enable='sudo systemctl enable mevboost.service'" >> ~/.bashrc
echo "alias mev-disable='sudo systemctl disable mevboost.service'" >> ~/.bashrc
echo "alias mev-update='~/mev-update-check.sh'" >> ~/.bashrc

source ~/.bashrc
```

### Firewall Configuration

Configure the firewall.

```bash
MEV_PORT=        # Default: 18550

sudo ufw allow ${MEV_PORT} comment 'Allow MEV Boost in'
```

### MEV Boost - Install

Build the latest version of `MEV Boost`.

```bash
cd ~
git clone https://github.com/flashbots/mev-boost.git
cd mev-boost
make build-portable
```

Move the compiled `MEV Boost` build to a new directory.

```bash
sudo cp ~/mev-boost/mev-boost /usr/local/bin
```

Create `MEV Boost` user and directory.

```bash
sudo useradd --no-create-home --shell /bin/false mevboost
sudo chown -R mevboost:mevboost ~/mev-boost
```

### MEV Boost - Configure Service

Configure `MEV Boost` service using the command line flags.

```bash
sudo vim /etc/systemd/system/mevboost.service
```

{% tabs %}
{% tab title="/etc/systemd/system/mevboost.service" %}
{% code title="/etc/systemd/system/mevboost.service" %}
```bash
[Unit]
Description=mev-boost
Wants=network-online.target
After=network-online.target

[Service]
Type=simple
User=mevboost
Group=mevboost
Restart=always
RestartSec=5

Environment=NETWORK=
Environment=ADDR_PORT=
Environment=MIN_BID=
Environment=RELAYS=

ExecStart=/usr/local/bin/mev-boost \
    -${NETWORK} \
    -addr localhost:${ADDR_PORT} \
    -min-bid ${MIN_BID} \
    -relay-check \
    -relays ${RELAYS}

[Install]
WantedBy=multi-user.target
```
{% endcode %}
{% endtab %}

{% tab title="MEV Boost Flags Explained" %}
<table data-header-hidden><thead><tr><th width="294">Flag</th><th>Description</th></tr></thead><tbody><tr><td><code>/usr/local/bin/mev-boost</code></td><td>Starts MEV Boost.</td></tr><tr><td><code>-${NETWORK}</code></td><td>Specifies the target network.</td></tr><tr><td><code>-addr</code></td><td>Set listening port.</td></tr><tr><td><code>-min-bid</code></td><td>Sets the minimum bid that needs to be offered to accept a block from the relay.<br><br>If no offer is higher than the <code>min-bid</code> then the validator will build its own block locally. </td></tr><tr><td><code>-relay-check</code></td><td>MEV Boost pings the relays to check they are still alive.</td></tr><tr><td><code>-relays</code></td><td>Comma separated list of relay addresses.</td></tr></tbody></table>
{% endtab %}
{% endtabs %}

Start the service and check it's working as expected.

{% tabs %}
{% tab title="Command Aliases" %}
```bash
daemon-reload      # Reload any changes made to the mevboost.service
mev-enable         # Enable the mevboost.service
mev-start          # Start the mevboost.service
mev-status         # View the status of the mevboost.service

mev-log            # View the mevboost.service logs
```
{% endtab %}

{% tab title="Full Commands" %}
```bash
sudo systemctl daemon-reload                              # Reload any changes made to the mevboost.service
sudo systemctl enable mevboost.service                    # Enable the mevboost.service
sudo systemctl start mevboost.service                     # Start the mevboost.service
sudo systemctl status mevboost.service                    # View the status of the mevboost.service

sudo journalctl -f -u mevboost.service -o cat | ccze -A   # View the mevboost.service logs
```
{% endtab %}
{% endtabs %}

### MEV Boost - Update Scripts

Create `MEV Boost` update script.

```bash
vim ~/mev-update.sh
```

{% code title="~/mev-update.sh" %}
```bash
#!/bin/bash
cd ~/mev-boost

read -p "Enter the commit hash you want to checkout: " commit_hash

git fetch
git checkout $commit_hash

echo "***************************************"
echo "Pulling latest changes for MEV Boost..."
echo "***************************************"
make build

echo
echo "*******************"
echo "Making MEV Boost..."
echo "*******************"
make build

echo "*********************"
echo "Stopping MEV Boost..."
sudo systemctl stop mevboost.service

echo "Replacing previous version..."
sudo rm /usr/local/bin/mev-boost
sudo cp ~/mev-boost/mev-boost /usr/local/bin

echo "Restarting MEV Boost..."
echo "***********************"
sudo systemctl start mevboost.service
```
{% endcode %}

Create `MEV Boost` update script checker script.

```bash
vim ~/mev-update-check.sh
```

{% code title="~/mev-update-check.sh" %}
```bash
#!/bin/bash
while true; do
    read -p "Are you sure you want to update MEV Boost? (Y/N) " yn
    case $yn in
        [Yy]* ) ~/mev-update.sh; break;;
        [Nn]* ) exit;;
        * ) echo "Please answer Y or N.";;
    esac
done
```
{% endcode %}

Make both scripts executable.

```bash
chmod u+x ~/mev-update.sh
chmod u+x ~/mev-update-check.sh
```
