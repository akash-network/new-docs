---
title: "Build a Cloud Provider"
linkTitle: "Build a Cloud Provider"
weight: 1
---

Akash leases are deployed via Kubernetes pods on provider clusters.
This guide details the build of the provider’s Kubernetes control plane and worker nodes.

The setup of a Kubernetes cluster is the responsibility of the provider.
This guide provides best practices and recommendations for setting up a Kubernetes cluster.
This document is not a comprehensive guide and assumes pre-existing Kubernetes knowledge.

## Prerequisites

The Kubernetes instructions in this guide are intended for audiences that have the following skills sets and knowledge.

* **System Administration Skills** - necessary for setting up servers/network making up the Kubernetes cluster.
* **Kubernetes Experience** - a base level of Kubernetes administration is highly recommended.

### Akash Wallet

Placing a bid on an order requires a 5 AKT deposit placed into collateral per bid won. If the provider desired 2 concurrent leases, the provider’s wallet would need minimum funding of 10AKT.

As every deployment request bid requires 5 AKT to be deposited in the escrow account, it's always good to have more so your provider can keep bidding. If your provider is ready to offer 10 deployments, then it's best to have 5 x 10 = 50 AKT and a little more to make sure provider can pay the fees for broadcasting the transactions on Akash Network.

The steps to create an Akash wallet are covered in the following documentation:

* [Getting Started](/docs/getting-started/environment-setup)

### **Kubernetes Cluster**

A full Kubernetes cluster is required with outbound internet access and be reachable from the internet.

If you need assistance in building a new cluster, visit the [Kubernetes Cluster for Akash Providers ](../kubernetes-cluster-for-akash-providers/)guide.

### RPC Node

Akash Providers need to run their own blockchain RPC node to remove dependence on public nodes.
This is highly recommended.

We have recently released documentation guiding thru the process of building a [RPC node via Helm Charts](../../../akash-nodes/akash-node-via-helm-charts/) with state sync.

### CPU Support

Akash Network providers support both x86_64 and arm64 processors.
If you find any issues with a particular architecture please open an issue on the support repository.

### Custom Kubernetes Cluster Settings

Akash Providers are deployed in many environments and we will make additions to these sections as when nuances are discovered.

* [VMware Tanzu](../../custom-kubernetes-cluster-settings/vmware-tanzu.md)