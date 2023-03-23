---
description: How to move from isolated solo staking setups to a redundant DVT system.
---

# 🖥 The Evolution of Solo Staking

<div>

<figure><img src="https://raw.githubusercontent.com/DVStakers/docs/main/.gitbook/assets/Eridian.png" alt=""><figcaption><p>Eridian</p></figcaption></figure>

 

<figure><img src=".gitbook/assets/Spacesider.png" alt=""><figcaption><p>Spacesider</p></figcaption></figure>

</div>

* [Step 1: Standard Solo Staking](the-evolution-of-solo-staking.md#step-1-standard-solo-staking)
* [Step 2: Redundant Local Hardware](the-evolution-of-solo-staking.md#step-2-redundant-local-hardware)
* [Step 3: Redundant Remote Beacon Nodes (Prod - Here today📍)](the-evolution-of-solo-staking.md#step-3-redundant-remote-beacon-nodes-prod-here-today)
* [Step 4: Obol DVT Validators (Dev - Here today📍)](the-evolution-of-solo-staking.md#step-4-obol-dvt-validators-dev-here-today)
* [Step 5: StakeWise V3 Vaults + Obol DVT Validators](the-evolution-of-solo-staking.md#step-5-stakewise-v3-vaults-+-obol-dvt-validators)
* [Step 6: DVT+++](the-evolution-of-solo-staking.md#step-6-dvt+++)

### Step 1: Standard Solo Staking

Standard solo staking on Ethereum today. Isolated solo stakers running all their own equipment.

<figure><img src=".gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

### Step 2: Redundant Local Hardware

Still isolated systems, but the single points of failure have been reduced, not eliminated... yet! This also improves client diversity as different EC/BN pairs can be used.

<figure><img src=".gitbook/assets/image (3) (1).png" alt=""><figcaption></figcaption></figure>

### Step 3: Redundant Remote Beacon Nodes (Prod - Here today📍)

Connect Beacon Nodes to another solo staker to increase redundancy. Further increase client diversity by running four different EC/BN pairs.

<figure><img src=".gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

### Step 4: Obol DVT Validators (Dev - Here today📍)

By splitting the keys of any existing validators into 9 keyshares each, a 6/9 Obol DVT cluster can be created. This means, that if any one system goes offline (e.g. due to power cuts, natural disaster or the Cloud provider deciding that supporting the future of [decentralized finance isn't for them...](https://www.coindesk.com/business/2022/08/26/ethereum-could-get-kicked-off-cloud-host-that-powers-10-of-crypto-network/)) the validators will continue to attest. True DVT!

The Cloud redundancy is useful in this setup because:

1. There are only two stakers so there needs to be a split of keyshares that doesn't allow for a slashing event. If the keys were split 3/6 with each staker holding half the keys, then if the connection between their Charon nodes dropped out, they would still both be able to perform their duties at the same time (a slashable offense!).
2. It should reduce latency. The idea is that it should be faster for the two home staker nodes to talk to the Cloud provider (if the Cloud node is physically located between the two of them) than it is to talk directly. This should mean better performance for the validators, better inclusion distances, and overall better effectiveness.
3. It shouldn't cost much. Since the bulk of the cost of a validator is running the EC and BN clients, the actual validator clients are very lightweight. This means the Cloud node can be running on minimal hardware, significantly reducing cost.
4. It doesn't remove any sovereignty or require any trust assumptions with the Cloud provider. If the Cloud provider goes out of business or decides to turn off the node, the two remaining nodes can function perfectly fine. Also, as the Cloud provider only has 3/9 keys, it is not a security risk, as 3 keys can't be used for anything if the signature threshold is 6/9.

<figure><img src=".gitbook/assets/image (8) (3).png" alt=""><figcaption></figcaption></figure>

### Step 5: StakeWise V3 Vaults + Obol DVT Validators

Adding StakeWise V3 Vaults as an additional Obol DVT Cluster.

<figure><img src=".gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

### Step 6: DVT+++

Coming soon...

