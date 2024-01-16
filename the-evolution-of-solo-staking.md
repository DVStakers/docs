---
description: The DVStakers journey from solo home staking to DVT validators.
---

# 🚄 Our Journey

* [Step 1: Standard Solo Staking](the-evolution-of-solo-staking.md#step-1-standard-solo-staking)
* [Step 2: Redundant Local Hardware](the-evolution-of-solo-staking.md#step-2-redundant-local-hardware)
* [Step 3: Redundant Remote Beacon Nodes](the-evolution-of-solo-staking.md#step-3-redundant-remote-beacon-nodes-mainnet-here-today)
* [Step 4: DVT Validators](the-evolution-of-solo-staking.md#step-4-dvt-validators)

<figure><img src=".gitbook/assets/image (2) (1).png" alt="Home Stakers Meme" width="410"><figcaption></figcaption></figure>

### Step 1: Standard Solo Staking

Standard solo staking on Ethereum today. Isolated solo stakers running all their own equipment. This is the standard staking setup, on a protocol designed to not harshly punish downtime.&#x20;

<figure><img src=".gitbook/assets/image (3) (3).png" alt=""><figcaption></figcaption></figure>

### Step 2: Redundant Local Hardware

Still isolated systems, but the single points of failure have been reduced, though not eliminated... yet! This also improves client diversity as different EC/BN pairs can be used.

Note that there is still only a single validator client (VC) for each operator. There is no failover setup as failover systems are very likely to cause slashing!

<figure><img src=".gitbook/assets/image (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### Step 3: Redundant Remote Beacon Nodes

Connect to the Beacon Nodes of other solo staker to increase redundancy. Further increasing client diversity by running different EC/BN pairs. This doesn't increase the risk of slashing, it simply increases the number of nodes that broadcast the signed messages, but there is still only one signed message (slashing occurs when there are two or more conflicting signed messages).

<figure><img src=".gitbook/assets/image (7) (1).png" alt=""><figcaption></figcaption></figure>

### Step 4: DVT Validators

SSV operators allow for full redundancy and have been configured by the DVStakers operators, including running on Raspberry Pis!

<figure><img src=".gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

With Obol, by splitting the keys of any existing validators into 9 keyshares each, a 6/9 Obol DVT cluster can be created. This means, that if any one system goes offline (e.g. due to power cuts, natural disaster, or a Cloud provider deciding that supporting the future of [decentralized finance isn't for them...](https://www.coindesk.com/business/2022/08/26/ethereum-could-get-kicked-off-cloud-host-that-powers-10-of-crypto-network/)) the validators will continue to perform their duties. True DVT!

The configuration instructions for this 6/9 cluster [can be found here](dvt-configuration/obol-dvt/cluster-config-6-9.md).

<figure><img src=".gitbook/assets/image (12) (3).png" alt=""><figcaption></figcaption></figure>



Take a look at the next stage of our journey with:

{% content-ref url="broken-reference" %}
[Broken link](broken-reference)
{% endcontent-ref %}
