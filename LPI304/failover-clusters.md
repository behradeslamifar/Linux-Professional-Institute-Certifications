# Failover Clusters

## History
The OpenAIS project was founded in January 2002 to implement Service Availability Forum Application Interface Specification APIs

The Corosync Cluster Engine was founded in January 2008 as a reduction of the OpenAIS project. (messaging and membership capabilities provided by cluster infrastructure, Corosync or Heartbeat)

A service engine is created by third parties to provide some form of cluster wide services. Some examples of these are Pacemaker, or CMAN.

### Heartbeat History
* Linux HA started with heartbeat in 1998  
* High availability for linux and related platform  
* Originally, 2 node only supported and there was no resource monitoring  

Heartbeat version 2 released in 2005
Multiple feature were added:
  * Resource monitoring
  * More than 2 nodes
  * Dependency definitions
  * Policies
  * XML code base instead of text files

Originally, managing heartbeat 2.0 was difficult and happend by manipulationg XML files. Since version 2.1.4 the pacemaker project split off and heartbeat become resonsible for the membership layer only. Currntly, corosync is the de facto standard for managing membership layer  

#### Other project:
  * OpenAIS
  * cman


## Pacemaker
Pacemaker [Pacemaker] is a scalable High-Availability cluster resource manager formerly part of Heartbeat [LinuxHA]. Pacemaker was first released as part of Heartbeat-2.0.0 in July 2005 and overcame the deficiencies of Heartbeat’s previous cluster resource manager:

• cib—Short for Cluster Information Base. Contains definitions of all cluster options, nodes, resources, their relationships to one another and current status. Synchronizes updates to all cluster nodes.  

• lrmd—Short for Local Resource Management Daemon. Non-cluster aware daemon that presents a common interface to the supported resource types. Interacts directly with resource agents (scripts).  

• pengine—Short for Policy Engine. Computes the next state of the cluster based on the current state and the configuration. Produces a transition graph contained a list of actions and dependencies.

• tengine—Short for Transition Engine. Coordinates the execution of the transition graph produced by the Policy Engine. 

• crmd—Short for Cluster Resource Management aemon. Largely a message broker for the PE, TE and LRM. Also elects a leader to coordinate the activities of the cluster.

### Pacemaker Overview
Pacemaker is the stack of different components, with crmd in the heart of it, that takes care of managing cluster resources. It stores the cluster configuration in the CIB, which consists of XML code and is managed through crmd. Different command line and web interfaces are available for managing Pacemaker
  * crmsh has been the default until Red Hat launched the Pacemaker stack
  * different commands, starting with crm exist also
  * and also some legacy commands, starting with cib


### Redhat clustering
in the early 2000s, Red Hat acquired their own HA stack. This was a complete independent development from the Heartbeat software. Solution was known as Red Hat Cluster Suite, and later the Red Hat HA Add-On.  
  * cman (cluster manager) was the heartbeat equivalent component, taking care of membership
  * rgmanager was the resource group manager, taking care of resources

In **RHEL 6** cman could be used with pacemaker  
in **RHEL 7** the default stack become corosync and pacemaker  


### Pacemaker’s key features include:
  * Detection and recovery of node and service-level failures
  * Storage agnostic, no requirement for shared storage
  * Resource agnostic, anything that can be scripted can be clustered
  * Supports fencing (also referred to as the STONITH acronym, deciphered later on) for ensuring data integrity
  * Supports large and small clusters
  * Supports both quorate and resource-driven clusters
  * Supports practically any redundancy configuration
  * Automatically replicated configuration that can be updated from any node
  * Ability to specify cluster-wide service ordering, colocation and anti-colocation
  * Support for advanced service types
  * Clones: for services which need to be active on multiple nodes
  * Multi-state: for services with multiple modes (e.g. master/slave, primary/secondary)
  * Unified, scriptable cluster management tools



