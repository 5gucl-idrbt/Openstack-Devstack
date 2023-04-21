# Openstack-Devstack
## Installation

```
sudo useradd -s /bin/bash -d /opt/stack -m stack
sudo chmod +x /opt/stack
echo "stack ALL=(ALL) NOPASSWD: ALL" | sudo tee /etc/sudoers.d/stack
sudo -u stack -i
```
as user stack
```
sudo mkdir stack
sudo chmod 777 stack
cd stack
git clone https://opendev.org/openstack/devstack --branch stable/yoga
cd devstack/
```
vi local.conf; # details in local.conf ,change your Host_IP
```
sudo apt install net-tools
cd /tmp
wget https://packages.cloud.google.com/apt/doc/apt-key.gpg
ls
sudo apt-key add apt-key.gpg
cd -
```
Following is to start the stack – repeat only after clean+ unstack+ reboot
```
./stack.sh
./clean.sh
./unstack.sh
sudo reboot now

```
