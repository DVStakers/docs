---
description: Notes on how to maintain and update a Lighthouse client.
---

# 🏗 Maintenance

### Lighthouse - Update Client

{% tabs %}
{% tab title="Command Aliases" %}
```bash
lighthouse-version-current    # Check current version number
lighthouse-build              # Download and build latest version
lighthouse-version-new        # Check the version of the newly built client
lighthouse-deploy             # Deploy the new client
```
{% endtab %}

{% tab title="Full Commands" %}
```bash
# <CHECK CURRENT VERSION OF LIGHTHOUSE>
# <lighthouse-version-current>
cd /usr/local/bin/
./lighthouse --version

# <BUILD LATEST VERSION>
# <lighthouse-build>
cd ~/lighthouse
git checkout stable
git pull
make

# <CHECK BUILD VERSION>
# <lighthouse-version-new>
cd ~/.cargo/bin/
./lighthouse --version

# <STOP SERVICES AND REPLACE BINARY>
# <lighthouse-deploy>
sudo systemctl stop lighthousebeacon.service
sudo systemctl stop lighthousevalidator.service

sudo rm /usr/local/bin/lighthouse
sudo cp ~/.cargo/bin/lighthouse /usr/local/bin

sudo systemctl start lighthousebeacon.service
sudo systemctl start lighthousevalidator.service
```
{% endtab %}
{% endtabs %}
