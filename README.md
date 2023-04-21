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
Access the openstack dashboard from web http://<your_IP_Address>/dashboard

## Instance creation

First you need to download an image and it should be .img file
In order to customise the image: 
```
rm -f vm_0001-focal-server-cloudimg-amd64.qcow2
qemu-img create -f qcow2 -F qcow2 -b focal-server-cloudimg-amd64.img  vm_0001-focal-server-cloudimg-amd64.qcow2 20G
qemu-img info vm_0001-focal-server-cloudimg-amd64.qcow2
VM_NAME="ubuntu-20-cloud-image"
USERNAME="programster"
PASSWORD="thisok"
echo "#cloud-config
system_info:
  default_user:
    name: $USERNAME
    home: /home/$USERNAME

password: $PASSWORD
chpasswd: { expire: False }
hostname: $VM_NAME

# configure sshd to allow users logging in using password 
# rather than just keys
ssh_pwauth: True
" | sudo tee user-data
cloud-localds ./cidata.iso user-data
qemu-system-x86_64 -m 2048 -smp 4 -hda ./vm_0001-focal-server-cloudimg-amd64.qcow2 \
      -cdrom ./cidata.iso -device e1000,netdev=net0 -netdev user,id=net0,hostfwd=tcp::5555-:22 -nographic
```
your image will be in .qcow2 format with new name

we have to convert it to the .img file 
example:
```
qemu-img convert -f qcow2 -O raw imgname.qcow2 imgname.img
```
select the newly converted file in the openstack image as QEMU format
```
compute > Images > create Image 
```
now create an instace

```
compute > Instances > launch Instance
```
make sure to create a router which connects to public network as well as the network in which the instance is in.
```
Network > Routers > create Router
```

Now open the instance and go the netplan and adjust the netplan setting in the instance.
```
cd etc/netplan
sudo nano 50-cloud-init.yaml
```
```
sudo netplan apply
```
now go to /etc/resolv.conf
and add the line nameserver 8.8.8.8 at the end and save the file


Note: Inorder to access the openstack dahboard from any pc we have to disable the ufw firewall or add exceptions for port 80 and 443.