### Pacemaker Artchitecture (highest level)
1. Non-cluster-aware components  
These pieces include the resources themselves; scripts that start, stop and monitor them; and a local daemon that masks the differences between the different standards these scripts implement  


2. Resource management.  
Pacemaker provides the brain that processes and reacts to events regarding the cluster.  


3. Low-level infrastructure.  
Projects like Corosync, CMAN and Heartbeat provide reliable messaging, membership and quorum information about the cluster.


Pacemaker has an amazingly rich set of configuration settings and features that allows very complex designs. Without going into too much details, here are some of the features offered:  

location rules: locating resources on nodes based on some criteria colocating rules: colocating resources based on some criteria clone set: a bunch of similar resource master-slave clone set: a clone set with different level of members a resource group: a group of resources forced to be together ordering rules: in which order should some operation be performed Attributes: kind of cluster wide variables, can be permanent or transient Monitoring: resource can be monitored Notification: resource can be notified of a cluster wide change

### Pacemaker Artchitecture (Internal Component)
1. Cluster Information Base (CIB)  
2. Cluster Resource Management daemon (CRMd)  
3. Local Resource Management daemon (LRMd)  
4. Policy Engine (PEngine or PE)  
5. Fencing daemon (STONITHd)  

### Types of Pacemaker Clusters
  * Active/Active
  * Active/Passive
  * N+1
  * N+M 
  * N-to-1 
  * N-to-N.

## Install
```
apt-get install pcs corosync pacemaker fence-agents
```

```
corosync-keygen
systemctl enable --now pcsd
pcs cluster auth pcmk-1 pcmk-2
pcs cluster setup --name linuxmotto node1 node2
pcs cluster start --all
```

### Pacemaker and Red Hat
In RHEL 6, Pacemaker was included in Red Hat Enterprise linux as a tech preview (Default is cman and rgmanager)  
Originally, it was used on top of the cman emebership layer manager  
In RHEL 7, Pacemaker has become the standard  
In the core it's all the same, but redhat has created the pcs command line interface, that accesses the pacemaker configuration  through a pcsd daemon.  
Installing crmsh on Red Hat is possible, but not common.  


### Setting up Corosync on SUSE Leap
On all nodes: 
```
zypper install ha-cluster-bootstrap
```

On node1: 
```
ha-cluster-init
```

On other nodes: 
```
ha-cluster-join -c node1
```

After installation: 
```
crm_mon
```

### Configuring a Base Cluster
```
yum install epel-release
yum install pcs fence-agents-all 
```
   
more fence agents on https://github.com/ClusterLabs/fence-agents  
```
firewall-cmd --permanent --add-service=high-availability or stop and disable firewalld  
systemctl enable --now pcsd
echo password | passwd --stdin hacluster
```

### Authenticating Nodes
Nodes need to be authenticated
```
pcs cluster auth node1 node2 node3
```

### Configure Base Cluster
```
pcs cluster setup --start --name mycluster node1 node2 node3
```

After a reboot, nodes will not automatically rejoin the cluster  
  * user "pcs cluster enable --all" to accomplish this anyway
  * make sure you really want this
Use "pcs cluster status" to verify operations


> :bulb: SUSE default install pacemaker based on multicast  
> :bulb: RHEL 7 install pacemaker with unicast and authenticating


### Cluster Architecture
At a high level, a cluster can be viewed as having these parts (which together are often referred to as
the cluster stack):
• Resources: These are the reason for the cluster’s being — the services that need to be kept highly
available.
• Resource agents: These are scripts or operating system components that start, stop, and
monitor resources, given a set of resource parameters. These provide a uniform interface between
Pacemaker and the managed services.
• Fence agents: These are scripts that execute node fencing actions, given a target and fence device
parameters.
• Cluster membership layer: This component provides reliable messaging, membership, and
quorum information about the cluster. Currently, Pacemaker supports Corosync4
 as this layer.
