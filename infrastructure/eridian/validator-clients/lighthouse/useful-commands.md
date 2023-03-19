---
description: Notes on how to use my Lighthouse Beacon Node.
---

# ⌨ Useful commands

<figure><img src="https://raw.githubusercontent.com/DVStakers/docs/main/.gitbook/assets/Eridian.png" alt=""><figcaption><p>Eridian</p></figcaption></figure>

### lighthousebeacon.service

{% tabs %}
{% tab title="Command Aliases" %}
```bash
beacon-log        # View the lighthousebeacon.service logs
beacon-start      # Start the lighthousebeacon.service
beacon-stop       # Stop the lighthousebeacon.service
beacon-restart    # Restart the lighthousebeacon.service
beacon-status     # View the status of the lighthousebeacon.service
beacon-enable     # Enable the lighthousebeacon.service
beacon-disable    # Disable the lighthousebeacon.service

beacon-config     # Open the /etc/systemd/system/lighthousebeacon.service in vim
daemon-reload     # Reload any changes made to the lighthousebeacon.service
```
{% endtab %}

{% tab title="Full Commands" %}
```bash
sudo journalctl -f -u lighthousebeacon.service -o cat | ccze -A   # View the lighthousebeacon.service logs
sudo systemctl start lighthousebeacon.service                     # Start the lighthousebeacon.service
sudo systemctl stop lighthousebeacon.service                      # Stop the lighthousebeacon.service
sudo systemctl restart lighthousebeacon.service                   # Restart the lighthousebeacon.service
sudo systemctl status lighthousebeacon.service                    # View the status of the lighthousebeacon.service
sudo systemctl enable lighthousebeacon.service                    # Enable the lighthousebeacon.service
sudo systemctl disable lighthousebeacon.service                   # Disable the lighthousebeacon.service

sudo vim /etc/systemd/system/lighthousebeacon.service             # Open the /etc/systemd/system/lighthousebeacon.service in vim
sudo systemctl daemon-reload                                      # Reload any changes made to the lighthousebeacon.service
```
{% endtab %}
{% endtabs %}
