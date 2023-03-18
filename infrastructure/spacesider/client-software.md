# ⛓ Client software

<figure><img src="../../.gitbook/assets/Spacesider.png" alt=""><figcaption><p>Spacesider</p></figcaption></figure>

Four clients are running on the machine, all of which are [minority clients](https://clientdiversity.org/).



The first pair is [Lighthouse ](https://github.com/sigp/lighthouse/releases)and [Nethermind](https://github.com/NethermindEth/nethermind/releases).

The second pair is [Teku ](https://github.com/ConsenSys/teku/releases)and [Besu](https://github.com/hyperledger/besu/releases/tag/23.1.1).

Only one validator client software is running, that being Lighthouse.



Teku-Besu is the primary pair as I have found it to be much more stable with less missed attestations (Specifically Besu over Nethermind), meaning Lighthouse-Nethermind is my secondary pair.

These are configured as redundant beacon nodes in the validator client itself, [and it is perfectly safe to do so.](https://lighthouse-book.sigmaprime.io/redundancy.html) This also provides the added benefit of never being offline even when upgrading client softwares, as if I take down Teku the validator will automatically start using Lighthouse until Teku is available for use again.
