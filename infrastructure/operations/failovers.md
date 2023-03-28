---
description: Our infrastructure is resilient to failures on multiple fronts.
---

# 📡 Failovers

<div>

<figure><img src="https://raw.githubusercontent.com/DVStakers/docs/main/.gitbook/assets/Eridian.png" alt=""><figcaption><p>Eridian</p></figcaption></figure>

 

<figure><img src="../../.gitbook/assets/Spacesider.png" alt=""><figcaption><p>Spacesider</p></figcaption></figure>

</div>

### Local failovers

We are running two Ethereum stacks each (a stack being a Beacon Node that is paired with an Execution Layer client), one being the primary and the other being the secondary. The validator client is configured to use both, so should the primary stack go offline then it will automatically use the secondary stack until the primary becomes available again.

### Global failovers

We have configured our validator clients to also use the other DVStakers beacon nodes too, so in the case where a DVStaker has both their local Ethereum stacks become unavailable, then the validator client will still continue to perform its duties by failing over to the other available stacks.

The global failover has some constraints such as increased latency due to geographic distances between the DVStakers.

In rare situations where an attestation is not submitted in the four-second window that it has, this could lead to the attestation being missed or having a [non-optimal inclusion distance](http://localhost:5000/s/KnJhWg57YoZq2MPfatKE/staking-glossary#inclusion-distance), but it will prevent the validator(s) from being offline.

In our experiences, we have not seen the latency between Validator Clients and Beacon Nodes cause any issues, but it is worth noting that there could be some.

### Cloud auxiliary client

Through the use of a Cloud Auxiliary Client, it is possible for a DVStaker to go completely offline but still have Validators remaining online and performing their duties. For more information, [please refer to this page.](../cloud-auxiliary-client.md)

For a very brief recap on how this was achieved, one-third of the operation is hosted in the Cloud but _only_ the Validator and DVT client. The Validators are pointed to the Beacon Nodes that the DVStakers are running locally.&#x20;

This means that at any point one entire cluster can go offline and the other 2/3's will still be running and performing their duties.

### Limitations

There are a few limitations that exist on a consensus level layer, where the Validator Clients will continue to use the Beacon Node it is pointed at when the execution client that it is paired with goes offline or is otherwise unavailable.

This is because the consensus engine is _technically_ still online and working, just not for validation purposes as there is nowhere to store or reference the transactions while the execution engine is offline. So in this state, all the attestations will be missed.

However, this issue does not exist across all consensus clients. [Teku has addressed this in their 23.3.0 release](https://github.com/ConsenSys/teku/releases/tag/23.3.0) while the [Lighthouse team are aware and are working on a fix](https://github.com/sigp/lighthouse/issues/3613).

That being said, this issue only occurs in a fairly specific situation and should not cause any major problems for us.