• Cluster resource manager: Pacemaker provides the brain that processes and reacts to events
that occur in the cluster. These events may include nodes joining or leaving the cluster; resource
events caused by failures, maintenance, or scheduled activities; and other administrative actions. To
achieve the desired availability, Pacemaker may start and stop resources and fence nodes.
• Cluster tools: These provide an interface for users to interact with the cluster. Various commandline and graphical (GUI) interfaces are available.


### Pacemaker’s key features include:
Detection of and recovery from node- and service-level failures
Ability to ensure data integrity by fencing faulty nodes
Support for one or more nodes per cluster
Support for multiple resource interface standards (anything that can be scripted can be clustered)
Support (but no requirement) for shared storage
Support for practically any redundancy configuration (active/passive, N+1, etc.)
Automatically replicated configuration that can be updated from any node
Ability to specify cluster-wide relationships between services, such as ordering, colocation and anti-colocation
Support for advanced service types, such as clones (services that need to be active on multiple nodes), stateful resources (clones that can run in one of two modes), and containerized services
Unified, scriptable cluster management tools



### Pacemaker architecture and components - v1.1
Cluster Information Base (CIB)
Cluster Resource Management daemon (CRMd)
Local Resource Management daemon (LRMd)
Policy Engine (PEngine or PE)
Fencing daemon (STONITHd)

### Pacemaker architecture and components - v2.0
pacemaker-based: The Cluster Information Base (CIB) manager (pacemaker-based) keeps the CIB synchronized across the cluster
pacemaker-attrd: The attribute manager maintains a database of attributes for all nodes, keeps it synchronized across the cluster, and handles requests to modify them
pacemaker-schedulerd: The scheduler determines what actions are necessary to achieve the desired state of the cluster
pacemaker-execd: The local executor handles requests to execute resource agents on the local cluster node, and returns the result
pacemaker-fenced: The fencer handles requests to fence nodes.
pacemaker-controld: The controller is Pacemaker’s coordinator, maintaining a consistent view of the cluster membership and orchestrating all the other components.

Pacemaker centralizes cluster decision-making by electing one of the controller instances as the Designated Controller (DC). Should the elected DC process (or the node it is on) fail, a new one is quickly established. 

```
# pcs resource list
# crm ra classes 
lsb
ocf / heartbeat pacemaker redhat     -> OCF is cluster aware 
service
stonith
systemd
```

### RedHat Resource Class
LSB — The Linux Standards Base agent abstracts the compliant services supported by the LSB, namely those services in /etc/init.d and the associated return codes for successful and failed service states (started, stopped, running status).
OCF — The Open Cluster Framework is superset of the LSB (Linux Standards Base) that sets standards for the creation and execution of server initialization scripts, input parameters for the scripts using environment variables, and more.
systemd — The newest system services manager for Linux based systems, systemd uses sets of unit files rather than initialization scripts as does LSB and OCF. These units can be manually created by administrators or can even be created and managed by services themselves. Pacemaker manages these units in a similar way that it manages OCF or LSB init scripts.
Upstart — Much like systemd, Upstart is an alternative system initialization manager for Linux. Upstart uses jobs, as opposed to units in systemd or init scripts.
STONITH — A resource agent exclusively for fencing services and fence agents using STONITH.
Nagios — Agents that abstract plug-ins for the Nagios system and infrastructure monitoring tool.

```
crm ra list ocf

pcs resource describe docker
crm ra info docker
```

```
crmsh
crm help
crm configure show
crm configure edit
crm configure save cib-backup.txt
crm configure load push cib-backup.txt
```

crm_*   exist from past

```
cibadmin 
cibadmin -Q
cibadmin -E --force   (destroy anything)
```
  
```
pcs resource list
pcs resource describe IPAddr2
pcs resource create <name> <type> [options] 
```

```
crm ra list ocf|systemd|...
```

```
pcs property list --all
pcs property set stonith-enable=false
```

