---
description: Lighthouse Beacon Node installation guide.
---

# 💾 Installation

* [Create Aliases](installation.md#create-aliases)
* [Lighthouse BN - Configure Service](installation.md#lighthouse-bn-configure-service)

### Create Aliases

```bash
echo "alias beacon-log='sudo journalctl -f -u lighthousebeacon.service -o cat | ccze -A'" >> ~/.bashrc
echo "alias beacon-start='sudo systemctl start lighthousebeacon.service'" >> ~/.bashrc
echo "alias beacon-stop='sudo systemctl stop lighthousebeacon.service'" >> ~/.bashrc
echo "alias beacon-restart='sudo systemctl restart lighthousebeacon.service'" >> ~/.bashrc
echo "alias beacon-status='sudo systemctl status lighthousebeacon.service'" >> ~/.bashrc
echo "alias beacon-config='sudo vim /etc/systemd/system/lighthousebeacon.service'" >> ~/.bashrc
echo "alias beacon-enable='sudo systemctl enable lighthousebeacon.service'" >> ~/.bashrc
echo "alias beacon-disable='sudo systemctl disable lighthousebeacon.service'" >> ~/.bashrc

source ~/.bashrc
```

### Firewall Configuration

Configure the firewall.

```bash
LIGHTHOUSE_P2P_PORT=              # Default: 9000
LIGHTHOUSE_HTTP_PORT=             # Default: 5052
LOCAL_VALIDATOR_IP=                     
SPACESIDER_IP=                

sudo ufw allow ${LIGHTHOUSE_P2P_PORT} comment 'Allow Lighthouse P2P in'
sudo ufw allow from ${LOCAL_VALIDATOR_IP} to any proto tcp port ${LIGHTHOUSE_HTTP_PORT} comment 'Allow Lighthouse http in from local validator IP'

# <SPACESIDER CONNECTIONS>
sudo ufw allow from ${SPACESIDER_IP} to any proto tcp port ${LIGHTHOUSE_HTTP_PORT} comment 'Allow Lighthouse http in from Spacesider'
```

### Lighthouse BN - Configure Service

Create `Lighthouse Beacon` user and set permissions.

```bash
sudo useradd --no-create-home --shell /bin/false lighthousebeacon
sudo mkdir -p /var/lib/lighthouse/beacon
sudo chown -R lighthousebeacon:lighthousebeacon /var/lib/lighthouse/beacon
```

Configure `Lighthouse` service using the command line flags.

```bash
sudo vim /etc/systemd/system/lighthousebeacon.service
```

{% tabs %}
{% tab title="lighthousebeacon.service" %}
{% code title="lighthousebeacon.service" %}
```bash
[Unit]
Description=Lighthouse Ethereum Client - Beacon Node
After=network-online.target
Wants=network-online.target

[Service]
Type=simple
User=lighthousebeacon
Group=lighthousebeacon
Restart=always
RestartSec=5

Environment=NETWORK=                     # E.g. mainnet or goerli
Environment=P2P_PORT=                    # Default: 9000
Environment=HTTP_ADDRESS=                # E.g. 0.0.0.0
Environment=HTTP_PORT=                   # Default: 5052
Environment=MONITORING_ENDPOINTS=
Environment=EXECUTION_ENDPOINTS=         
Environment=SUGGESTED_FEE_RECIPIENT=     
Environment=CHECKPOINT_SYNC_URL=
Environment=BUILDER=

ExecStart=/usr/local/bin/lighthouse bn \
    --network ${NETWORK} \
    --datadir /var/lib/lighthouse \
    --port ${P2P_PORT} \
    --http \
    --http-address=${HTTP_ADDRESS} \
    --http-port=${HTTP_PORT} \
    --metrics \
    --validator-monitor-auto \
    --monitoring-endpoint ${MONITORING_ENDPOINTS} \
    --execution-endpoints ${EXECUTION_ENDPOINTS} \
    --jwt-secrets="/var/lib/goethereum/jwtsecret" \
    --suggested-fee-recipient ${SUGGESTED_FEE_RECIPIENT} \
    --checkpoint-sync-url ${CHECKPOINT_SYNC_URL} \
    --builder ${BUILDER} \
    --reconstruct-historic-states

[Install]
WantedBy=multi-user.target
```
{% endcode %}
{% endtab %}

{% tab title="Lighthouse BN Flags Explained" %}
| `/usr/local/bin/lighthouse bn`  | Starts the `Beacon` node.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| ------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `--network`                     | Name of the chain Lighthouse will sync and follow (e.g. `mainnet` or `goerli`).                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| `--datadir`                     | Used to specify a custom root data directory for lighthouse keys and databases.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| `--port`                        | The TCP/UDP port to listen on for peer discovery.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| `--http`                        | <p>Enable the RESTful HTTP API server.</p><ul><li>Disabled by default.</li></ul>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| `--http-address`                | Specify the listening address of the server.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| `--http-port`                   | Specify the listening port of the server.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| `--metrics`                     | <p>Enable the Prometheus metrics HTTP server.</p><ul><li>Disabled by default.</li></ul>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| `--validator-monitor-auto`      | <p>When the <code>--validator-monitor-auto</code> flag is supplied, any validator which uses the <code>beacon_committee_subscriptions</code> API endpoint will be enrolled for additional monitoring.</p><ul><li>All active validators will use this endpoint each epoch, so you can expect it to detect all local and active validators within several minutes after start up.</li><li><a href="https://lighthouse-book.sigmaprime.io/validator-monitoring.html#automatic">https://lighthouse-book.sigmaprime.io/validator-monitoring.html#automatic</a></li></ul>                        |
| `--monitoring-endpoint`         | <p>Enables the monitoring service for sending system metrics to a remote endpoint.</p><ul><li>This can be used to monitor your setup on certain services (e.g. <a href="https://beaconcha.in/user/settings#app">https://beaconcha.in/user/settings#app</a>).</li><li>This flag sets the endpoint where the beacon node metrics will be sent.</li><li>Note: This will send information to a remote server which may identify and associate your validators, IP address, and other personal information.</li><li>Always use a HTTPS connection and never provide an untrusted URL.</li></ul> |
| `--execution-endpoints`         | <p>One or more comma-delimited server endpoints for HTTP JSON-RPC connection.</p><ul><li>If multiple endpoints are given the endpoints are used as fallback in the given order.</li></ul>                                                                                                                                                                                                                                                                                                                                                                                                  |
| `--jwt-secrets`                 | <p>The JWT secret is automatically generated when <code>Geth</code> is first run and added to <code>/var/lib/goethereum/</code></p><ul><li>This is same location that is pointed to in both the <code>Geth</code> and <code>Lighthouse</code> configuration so that they use the same JWT secret and can talk to each other.</li></ul>                                                                                                                                                                                                                                                     |
| `--suggested-fee-recipient`     | <p>This address receives transaction fees collected from any blocks produced by this node.</p><ul><li>The <code>--suggested-fee-recipient</code> can be provided to the <code>Beacon Node</code> to act as a default value when the validator client does not transmit a <code>suggested_fee_recipient</code> to the <code>Beacon Node</code></li></ul>                                                                                                                                                                                                                                    |
| `--checkpoint-sync-url`         | <p>Used to fast sync the chain rather than starting at genesis.</p><ul><li><a href="https://lighthouse-book.sigmaprime.io/checkpoint-sync.html#automatic-checkpoint-sync">https://lighthouse-book.sigmaprime.io/checkpoint-sync.html#automatic-checkpoint-sync</a></li><li>Only needed for the initial sync, I can remove it after.</li></ul>                                                                                                                                                                                                                                              |
| `--builder`                     | <p>Used to query the provided URL during block production for a block payload with stubbed-out transactions.</p><ul><li>If this request fails, Lighthouse will fall back to the local execution engine and produce a block using transactions gathered and verified locally.</li></ul>                                                                                                                                                                                                                                                                                                     |
| `--reconstruct-historic-states` | <p>Used to reconstruct the history of the beacon chain when checkpoint sync was used.</p><ul><li>Allows access to all historical data.</li><li><a href="https://lighthouse-book.sigmaprime.io/checkpoint-sync.html#reconstructing-states">https://lighthouse-book.sigmaprime.io/checkpoint-sync.html#reconstructing-states</a></li></ul>                                                                                                                                                                                                                                                   |
| `--purge-db`                    | Add this flag to delete the existing database.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
{% endtab %}
{% endtabs %}

Start the service and check it's working as expected.

{% tabs %}
{% tab title="Command Aliases" %}
```bash
daemon-reload     # Reload any changes made to the lighthousebeacon.service
beacon-enable     # Enable the lighthousebeacon.service
beacon-start      # Start the lighthousebeacon.service
beacon-status     # View the status of the lighthousebeacon.service

beacon-log        # View the lighthousebeacon.service logs
```
{% endtab %}

{% tab title="Full Commands" %}
```bash
sudo systemctl daemon-reload                                     # Reload any changes made to the lighthousebeacon.service
sudo systemctl enable lighthousebeacon.service                   # Enable the lighthousebeacon.service
sudo systemctl start lighthousebeacon.service                    # Start the lighthousebeacon.service
sudo systemctl status lighthousebeacon.service                   # View the status of the lighthousebeacon.service

sudo journalctl -f -u lighthousebeacon.service -o cat | ccze -A  # View the lighthousebeacon.service logs
```
{% endtab %}
{% endtabs %}
