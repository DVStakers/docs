# 🏗 Maintenance

<figure><img src="https://raw.githubusercontent.com/DVStakers/docs/main/.gitbook/assets/Eridian.png" alt=""><figcaption><p>Eridian</p></figcaption></figure>

### Lighthouse - Update lighthousebeacon.service

{% tabs %}
{% tab title="Command Aliases" %}
```bash
beacon-stop
beacon-config

# MAKE ANY CHANGES TO THE CONFIG

daemon-reload
beacon-start
beacon-status
```
{% endtab %}

{% tab title="Full Commands" %}
```bash
sudo systemctl stop lighthousebeacon.service
sudo vim /etc/systemd/system/lighthousebeacon.service

# MAKE ANY CHANGES TO THE CONFIG

sudo systemctl daemon-reload
sudo systemctl start lighthousebeacon.service
sudo systemctl status lighthousebeacon.service
```
{% endtab %}
{% endtabs %}

d
