# 🏗 Maintenance

<figure><img src="https://raw.githubusercontent.com/DVStakers/docs/main/.gitbook/assets/Eridian.png" alt=""><figcaption><p>Eridian</p></figcaption></figure>

* Go - Update

### Go - Update

Find the latest version of `Go` here: [https://go.dev/doc/install](https://go.dev/doc/install)

```bash
GO_LATEST_VERSION=    # Add the latest Go version here

cd ~/
wget https://go.dev/dl/go${GO_LATEST_VERSION}.linux-amd64.tar.gz
sudo rm -rf /usr/local/go && sudo tar -C /usr/local -xzf go${GO_LATEST_VERSION}.linux-amd64.tar.gz
export PATH=$PATH:/usr/local/go/bin
echo 'PATH="$PATH:/usr/local/go/bin"' >> ~/.profile
```

### Geth - Update Client

{% tabs %}
{% tab title="Command Aliases" %}
```bash
geth-update
```
{% endtab %}

{% tab title="All Commands" %}
```bash
# <BUILD LATEST VERSION>
cd ~/go-ethereum
git checkout master
git pull
make geth

# <STOP SERVICE AND REPLACE BINARY>
sudo systemctl stop geth.service

sudo rm /usr/local/bin/geth
sudo cp ~/go-ethereum/build/bin/geth /usr/local/bin

sudo systemctl start geth.service
sudo systemctl status geth.service
```
{% endtab %}
{% endtabs %}

### Geth - Update geth.service





