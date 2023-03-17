---
description: My Lighthouse client installation guide.
---

# 💾 Installation

<figure><img src="https://raw.githubusercontent.com/DVStakers/docs/main/.gitbook/assets/Eridian.png" alt=""><figcaption><p>Eridian</p></figcaption></figure>

* [Create Aliases](installation.md#create-aliases)
* [Rust - Install](installation.md#rust-install)
* [Lighthouse - Install](installation.md#lighthouse-install)
* [Lighthouse - Update Scripts](installation.md#lighthouse-update-scripts)

### Create Aliases

```bash
echo "alias lighthouse-version-current='cd /usr/local/bin/; ./lighthouse --version; cd;'" >> ~/.bashrc
echo "alias lighthouse-build='cd; ./lighthouse-build.sh'" >> ~/.bashrc
echo "alias lighthouse-version-new='cd ~/.cargo/bin/; ./lighthouse --version; cd;'" >> ~/.bashrc
echo "alias lighthouse-deploy='cd; ./lighthouse-deploy-check.sh'" >> ~/.bashrc

source ~/.bashrc
```

### Rust - Install

```bash
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh

<1>
<ENTER>

source $HOME/.cargo/env
```

Make sure `protoc` is installed otherwise the build will fail.

```bash
sudo apt-get install -y protobuf-compiler
protoc --version
```

### Lighthouse - Install

Build the latest version of `Lighthouse`.

```bash
cd ~
git clone -b stable https://github.com/sigp/lighthouse.git
cd ~/lighthouse
make
```

Move the compiled `Lighthouse` build to a new directory.

```bash
sudo cp ~/.cargo/bin/lighthouse /usr/local/bin
```

Check version.

```bash
cd /usr/local/bin/
./lighthouse --version

/usr/local/bin/lighthouse --version
```

Create `Lighthouse` directory.

```bash
sudo mkdir -p /var/lib/lighthouse
```

### Lighthouse - Update Scripts

Create `Lighthouse` build script.

```bash
vim ~/lighthouse-build.sh
```

{% code title="~/lighthouse-build.sh" %}
```bash
#!/bin/bash
cd ~/lighthouse
git checkout stable

echo "****************************************"
echo "Pulling latest changes for Lighthouse..."
echo "****************************************"
git pull

echo
echo "********************"
echo "Making Lighthouse..."
echo "********************"
make
```
{% endcode %}

Create `Lighthouse` deploy script.

```bash
vim ~/lighthouse-deploy.sh
```

{% code title="~/lighthouse-deploy.sh" %}
```bash
#!/bin/bash
echo "**********************"
echo "Stopping Lighthouse..."
sudo systemctl stop lighthousebeacon.service
sudo systemctl stop lighthousevalidator.service


echo "Replacing previous version..."
sudo rm /usr/local/bin/lighthouse
sudo cp ~/.cargo/bin/lighthouse /usr/local/bin

echo "Restarting Lighthouse..."
sudo systemctl start lighthousebeacon.service
sudo systemctl start lighthousevalidator.service
```
{% endcode %}

Create `Lighthouse` deploy script checker script.

{% code title="~/lighthouse-deploy-check.sh" %}
```bash
#!/bin/bash
while true; do
    read -p "Are you sure you want to deploy Lighthouse? (Y/N) " yn
    case $yn in
        [Yy]* ) cd; ./lighthouse-deploy.sh; break;;
        [Nn]* ) exit;;
        * ) echo "Please answer Y or N.";;
    esac
done
```
{% endcode %}

Make all scripts executable.

```bash
chmod u+x ~/lighthouse-build.sh
chmod u+x ~/lighthouse-deploy.sh
chmod u+x ~/lighthouse-deploy-check.sh
```
