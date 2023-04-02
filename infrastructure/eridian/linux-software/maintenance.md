---
description: Notes on how to maintain and update my Linux system.
---

# 🏗 Maintenance

<figure><img src="https://raw.githubusercontent.com/DVStakers/docs/main/.gitbook/assets/Eridian.png" alt=""><figcaption><p>Eridian</p></figcaption></figure>

### General System Updates

Update system packages and [firmware](https://github.com/fwupd/fwupd).

{% tabs %}
{% tab title="Command Aliases" %}
```bash
update-system    # Update all system packages
update-firmware  # Update firmware
```
{% endtab %}

{% tab title="Full Commands" %}
```bash
sudo apt-get update -y && sudo apt-get upgrade -y && sudo apt-get dist-upgrade -y    # Update all system packages
fwupdmgr refresh && fwupdmgr get-updates && fwupdmgr update                          # Update firmware
```
{% endtab %}
{% endtabs %}
