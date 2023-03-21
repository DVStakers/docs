---
description: Notes on how to configure a Charon 4/6 threshold cluster.
---

# 🔧 Cluster Config - 4/6

* [Pre-requisites](cluster-config-4-6.md#pre-requisites)
* [Step 1: Router & Firewall Configuration](cluster-config-4-6.md#step-1-router-and-firewall-configuration)
* [Step 2: Clone the repo](cluster-config-4-6.md#step-2-clone-the-repo)
* [Step 3: Edit .env](cluster-config-4-6.md#step-3-edit-.env)
* [Step 4: Edit docker-compose.override.yml](cluster-config-4-6.md#step-4-edit-docker-compose.override.yml)
* [Step 5: Split existing validator keys](cluster-config-4-6.md#step-5-split-existing-validator-keys)
* [Step 6: Run the Cluster](cluster-config-4-6.md#step-6-run-the-cluster)

Obol has a [GitHub repo](https://github.com/ObolNetwork/charon-distributed-validator-cluster) that contains everything you need to run a full charon cluster on your own. Without performing a DKG and simply splitting existing validator keys, this repository can be used to construct any cluster configuration.

In this cluster configuration section, we will show how to create a 4/6 cluster that can be split between:

* 2 stakers + 1 cloud auxiliary node
* 3 stakers

Where each participant holds two keyshares. Obol doesn't support 2/3 thresholds, so 4/6 is the only way to currently achieve this setup with only two stakers. However, it has the added benefit of allowing for increased client diversity as two validator clients can be run per cluster.

### Pre-requisites

* An existing validator key (or set of keys) that has been [split into DVT keyshares](../split-validator-keys.md).
* Existing EL and BN clients are synced and ready for connections from charon.

### Step 1: Router & Firewall Configuration

The default port used by charon is `3610`. This port needs to be configured on the router to allow `tcp` connections to be forwarded to your machine.

If you want to change this default charon port, it needs to be configured in the `.docker-compose.override.yml` (shown in [Step 4](cluster-config-4-6.md#step-4-edit-docker-compose.override.yml)).

Configure the firewall.

```bash
CHARON_PUBLIC_P2P_PORT=      # Default: 3610

sudo ufw allow ${CHARON_PUBLIC_P2P_PORT}/tcp comment 'Allow Charon P2P in'
sudo ufw allow out ${CHARON_PUBLIC_P2P_PORT}/tcp comment 'Allow Charon P2P out'
```

### Step 2: Clone the repo

```bash
cd ~
git clone https://github.com/ObolNetwork/charon-distributed-validator-cluster.git
cd charon-distributed-validator-cluster
```

### Step 3: Edit `.env`

The repo comes with a `.env.sample` file that can be used as a template.

If you want to use that template as a starting point, make a copy.

```bash
cp .env.sample .env
```

Edit the `.env` file.

```bash
vim .env
```

{% code title=".env" %}
```bash
# Set the required versions of each client, or simply set "latest"
NETHERMIND_VERSION="<NETHERMIND_VERSION>"
LIGHTHOUSE_VERSION="<LIGHTHOUSE_VERSION>"
TEKU_VERSION="<TEKU_VERSION>"
CHARON_VERSION="<CHARON_VERSION>"

# Specify the IP addresses and ports used by the other participants to allow direct P2P connections
CHARON_P2P_RELAYS="http://<IP_ADDRESS>:<CHARON_RELAY_PORT>/enr,http://<IP_ADDRESS>:<CHARON_RELAY_PORT>/enr"

# Set the IP address and port of your existing Beacon Node client
CHARON_BEACON_NODE_ENDPOINTS="http://<IP_ADDRESS>:<BEACON_PORT>"
```
{% endcode %}

### Step 4: Edit `docker-compose.override.yml`

The `.docker-compose.override.yml` file is used to change variables without creating conflicts with future versions of charon. No changes should be made directly to the `.docker-compose.yml` file as that will create conflicts with future updates.

The repo comes with a `.docker-compose.override.yml` file that can be used as a template.

If you want to use that template as a starting point, make a copy.

```bash
cp docker-compose.override.yml.sample docker-compose.override.yml
```

Edit the `.docker-compose.override.yml` file.

```bash
vim docker-compose.override.yml
```

{% tabs %}
{% tab title="Staker 1" %}
Staker 1 runs Lighthouse and Teku validator clients.

{% code title="docker-compose.override.yml" %}
```bash
services:
  nethermind:
    # Disable nethermind
    profiles: [disable]

  lighthouse:
    # Disable lighthouse
    profiles: [disable]

  # Disable the charon nodes used by the other participants
  node2:
    profiles: [disable]

  node3:
    profiles: [disable]

  node4:
    profiles: [disable]

  node5:
    profiles: [disable]

  # Edit the relay ports
  relay:
    ports:
      - 3610:<CHARON_PUBLIC_P2P_PORT>/tcp

  # Disable the validator clients used by the other participants
  vc2-nimbus:
    profiles: [disable]

  vc3-lighthouse:
    profiles: [disable]

  vc4-teku:
    profiles: [disable]

  vc5-nimbus:
    profiles: [disable]
    
# If you are using existing monitoring tools
# you may need to disable the monitoring containers as well
  prometheus:
    profiles: [disable]

  grafana:
    profiles: [disable]

  node-exporter:
    profiles: [disable]

  jaeger:
    profiles: [disable]
```
{% endcode %}
{% endtab %}

{% tab title="Staker 2" %}
Staker 2 runs Nimbus and Lighthouse validator clients.

{% code title="docker-compose.override.yml" %}
```bash
services:
  nethermind:
    # Disable nethermind
    profiles: [disable]

  lighthouse:
    # Disable lighthouse
    profiles: [disable]

  # Disable the charon nodes used by the other participants
  node0:
    profiles: [disable]

  node1:
    profiles: [disable]

  node4:
    profiles: [disable]

  node5:
    profiles: [disable]

  # Edit the relay ports
  relay:
    ports:
      - 3610:<CHARON_PUBLIC_P2P_PORT>/tcp

  # Disable the validator clients used by the other participants
  vc0-lighthouse:
    profiles: [disable]

  vc1-teku:
    profiles: [disable]

  vc4-teku:
    profiles: [disable]

  vc5-nimbus:
    profiles: [disable]
    
# If you are using existing monitoring tools
# you may need to disable the monitoring images as well
  prometheus:
    profiles: [disable]

  grafana:
    profiles: [disable]

  node-exporter:
    profiles: [disable]

  jaeger:
    profiles: [disable]
```
{% endcode %}
{% endtab %}

{% tab title="Cloud Auxiliary Node / Staker 3" %}
Cloud Auxiliary Node / Staker 3 runs Teku and Nimbus validator clients.

{% code title="docker-compose.override.yml" %}
```bash
services:
  nethermind:
    # Disable nethermind
    profiles: [disable]

  lighthouse:
    # Disable lighthouse
    profiles: [disable]

  # Disable the charon nodes used by the other participants
  node0:
    profiles: [disable]

  node1:
    profiles: [disable]

  node2:
    profiles: [disable]

  node3:
    profiles: [disable]

  # Edit the relay ports
  relay:
    ports:
      - 3610:<CHARON_PUBLIC_P2P_PORT>/tcp

  # Disable the validator clients used by the other participants
  vc0-lighthouse:
    profiles: [disable]

  vc1-teku:
    profiles: [disable]

  vc2-nimbus:
    profiles: [disable]

  vc3-lighthouse:
    profiles: [disable]
    
# If you are using existing monitoring tools
# you may need to disable the monitoring images as well
  prometheus:
    profiles: [disable]

  grafana:
    profiles: [disable]

  node-exporter:
    profiles: [disable]

  jaeger:
    profiles: [disable]
```
{% endcode %}
{% endtab %}
{% endtabs %}

### Step 5: Import keyshares

At this step, when each participant has their cluster configured, all that is needed is to import the keyshares. These should have already been [generated](../validator-key-generation.md) and [split](../split-validator-keys.md).

Copy the numbered `node` directories to each participant. They should be placed in the `.charon` directory of each in a sub-directory called `cluster` will need to be created.&#x20;

```bash
mkdir ~/charon-distributed-validator-cluster/.charon/cluster
```

{% tabs %}
{% tab title="Staker 1" %}
```
~/charon-distributed-validator-cluster/.charon/cluster/
├─ node[0]/
│  ├─ charon-enr-private-key    
│  ├─ cluster-lock.json        
│  ├─ validator_keys            
│  │  ├─ keystore-*.json      
│  │  ├─ keystore-*.txt        
├─ node[1]/
│  ├─ charon-enr-private-key    
│  ├─ cluster-lock.json        
│  ├─ validator_keys            
│  │  ├─ keystore-*.json      
│  │  ├─ keystore-*.txt  
```
{% endtab %}

{% tab title="Staker 2" %}
```
~/charon-distributed-validator-cluster/.charon/cluster/
├─ node[2]/
│  ├─ charon-enr-private-key    
│  ├─ cluster-lock.json        
│  ├─ validator_keys            
│  │  ├─ keystore-*.json      
│  │  ├─ keystore-*.txt        
├─ node[3]/
│  ├─ charon-enr-private-key    
│  ├─ cluster-lock.json        
│  ├─ validator_keys            
│  │  ├─ keystore-*.json      
│  │  ├─ keystore-*.txt  
```
{% endtab %}

{% tab title="Cloud Auxiliary Node / Staker 3" %}
```
~/charon-distributed-validator-cluster/.charon/cluster/
├─ node[4]/
│  ├─ charon-enr-private-key    
│  ├─ cluster-lock.json        
│  ├─ validator_keys            
│  │  ├─ keystore-*.json      
│  │  ├─ keystore-*.txt        
├─ node[5]/
│  ├─ charon-enr-private-key    
│  ├─ cluster-lock.json        
│  ├─ validator_keys            
│  │  ├─ keystore-*.json      
│  │  ├─ keystore-*.txt  
```
{% endtab %}
{% endtabs %}

### Step 6: Run the Cluster

Once everything has been configured, it should be as simple as running the command to start the docker containers.

To avoid any conflicts with container names in the future, you can give a prefix to all containers created by the `docker compose` command by using the `-p` flag:

```bash
CONTAINER_PREFIX=        # E.g. Cluster1

docker compose -p ${CONTAINER_PREFIX} up --build
```
