---
description: Notes on how to generate validator keys using the Ethereum CLI tool.
---

# 🔑 Validator Key Generation

<div>

<figure><img src="https://raw.githubusercontent.com/DVStakers/docs/main/.gitbook/assets/Eridian.png" alt=""><figcaption><p>Eridian</p></figcaption></figure>

 

<figure><img src="../.gitbook/assets/Spacesider.png" alt=""><figcaption><p>Spacesider</p></figcaption></figure>

</div>

{% hint style="info" %}
These are notes on how to generate validator keys using the Ethereum staking deposit CLI tool. If you plan to generate your validator keys another way (e.g. using a DKG ceremony) then you can skip this section.
{% endhint %}

### Staking deposit CLI

Generating new validator keys can be achieved using the Ethereum staking deposit CLI tool. This tool can be found on the [Ethereum GitHub page](https://github.com/ethereum/staking-deposit-cli).&#x20;

Download and extract the latest version, appropriate for your operating system of choice.

The instructions for key generation will vary slightly depending on what OS you are using, but the configurable flags are the same.

* Linux and macOS users, [please start here](https://github.com/ethereum/staking-deposit-cli#tutorial-for-users).
* Docker users, [please start here](https://github.com/ethereum/staking-deposit-cli#option-4-use-docker-image).
* Windows users, [please start here](https://github.com/ethereum/staking-deposit-cli#tutorial-for-users).

### Generating keys

While the above links provide full instructions, if you are after a quick command you can use the following (edit the options where appropriate):

#### New mnemonic (Never used staking deposit CLI tool before)

```bash
NUM_VALIDATORS=     # e.g. 5
NETWORK=            # e.g. mainnet or goerli

./deposit new-mnemonic --num_validators ${NUM_VALIDATORS} --chain ${NETWORK}
```

#### Existing mnemonic

This command assumes you already have 10 validators (Specified as the validator start index) and an existing mnemonic, and you want to generate 5 more validator keys with that same mnemonic.

```bash
VALIDATOR_STARTING_INDEX=        # e.g. 0 if you this is the first time creating keys with this mnemonic
NUM_VALIDATORS=                  # The number of new validator keys you want to generate
NETWORK=                         # e.g. mainnet or goerli
WITHDRAWAL_ADDRESS=              # The withdrawal address where your ETH will be returned to after exiting the validators in the future

./deposit existing-mnemonic --validator_start_index ${VALIDATOR_STARTING_INDEX} --num_validators ${NUM_VALIDATORS} --chain ${NETWORK} --execution_address ${WITHDRAWAL_ADDRESS}
```

Once the command has been executed, you may be asked a few follow-up questions such as which language you wish to use, and for a validator keystore password to be set.

You will see the CLI tool generate your keys in real-time, and when completed you will be informed of which folder the keys are in. Make sure you keep your mnemonic safe as if you lose your validator keys, you won't be able to recover your ETH!

<figure><img src="../.gitbook/assets/image (20).png" alt=""><figcaption><p>Success!</p></figcaption></figure>
