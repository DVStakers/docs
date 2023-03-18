---
description: My Grafana Client installation guide.
---

# 💾 Installation

<figure><img src="https://raw.githubusercontent.com/DVStakers/docs/main/.gitbook/assets/Eridian.png" alt=""><figcaption><p>Eridian</p></figcaption></figure>



### Firewall Configuration

Confirm UFW is disabled before blocking everything or you could be disconnected.

```bash
sudo ufw disable
sudo ufw status
```

Configure the firewall.

```bash
GRAFANA_PORT=

sudo ufw allow ${GRAFANA_PORT} comment 'Allow Grafana in'
```

