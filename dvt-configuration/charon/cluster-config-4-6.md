---
description: Notes on how to configure a Charon 4/6 threshold cluster.
---

# 🔧 Cluster Config - 6/9

* [Pre-requisites](cluster-config-4-6.md#pre-requisites)
* [Step 1: Router & Firewall Configuration](cluster-config-4-6.md#step-1-router-and-firewall-configuration)
* [Step 2: Clone the repo](cluster-config-4-6.md#step-2-clone-the-repo)
* [Step 3: Edit .env](cluster-config-4-6.md#step-3-edit-.env)
* [Step 4: Split existing validator keys](cluster-config-4-6.md#step-4-import-keyshares)
* [Step 5: Run the Cluster](cluster-config-4-6.md#step-5-run-the-cluster)

DVStakers has a [GitHub repo](https://github.com/DVStakers/charon-distributed-validator-cluster) that contains the docker configuration needed to set up three identical clusters, each with three nodes. Without performing a DKG and simply splitting existing validator keys, this repository can be used to construct the required cluster configuration for a 6/9 threshold DVT validator.

In this cluster configuration section, we will show how to create a 6/9 cluster that can be split between:

* 2 participants + 1 cloud auxiliary node
* 3 participants

Where each participant holds three keyshares. Obol doesn't currently support 2/3 thresholds, so 6/9 was chosen so that each of the three participants is running 3 different validator clients, increasing client diversity.

### Pre-requisites

* An existing validator key (or set of keys) that has been [split into DVT keyshares](../split-validator-keys.md).
* Existing EL and BN clients are synced and ready for connections from charon.

### Step 1: Router & Firewall Configuration

The default port used by charon is `3610`. This port needs to be configured on the router to allow `tcp` connections to be forwarded to your machine.

If you want to change this default charon port, it needs to be configured in the `.env` ([shown in Step 3](cluster-config-4-6.md#step-3-edit-.env)).

Configure the firewall.

```bash
CHARON_PUBLIC_P2P_PORT=      # Default: 3610
CHARON_RELAY_PORT=           # Default: 3640

sudo ufw allow ${CHARON_PUBLIC_P2P_PORT}/tcp comment 'Allow Charon P2P in'
sudo ufw allow out ${CHARON_PUBLIC_P2P_PORT}/tcp comment 'Allow Charon P2P out'

sudo ufw allow ${CHARON_RELAY_PORT}/tcp comment 'Allow Charon Relay in'
sudo ufw allow out ${CHARON_RELAY_PORT}/tcp comment 'Allow Charon Relay out'
```

### Step 2: Clone the repo

```bash
cd ~
git clone https://github.com/DVStakers/charon-distributed-validator-cluster.git
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

{% tabs %}
{% tab title="Participant 1" %}
{% code title=".env" %}
```bash
# Set the network e.g. mainnet or goerli
NETWORK=

# Set the nodes for the current participant
FIRST_NODE_NUMBER=0
SECOND_NODE_NUMBER=1
THIRD_NODE_NUMBER=2

# Set the fee recipient address for the validator client e.g. 0x000...
FEE_RECIPIENT=

# Specify the IP addresses and ports used by the other participants to allow direct P2P connections
CHARON_P2P_RELAYS="http://<LOCAL_IP_ADDRESS_OF_CURRENT_MACHINE>:<LOCAL_CHARON_RELAY_PORT>/enr,http://<PUBLIC_IP_ADDRESS_OF_PARTICIPANT_2_REMOTE_RELAY>:<PARTICIPANT_2_CHARON_RELAY_PORT>/enr,http://<PUBLIC_IP_ADDRESS_OF_PARTICIPANT_3_REMOTE_RELAY>:<PARTICIPANT_3_CHARON_RELAY_PORT>/enr"

# Set the IP address and port of the Beacon Node client(s)
CHARON_BEACON_NODE_ENDPOINTS="http://<BEACON_NODE_IP_ADDRESS>:<BEACON_NODE_PORT>"

# Set the static IP address of the current machine so it can be reached by the other participants 
CHARON_P2P_EXTERNAL_HOSTNAME="<STATIC_PUBLIC_IP_OF_CURRENT_MACHINE>"

# Set the charon relay port that will be used by the other participants to establish a P2P connection
CHARON_RELAY_PORT="<CHARON_RELAY_PORT>"

# Set the charon port used for direct P2P communication once the connection to the other participants has been established through the relay
CHARON_P2P_TCP_ADDRESS="0.0.0.0:<CHARON_PUBLIC_P2P_PORT>"

# Set the Grafana external port to allow external access
MONITORING_PORT_GRAFANA="<GRAFANA_PUBLIC_PORT>"
```
{% endcode %}
{% endtab %}

{% tab title="Participant 2" %}
{% code title=".env" %}
```bash
# Set the network e.g. mainnet or goerli
NETWORK=

# Set the nodes for the current participant
FIRST_NODE_NUMBER=3
SECOND_NODE_NUMBER=4
THIRD_NODE_NUMBER=5

# Set the fee recipient address for the validator client e.g. 0x000...
FEE_RECIPIENT=

# Specify the IP addresses and ports used by the other participants to allow direct P2P connections
CHARON_P2P_RELAYS="http://<LOCAL_IP_ADDRESS_OF_CURRENT_MACHINE>:<LOCAL_CHARON_RELAY_PORT>/enr,http://<PUBLIC_IP_ADDRESS_OF_PARTICIPANT_2_REMOTE_RELAY>:<PARTICIPANT_2_CHARON_RELAY_PORT>/enr,http://<PUBLIC_IP_ADDRESS_OF_PARTICIPANT_3_REMOTE_RELAY>:<PARTICIPANT_3_CHARON_RELAY_PORT>/enr"

# Set the IP address and port of the Beacon Node client(s)
CHARON_BEACON_NODE_ENDPOINTS="http://<BEACON_NODE_IP_ADDRESS>:<BEACON_NODE_PORT>"

# Set the static IP address of the current machine so it can be reached by the other participants 
CHARON_P2P_EXTERNAL_HOSTNAME="<STATIC_PUBLIC_IP_OF_CURRENT_MACHINE>"

# Set the charon relay port that will be used by the other participants to establish a P2P connection
CHARON_RELAY_PORT="<CHARON_RELAY_PORT>"

# Set the charon port used for direct P2P communication once the connection to the other participants has been established through the relay
CHARON_P2P_TCP_ADDRESS="0.0.0.0:<CHARON_PUBLIC_P2P_PORT>"

# Set the Grafana external port to allow external access
MONITORING_PORT_GRAFANA="<GRAFANA_PUBLIC_PORT>"
```
{% endcode %}
{% endtab %}

{% tab title="Participant 3 / Cloud Auxiliary Node" %}
{% code title=".env" %}
```bash
# Set the network e.g. mainnet or goerli
NETWORK=

# Set the nodes for the current participant
FIRST_NODE_NUMBER=6
SECOND_NODE_NUMBER=7
THIRD_NODE_NUMBER=8

# Set the fee recipient address for the validator client e.g. 0x000...
FEE_RECIPIENT=

# Specify the IP addresses and ports used by the other participants to allow direct P2P connections
CHARON_P2P_RELAYS="http://<LOCAL_IP_ADDRESS_OF_CURRENT_MACHINE>:<LOCAL_CHARON_RELAY_PORT>/enr,http://<PUBLIC_IP_ADDRESS_OF_PARTICIPANT_2_REMOTE_RELAY>:<PARTICIPANT_2_CHARON_RELAY_PORT>/enr,http://<PUBLIC_IP_ADDRESS_OF_PARTICIPANT_3_REMOTE_RELAY>:<PARTICIPANT_3_CHARON_RELAY_PORT>/enr"

# Set the IP address and port of the Beacon Node client(s)
CHARON_BEACON_NODE_ENDPOINTS="http://<BEACON_NODE_IP_ADDRESS>:<BEACON_NODE_PORT>"

# Set the static IP address of the current machine so it can be reached by the other participants 
CHARON_P2P_EXTERNAL_HOSTNAME="<STATIC_PUBLIC_IP_OF_CURRENT_MACHINE>"

# Set the charon relay port that will be used by the other participants to establish a P2P connection
CHARON_RELAY_PORT="<CHARON_RELAY_PORT>"

# Set the charon port used for direct P2P communication once the connection to the other participants has been established through the relay
CHARON_P2P_TCP_ADDRESS="0.0.0.0:<CHARON_PUBLIC_P2P_PORT>"

# Set the Grafana external port to allow external access
MONITORING_PORT_GRAFANA="<GRAFANA_PUBLIC_PORT>"
```
{% endcode %}
{% endtab %}
{% endtabs %}

### Step 4: Import keyshares

At this step, when each participant has their cluster configured, all that is needed is to import the keyshares. These should have already been [generated](../validator-key-generation.md) and [split](../split-validator-keys.md).

Copy the numbered `node` directories to each participant. They should be placed in the `.charon` directory of each in a sub-directory called `cluster` will need to be created.&#x20;

```bash
mkdir ~/charon-distributed-validator-cluster/.charon/cluster
```

{% tabs %}
{% tab title="Participant 1" %}
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

{% tab title="Participant 2" %}
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

{% tab title="Participant 3 / Cloud Auxiliary Node" %}
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

### Step 5: Run the Cluster

Once everything has been configured, it should be as simple as running the command to start the docker containers.

To avoid any conflicts with container names in the future, you can give a prefix to all containers created by the `docker compose` command by using the `-p` flag:

```bash
CONTAINER_PREFIX=        # E.g. Cluster1

docker compose -p ${CONTAINER_PREFIX} up --build
```
