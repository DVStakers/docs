---
description: Notes on how to install Docker.
---

# 🐳 Install Docker

<div>

<figure><img src="https://raw.githubusercontent.com/DVStakers/docs/main/.gitbook/assets/Eridian.png" alt=""><figcaption><p>Eridian</p></figcaption></figure>

 

<figure><img src="../../.gitbook/assets/Spacesider.png" alt=""><figcaption><p>Spacesider</p></figcaption></figure>

</div>

Install Docker.

```bash
# Update the package index
sudo apt-get update

# Install packages to allow apt to use a repository over HTTPS
sudo apt-get install -y \
    apt-transport-https \
    ca-certificates \
    gnupg \
    lsb-release
    
# Add Docker's official GPG key
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

# Set up the stable repository
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# Update the package index again
sudo apt-get update

# Install the latest version of Docker Engine and containerd
sudo apt-get install docker-ce docker-ce-cli containerd.io

# To use Docker without sudo, add your current user to the docker group
sudo usermod -aG docker $USER
```

Log out and log back in or restart your machine for the group membership to take effect. You should now be able to run Docker commands without `sudo`.
