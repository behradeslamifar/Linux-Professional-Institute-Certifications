# Xen
## How Xen works
Xen works by introducing a very small, very compact and focused piece of software that runs directly on the hardware and provides services to the virtualized operating systems

Xen would have to manage CPU time, interrupts, memory, block devices, and network.

The hypervisor operates much like the very core of a traditional operating system

Hypercall: ake the place of a standard operating system’s system calls, with a similar interface

### dom0
1. dom0 functions as an area from which to administer Xen. From the dom0, the administrator can control the other domains running on the machine - create, destroy, save, restore, etc. Network and storage devices can also be manipulated—created, presented to the kernel, assigned to domUs, etc.  
2. dom0 has uniquely privileged access to hardware. The domain 0 kernel has the usual hardware drivers and uses them to export abstractions of hardware devices to the hypervisor and thence to virtual machines  

### Install
1. Install Xen.  
```
apt-get install xen-linux-system-3.16.0-4-amd64 xen-tools
```
2. Familiarize yourself with the Xen environment.  
3. Install a domU.  
4. Log in to your domU and configure it to make sure everything works.  
5. Reset.  


### Configuration files
xend-config.sxp deprecated, use xl, xm or libvirt. You can find manuals in [xenproject wiki](https://wiki.xenproject.org/wiki/Xen_Project_4.11_Man_Pages)

```
# tree /etc/xen
/etc/xen
├── scripts					(Scripts to handles task, like setting up virtual devices)
│   ├── block
│   ├── block-common.sh
│   ├── block-enbd
│   ├── block-iscsi
│   ├── xen-hotplug-common.sh
│   ├── xen-network-common.sh
│   └── ...
├── xenguest1.cfg
├── xenguest2.cfg
├── xl.conf					(domain configuration files)
├── xlexample.hvm
└── xlexample.pvlinux
```

```
# vi /etc/default/xen
GRUB_CMDLINE_XEN_DEFAULT="dom0_mem=1024M,max:1024M"  (nloopbacks, pci.hide, dom0_max_vcpus=1 dom0_vcpus_pin ...)
GRUB_CMDLINE_XEN=""
```

### xl/xm tools
```
# xl list
Name                                        ID   Mem VCPUs	State	Time(s)
Domain-0                                     0  1024     4     r-----      87.4
```

#### toolstack
Choose toolstack [reference](https://wiki.xenproject.org/wiki/Choice_of_Toolstacks#Choice_of_Toolstacks)  
| TOOLSTACK |  XL | XAPI | LIBVIRT | XEND |
| --------- | --- | ---- | ------- | ---- |
| CLI tool  | xl  |  xe  |  virsh  |  xm  |

1. **xl** is very lightweight and is a great choice for develpers or users who are most comfortable with Unix like CLI tools. xl was designed to be backwards compatible with the xm CLI for xend
2. **XAPI/XE** is a powerful toolstack that provides domain life-cycle management, pool-awareness (multiple Xen systems added to a central logical pool), and many other advanced features available with Xen Project software.  
3. **libvirt/VIRSH** is a generalized virtualization layer for managing various virtualization technlogies with a common toolkit (or toolstack).  
4. **XEND/xm** (Deprecated in Xen 4.1; Removed in 4.5)  

```
Xen Project 4.1 and prior	xm is the recommended toolstack
Xen Project 4.2 to 4.4	xm is deprecated, and xl is available
Xen Project 4.5 and newer	Only xl is available
```

### change default 
```
# vi /etc/default/xen
```

### Create DomU
#### Create image
type 1(LVM): 
```
xen-create-image --hostname=xenguest1 --memory=1000mb --vcpus=1 --lvm=vg_sys --ip=192.168.249.131 --gateway=192.168.249.2 --netmask=255.255.255.0 --nameserver=192.168.249.2 --pygrub --dist=jessie --size=30Gb
```

```
# vi /etc/xen-tools/xen-tools.conf
debootstrap-cmd = "/usr/sbin/debootstrap --no-check-gpg"
```

#### Create jessie image from DVD, and dom0 kernel (kernel file specify in xen-tools.conf)
type 2(DIR): 
```
xen-create-image --hostname=xenguest1 --memory=1000mb --vcpus=1 --dir /var/lib/xen/xenguest1 --mirror=file:///media/cdrom0/ --ip=192.168.249.131 --gateway=192.168.249.2 --netmask=255.255.255.0 --nameserver=192.168.249.2  --dist=jessie --size=10Gb
```

#### Create Jessie image from internet and load kernel from image file (--pygrub)
```
xen-create-image --hostname=xenguest1 --memory=1000mb --vcpus=1 --cache=yes --dir /var/lib/xen --mirror=http://ftp.de.debian.org/debian/ --ip=192.168.249.131 --gateway=192.168.249.2 --netmask=255.255.255.0 --nameserver=192.168.249.2 --pygrub --arch=amd64 --dist=jessie --size=10Gb
```

#### Manage VMs with xl
```
xl create xenguest1
xl console xenguest1
xl destroy xenguest1
```

