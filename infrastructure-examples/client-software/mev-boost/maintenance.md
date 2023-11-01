---
description: Notes on how to maintain and update an MEV Boost client.
---

# 🏗 Maintenance

### MEV Boost - Update Client

{% tabs %}
{% tab title="Command Aliases" %}
```bash
mev-update
```
{% endtab %}
{% endtabs %}

### MEV Boost - Update mevboost.service

{% tabs %}
{% tab title="Command Aliases" %}
```bash
mev-stop
mev-config

# MAKE ANY CHANGES TO THE CONFIG

daemon-reload
mev-start
mev-status
```
{% endtab %}

{% tab title="Full Commands" %}
```bash
sudo systemctl stop mevboost.service
sudo vim /etc/systemd/system/mevboost.service

# MAKE ANY CHANGES TO THE CONFIG

sudo systemctl daemon-reload
sudo systemctl start mevboost.service
sudo systemctl status mevboost.service
```
{% endtab %}
{% endtabs %}
