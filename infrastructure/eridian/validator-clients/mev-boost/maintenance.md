# 🏗 Maintenance

<figure><img src="https://raw.githubusercontent.com/DVStakers/docs/main/.gitbook/assets/Eridian.png" alt=""><figcaption><p>Eridian</p></figcaption></figure>

### MEV Boost - Update Client

{% tabs %}
{% tab title="Command Aliases" %}
```bash
mev-update
```
{% endtab %}

{% tab title="Full Commands" %}
```bash
# <BUILD LATEST VERSION>
cd ~/mev-boost
git checkout main
git pull
make build-portable

# <STOP SERVICE AND REPLACE BINARY>
sudo systemctl stop mevboost.service

sudo rm /usr/local/bin/mev-boost
sudo cp ~/mev-boost/mev-boost /usr/local/bin

# <START SERVICE>
sudo systemctl start mevboost.service
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
