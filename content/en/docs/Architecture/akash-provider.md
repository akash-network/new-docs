---
categories: ["Architecture"]
tags: []
title: "Akash Provider"
linkTitle: "Akash Provider"
weight: 4
description: >-
  Akash Providers are the entities in the Akash Network that offer their computing resources for users to deploy applications.
---

They run the [Akash Provider software][akash-providers], which enables them to manage their resources, submit bids, and interact with users. In this context, we will discuss the key components and responsibilities of Akash Providers.

## Key Components

1. **[Provider Daemon (`akashd`)][akashd]**: The Provider Daemon is a software component that manages the provider's resources, communicates with the Akash blockchain, and handles resource allocation for deployments. It is responsible for receiving and processing deployment orders, submitting bids, and orchestrating user application deployment. 
2. [**Container Orchestration**][kubernetes]: Akash Providers utilize container orchestration systems, such as Kubernetes or Docker Swarm, to manage the deployment and scaling of user applications. These systems enable efficient resource allocation and ensure that applications are isolated and run securely within the provider's infrastructure.

## Responsibilities

### Resource Management
Akash Providers are responsible for managing their computing resources, such as CPU, memory, storage, and bandwidth. They should ensure that resources are allocated efficiently and securely to user applications, while monitoring their infrastructure's health and performance.

### Bidding on Orders
When users submit deployment configurations, orders are generated and broadcast to the network. Akash Providers analyze these orders and place bids on them, offering their resources at competitive prices to attract users. Providers must balance their bids to maximize utilization and revenue while maintaining competitiveness in the marketplace.

### Lease Management
Once a user selects a winning bid, a lease is created between the user and the provider. Akash Providers must manage these leases, ensuring that resources are allocated according to the lease terms and that the user's applications are deployed and run securely and efficiently.

### Deployment Management
Providers are responsible for deploying and managing user applications within their infrastructure. This includes handling the application's lifecycle, such as starting, stopping, and scaling the application, as well as ensuring the application's security and isolation from other deployments. 

### Monitoring and Reporting
Akash Providers must monitor their infrastructure and user applications, ensuring that they are running optimally and securely. They should also report relevant metrics and events to users and the Akash Network, such as resource utilization, deployment status, and billing information.

In summary, Akash Providers are essential actors within the Akash Network, responsible for offering computing resources and managing user application deployments. They interact with the Akash blockchain through the [Provider Daemon (`akashd`)][akashd] and utilize container orchestration systems to ensure secure and efficient deployment of applications. Providers must manage their resources, bid on orders, handle leases, and monitor their infrastructure to maintain a competitive edge in the marketplace.

[kubernetes]: {{< ref "kubernetes-containers.md" >}}
[akash-providers]: https://github.com/akash-network/provider
[akashd]: https://github.com/akash-network/provider