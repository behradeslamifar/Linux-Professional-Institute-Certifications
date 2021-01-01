# LPIC-304 Objectives V2
These objectives are version 2.0.0.

## Topic 330: Virtualization
### 330.1 Virtualization Concepts and Theory (weight: 8)
#### Emulation and Simulation
A **simulation** is a system that behaves similar to something else, but is implemented in an entirely different way. It provides the basic behaviour of a system, but may not necessarily adhere to all of the rules of the system being simulated. It is there to give you an idea about how something works.  
  
`Example:` Think of a flight simulator as an example. It looks and feels like you are flying an airplane, but you are completely disconnected from the reality of flying the plane, and you can bend or break those rules as you see fit. For example, fly an Airbus A380 upside down between London and Sydney without breaking it.  
  
An **emulation** is a system that behaves exactly like something else, and adheres to all of the rules of the system being emulated. It is effectively a complete replication of another system, right down to being binary compatible with the emulated system's inputs and outputs, but operating in a different environment to the environment of the original emulated system. The rules are fixed, and cannot be changed, or the system fails.  
  
`Example:` The M.A.M.E. system is built around this very premise. All those old arcade systems that have been long forgotten, that were implemented almost entirely in hardware, or in the firmware of their hardware systems can be emulated right down to the original bugs and crashes that would occur when you reached the highest possible score.  
 
#### v2v and p2v
1- v2v redhat: https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html-single/V2V_Guide/index.html
2- p2v libguestfs: http://libguestfs.org/virt-v2v/

#### History
VM/370, IBM: to ensure that their new machine would be able to run
programs developed for an older model
Xbox 360 software emulation


#### Virtualization
Emulators: VMWare, Microsoft Virtual PC, QEMU, running on simulate processors
Xen actually runs all software directly on the processor (paravirtualization: para means similar to)
Xen have abstraction layer for flexibility

#### Virtualization Techniques
#### Full Virtualization
Full virtualization or emulation: VMWare and QEMU
Unaccelerated QEMU is an order of magnitude slower than native execution, and accelerated QEMU or VMware ESX server can only accelerate the emulated machine if it’s the same architecture as the
underlying hardware.

#### OS Virtualization or Container Virtualization
s a server-virtualization method where the kernel of an operating system allows for multiple isolated user-space instances, instead of just one. Such instances (sometimes called containers, software containers,[1] virtualization engines (VE), virtual private servers (VPS), or jails)
chroot, docker, LXC, OpenVZ, Virtuozzo, FreeBSD jails and Solaris Containers (or Zones)

#### Paravirtualization
somewhere between the two, there’s paravirtualization, which relies on the operating system being modified to work in concert
Xen
#### Hypervisor
#### Type-1, native or bare-metal hypervisors
These hypervisors run directly on the host's hardware to control the hardware and to manage guest operating systems
Oracle VM Server for x86, the Citrix XenServer, and VMware ESX/ESXi.

#### Type-2 or hosted hypervisors
These hypervisors run on a conventional operating system just as other computer programs do
VMware Workstation, VMware Player, VirtualBox and QEMU

#### Xen limitation
Only works with operating systems that have been specifically modified to support it
Also more work to set up than a pure software emulator
The state of the Xen documentation is pretty dreadful.