```
crm configure get_property
crm configure property enable-acl=yes
```

### LAB1: crmsh
HA web server

```
crm configure edit 
primitive ip-apache ocf:heartbeat:IPaddr2 \
        params cidr_netmask="24" ip="192.168.4.101" \
        op monitor interval=10 timeout=20
primitive apache-service ocf:heartbeat:apache \
        op start interval=0 timeout=60 \
        op stop interval=0 timeout=60 \
        op monitor interval=10 timeout=30
```

install apache2
```
crm resource cleanup apache-service

crm_mon  (monitor what you do)
```

### LAB2: pcs
HA web server

```
pcs resource create ip-apache IPaddr2 ip=192.168.4.101 cidr_netmask=24
pcs property set stonith-enabled=false
pcs resource create service-apache apache 

pcs resource show    (monitor what you do)
```

### Quorom Calculation Options
While building the cluster with "pcs cluster setup" on Red Hat, specific options can be used to manipulate quorom behavior
--wait_for_all: wait for all nodes to join the cluster before applying quorum calculations
--auto_tie_breaker: 50% will give quorom as well. In this case, the side with the lowest node ID will win
--last_man_standing: quorum recalculate every 10 seconds, which allow admins to takeout quorom nodes one by one. Must use with --wait_for_all 
--two_node: allow for a 2 node cluster

```
pcs cluster sync
pcs cluster start --all
```

On SUSE, use setting in corosync.conf
```
quorum {
  provider: corosync_votequorom
  last_man_standing: 1
  wait_for_all: 1
  auto_tie_breaker: 1
  auto_tie_breaker_node: lowest
}
```


schedule maitenance on operation cluster
```
systemctl restart corosync
```

user "corosync-quoromtool" to check what you did


### Fencing Mechanisms
Power fencing: Cut the victim off from power
  * Also referred to STONITH
  * Node may or may not automatilly rejoin

Fabric fencing disconnects a machine from storage
  * By closing port on a FC switch
  * or by using SCSI reservations
  * node will not automatically rejoin 


Fencing Agents overview
  * disk
  * hypervisor
  * power switch
  * management board
  * test agent


List fence agent
  * fence_<tab><tab>	-> all fencing agent that exist on OS
  * RedHat: pcs stonith list   -> all fencing agent that exist in pacemaker environment
  * SUSE: stonith -L
...
meatware
...

stonith -h | less
(search meatware)

Fencing example
  * ipmi
  * libvirt
  * sbd  (Split Brain Detection/STONITH by Disk) - poision pill



Configuring Fencing 
To use fencing device, a fencing agent needs to be configured
```
pcs stonith list
pcs stonith describe 
  or man page
pcs stonith create fencing_agent parameters
```

#### Common Fencing Device Options
stonith-timeout: The time to wait for fencing to complete. Set to 60 second by default
priority: Used to set priority if mutiple devices are used
pcmk_host_map: Maps a hostname to port, which is required by some specific devices such APC
pcmk_host_check: Defines how the cluster tells which machines may be controlled from the fencing device:
  dynamic-list, cluster will query the fencing device
  static-list, use "pcmk_host" list to set
  none, every device can fence every node


Fencing Virtual Machine
fenc-virtd is used to fence libvirt managed virtual machines
Multiple hypervisors are supported
  Eatch hypervisor must fence-virtd
  They must be in the same multicast group
  And must use the same shared secret key

Setting up fence-virtd
On the hypervisor install fence-virtd
  yum -y install fence-virt fence-virtd fence-virtd-libvirt fence-virtd-multicast
On the hypervisor create shered secret :
  mkdir /etc/cluster/
  dd if=/dev/urandom of=/etc/cluster/fence_xvm.key bs=1k count=4
On the hypervisor, configure fence_virtd daemon 
  fence_vertd -c
Use systemctl enable --now fence_virtd 
Copy shared secret to all other hypervisor node
Use fence_xvm on virtual machines


