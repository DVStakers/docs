# 💾 Installation

<figure><img src="https://raw.githubusercontent.com/DVStakers/docs/main/.gitbook/assets/Eridian.png" alt=""><figcaption><p>Eridian</p></figcaption></figure>

### Create Aliases

```
echo "alias geth-log='sudo journalctl -f -u geth.service -o cat | ccze -A'" >> ~/.bashrc
echo "alias geth-start='sudo systemctl start geth.service'" >> ~/.bashrc
echo "alias geth-stop='sudo systemctl stop geth.service'" >> ~/.bashrc
echo "alias geth-restart='sudo systemctl restart geth.service'" >> ~/.bashrc
echo "alias geth-status='sudo systemctl status geth.service'" >> ~/.bashrc
echo "alias geth-config='sudo vim /etc/systemd/system/geth.service'" >> ~/.bashrc

source ~/.bashrc
```

### Go - Install

Find the latest version of `Go` here: [https://go.dev/doc/install](https://go.dev/doc/install).

Replace `<LATEST>_VERSION_NUMBER>`.

```
cd ~
wget https://go.dev/dl/go<LATEST>_VERSION_NUMBER>.linux-amd64.tar.gz
```

Remove any existing `Go` version and untar the newly downloaded latest version.

Replace `<LATEST>_VERSION_NUMBER>`.

```
sudo rm -rf /usr/local/go && sudo tar -C /usr/local -xzf go<LATEST>_VERSION_NUMBER>.linux-amd64.tar.gz

export PATH=$PATH:/usr/local/go/bin
echo 'PATH="$PATH:/usr/local/go/bin"' >> ~/.profile
```

### Geth - Install







