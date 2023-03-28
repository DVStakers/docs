# Failovers

Our infrastructure is resilient to failures on multiple fronts.

### Local failovers

* We are running two Ethereum stacks (A stack being a consensus node that is paired with an execution node), one being the primary and the other being the secondary. The validator client is configured to use both, so should the primary stack go offline then it will automatically use the secondary stack until the primary becomes available again.

### Global failovers

* We have configured our validator clients to also use the other DVStakers beacon nodes too, so in the case where a DVStaker has both their local Ethereum stacks become unavailable, then the validator client will still have a machine to failover to.&#x20;

The global failover has some constraints such as increased latency due to geographic distances between the DVStakers.

In rare situations where an attestation is not submitted in the four second window that it has, this could lead to the attestation being missed or having a non optimal inclusion distance, but it will prevent the validator(s) from being offline.

In our experiences we have not seen the latency between validator clients and beacon nodes cause any issues, but it is worth noting that there could be some.

### Cloud auxiliary client

Through the use of a cloud auxiliary client, it is possible for a DVStaker to go completely offline but still have the overall operation online and running. For more information, [please refer to this page.](../cloud-auxiliary-client.md)

For a very brief recap on how this was achieved, one third of the operation is hosted in the cloud but _only_ the validators and DVT client. The validators are pointed to the beacon nodes that the DVStakers are running locally.&#x20;

This means that at any point one entire cluster can go offline and the other 2/3's will still be running and attesting.

### Limitations

There are a few limitations that exist on a consensus level layer, where the validator clients will continue to use the beacon node it is pointed at when the execution client that it is paired with goes offline or is otherwise unavailable.

This is because the consensus engine is _technically_ still online and working, just not for validation purposes as there is nowhere to store or reference the transactions while the execution engine is offline. So in this state all the attestations will be missed.

However this issue does not exist across all consensus clients. [Teku has addressed this in their 23.3.0 release](https://github.com/ConsenSys/teku/releases/tag/23.3.0) while the [Lighthouse team are aware and are working on a fix](https://github.com/sigp/lighthouse/issues/3613).

That being said, this issue only occurs in a fairly specific situation and should not cause any major problems for us.
