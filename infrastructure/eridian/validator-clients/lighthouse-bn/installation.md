# 💾 Installation

<figure><img src="https://raw.githubusercontent.com/DVStakers/docs/main/.gitbook/assets/Eridian.png" alt=""><figcaption><p>Eridian</p></figcaption></figure>

### Create Aliases

These aliases make interacting with `Lighthouse` on the command line easier.

```
echo "alias beacon-log='sudo journalctl -f -u lighthousebeacon.service -o cat | ccze -A'" >> ~/.bashrc
echo "alias beacon-start='sudo systemctl start lighthousebeacon.service'" >> ~/.bashrc
echo "alias beacon-stop='sudo systemctl stop lighthousebeacon.service'" >> ~/.bashrc
echo "alias beacon-restart='sudo systemctl restart lighthousebeacon.service'" >> ~/.bashrc
echo "alias beacon-status='sudo systemctl status lighthousebeacon.service'" >> ~/.bashrc
echo "alias beacon-config='sudo vim /etc/systemd/system/lighthousebeacon.service'" >> ~/.bashrc
echo "alias beacon-enable='sudo systemctl enable lighthousebeacon.service'" >> ~/.bashrc
echo "alias beacon-disable='sudo systemctl disable lighthousebeacon.service'" >> ~/.bashrc

echo "alias lighthouse-version-current='cd /usr/local/bin/; ./lighthouse --version; cd;'" >> ~/.bashrc
echo "alias lighthouse-version-new='cd ~/.cargo/bin/; ./lighthouse --version; cd;'" >> ~/.bashrc

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

### Lighthouse - Configure Service

Create `Lighthouse Beacon` user and set permissions

```bash
sudo useradd --no-create-home --shell /bin/false lighthousebeacon
sudo mkdir -p /var/lib/lighthouse/beacon
sudo chown -R lighthousebeacon:lighthousebeacon /var/lib/lighthouse/beacon
```

Configure `Lighthouse` service using the command line flags.

```bash
sudo vim /etc/systemd/system/lighthousebeacon.service
```

{% code title="lighthousebeacon.service" %}
```
TODO
- Create file then change variables with a script after.
```
{% endcode %}

Start the service and check it's working as expected.

{% tabs %}
{% tab title="Command Aliases" %}
```bash
daemon-reload     # Reload any changes made to the lighthousebeacon.service
beacon-enable     # Enable the lighthousebeacon service
beacon-start      # Start the lighthousebeacon service
beacon-status     # View the lighthousebeacon of the Geth service

beacon-log        # View the lighthousebeacon logs
```
{% endtab %}

{% tab title="Full Commands" %}
```bash
sudo systemctl daemon-reload                                     # Reload any changes made to the lighthousebeacon.service
sudo systemctl enable lighthousebeacon.service                   # Enable the lighthousebeacon service
sudo systemctl start lighthousebeacon.service                    # Start the lighthousebeacon service
sudo systemctl status lighthousebeacon.service                   # View the lighthousebeacon of the Geth service

sudo journalctl -f -u lighthousebeacon.service -o cat | ccze -A  # View the lighthousebeacon logs
```
{% endtab %}
{% endtabs %}

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
