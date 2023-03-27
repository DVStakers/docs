---
description: Notes on how to use the Charon cluster.
---

# ⌨ Useful commands

<div>

<figure><img src="https://raw.githubusercontent.com/DVStakers/docs/main/.gitbook/assets/Eridian.png" alt=""><figcaption><p>Eridian</p></figcaption></figure>

 

<figure><img src="../../.gitbook/assets/Spacesider.png" alt=""><figcaption><p>Spacesider</p></figcaption></figure>

</div>

{% tabs %}
{% tab title="Command Aliases" %}
```bash
<CLUSTER_PREFIX>-log                  # View all the cluster logs

<CLUSTER_PREFIX>-log-node-lighthouse  # View the charon logs for the Lighthouse node
<CLUSTER_PREFIX>-log-node-teku        # View the charon logs for the Teku node
<CLUSTER_PREFIX>-log-node-nimbus      # View the charon logs for the Nimbus node
<CLUSTER_PREFIX>-log-vc-lighthouse    # View the logs for the Lighthouse VC
<CLUSTER_PREFIX>-log-vc-teku          # View the logs for the Teku VC
<CLUSTER_PREFIX>-log-vc-nimbus        # View the logs for the Nimbus VC

<CLUSTER_PREFIX>-start                # Start the cluster
<CLUSTER_PREFIX>-build                # Start the cluster and build any new versions
<CLUSTER_PREFIX>-stop                 # Stop the cluster

<CLUSTER_PREFIX>-status               # View the containers running with the name "<CLUSTER_PREFIX>"
<CLUSTER_PREFIX>-config               # Edit the cluster .env
```
{% endtab %}

{% tab title="Full Commands" %}
```bash
CLUSTER_PREFIX=    # E.g. obol

docker compose -f ~/${CLUSTER_PREFIX}/docker-compose.yml -p ${CLUSTER_PREFIX} logs -f        # View all the cluster logs

docker logs obol-node-lighthouse-1 -f    # View the charon logs for the Lighthouse node
docker logs obol-node-teku-1 -f          # View the charon logs for the Teku node
docker logs obol-node-nimbus-1 -f        # View the charon logs for the Nimbus node
docker logs obol-vc-lighthouse-1 -f      # View the logs for the Lighthouse VC
docker logs obol-vc-teku-1 -f            # View the logs for the Teki VC
docker logs obol-vc-nimbus-1 -f          # View the logs for the Nimbus VC

docker compose -f ~/${CLUSTER_PREFIX}/docker-compose.yml -p ${CLUSTER_PREFIX} up -d          # Start the cluster
docker compose -f ~/${CLUSTER_PREFIX}/docker-compose.yml -p ${CLUSTER_PREFIX} up -d --build  # Start the cluster and build any new versions
docker compose -f ~/${CLUSTER_PREFIX}/docker-compose.yml -p ${CLUSTER_PREFIX} down           # Stop the cluster

docker ps --filter "name=${CLUSTER_PREFIX}"                                                  # View the containers running with the name "<CLUSTER_PREFIX>"
vim ~/${CLUSTER_PREFIX}/.env                                                                 # Edit the cluster .env
```
{% endtab %}
{% endtabs %}