Configuring fence-xvm
fence-xvm is used on VMs that are on a fence_virtd hypervisor
Copy /etc/cluster/fence_xvm.key from host to all vms
Use pcs stonith create fence-vm1 fence_xvm port="vm1" pcmk_host_list="vm1.example.com"
  fence-vm1: is the name of fence device
  fence_xvm: is the name of agent module
  port: specifies the (virsh) name of vm
  pcm_host_list: refers to the cluster node name
Test with "pcs stonith fence nodename [--off]"
  --off: shutdown. whiout of reboot the node

fence_virt multicast
The default traffice between fenc_xvm and fenc_virt is multicast
Make sure firewall accept multicast, or shutdown firewall on the host to allow for the multicast traffic to happen
  use "tcpdump -i virbr0 -s0 -vv net 224.0.0.0/4" to verify
  firewall-cmd --permanent --direct --add-rule ipv4 filter INPUT 0 -m pkttype --pkt-type multicast -j ACCEPT
  or easier: firewall-cmd --permanent  --add-port=1229/udp

Test fence: freeze one machine
echo c > /proc/sysrq-trigger


### Configuring Nested Fencing on Red Hat
#### Configuring Storage Fencing
"fence_scsi" allows for storage fencing
A device is needed that supports SCSI reservations
  RHEL 7 iSCSI target does support it
Cluster nodes will add a key to the shared SCSI device after starting 
When a node need to be fenced, the other node will remove the node key of the fence target

#### Creating a fence_scsi device
Like any other fence, but with some specific options
  devices=devicepath (use /dev/disk/by-uuid)
  pcmk_monitor_action=metadata (currently needed to work around a bug in the software)
  pcmk_host_list=FQDN
  pcmk_reboot_action="off"
  meta provides="unfencing"
     This options sets the node status to unfenced after it re-joins the cluster


Configure Redundant Fencing
Redundant fencing is configured by fencing levels
  All level 1 devices are attemped first, befor trying level 2 devices
pcs stonith level add <level> <node> <devices>
  pcs stonith level add 1 node1 fence_node1_ilo
  pcs stonith level add 2 node1 fence_node1_apc
  Optionally, use "pcs stonith level remove" to remove a level
Use "pcs stonith level" to view fence level configuration


Configuring the SBD Device on SUSE
Configuring SBD
SBD require a shared disk, with partition with a minimal size of 8MiB
On this partition, each node get one node slot to track status information
Each node runs an sbd daemon, which is started through corosync as a dependency
Each node has /etc/sysconfig/sbd, which contains a STONITH device list
A node is fenced by the cluster writing a poison pill to its slot 
Using watchdog is required
  watchdog is a hardware timer that generate a reset if it reaches zero


Configuring Watchdog
Some hardware uses hardware watchdog kernel modules
This is not case for virtual machines in a typical lab environment, where you'll need to load the softdog kernel module
Use "systemctl status systemd-modules-load"
Put configuration file in /etc/modules-load.d/ with the name softdog.conf (.conf extention is mandatory)
In this file just enter the line "softdog"
Use "systemctl restart systemd-modules-load"
Use "lsmod | grep dog" and "ls /dev/watchdog" to verify that the watchdog module is active


Configuring SBD - Part 1
Automatically through ha-cluster-init when setting up the cluster
or using sbd -d /dev/sdx create
edit /etc/sysocnfig/sbd (SUSE) or /etc/default/sbd (Ubuntu)
  SBD_DEVICE="/dev/sdx"
  SBD_WATCHDOG="yes"
  SBD_STARTMODE="clean"
     set to "clean" to allow anode to rejoing the cluster, only if its clean, or set to "always" to always allow rejoin
Make sure to syncronize cluster config
Restart cluster stack on all nodes

Configuring SBD - Part 2
crm configure
create the cluster resource
  primitive stonith-sbd stonith:external/sbd
