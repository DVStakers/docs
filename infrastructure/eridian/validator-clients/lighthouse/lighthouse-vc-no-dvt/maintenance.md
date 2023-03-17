---
description: Notes on how to maintain and update my Lighthouse Validator Client.
---

# 🏗 Maintenance

<figure><img src="https://raw.githubusercontent.com/DVStakers/docs/main/.gitbook/assets/Eridian.png" alt=""><figcaption><p>Eridian</p></figcaption></figure>

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
