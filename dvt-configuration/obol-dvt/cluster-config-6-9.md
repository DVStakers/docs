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

* An existing validator key (or set of keys) that has been split into [validator keyshares](validator-keyshares/).
* Existing EL and BN clients are synced and ready for connections from charon.

### Create Aliases

```bash
CLUSTER_PREFIX=        # E.g. obol

echo "alias ${CLUSTER_PREFIX}-log='docker compose -f ~/${CLUSTER_PREFIX}/docker-compose.yml -p ${CLUSTER_PREFIX} logs -f'" >> ~/.bashrc
echo "alias ${CLUSTER_PREFIX}-log-node-lighthouse='docker logs obol-node-lighthouse-1 -f'" >> ~/.bashrc
echo "alias ${CLUSTER_PREFIX}-log-node-teku='docker logs obol-node-teku-1 -f'" >> ~/.bashrc
echo "alias ${CLUSTER_PREFIX}-log-node-nimbus='docker logs obol-node-nimbus-1 -f'" >> ~/.bashrc
echo "alias ${CLUSTER_PREFIX}-log-vc-lighthouse='docker logs obol-vc-lighthouse-1 -f'" >> ~/.bashrc
echo "alias ${CLUSTER_PREFIX}-log-vc-teku='docker logs obol-vc-teku-1 -f'" >> ~/.bashrc
echo "alias ${CLUSTER_PREFIX}-log-vc-nimbus='docker logs obol-vc-nimbus-1 -f'" >> ~/.bashrc
echo "alias ${CLUSTER_PREFIX}-start='docker compose -f ~/${CLUSTER_PREFIX}/docker-compose.yml -p ${CLUSTER_PREFIX} up --detach'" >> ~/.bashrc
echo "alias ${CLUSTER_PREFIX}-build='docker compose -f ~/${CLUSTER_PREFIX}/docker-compose.yml -p ${CLUSTER_PREFIX} up --detach --build'" >> ~/.bashrc
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
sudo ufw allow ${CHARON_RELAY_P2P_TCP_ADDRESS_PORT}/tcp comment "Allow ${CLUSTER_PREFIX} Charon Relay P2P in"
sudo ufw allow ${CHARON_LIGHTHOUSE_P2P_TCP_ADDRESS_PORT}/tcp comment "Allow ${CLUSTER_PREFIX} Charon Lighthouse P2P in"
sudo ufw allow ${CHARON_TEKU_P2P_TCP_ADDRESS_PORT}/tcp comment "Allow ${CLUSTER_PREFIX} Charon Teku P2P in"
sudo ufw allow ${CHARON_NIMBUS_P2P_TCP_ADDRESS_PORT}/tcp comment "Allow ${CLUSTER_PREFIX} Charon Nimbus P2P in"
```

### Step 2: Clone the repo

```bash
cd ~
mkdir ${CLUSTER_PREFIX}
cd ${CLUSTER_PREFIX}
git clone https://github.com/DVStakers/charon-distributed-validator-cluster.git .
```

### Step 3: Configure the environment variables

The repo comes with a `.env.sample` file that should be used as a template.

```bash
cp .env.sample .env
```

Edit the `.env` file and set all the required variables.

```bash
vim .env
```

For the `*_NODE_NUMBER` variables, use the following values for each participant.

| Participant 1                                                                                                                 | Participant 2                                                                                                                 | Participant 3 / Cloud Auxiliary Client                                                                                        |
| ----------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------- |
| <pre class="language-bash"><code class="lang-bash">FIRST_NODE_NUMBER=0
SECOND_NODE_NUMBER=1
THIRD_NODE_NUMBER=2
</code></pre> | <pre class="language-bash"><code class="lang-bash">FIRST_NODE_NUMBER=3
SECOND_NODE_NUMBER=4
THIRD_NODE_NUMBER=5
</code></pre> | <pre class="language-bash"><code class="lang-bash">FIRST_NODE_NUMBER=6
SECOND_NODE_NUMBER=7
THIRD_NODE_NUMBER=8
</code></pre> |

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

Starting the cluster with the `--detach` flag means the containers will be detached and start in the background. The logs can then be viewed using the `logs -f` command flag.

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
docker compose -f ~/${CLUSTER_PREFIX}/docker-compose.yml -p ${CLUSTER_PREFIX} up --detach    # Start the cluster
docker ps --filter "name=${CLUSTER_PREFIX}"                                                  # View the containers running with the name "<CLUSTER_PREFIX>"

docker compose -f ~/${CLUSTER_PREFIX}/docker-compose.yml -p ${CLUSTER_PREFIX} logs -f        # View the cluster logs
```
{% endtab %}
{% endtabs %}
