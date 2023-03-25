---
description: Notes on how to use the Charon cluster.
---

# ⌨ Useful commands

{% tabs %}
{% tab title="Command Aliases" %}
```bash
<CLUSTER_PREFIX>-log          # View the cluster logs
<CLUSTER_PREFIX>-start        # Start the cluster
<CLUSTER_PREFIX>-build        # Start the cluster and build any new versions
<CLUSTER_PREFIX>-stop         # Stop the cluster

<CLUSTER_PREFIX>-status       # View the containers running with the name "<CLUSTER_PREFIX>"
<CLUSTER_PREFIX>-config       # Edit the cluster .env
```
{% endtab %}

{% tab title="Full Commands" %}
```bash
CLUSTER_PREFIX=    # E.g. obol

docker compose -f ~/${CLUSTER_PREFIX}/docker-compose.yml -p ${CLUSTER_PREFIX} logs -f        # View the cluster logs
docker compose -f ~/${CLUSTER_PREFIX}/docker-compose.yml -p ${CLUSTER_PREFIX} up -d          # Start the cluster
docker compose -f ~/${CLUSTER_PREFIX}/docker-compose.yml -p ${CLUSTER_PREFIX} up -d --build  # Start the cluster and build any new versions
docker compose -f ~/${CLUSTER_PREFIX}/docker-compose.yml -p ${CLUSTER_PREFIX} down           # Stop the cluster

docker ps --filter "name=${CLUSTER_PREFIX}"                                                  # View the containers running with the name "<CLUSTER_PREFIX>"
vim ~/${CLUSTER_PREFIX}/.env                                                                 # Edit the cluster .env
```
{% endtab %}
{% endtabs %}
