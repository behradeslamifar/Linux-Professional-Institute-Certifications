# Failover Clusters

## History
The OpenAIS project was founded in January 2002 to implement Service Availability Forum Application Interface Specification APIs

The Corosync Cluster Engine was founded in January 2008 as a reduction of the OpenAIS project. (messaging and membership capabilities provided by cluster infrastructure, Corosync or Heartbeat)

A service engine is created by third parties to provide some form of cluster wide services. Some examples of these are Pacemaker, or CMAN.

## Pacemaker
Pacemaker [Pacemaker] is a scalable High-Availability cluster resource manager formerly part of Heartbeat [LinuxHA]. Pacemaker was first released as part of Heartbeat-2.0.0 in July 2005 and overcame the deficiencies of Heartbeat’s previous cluster resource manager:

• cib—Short for Cluster Information Base. Contains definitions of all cluster options, nodes, resources, their relationships to one another and current status. Synchronizes updates to all cluster nodes.  

• lrmd—Short for Local Resource Management Daemon. Non-cluster aware daemon that presents a common interface to the supported resource types. Interacts directly with resource agents (scripts).  

• pengine—Short for Policy Engine. Computes the next state of the cluster based on the current state and the configuration. Produces a transition graph contained a list of actions and dependencies.

• tengine—Short for Transition Engine. Coordinates the execution of the transition graph produced by the Policy Engine. 

• crmd—Short for Cluster Resource Management aemon. Largely a message broker for the PE, TE and LRM. Also elects a leader to coordinate the activities of the cluster.


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
1- Non-cluster-aware components  
These pieces include the resources themselves; scripts that start, stop and monitor them; and a local daemon that masks the differences between the different standards these scripts implement  


2- Resource management.  
Pacemaker provides the brain that processes and reacts to events regarding the cluster.  


3- Low-level infrastructure.  
Projects like Corosync, CMAN and Heartbeat provide reliable messaging, membership and quorum information about the cluster.


Pacemaker has an amazingly rich set of configuration settings and features that allows very complex designs. Without going into too much details, here are some of the features offered:  

location rules: locating resources on nodes based on some criteria colocating rules: colocating resources based on some criteria clone set: a bunch of similar resource master-slave clone set: a clone set with different level of members a resource group: a group of resources forced to be together ordering rules: in which order should some operation be performed Attributes: kind of cluster wide variables, can be permanent or transient Monitoring: resource can be monitored Notification: resource can be notified of a cluster wide change

### Pacemaker Artchitecture (Internal Component)
1- Cluster Information Base (CIB)  
2- Cluster Resource Management daemon (CRMd)  
3- Local Resource Management daemon (LRMd)  
4- Policy Engine (PEngine or PE)  
5- Fencing daemon (STONITHd)  

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
