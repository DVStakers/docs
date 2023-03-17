# 💾 Installation

<figure><img src="https://raw.githubusercontent.com/DVStakers/docs/main/.gitbook/assets/Eridian.png" alt=""><figcaption><p>Eridian</p></figcaption></figure>

* [Create Aliases](installation.md#create-aliases)
* [Go - Install](installation.md#go-install)
* [Geth - Install](installation.md#geth-install)
* [Geth - Configure Service](installation.md#geth-configure-service)
* [Geth - Update Scripts](installation.md#geth-update-scripts)
* [Geth - Configure JavaScript Console](installation.md#geth-configure-javascript-console)

### Create Aliases

These aliases make interacting with `Geth` on the command line easier.

```
echo "alias geth-log='sudo journalctl -f -u geth.service -o cat | ccze -A'" >> ~/.bashrc
echo "alias geth-start='sudo systemctl start geth.service'" >> ~/.bashrc
echo "alias geth-stop='sudo systemctl stop geth.service'" >> ~/.bashrc
echo "alias geth-restart='sudo systemctl restart geth.service'" >> ~/.bashrc
echo "alias geth-status='sudo systemctl status geth.service'" >> ~/.bashrc
echo "alias geth-config='sudo vim /etc/systemd/system/geth.service'" >> ~/.bashrc
echo "alias geth-enable='sudo systemctl enable geth.service'" >> ~/.bashrc
echo "alias geth-disable='sudo systemctl disable geth.service'" >> ~/.bashrc
echo "alias geth-update='cd; ./geth-update-check.sh'" >> ~/.bashrc
echo "alias geth-attach='sudo geth attach --preload ~/geth-console-script.js /var/lib/goethereum/geth.ipc'" >> ~/.bashrc
echo "alias geth-blockNumber='sudo geth --exec \"eth.blockNumber\" attach /var/lib/goethereum/geth.ipc'" >> ~/.bashrc
echo "alias geth-peerCount='sudo geth --exec \"net.peerCount\" attach /var/lib/goethereum/geth.ipc'" >> ~/.bashrc
echo "alias geth-nodeInfo='sudo geth --exec \"admin.nodeInfo\" attach /var/lib/goethereum/geth.ipc'" >> ~/.bashrc

source ~/.bashrc
```

### Go - Install

Find the latest version of `Go` here: [https://go.dev/doc/install](https://go.dev/doc/install)

```bash
GO_LATEST_VERSION=    # Add the latest Go version here

cd ~/
wget https://go.dev/dl/go${GO_LATEST_VERSION}.linux-amd64.tar.gz
sudo rm -rf /usr/local/go && sudo tar -C /usr/local -xzf go${GO_LATEST_VERSION}.linux-amd64.tar.gz
export PATH=$PATH:/usr/local/go/bin
echo 'PATH="$PATH:/usr/local/go/bin"' >> ~/.profile
```

### Geth - Install

Build the latest version of `Geth`.

```bash
cd ~
git clone -b master https://github.com/ethereum/go-ethereum.git
cd go-ethereum
make geth
```

Move the compiled `Geth` build to a new directory.

```bash
sudo cp ~/go-ethereum/build/bin/geth /usr/local/bin
```

Create `Geth` user and directory.

```bash
sudo useradd --no-create-home --shell /bin/false goeth
sudo mkdir -p /var/lib/goethereum
```

Create JWT Secret.

```bash
sudo openssl rand -hex 32 | tr -d "\n" > "/tmp/jwtsecret"
sudo mv /tmp/jwtsecret /var/lib/goethereum/
sudo chmod +r /var/lib/goethereum/jwtsecret
```

### Geth - Configure Service

Set permissions.

```bash
sudo chown -R goeth:goeth /var/lib/goethereum
```

Configure `Geth` service using the command line flags.

```bash
sudo vim /etc/systemd/system/geth.service
```

{% code title="/etc/systemd/system/geth.service" %}
```bash
[Unit]
Description=Go Ethereum Client (Geth) - Execution Node
After=network.target
Wants=network.target

[Service]
User=goeth
Group=goeth
Type=simple
Restart=always
RestartSec=5
TimeoutStopSec=1200
ExecStart=/usr/local/bin/geth \
    --mainnet \
    --syncmode=snap \
    --http \
    --http.api="engine,eth,web3,net" \
    --datadir /var/lib/goethereum \
    --metrics \
    --metrics.expensive \
    --pprof \
    --authrpc.jwtsecret=/var/lib/goethereum/jwtsecret \
    --maxpeers 50 \
    --cache 32768

[Install]
WantedBy=default.target
```
{% endcode %}

Start the service and check it's working as expected.

{% tabs %}
{% tab title="Command Aliases" %}
```bash
daemon-reload   # Reload any changes made to the geth.service
geth-enable     # Enable the Geth service
geth-start      # Start the Geth service
geth-status     # View the status of the Geth service

geth-log        # View the Geth logs
```
{% endtab %}

{% tab title="Full Commands" %}
```bash
sudo systemctl daemon-reload                          # Reload any changes made to the geth.service
sudo systemctl enable geth.service                    # Enable the Geth service
sudo systemctl start geth.service                     # Start the Geth service
sudo systemctl status geth.service                    # View the status of the Geth service

sudo journalctl -f -u geth.service -o cat | ccze -A   # View the Geth logs
```
{% endtab %}
{% endtabs %}

### Geth - Update Scripts

Create `Geth` update script.

```bash
vim ~/geth-update.sh
```

{% code title="~/geth-update.sh" %}
```bash
#!/bin/bash
cd ~/go-ethereum
git checkout master

echo "**********************************"
echo "Pulling latest changes for Geth..."
echo "**********************************"
git pull

echo
echo "**************"
echo "Making Geth..."
echo "**************"
make geth

echo "****************"
echo "Stopping Geth..."
sudo systemctl stop geth.service

echo "Replacing previous version..."
sudo rm /usr/local/bin/geth
sudo cp ~/go-ethereum/build/bin/geth /usr/local/bin

echo "Restarting Geth..."
echo "******************"
sudo systemctl start geth.service
```
{% endcode %}

Create `Geth` update script checker script.

```bash
vim ~/geth-update-check.sh
```

{% code title="~/geth-update-check.sh" %}
```bash
#!/bin/bash
while true; do
    read -p "Are you sure you want to update Geth? (Y/N) " yn
    case $yn in
        [Yy]* ) cd; ./geth-update.sh; break;;
        [Nn]* ) exit;;
        * ) echo "Please answer Y or N.";;
    esac
done
```
{% endcode %}

Make both scripts executable.

```bash
chmod u+x ~/geth-update.sh
chmod u+x ~/geth-update-check.sh
```

### Geth - Configure JavaScript Console

Use `--preload` to load pre-written commands and functions stored in a script file.

{% code title="~/geth-console-script.js" %}
```bash
function blockInfo() {
    var blockInfo;
    web3.eth.getBlock(eth.blockNumber, function(e, r) { blockInfo = r; });
    return blockInfo;
}
```
{% endcode %}

Check `Geth` details by attaching to the JavaScript console

{% tabs %}
{% tab title="Command Alias" %}
```bash
geth-attach
```
{% endtab %}

{% tab title="Full Commands" %}
```bash
sudo geth attach --preload ~/geth-console-script.js /var/lib/goethereum/geth.ipc
```
{% endtab %}
{% endtabs %}

`Geth` JavaScript console commands.

```bash
eth.syncing
net.peerCount
```