set cluster properties
  property stonith-enabled="true"
  property stonith-timeout="30"

Managing STONITH
Verify the configuration
  sbd -d /dev/sdx list
  sbd -d /dev/sdx dump
Send message to a device 
  sbd -d /dev/sdx message node1 {test|reset|poweroff}
clear a poison pill from a node slot
  sbd -d /dev/sdx message node clear
     This is mandatory if SBD_STARTMODE="clean" was used to allow a node to get back after it has been fenced

pcs stonith fence nodename / sbd -d /dev/sdx meassage nodename {test|reset|poweroff}
fenc_xvm -o list



## RESOURCE 
### Understanding Resources
A resource is anything is managed by the cluster  
Resources are provided by resource scripts 
  * OCF
  * systemd / LSB(deprecated)
  * Heartbeat (deprecated)
  * Stonith
Resource configuratin is stored in the CIB  

### Understanding Resource Types
  * Primitive
  * Clone 
  * Multi State (aka Master/Slave)
  * Group


#### Resource Stickiness
Default resource stickiness defines where a resource should go after the original situation is restored

```
# locate -bei IPaddr2
/usr/lib/ocf/resource.d/heartbeat/IPaddr2
/usr/share/resource-agents/ocft/configs/IPaddr2

crm configure primitive ipaddr ocf:heartbeat:IPaddr2 params ip=192.168.10.20 op monitor interval=10s
pcs create resource ipaddr IPaddr2 ip=192.168.10.21
```

#### Understanding Resource Constraints
3 type of constraints
  location constraints — A location constraint determines which nodes a resource can run on.
  order constraints — An order constraint determines the order in which the resources run.
  colocation constraints — A colocation constraint determines where resources will be placed relative to other resources.


#### Understanding Scores
  * INFINITY (1,000,000): must happen
  * -INFINITY (-1,000,000): may not happen

Resource migration will enforce a constraint on the resource
  * crm migrate
  * pcs resource move
  * use crm resource unmigrate / pcs resourc clear to remove

> :bulb: Note that -INFINITY on a location contraint will never run the resource on the node specified, not event its the last node remianing in the cluster

#### Using Constraints: crm
```
loaction db-on-node2 mariadb 1000: node2
order ip-before-web Mandatory: ip web
order ip-befor-DRBD-promote inf: ip:start drbd:promote
colocate web1-not-with-web2 -inf: web1 web2
```

#### Using Constraints: pcs
```
pcs constraint location web prefers node1
pcs constraint location web prefers node1=1000; pcs constraint location web prefers node2=500
pcs constraint location web avoids node1
pcs constraint colocation add web1 with db1
pcs constraint colocation add web1 with db1 -INFINITY
pcs constraint order webip then webserver
```

Notice that all pcs command add names to the constraints automatically

Simulate Cluster Status
```
crm_simulate -sL : Show current score
```

#### Understanding Groups
Groups benefits
  * Combines resource primitives in a single entity that can be managed as such
  * Provides specific ordering 
  * Provides specific dependencies
  * If using constraints, they should point at the entire group and not its members
  * Stickness score is the sum of all members stickness scores

Create Groups: pcs
```
pcs resource create ipaddr IPaddr2 ip=192.168.10.20 --group newgroup
pcs resource create fs1 Filesystem device=/dev/sda1 directory=/mnt/fs1 --group newgroup 

pcs resource group add newgroup ipaddr fs1
```

Create Groups: crm 
```
crm configure group newgroup ipaddr fs1
```

#### Understanign Clones
Use to run a primitive or group on multiple nodes
Anonymous clones run exactly the same primitive
  Useful for services such as DLM
Globaly Unique Clones
  Can have per clone unique configuration, such as ip addresses
Number of clones typically the same as the number of nodes, but can be limited
Use "interleave=true" to allow clones to run independent of one another
Multi state resources are a special case of a clone, used for active/passive configurations
  Using an additional Promote and Demote operation


