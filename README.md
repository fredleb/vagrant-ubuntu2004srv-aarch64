# vagrant-ubuntu2004srv-aarch64
A basic vagrant box development and deployment for Ubuntu server 20.04 for aarch64 (Arm 64 bit)

# Building the box

## Standard installation
I downloaded the Ubuntu server 20.04 LTS image for ARM here : https://ubuntu.com/download/server/arm

I built a virtual machine for libvirt (see **libvirt.xml** (the CDROM used for installation is missing)) and proceeded to installation, following the vagrant guide: https://www.vagrantup.com/docs/boxes/base.

## Keeping eth0

The recent ubuntu distributions rely on the newer predictable network interface naming scheme but vagrant insists on having its first interface named "eth0".

Therefore I needed to disable the new naming scheme.

This is a kernel option that is inserted in __/etc/default/grub__:
```
GRUB_CMDLINE_LINUX="net.ifnames=0"
```

Then rebuild the initramfs:
```
sudo grub-mkconfig -o /boot/grub/grub.cfg
```

## DHCP client-id

I was having terrible problems getting more than one machine up because my VMs would get an IP address from dnsmasq using different MACs as expected but same client-id (DUID by default) because it is derived from the same starting host name (all machines start as "vagrant"). I thought about changing the ClientIdentifier to "mac" in the systemd file for the eth0 interface but that did not work neither.

However I noticed this: https://salsa.debian.org/cloud-team/vagrant-boxes/blob/master/helpers/vagrant-setup#L103

The solution is to __empty__ "/etc/machine-id" (don't delete it !).

## Extracting the disk image

I have not exactly find it how or where but it seems libvirt uses vagrant's box image as the base and adds an extra overly one to just have the deltas. Unfortunately that makes extracting the resulting image more complex.

So I use virt-manager to first clone the vm and then extracted the resulting virtual disk image to a local sparse file:
```
qemu-img convert -O qcow2 /home/vm/pool/ubuntu20.04-aarch64.qcow2 ./box.img
```

## Putting it all in a box
### Nope
I then had the very bad idea of following the vagrant-libvirt "Create Box" instructions (https://github.com/vagrant-libvirt/vagrant-libvirt#create-box) which use their own "create_box.sh" tool.

The problem is that this tool creates a default Vagrantfile that is useless to this image...

It's better to follow these: https://unix.stackexchange.com/a/222907/471682 or basically the ones below.

### Yeap

I prepare my own Vagrantfile (see below) and metdata.json.

Then I pack them together in a box file:
```
tar cvzf ubuntu2004-server-aarch64.box ./metadata.json ./info.json ./Vagrantfile ./box.img
```
or faster:
```
tar cv -S --totals ./metadata.json ./info.json ./Vagrantfile ./box.img | pigz -c > ubuntu2004-server-aarch64.box
```

And added the box to my local vagrant:
```
vagrant box add ubuntu2004-server-aarch64.box --name ubuntu2004-server-aarch64
```

But I still need to manually delete the box image file from libvirt for some reason...

# Using the box

For some reason I don't understand yet the vagrant init command pulls a standard Vagrantfile instead of the one I put in the box...
```
vagrant init ubuntu20.04-aarch64
```
