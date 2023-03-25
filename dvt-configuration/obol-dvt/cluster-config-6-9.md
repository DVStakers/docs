---
description: Notes on how to configure a Charon 6/9 threshold cluster.
---

# 🔧 Cluster Config - 6/9

* [Pre-requisites](cluster-config-6-9.md#pre-requisites)
* [Create Aliases](cluster-config-6-9.md#create-aliases)
* [Step 1: Router & Firewall Configuration](cluster-config-6-9.md#step-1-router-and-firewall-configuration)
* [Step 2: Clone the repo](cluster-config-6-9.md#step-2-clone-the-repo)
* [Step 3: Configure the environment variables](cluster-config-6-9.md#step-3-configure-the-environment-variables)
* [Step 4: Import the keyshares](cluster-config-6-9.md#step-4-import-the-keyshares)
* [Step 5: Run the Cluster](cluster-config-6-9.md#step-5-run-the-cluster)

DVStakers has a [GitHub repo](https://github.com/DVStakers/charon-distributed-validator-cluster) that contains the docker configuration needed to set up three identical clusters, each with three nodes. Without performing a DKG and simply splitting existing validator keys, this repository can be used to construct the required cluster configuration for a 6/9 threshold DVT validator.

In this cluster configuration section, we will show how to create a 6/9 cluster that can be split between:

* 2 Participants + 1 Cloud Auxiliary Client
* 3 Participants

Where each participant holds three keyshares. Obol doesn't currently support 2/3 thresholds, so 6/9 was chosen so that each of the three participants is running 3 different validator clients, increasing client diversity.

### Pre-requisites

* An existing validator key (or set of keys) that has been [split into DVT keyshares](validator-keyshares/split-existing-keys.md).
* Existing EL and BN clients are synced and ready for connections from charon.

### Create Aliases

```bash
CLUSTER_PREFIX=        # E.g. obol

echo "alias ${CLUSTER_PREFIX}-log='docker compose -f ~/${CLUSTER_PREFIX}/docker-compose.yml -p ${CLUSTER_PREFIX} logs -f'" >> ~/.bashrc
echo "alias ${CLUSTER_PREFIX}-start='docker compose -f ~/${CLUSTER_PREFIX}/docker-compose.yml -p ${CLUSTER_PREFIX} up -d'" >> ~/.bashrc
echo "alias ${CLUSTER_PREFIX}-build='docker compose -f ~/${CLUSTER_PREFIX}/docker-compose.yml -p ${CLUSTER_PREFIX} up -d --build'" >> ~/.bashrc
echo "alias ${CLUSTER_PREFIX}-stop='docker compose -f ~/${CLUSTER_PREFIX}/docker-compose.yml -p ${CLUSTER_PREFIX} down'" >> ~/.bashrc
echo "alias ${CLUSTER_PREFIX}-status='docker ps --filter \"name=${CLUSTER_PREFIX}\"'" >> ~/.bashrc
echo "alias ${CLUSTER_PREFIX}-config='vim ~/${CLUSTER_PREFIX}/.env'" >> ~/.bashrc

source ~/.bashrc
```

### Step 1: Router & Firewall Configuration

Configure the firewall for this cluster.

```bash
CHARON_RELAY_PORT=
CHARON_RELAY_P2P_TCP_ADDRESS_PORT=
CHARON_LIGHTHOUSE_P2P_TCP_ADDRESS_PORT=
CHARON_TEKU_P2P_TCP_ADDRESS_PORT=
CHARON_NIMBUS_P2P_TCP_ADDRESS_PORT=

sudo ufw allow ${CHARON_RELAY_PORT}/tcp comment "Allow ${CLUSTER_PREFIX} Charon Relay in"
sudo ufw allow out ${CHARON_RELAY_PORT}/tcp comment "Allow ${CLUSTER_PREFIX} Charon Relay out"

sudo ufw allow ${CHARON_RELAY_P2P_TCP_ADDRESS_PORT}/tcp comment "Allow ${CLUSTER_PREFIX} Charon Relay P2P in"
sudo ufw allow out ${CHARON_RELAY_P2P_TCP_ADDRESS_PORT}/tcp comment "Allow ${CLUSTER_PREFIX} Charon Relay P2P out"

sudo ufw allow ${CHARON_LIGHTHOUSE_P2P_TCP_ADDRESS_PORT}/tcp comment "Allow ${CLUSTER_PREFIX} Charon Lighthouse P2P in"
sudo ufw allow out ${CHARON_LIGHTHOUSE_P2P_TCP_ADDRESS_PORT}/tcp comment "Allow ${CLUSTER_PREFIX} Charon Lighthouse P2P out"

sudo ufw allow ${CHARON_TEKU_P2P_TCP_ADDRESS_PORT}/tcp comment "Allow ${CLUSTER_PREFIX} Charon Teku P2P in"
sudo ufw allow out ${CHARON_TEKU_P2P_TCP_ADDRESS_PORT}/tcp comment "Allow ${CLUSTER_PREFIX} Charon Teku P2P out"

sudo ufw allow ${CHARON_NIMBUS_P2P_TCP_ADDRESS_PORT}/tcp comment "Allow ${CLUSTER_PREFIX} Charon Nimbus P2P in"
sudo ufw allow out ${CHARON_NIMBUS_P2P_TCP_ADDRESS_PORT}/tcp comment "Allow ${CLUSTER_PREFIX} Charon Nimbus P2P out"
```

### Step 2: Clone the repo

```bash
cd ~
mkdir ${CLUSTER_PREFIX}
cd ${CLUSTER_PREFIX}
git clone https://github.com/DVStakers/charon-distributed-validator-cluster.git .
```

### Step 3: Configure the environment variables

The repo comes with a `.env.sample` file that can be used as a template.

If you want to use that template as a starting point, make a copy.

```bash
cp .env.sample .env
```

Then edit the `.env` file.

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

# Set the graffiti for the validator client that will be attached to each proposed block
GRAFFITI=DVStakers

# Specify the IP addresses and ports used by the other participants to allow direct P2P connections
CHARON_P2P_RELAYS="http://<LOCAL_IP_ADDRESS_OF_CURRENT_MACHINE>:<LOCAL_CHARON_RELAY_PORT>/enr,http://<PUBLIC_IP_ADDRESS_OF_PARTICIPANT_2_REMOTE_RELAY>:<PARTICIPANT_2_CHARON_RELAY_PORT>/enr,http://<PUBLIC_IP_ADDRESS_OF_PARTICIPANT_3_REMOTE_RELAY>:<PARTICIPANT_3_CHARON_RELAY_PORT>/enr"

# Set the IP address and port of the Beacon Node client(s)
CHARON_BEACON_NODE_ENDPOINTS="http://<BEACON_NODE_IP_ADDRESS>:<BEACON_NODE_PORT>"

# Set the static IP address of the current machine so it can be reached by the other participants 
CHARON_P2P_EXTERNAL_HOSTNAME="<STATIC_PUBLIC_IP_OF_CURRENT_MACHINE>"

# Set the relay port that will be used by the other participants to establish a P2P connection
CHARON_RELAY_PORT="<CHARON_RELAY_PORT>"

# Set the port used for direct P2P communication to the relay
CHARON_RELAY_P2P_TCP_ADDRESS_PORT="<CHARON_RELAY_P2P_TCP_ADDRESS_PORT>"

# Set the port used by the Lighthouse node for direct P2P communication once the connection to the other participants has been established through the relay
CHARON_LIGHTHOUSE_P2P_TCP_ADDRESS_PORT="<CHARON_LIGHTHOUSE_P2P_TCP_ADDRESS_PORT>"

# Set the port used by the Teku node for direct P2P communication once the connection to the other participants has been established through the relay
CHARON_TEKU_P2P_TCP_ADDRESS_PORT="<CHARON_TEKU_P2P_TCP_ADDRESS_PORT>"

# Set the port used by the Nimbus node for direct P2P communication once the connection to the other participants has been established through the relay
CHARON_NIMBUS_P2P_TCP_ADDRESS_PORT="<CHARON_NIMBUS_P2P_TCP_ADDRESS_PORT>"

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

# Set the graffiti for the validator client that will be attached to each proposed block
GRAFFITI=DVStakers

# Specify the IP addresses and ports used by the other participants to allow direct P2P connections
CHARON_P2P_RELAYS="http://<LOCAL_IP_ADDRESS_OF_CURRENT_MACHINE>:<LOCAL_CHARON_RELAY_PORT>/enr,http://<PUBLIC_IP_ADDRESS_OF_PARTICIPANT_1_REMOTE_RELAY>:<PARTICIPANT_1_CHARON_RELAY_PORT>/enr,http://<PUBLIC_IP_ADDRESS_OF_PARTICIPANT_3_REMOTE_RELAY>:<PARTICIPANT_3_CHARON_RELAY_PORT>/enr"

# Set the IP address and port of the Beacon Node client(s)
CHARON_BEACON_NODE_ENDPOINTS="http://<BEACON_NODE_IP_ADDRESS>:<BEACON_NODE_PORT>"

# Set the static IP address of the current machine so it can be reached by the other participants 
CHARON_P2P_EXTERNAL_HOSTNAME="<STATIC_PUBLIC_IP_OF_CURRENT_MACHINE>"

# Set the relay port that will be used by the other participants to establish a P2P connection
CHARON_RELAY_PORT="<CHARON_RELAY_PORT>"

# Set the port used for direct P2P communication to the relay
CHARON_RELAY_P2P_TCP_ADDRESS_PORT="<CHARON_RELAY_P2P_TCP_ADDRESS_PORT>"

# Set the port used by the Lighthouse node for direct P2P communication once the connection to the other participants has been established through the relay
CHARON_LIGHTHOUSE_P2P_TCP_ADDRESS_PORT="<CHARON_LIGHTHOUSE_P2P_TCP_ADDRESS_PORT>"

# Set the port used by the Teku node for direct P2P communication once the connection to the other participants has been established through the relay
CHARON_TEKU_P2P_TCP_ADDRESS_PORT="<CHARON_TEKU_P2P_TCP_ADDRESS_PORT>"

# Set the port used by the Nimbus node for direct P2P communication once the connection to the other participants has been established through the relay
CHARON_NIMBUS_P2P_TCP_ADDRESS_PORT="<CHARON_NIMBUS_P2P_TCP_ADDRESS_PORT>"

# Set the Grafana external port to allow external access
MONITORING_PORT_GRAFANA="<GRAFANA_PUBLIC_PORT>"
```
{% endcode %}
{% endtab %}

{% tab title="Participant 3 / Cloud Auxiliary Client" %}
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

# Set the graffiti for the validator client that will be attached to each proposed block
GRAFFITI=DVStakers

# Specify the IP addresses and ports used by the other participants to allow direct P2P connections
CHARON_P2P_RELAYS="http://<LOCAL_IP_ADDRESS_OF_CURRENT_MACHINE>:<LOCAL_CHARON_RELAY_PORT>/enr,http://<PUBLIC_IP_ADDRESS_OF_PARTICIPANT_1_REMOTE_RELAY>:<PARTICIPANT_1_CHARON_RELAY_PORT>/enr,http://<PUBLIC_IP_ADDRESS_OF_PARTICIPANT_2_REMOTE_RELAY>:<PARTICIPANT_2_CHARON_RELAY_PORT>/enr"

# Set the IP address and port of the Beacon Node client(s)
CHARON_BEACON_NODE_ENDPOINTS="http://<BEACON_NODE_IP_ADDRESS>:<BEACON_NODE_PORT>"

# Set the static IP address of the current machine so it can be reached by the other participants 
CHARON_P2P_EXTERNAL_HOSTNAME="<STATIC_PUBLIC_IP_OF_CURRENT_MACHINE>"

# Set the relay port that will be used by the other participants to establish a P2P connection
CHARON_RELAY_PORT="<CHARON_RELAY_PORT>"

# Set the port used for direct P2P communication to the relay
CHARON_RELAY_P2P_TCP_ADDRESS_PORT="<CHARON_RELAY_P2P_TCP_ADDRESS_PORT>"

# Set the port used by the Lighthouse node for direct P2P communication once the connection to the other participants has been established through the relay
CHARON_LIGHTHOUSE_P2P_TCP_ADDRESS_PORT="<CHARON_LIGHTHOUSE_P2P_TCP_ADDRESS_PORT>"

# Set the port used by the Teku node for direct P2P communication once the connection to the other participants has been established through the relay
CHARON_TEKU_P2P_TCP_ADDRESS_PORT="<CHARON_TEKU_P2P_TCP_ADDRESS_PORT>"

# Set the port used by the Nimbus node for direct P2P communication once the connection to the other participants has been established through the relay
CHARON_NIMBUS_P2P_TCP_ADDRESS_PORT="<CHARON_NIMBUS_P2P_TCP_ADDRESS_PORT>"

# Set the Grafana external port to allow external access
MONITORING_PORT_GRAFANA="<GRAFANA_PUBLIC_PORT>"
```
{% endcode %}
{% endtab %}
{% endtabs %}

### Step 4: Import the keyshares

At this step, when each participant has their cluster configured, all that is needed is to import the keyshares. These should have already been created at a previous step, either by [splitting existing validator keys](validator-keyshares/split-existing-keys.md) or using a [DKG ceremony](validator-keyshares/obol-dkg-ceremony.md).

The numbered `node*` directories should be placed in the `.charon/cluster` directory.&#x20;

```bash
mkdir -p .charon/cluster
```

{% tabs %}
{% tab title="Participant 1" %}
```
.charon/cluster/
├─ node0/
   ├─ charon-enr-private-key    
   ├─ cluster-lock.json        
   ├─ validator_keys            
      ├─ keystore-*.json      
      ├─ keystore-*.txt        
├─ node1/
   ├─ charon-enr-private-key    
   ├─ cluster-lock.json        
   ├─ validator_keys            
      ├─ keystore-*.json      
      ├─ keystore-*.txt
├─ node2/
   ├─ charon-enr-private-key    
   ├─ cluster-lock.json        
   ├─ validator_keys            
      ├─ keystore-*.json      
      ├─ keystore-*.txt  
```
{% endtab %}

{% tab title="Participant 2" %}
```
.charon/cluster/
├─ node3/
   ├─ charon-enr-private-key    
   ├─ cluster-lock.json        
   ├─ validator_keys            
      ├─ keystore-*.json      
      ├─ keystore-*.txt        
├─ node4/
   ├─ charon-enr-private-key    
   ├─ cluster-lock.json        
   ├─ validator_keys            
      ├─ keystore-*.json      
      ├─ keystore-*.txt
├─ node5/
   ├─ charon-enr-private-key    
   ├─ cluster-lock.json        
   ├─ validator_keys            
      ├─ keystore-*.json      
      ├─ keystore-*.txt  
```
{% endtab %}

{% tab title="Participant 3 / Cloud Auxiliary Client" %}
```
.charon/cluster/
├─ node6/
   ├─ charon-enr-private-key    
   ├─ cluster-lock.json        
   ├─ validator_keys            
      ├─ keystore-*.json      
      ├─ keystore-*.txt        
├─ node7/
   ├─ charon-enr-private-key    
   ├─ cluster-lock.json        
   ├─ validator_keys            
      ├─ keystore-*.json      
      ├─ keystore-*.txt
├─ node8/
   ├─ charon-enr-private-key    
   ├─ cluster-lock.json        
   ├─ validator_keys            
      ├─ keystore-*.json      
      ├─ keystore-*.txt  
```
{% endtab %}
{% endtabs %}

### Step 5: Run the Cluster

Once everything has been configured, it should be as simple as running the command to start the docker containers.

To avoid any conflicts with container names in the future, you can give a prefix to all containers created by the `docker compose` command by using the `-p` flag.&#x20;

Starting the cluster with the `-d` flag means the containers will be detached and start in the background. The logs can then be viewed using the `logs -f` command flag.

Start the containers and check it's working as expected.

{% tabs %}
{% tab title="Command Aliases" %}
```bash
<CLUSTER_PREFIX>-start          # Start the cluster
<CLUSTER_PREFIX>-status         # View the containers running with the name "<CLUSTER_PREFIX>"

<CLUSTER_PREFIX>-log            # View the cluster logs
```
{% endtab %}

{% tab title="Full Commands" %}
```bash
docker compose -f ~/${CLUSTER_PREFIX}/docker-compose.yml -p ${CLUSTER_PREFIX} up -d      # Start the cluster
docker ps --filter "name=${CLUSTER_PREFIX}"                                              # View the containers running with the name "<CLUSTER_PREFIX>"

docker compose -f ~/${CLUSTER_PREFIX}/docker-compose.yml -p ${CLUSTER_PREFIX} logs -f    # View the cluster logs
```
{% endtab %}
{% endtabs %}
