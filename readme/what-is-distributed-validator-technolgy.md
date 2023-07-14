# What is Distributed Validator Technolgy

### What is DVT?

Distributed Validator Technology. In a traditional solo staking setup, if a single validator node goes offline due to technical issues or maintenance, it immediately stops performing its validator duties (attestations, block proposals, etc.). This can lead to missed rewards and penalties for the staker.

However, with Distributed Validator Technology (DVT), a group of stakers work together as a single unit. If one of the stakers within the group experiences node downtime, the remaining stakers in the group continue to perform the validator duties collectively. This ensures that the staking process remains uninterrupted, maintaining network stability and allowing the group to continue earning rewards while minimizing the risks associated with individual node failures.

Distributed validator technology (DVT) allows a solo staker with one machine to spread one validator out to multiple machines worldwide.

Groups can collectively pool financial and computational resources to run validators.

This distributed validation design has many advantages over solo stakers and professional node operators.

* Security: By distributing the validation process among multiple nodes, it is much more difficult for a single entity to gain control of the network and manipulate it maliciously.
* Decentralization: Distributing the validation process helps avoid central points of control, which is one of the leading value propositions of blockchain technology.
* Fault Tolerance: A distributed network of validators can provide better failure resilience. If one node goes offline, the validator keeps working.
* Regulatory resistance: If one geographical area stops operating, the validator will keep operating
* Transparency and Trust: No single entity has complete control over validating transactions.
* Client Diversity: Each machine can run [different clients](https://ethereum.org/en/developers/docs/nodes-and-clients/)

A less discussed aspect is the 32ETH Bond can be dramatically changed thanks to DVT.

* Solo stakers are single entities validating the network; in almost all cases, they must deposit 32ETH.
* Staking Pools allow users to deposit any amount of ETH and use node operators to validate the network. Most node operators do not have to put in their collateral.&#x20;

Staking pools do not use solo stakers because of security issues (one holder has the key) and potential performance issues if the node goes offline.

Some use cases:

* DVT clusters could form and collectively deposit the 32ETH.
* Four DVT users could form Rocketpool mini node by depositing 2ETH each.
* DVT cluster could join larger staking pools by pairing with existing node operators to increase the decentralized nature of the pool.

\
How do DVT withdrawals work?

How much do you need to trust others in your cluster?