#### Manging Failed Resources
First read logs to find out why the resource has failed
  /var/log/messages, /var/log/syslog
  journalctl
A resource that fails will set its fail counter to INFINITY and stop trying 
This means that nothing will happen after you have identified and fixed the issue
You'll need to reset the resource failcounter

Resetting the Resource Failcounter
pcs resource failcount show
pcs resource debug-start myresource
pcs resource failcount myresource mynode
pcs resource cleanup myresource

crm resource cleanup myresource


#### Lab1: Resource group with pcs
Configure two resource group, one for Apache and one for vsftpd  
In the groups, have the services started and give the both of them a unique ip address  
The resource group should be started before the Apache resource group  

```
pcs resource group add apache-group apache-ip
pcs resource group add apache-group apache-service

pcs resource create vsftpd-ip IPaddr2 ip=192.168.20.102 cidr_netmask=24 --group ftp-group
pcs resource create vsftpd-service systemd:vsftpd  --group ftp-group

pcs constraint colocation add apache-group with ftp-group -10000
pcs constraint order apache-group then ftp-group
```


Note
```
rsc_defaults rsc-options: \
  resource-stickiness=1 \
  migration-threshold=3
op_defaults op-options: \
  tiemout=600 \
  record-pending=true
```

```
# crm configure edit

group apache-group apache-ip apache-service
primitive ftp-ip ocf:heartbeat:IPaddr2 params cidr_netmask=24 ip=192.168.20.102
primitive ftp-service systemd:vsftpd
group ftp-group ftp-ip ftp-service
colocation  web-not-ftp -10000: ftp-group apache-group
order ftp-after-apache apache-group ftp-group
```

#### Managing Resource States - Part 1
After creating, resources will automatically started (enabled)
  * Use "pcs resource enable|disable" to manage the state
  * No equivalent in crm  

Administrator can restart resources
```
pcs resource restart
crm resource restart 
```

Resource properties can be changed 
```
pcs resource update, pcs resource op add|remove
crm configure edit
```

#### Starting Resources
In pcs, there is no start command, resources are started by pacemaker automatically, unless something preventing that
If a pcs resource isn't starting, use "pcs resource debute-start <resource_name>" to find out why, and use "pcs resource failcount reset" to reset the resource.

#### Managing Resource State - Part 2
Resources can be set in (un)managed mode as well, so that the cluster temperarily takes it hands off and act as if its running locally
```
pcs resource [un]manage
crm resource [un]manage
```

In pcs, you can use "pcs resource relocate" to relocate resources to the node that accourding to their location constraints is the preferred node

#### Using Resource Utilization 
Resource utilization can be used to calculate if a node has sufficient resources
Utilization parameters need to be set on a node as well as the resource
Utilization uses an arbitary value that matches node and resource utilization and is applied to ram and cpu
```
  pcs resource utilization myresource ram=5 cpu=10
  crm resource utilization myresource ram=10 cpu=20

  pcs node utilization myresource ram=20 cpu=40
  crm configure edit
    node 1: node1 \
        utilization cpu=4 ram=8
```

#### Managing Cluster Membership
There are many different ways to control membership of cluster nodes, approach is very different between pcs and crm

Starting and stoping cluster services
```
pcs cluster start; pcs cluster stop node2.example.com; pcs cluster stop --all
```

Enabling/disabling cluster service for automatic joining
```
pcs cluster disable node1.example.com
```

Adding and removing nodes
```
pcs cluster node {add|remove} node4.example.com
```

#### Adding Nodes (pcs)
Conditions to be met to add or remove a node  
  * firewall is opened
  * pcs and fence-agents-all packages are installed
  * pcsd started and enabled 
  * hacluster user has a password
  * from and existing node, "pcs cluster auth node4.example.com" has been used to add the new node to the cluster.

After adding the node, the "pcs cluster auth" command needs to be used from the new node to authorized existing node

