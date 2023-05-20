---
description: Notes on my hardware.
---

# 💻 Hardware

<div align="left">

<figure><img src="https://raw.githubusercontent.com/DVStakers/docs/main/.gitbook/assets/Eridian.png" alt=""><figcaption><p>Eridian</p></figcaption></figure>

</div>

To avoid duplication these details can be found on the EthStaker Knowledge Base.

{% content-ref url="http://localhost:5000/s/KnJhWg57YoZq2MPfatKE/hardware/hardware-examples/nuc-eridian" %}
[NUC - Eridian](http://localhost:5000/s/KnJhWg57YoZq2MPfatKE/hardware/hardware-examples/nuc-eridian)
{% endcontent-ref %}

<figure><img src="../.gitbook/assets/image (12) (1).png" alt="Eridian NUC"><figcaption><p>Eridian Hardware - NUC</p></figcaption></figure>

<div align="left">

<figure><img src="../.gitbook/assets/Spacesider.png" alt=""><figcaption><p>Spacesider</p></figcaption></figure>

</div>

Spacesider is using a custom-built machine with an AMD Ryzen 7 5700G CPU and 64GB of DDR4 RAM which is incredibly powerful for staking purposes and gets the job done without breaking a sweat.

The machine is currently hosting 2 client pairs (Lighthouse-Nethermind and Teku-Besu) and sits at roughly 16% CPU usage.

It has 3 x 2TB SSD's with two being in active use (Each hosting a client pair) and the 3rd is plugged in as a spare ready to go should one of the active ones fail.

Full node backups are performed manually over the network to a NAS, usually once a month.

In the case that an SSD dies, the backup can be restored to the spare SSD, and the machine will pick up quickly resync and pick up where it left off.

Because there are two client pairs on the machine, the local setup will still work while the other client pair is being restored and a spare disk is sourced. There are future plans to move one of the client pairs to a separate machine for increased local redundancy.
