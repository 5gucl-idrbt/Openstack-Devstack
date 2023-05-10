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
vi local.conf   # details in local.conf ,change your Host_IP
```
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

# Instance creation

First you need to download an image and it should be .img file

For Ubuntu Cloud Image download
https://cloud-images.ubuntu.com/focal/current/

If it is ubuntu image we need to customise the image: 
```
rm -f vm_0001-focal-server-cloudimg-amd64.qcow2
qemu-img create -f qcow2 -F qcow2 -b focal-server-cloudimg-amd64.img  vm_0001-focal-server-cloudimg-amd64.qcow2 20G
qemu-img info vm_0001-focal-server-cloudimg-amd64.qcow2
VM_NAME="ubuntu-20-cloud-image"
USERNAME="idrbt"
PASSWORD="idrbt"
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
If you are facing any issue for customization then run this command

>sudo update-grub

your image will be in .qcow2 format with new name

we have to convert it to the .img file 
example:
```
qemu-img convert -f qcow2 -O raw imgname.qcow2 imgname.img
```
select the newly converted file in the openstack image as QEMU format

## Create Image
```
compute > Images > create Image 
```
## Create a Router

```
Network > Routers > create Router
```
Give Router Name
select  external network 
create


## Create a Network

```
Network > networks > Create network /Edit Network 
```
if no need of network creation just edit network which you need.

For Floating IP Creation,

>Select a network and click on it then Create a subnet
>
>Give subnet name,Network address, Gateway IP
>
>Click on subnet details Give allocation Pools and DNS Nameserver 
>
>Save

Now create an instance

```
compute > Instances > launch Instance
```
Assign Floating IP

Click on down bar in Action section of an Instance 
>Select Associate Floating IP

add a route in Physical Router
>destination ip : <Instance_FloatingIP>
>
>Gateway: <BareMetal_IP> 
>
here baremetal is openstack installed PC

Now, You can able to access Instance via SSH  

If you able to communicate 8.8.8.8 but not google.com then,
go to /etc/resolv.conf
and add the line
```
nameserver 8.8.8.8
```
at the end and save the file


Note: Inorder to access the openstack dahboard from any pc we have to disable the ufw firewall or add exceptions for port 80 and 443.







