---
description: Notes on how to maintain and update a Lighthouse Validator Client.
---

# 🏗 Maintenance

### Lighthouse - Update lighthousevalidator.service

{% tabs %}
{% tab title="Command Aliases" %}
```bash
validator-stop
validator-config

# MAKE ANY CHANGES TO THE CONFIG

daemon-reload
validator-start
validator-status
```
{% endtab %}

{% tab title="Full Commands" %}
```bash
sudo systemctl stop lighthousevalidator.service
sudo vim /etc/systemd/system/lighthousevalidator.service

# MAKE ANY CHANGES TO THE CONFIG

sudo systemctl daemon-reload
sudo systemctl start lighthousevalidator.service
sudo systemctl status lighthousevalidator.service
```
{% endtab %}
{% endtabs %}
