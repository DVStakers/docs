# 💾 Installation

<figure><img src="https://raw.githubusercontent.com/DVStakers/docs/main/.gitbook/assets/Eridian.png" alt=""><figcaption><p>Eridian</p></figcaption></figure>



### Create Aliases

```
echo "alias validator-log='sudo journalctl -f -u lighthousevalidator.service -o cat | ccze -A'" >> ~/.bashrc
echo "alias validator-start='sudo systemctl start lighthousevalidator.service'" >> ~/.bashrc
echo "alias validator-stop='sudo systemctl stop lighthousevalidator.service'" >> ~/.bashrc
echo "alias validator-restart='sudo systemctl restart lighthousevalidator.service'" >> ~/.bashrc
echo "alias validator-status='sudo systemctl status lighthousevalidator.service'" >> ~/.bashrc
echo "alias validator-config='sudo vim /etc/systemd/system/lighthousevalidator.service'" >> ~/.bashrc

source ~/.bashrc
```



### Install

###

