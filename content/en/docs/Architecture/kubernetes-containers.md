---
categories: ["Architecture"]
tags: ["Kubernetes"]
title: "Containers & Kubernetes"
linkTitle: "Containers & Kubernetes"
weight: 2
---

The **Akash Container Platform** is a deployment platform for hosting and managing [containers](##Containers) where users can run _**any**_ Cloud-Native application.
The Akash Network is built with a set of cloud management services including [Kubernetes](https://kubernetes.io) to orchestrate and manage containers.

## Containers

A **container** is a standard unit of software that packages up code and all its dependencies so the application runs quickly and reliably from one computing environment to another.
A **container image** is a lightweight, standalone, executable package of software that includes everything needed to run an application: code, runtime, system tools, system libraries, and settings.
**Container images** become **containers** at runtime. Available for both Linux and Windows-based applications, containerized software will always run the same, regardless of the infrastructure.
Containers isolate software from its environment and ensure that it works uniformly despite differences for instance between development and staging.

## Kubernetes

Akash Network works as a peer to peer network of clusters of computation nodes.
These nodes are part of clusters running Kubernetes.
From the official documentation:
> Kubernetes is a portable, extensible, open source platform for managing containerized workloads and services, that facilitates both declarative configuration and automation.
> It has a large, rapidly growing ecosystem.
> Kubernetes services, support, and tools are widely available.

> Kubernetes runs your workload by placing containers into Pods to run on Nodes.
> A node may be a virtual or physical machine, depending on the cluster.
> Each node is managed by the control plane and contains the services necessary to run Pods.

The scalability, resilience and security of Kubernetes makes it a good solution for providers to run their tenant's workloads.
Leveraging this technology Akash Network serves as a decentralized Serverless Compute marketplace.
