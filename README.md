# KVM on the Raspberry Pi 3

How to run virtual machines with KVM (Kernel-based Virtual Machine) on the Raspberry Pi 3.

## Initial Setup

Install OpenSuse and KVM on Raspberry Pi 3 with this tutorial:

[https://medium.com/@valdiz777/setting-up-kvm-on-raspberry-pi-3-using-a-64bit-opensuse-pi3-leap-42-2-xfce-image-22faddf02f48](https://medium.com/@valdiz777/setting-up-kvm-on-raspberry-pi-3-using-a-64bit-opensuse-pi3-leap-42-2-xfce-image-22faddf02f48)

## Working Distributions

I only managed to get arm64-distributions running on the Raspberry Pi 3 SoC (BCM2837).

- debian-9.4.0-arm64-netinst.iso
- ubuntu-16.04.4-server-arm64.iso

## Setup scripts

### Ubuntu 16.04.4

```
sudo virt-install \
    --name ubuntu16 \
    --arch aarch64 \
    --cdrom /root/ubuntu-16.04.4-server-arm64.iso \
    --boot cdrom \
    --machine virt \
    --ram 512 \
    --vcpus 2 \
    --debug \
    --quiet \
    --video virtio \
    --graphics vnc,listen=0.0.0.0,keymap=de --noautoconsole \
    --os-type linux \
    --disk size=8 \
    --controller type=usb,model=ich9-ehci1 \
    --input keyboard,bus=usb \
    --network network=default,model=virtio,rom_bar=off
```

### Debian 9.4.0

```
sudo virt-install \
    --name debian1 \
    --arch aarch64 \
    --accelerate \
    --hvm \
    --cdrom /root/debian-9.4.0-arm64-netinst.iso \
    --boot cdrom \
    --ram 256 \
    --os-type linux \
    --disk size=4,bus=virtio \
    --vnc \
    --video virtio \
    --network bridge=br0,model=virtio,rom_bar=off
```

## Running 2 VMs at once on Raspberry Pi 3

I was able to run 2 Debian VMs concurrently by giving each VM no more than 180 Megabytes of RAM thus limiting the need for opensuse to swap data to the SD-Card. 

## Problems and Solutions

### EFI-ROM  issues

To get the ethernet working on the raspberry pi 3 you need to add this line to your ethernet or bridge interface: `<rom bar='off'/>`
 
```
<interface type='bridge'>
  <mac address='52:54:00:73:e2:e0'/>
  <source bridge='br0'/>
  <model type='virtio'/>
  <rom bar='off'/>
  <address type='pci' domain='0x0000' bus='0x01' slot='0x00' function='0x0'/>
</interface>
```

### Snapshot issues

Snapshots can be created like this, but they can't be reverted to.

```
$ virsh snapshot-create-as ubuntu17-netboot-1 snapshot1 "My Snapshot" --disk-only --atomic
```

## Helpful links

[https://linux.die.net/man/1/virt-install]() (english)

[https://en.opensuse.org/HCL:Raspberry_Pi3]() (english)

[https://www.thomas-krenn.com/de/wiki/Virsh_-_Kommandozeilenwerkzeug_zur_Verwaltung_virtueller_Maschinen]() (german)

[https://wiki.libvirt.org/page/VM_lifecycle]() (english)