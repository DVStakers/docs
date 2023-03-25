---
description: Notes on how to split existing validator keys into charon keyshares.
---

# ✂ Split Existing Keys

{% hint style="info" %}
If you want to use the Obol DKG ceremony to create new validator keyshares directly on each machine, you can find those instructions [here](obol-dkg-ceremony.md).
{% endhint %}

Make sure you have your validator keys ready to be split. Instructions on how to generate validator keys can be found here:

{% content-ref url="../../validator-key-generation.md" %}
[validator-key-generation.md](../../validator-key-generation.md)
{% endcontent-ref %}

Create a new directory that will be used for all keys in this cluster. This is useful if in the future you want to add additional validators, as you will need to keep the original keys to run them all on the same charon cluster.

<details>

<summary>How to add more validators to an existing cluster</summary>

To add more keys, add the additional keys to the existing keys in the `split_keys` directory, repeating [Step 2](split-existing-keys.md#step-2-create-a-directory-containing-keys-to-split) and [Step 3](split-existing-keys.md#step-3-split-the-keys-into-keyshares).

Before.

```
├── split_keys
    ├── keystore-0.json        # Two existing validator keys
    ├── keystore-0.txt
    ├── keystore-1.json
    ├── keystore-1.txt
```

After.

```
├── split_keys
    ├── keystore-0.json        # Two existing validator keys
    ├── keystore-0.txt
    ├── keystore-1.json
    ├── keystore-1.txt
    ├── keystore-2.json        # Two new validator keys
    ├── keystore-2.txt
    ├── keystore-3.json
    ├── keystore-3.txt
```

</details>

These steps should only be carried out by one participant, who will then share the keyshares with the others.

* [Step 1: Clone the charon repo](split-existing-keys.md#step-1-clone-the-charon-repo)
* [Step 2: Create a directory containing keys to split](split-existing-keys.md#step-2-create-a-directory-containing-keys-to-split)
* [Step 3: Split the keys into keyshares](split-existing-keys.md#step-3-split-the-keys-into-keyshares)

### Step 1: Clone the charon repo

Clone the [charon](https://github.com/ObolNetwork/charon) repo into a new directory called `charon-key-splitter`.

```bash
cd ~
mkdir charon-key-splitter
cd charon-key-splitter
git clone https://github.com/ObolNetwork/charon.git .
mkdir split_keys
```

### Step 2: Create a directory containing keys to split

Copy the existing validator `keystore.json` files into this new folder. Alongside them, with a matching filename but ending with `.txt` should be the password to the keystore. E.g., `keystore-0.json` `keystore-0.txt`. These matching `.txt` files can be generated with a bash script.

At the start of this process, you should have a tree that looks something like this.

```
├── split_keys
    ├── keystore-0.json
    ├── keystore-1.json
    ...
    ├── keystore-*.json
```

Create the bash script file.

```bash
cd split_keys
vim generate_txt_files.sh
```

```bash
#!/bin/bash

# Get the input string (password) from the user
echo "Enter the string (password) to be used in all the .txt files:"
read input_string

# Iterate through all .json files in the directory
for file in *.json; do
    # Check if the file is a regular file (not a directory)
    if [[ -f "$file" ]]; then
        # Get the file name without the extension
        filename="${file%.*}"
        # Create a .txt file with the same name and add the input string to it
        echo "$input_string" > "${filename}.txt"
    fi
done
```

Save this script as and make it executable.

```bash
chmod +x generate_txt_files.sh
```

Run the script.

```bash
./generate_txt_files.sh
```

At the end of this process, you should have a tree like this.

```
├── split_keys
    ├── keystore-0.json
    ├── keystore-0.txt
    ├── keystore-1.json
    ├── keystore-1.txt
    ...
    ├── keystore-*.json
    ├── keystore-*.txt
```

### Step 3: Split the keys into keyshares

Split the validator keys into keyshares.

```bash
CHARON_VERSION=                # E.g. v0.14.0
CLUSTER_NAME=                  # E.g. DVStakers
WITHDRAWAL_ADDRESS=            # Set withdrawal address
FEE_RECIPIENT_ADDRESS=         # Set fee recipient address
THRESHOLD=                     # E.g. 6
NODES=                         # E.g. 9                

docker run --rm -v $(pwd):/opt/charon obolnetwork/charon:${CHARON_VERSION} create cluster --name="${CLUSTER_NAME}" --withdrawal-addresses="${WITHDRAWAL_ADDRESS}" --fee-recipient-addresses="${FEE_RECIPIENT_ADDRESS}" --split-existing-keys --split-keys-dir=/opt/charon/split_keys --threshold ${THRESHOLD} --nodes ${NODES}
```

In the `.charon` directory there will now be a `cluster` directory containing 6 sub-directories, each named `node0`, `node1`, etc.&#x20;

```
.charon/cluster/
├─ node[0-*]/                   Directory for each node
   ├─ charon-enr-private-key    Charon networking private key for node authentication
   ├─ cluster-lock.json         Cluster lock defines the cluster lock file which is signed by all nodes
   ├─ deposit-data.json         Deposit data file is used to activate a Distributed Validator on DV Launchpad
   ├─ validator_keys            Validator keystores and password
      ├─ keystore-*.json        Validator private share key for duty signing
      ├─ keystore-*.txt         Keystore password files for keystore-*.json
```

These split keys can now be shared with the other participants and used to [start a charon cluster](../).
