---
description: Notes on how to use my Geth client.
---

# ⌨ Useful commands

<figure><img src="https://raw.githubusercontent.com/DVStakers/docs/main/.gitbook/assets/Eridian.png" alt=""><figcaption><p>Eridian</p></figcaption></figure>

{% hint style="info" %}
All of the alias commands have been defined as aliases in \~/`.bashrc when`[`installing Geth`](installation.md)
{% endhint %}

* [geth.service](useful-commands.md#geth.service)
* [Geth Direct Queries](useful-commands.md#geth-direct-queries)
* [Geth JavaScript Console](useful-commands.md#geth-javascript-console)
* [Other Useful Commands](useful-commands.md#other-useful-commands)
* [Data Locations](useful-commands.md#data-locations)

### geth.service

{% tabs %}
{% tab title="Command Aliases" %}
```bash
geth-log        # View the geth.service logs
geth-start      # Start the geth.service
geth-stop       # Stop the geth.service
geth-restart    # Restart the geth.service
geth-status     # View the status of the geth.service
geth-enable     # Enable the geth.service
geth-disable    # Disable the geth.service

geth-config     # Open the /etc/systemd/system/geth.service in vim
daemon-reload   # Reload any changes made to the geth.service
```
{% endtab %}

{% tab title="Full Commands" %}
```bash
sudo journalctl -f -u geth.service -o cat | ccze -A   # View the geth.service logs
sudo systemctl start geth.service                     # Start the geth.service
sudo systemctl stop geth.service                      # Stop the geth.service
sudo systemctl restart geth.service                   # Restart the geth.service
sudo systemctl status geth.service                    # View the status of the geth.service
sudo systemctl enable geth.service                    # Enable the geth.service
sudo systemctl disable geth.service                   # Disable the geth.service

sudo vim /etc/systemd/system/geth.service             # Open the /etc/systemd/system/geth.service in vim
sudo systemctl daemon-reload                          # Reload any changes made to the geth.service
```
{% endtab %}
{% endtabs %}

### Geth Direct Queries

To make this easier, these commands can be executed directly from the command line without attaching the JS console.

```bash
geth-blockNumber
geth-peerCount
geth-nodeInfo
```

### Geth JavaScript Console

Attach to the `Geth` JavaScript console.

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

Console commands.

```javascript
eth.syncing                                            // Check if Geth is syncing
eth.blockNumber                                        // Show the current block number
eth.getTransaction("0x000...."                         // Get details of a specific transaction
eth.syncing.highestBlock - eth.syncing.currentBlock    // Distance remaining to sync
net.listening                                          // Report whether the Geth node is listening for inbound requests
net.peerCount                                          // Show number of active peers
admin.peers                                            // Show info about all peers
admin.peers[0]                                         // Show info about specific peer
admin.nodeInfo                                         // Show info about your own node
admin.peers.map((el) => el.network.inbound)            // You should see both true and false values meaning that your node is discoverable in the P2P network. If you’re seeing only false, you probably did not publicly expose the TCP and UDP port
blockInfo()                                            // Show information about the current block
blockInfo().totalDifficulty                            // Show the current block total difficulty
```

Exit the `Geth` JavaScript console.

```bash
exit
```

### Other Useful Commands

Checks `Geth` is running.

```bash
GETH_PORT=        # Default 8545

curl -H "Content-Type: application/json" -X POST --data '{"jsonrpc":"2.0","method":"web3_clientVersion","params":[],"id":67}' http://localhost:${GETH_PORT}
```

Check ports can be accessed.

```bash
PORTS_TO_CHECK=        # E.g. 30303,9000

curl https://eth2-client-port-checker.vercel.app/api/checker?ports=${PORTS_TO_CHECK}
```

Checks the validator status using the validator's public address.

```bash
VALIDATOR_HTTP_PORT=        # Lighthouse default: 5052
VALIDATOR_PUBLIC_KEY=       # E.g. 0x88426dd9c3d2bb71ad862a2e47d537304de528e88f5164f6db6ec423f1f7ed24d050c27ae4df45b37d2a4931fc820edf

curl -s http://127.0.0.1:${VALIDATOR_HTTP_PORT}/eth/v1/beacon/states/head/validators/${VALIDATOR_PUBLIC_KEY} |jq
```

Check validator status using a public endpoint in a browser.

```html
https://beaconstate.info/eth/v1/beacon/states/head/validators/<VALIDATOR_PUBLIC_KEY>
```

### Data Locations

`Geth` `chaindata` location.

```
/var/lib/goethereum/geth/chaindata
```