#### Removing Nodes (pcs)
First use, "pcs cluster node remove node4.example.com"
Next changing fencing configuration, using "pcs stonith delete node4.example.com"
Manage node status with "pcs node <status>"

#### Managing Standby State
no resource running on node4 anyway
```
pcs node standby node4.example.com
```

```
pcs node unstandby node4
```

#### Adding/Removing Nodes (crm)
crm cluster add to add nodes
Alternatively, work through corosync instead 
  To add a node, install corosync on that node, when using multicast it will automatically rejoin
  To remove a node, use corosync-cfgtool -H  to halt it on the local node and next "systemctl stop corosync"
  "corosync-cfgtool -k" kill a node based on its node-id

```
pcs status nodes
pcs status corosync
pcs status pcsd
pcs status resources
pcs status cluster
```

#### Managing Maintenance and Standby
A node in maintenance mode will still run resources, a node in standby mode will no longer run resources. For update cluster stack you can use maintenance mode to prevent to cluster be affected.  
```
pcs node [un]maintenance [--all|<node>]
crm node maintenance <node> / crm node ready <node>
pcs node [un]standby [--all|<node>]
crm node standby <node> / crm configure edit (remove standby)
```

#### Maintenance Level
Maintenance mode applies to different levels
  node: crm node maintenance node4 / crm node ready node4
  resource: crm meta <resource> set maintenance true / pcs resource update meta maintenance=true
  cluster: crm configure property maintenance-mode=true / pcs property set maintenance-mode=true


#### Moving Resources
Resources can be migrated (moved) away from this node, which will create a -INIFINITY location constraint
  pcs resource move <force|resource_name>
  crm resource move <force|resource_name>
  pcs resource ban 
    for both, consider using lifetime=nn to specify how long
To get resource back to their original location, they need to be "unmoved"
  pcs resource clear <resource>
  crm resource clear <resource>

#### Managing Failed Resources
A resource that fails to start, will set its failcount to INIFINITY
  The result is that the resource won't be able to start anymore until the failure timeout exipres
  Use "pcs resource failcount show" to monitor
  Use "pcs resource debut-start <resource_name>" to more information
  Use "pcs resource failtcount reset <resource_name> <node>" to reset the failcount and try again
  In crm, use "crm resource cleanup"

#### Corosync Logging
Corosync logs to syslog by default
Use the logging seciton in /etc/corosync/corosync.conf to control logging 
or have corosync send message to stderr so that systemd will process them through journald
  logging {
     to_stderr:yes
  }
Use logfile_priority and syslog_priority inside a logging block to set alert, crit, debug, emerg, err, info, notice or warning
After making change on one node, use "pcs cluster sync" to synchronize the changes. 
On each node, use "pcs cluster reload corosync" to activate the changes
Consider using logrotate

#### Pacemaker Logging
Packemaker follows corosync logs 
Use PCMK_log* options in /etc/sysconfig/pacemaker or /etc/default/pacemaker to provide alternative logging
  like PCMK_debug=yes
Monitor logs through journald using "journalctl -l -u pacemaker.service -u corosync.service


#### Using MailTo
Use the MailTo resource with the email= argument to ensure the administrator gets an email after the cluster migrates a resource
  Its a resource, not an option
pcs resource create web-mailto MailTo email=b.eslamifar@gmail.com subject="web group migrate" --group web
  This needs mailx package to be installed
  It also needs Postfix to be able to send message
crm configure primitive web-mailto MailTo email=b.eslamifar@gmail.com subject="web group migrate"
  Dont forget to put it in a group also

#### Cluster Monitoring
Use crm_mon from the command line
The ClusterMon resource agent automates monitoring, using a few options
  user
  update: update interval (default is 15 seconds)
  extra_options: crm_mon command line options
  htmlfile: path to write output to 
Run ClusterMon on each node to get a complete state overview
  Consider using resource clones 
