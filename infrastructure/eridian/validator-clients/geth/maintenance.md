---
description: Notes on how to maintain and update my Geth client.
---

# 🏗 Maintenance

<figure><img src="https://raw.githubusercontent.com/DVStakers/docs/main/.gitbook/assets/Eridian.png" alt=""><figcaption><p>Eridian</p></figcaption></figure>

* [Go - Update](maintenance.md#go-update)
* [Geth - Update Client](maintenance.md#geth-update-client)
* [Geth - Update geth.service](maintenance.md#geth-update-geth.service)
* [Geth - Rollback Chain to Previous Block Number](maintenance.md#geth-rollback-chain-to-previous-block-number)
* [Geth - Resync after an Unexpected Shutdown](maintenance.md#geth-resync-after-an-unexpected-shutdown)
* [Geth - Pruning](maintenance.md#geth-pruning)

### Go - Update

Find the latest version of `Go` here: [https://go.dev/doc/install](https://go.dev/doc/install)

```bash
GO_LATEST_VERSION=    # Add the latest Go version here

cd ~/
wget https://go.dev/dl/go${GO_LATEST_VERSION}.linux-amd64.tar.gz
sudo rm -rf /usr/local/go && sudo tar -C /usr/local -xzf go${GO_LATEST_VERSION}.linux-amd64.tar.gz
export PATH=$PATH:/usr/local/go/bin
echo 'PATH="$PATH:/usr/local/go/bin"' >> ~/.profile
```

### Geth - Update Client

{% tabs %}
{% tab title="Command Aliases" %}
```bash
geth-update
```
{% endtab %}

{% tab title="Full Commands" %}
```bash
# <BUILD LATEST VERSION>
cd ~/go-ethereum
git checkout master
git pull
make geth

# <STOP SERVICE AND REPLACE BINARY>
sudo systemctl stop geth.service

sudo rm /usr/local/bin/geth
sudo cp ~/go-ethereum/build/bin/geth /usr/local/bin

sudo systemctl start geth.service
```
{% endtab %}
{% endtabs %}

### Geth - Update geth.service

{% tabs %}
{% tab title="Command Aliases" %}
```bash
geth-stop
geth-config

# MAKE ANY CHANGES TO THE CONFIG

daemon-reload
geth-start
geth-status
```
{% endtab %}

{% tab title="Full Commands" %}
```bash
sudo systemctl stop geth.service
sudo vim /etc/systemd/system/geth.service

# MAKE ANY CHANGES TO THE CONFIG

sudo systemctl daemon-reload
sudo systemctl start geth.service
sudo systemctl status geth.service
```
{% endtab %}
{% endtabs %}

### Geth - Rollback Chain to Previous Block Number

This was needed for a bug introduced in Geth v.1.10.22 that required a rollback to a previous block

Add `debug` flag to `--http.api`

{% tabs %}
{% tab title="Command Aliases" %}
```bash
geth-stop
geth-config

# Add "debug" to http.api
# --http.api="engine,eth,web3,net,debug"

daemon-reload
geth-start
geth-attach
```
{% endtab %}

{% tab title="Full Commands" %}
```bash
sudo systemctl stop geth.service
sudo vim /etc/systemd/system/geth.service

# Add "debug" to http.api
# --http.api="engine,eth,web3,net,debug"

sudo systemctl daemon-reload
sudo systemctl start geth.service
sudo geth attach --preload ~/geth-console-script.js /var/lib/goethereum/geth.ipc
```
{% endtab %}
{% endtabs %}

In the `Geth` console set the new block head e.g. `debug.setHead("0xEAC1A8")`.

```javascript
debug.setHead("0x<BLOCK_NUMBER_IN_HEX>")
```

Once re-sync has been completed, go back and remove the `debug` flag from the `--http.api` argument.

### Geth - Resync after an Unexpected Shutdown

To avoid duplication these details can be found on the EthStaker Knowledge Base.

* [How to resync Geth](https://ethstaker.gitbook.io/ethstaker-knowledge-base/tutorials/resync-geth)

### Geth - Pruning

TODO

