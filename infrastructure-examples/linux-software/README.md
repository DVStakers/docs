---
description: Notes on how to install and maintain Linux software.
---

# 💿 Linux software

<figure><img src="https://raw.githubusercontent.com/DVStakers/docs/main/.gitbook/assets/Eridian.png" alt=""><figcaption><p>Eridian</p></figcaption></figure>

{% content-ref url="installation.md" %}
[installation.md](installation.md)
{% endcontent-ref %}

{% content-ref url="maintenance.md" %}
[maintenance.md](maintenance.md)
{% endcontent-ref %}



<figure><img src="../../.gitbook/assets/Spacesider.png" alt=""><figcaption><p>Spacesider</p></figcaption></figure>

[Proxmox ](https://www.proxmox.com/en/proxmox-ve)has been deployed as a hypervisor where multiple virtual machines are being managed, all of which are running [Ubuntu Server](https://ubuntu.com/download/server) where they serve a specific purpose.

One hosts the Lighthouse vaildator client, one hosts Lighthouse(Consensus)-Nethermind(Execution) and the other hosts Teku(Consensus)-Besu(Execution).

The 4th SSD is a spare which is plugged in ready to go at all times.

Below is an image of the full hardware/software setup.

<figure><img src="../../.gitbook/assets/image (2) (3).png" alt=""><figcaption></figcaption></figure>