### 330.2 Xen (weight: 9)
  * [330.2 Notes](xen.md)
  * [xl command](https://xenbits.xen.org/docs/unstable/man/xl.1.html)

### 330.3 KVM (weight: 9)
  * [man qemu-system-x86_64](https://manpages.debian.org/jessie/qemu-system-x86/qemu-system-x86_64.1.en.html)
  * [man qemu-img](https://manpages.debian.org/testing/qemu-utils/qemu-img.1.en.html)
  * [Qemu monitor](http://people.redhat.com/pbonzini/qemu-test-doc/_build/html/topics/pcsys_005fmonitor.html)
  * [Product Documentation for Red Hat Enterprise Linux 7](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/)
  * [KVM monitor commands](https://en.wikibooks.org/wiki/QEMU/Monitor)
  * [virsh change-media](https://www.ndchost.com/wiki/libvirt/change-media)


### 330.4 Other Virtualization Solutions (weight: 3)
  * [Virtualbox End-user Documentation](https://www.virtualbox.org/manual/UserManual.html)

### 330.5 Libvirt and Related Tools (weight: 5)
  * [Libvirt URI Reference](https://libvirt.org/remote.html#Remote_URI_reference)

### 330.6 Cloud Management Tools (weight: 2)

## Topic 334: High Availability Cluster Management
### 334.1 High Availability Concepts and Theory (weight: 5)

### 334.2 Load Balanced Clusters (weight: 6)
  * [Linux Virtual Server Tutorial](http://www.ultramonkey.org/papers/lvs_tutorial/html/)
  * [DRouting ipvs](http://www.linuxvirtualserver.org/VS-DRouting.html)
  * [keealived User Manual](https://www.keepalived.org/doc/index.html)
  * [Linux IP sysctl Documentation](https://www.kernel.org/doc/Documentation/networking/ip-sysctl.txt)
  * [Virtual Router Redundancy Protocol (VRRP)](lpi304:vrrp)
  * [loadbalancer and LVS](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/load_balancer_administration/s1-lvs-direct-vsa#s2-lvs-direct-arptables-VSA)
  * [HAProxy Documentation](http://cbonte.github.io/haproxy-dconv/1.8/intro.html)
  * [HAProxy - DDoS Trottling](https://www.haproxy.com/blog/use-a-load-balancer-as-a-first-row-of-defense-against-ddos/)
  * [HAProxy Multi Layer Security](https://www.haproxy.com/content-library/the-haproxy-guide-to-multi-layer-security/)
  * [HAProxy ACLs](https://www.haproxy.com/blog/introduction-to-haproxy-acls/)

### 334.3 Failover Clusters (weight: 6)
  * [334.3 Notes](failover-clusters.md)
  * [Pacemaker 1.1 Cluster from Scratch](https://clusterlabs.org/pacemaker/doc/en-US/Pacemaker/1.1/html/Clusters_from_Scratch/)
  * [Pacemaker 2.0 Cluster from Scratch](https://clusterlabs.org/pacemaker/doc/en-US/Pacemaker/2.0/html/Clusters_from_Scratch)
  * [crm cli tool](http://crmsh.github.io/documentation/index.html)
  * [Linux High Availability: Red Hat EX436 and LPIC-3 304](https://learning.oreilly.com/videos/linux-high-availability/9780134836089)
  * [Linux Academy - LPIC-3 Exam 304: Virtualization and High Availability](https://linuxacademy.com/course/linux-academys-lpic-3-304-virtualization-and-high-availability-preparation-course/)

### 334.4 High Availability in Enterprise Linux Distributions (weight: 1)
  * [RedHat HA add-on overview](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Redhat 7.x Documentation](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/#Clustering)
  * [SUSE Linux Enterprise High Availability Extension 15 SP2](https://documentation.suse.com/sle-ha/15-SP2/?_ga=2.160471165.1119497559.1609524204-373452087.1547928518)

## Topic 335: High Availability Cluster Storage
### 335.1 DRBD / cLVM (weight: 3)
  * [DRBD v9 - User Guide](https://www.linbit.com/downloads/tech-guides/User_Guide_DRBD_9.pdf)
  * [cLVM or HA-LVM](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/logical_volume_manager_administration/lvm_cluster_overview)

### 335.2 Clustered File Systems (weight: 3)
  * [Clustered File Systems](https://en.wikipedia.org/wiki/Clustered_file_system)
  * [OCFS2 Oracle blog post](https://blogs.oracle.com/cloud-infrastructure/a-simple-guide-to-oracle-cluster-file-system-ocfs2-using-iscsi-on-oracle-cloud-infrastructure)
  * [GFS2 Red Hat Documentation](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/global_file_system_2/index)
  * [OCFS2 documentation](https://www.mankier.com/7/ocfs2)
  * [GlusterFS](https://docs.gluster.org/en/latest/Quick-Start-Guide/Quickstart/)
  * [OpenAFS](https://docs.openafs.org/QuickStartUnix/HDRWQ41.html)





