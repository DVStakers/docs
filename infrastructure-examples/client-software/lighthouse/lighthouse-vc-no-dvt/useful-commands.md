---
description: Notes on how to use a Lighthouse Validator Client.
---

# ⌨ Useful commands

### lighthousevalidator.service

{% tabs %}
{% tab title="Command Aliases" %}
```bash
validator-log        # View the lighthousevalidator.service logs
validator-start      # Start the lighthousevalidator.service
validator-stop       # Stop the lighthousevalidator.service
validator-restart    # Restart the lighthousevalidator.service
validator-status     # View the status of the lighthousevalidator.service
validator-enable     # Enable the lighthousevalidator.service
validator-disable    # Disable the lighthousevalidator.service

validator-config     # Open the /etc/systemd/system/lighthousevalidator.service in vim
daemon-reload        # Reload any changes made to the lighthousevalidator.service
```
{% endtab %}

{% tab title="Full Commands" %}
```bash
sudo journalctl -f -u lighthousevalidator.service -o cat | ccze -A   # View the lighthousevalidator.service logs
sudo systemctl start lighthousevalidator.service                     # Start the lighthousevalidator.service
sudo systemctl stop lighthousevalidator.service                      # Stop the lighthousevalidator.service
sudo systemctl restart lighthousevalidator.service                   # Restart the lighthousevalidator.service
sudo systemctl status lighthousevalidator.service                    # View the status of the lighthousevalidator.service
sudo systemctl enable lighthousevalidator.service                    # Enable the lighthousevalidator.service
sudo systemctl disable lighthousevalidator.service                   # Disable the lighthousevalidator.service

sudo vim /etc/systemd/system/lighthousevalidator.service             # Open the /etc/systemd/system/lighthousevalidator.service in vim
sudo systemctl daemon-reload                                         # Reload any changes made to the lighthousevalidator.service
```
{% endtab %}
{% endtabs %}
