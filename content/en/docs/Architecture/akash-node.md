---
categories: ["Architecture"]
tags: []
title: "Akash Node"
linkTitle: "Akash Node"
weight: 3
description: >-
  The Akash Node enables interactions with the network, validation of transactions, and participation in the consensus process.
---

Akash Node is a crucial component of the Akash Network, a decentralized cloud computing platform providing a marketplace for computing resources. The Akash Node enables interactions with the network, validation of transactions, and participation in the consensus process.
Overview

An Akash Node is a software component that connects to the Akash Network and maintains a synchronized copy of the blockchain. It allows users, providers, and validators to interact with the network, submit transactions, and query the state of the network.

## Key Responsibilities

1. **Blockchain Synchronization**: An Akash Node synchronizes with the network to maintain an up-to-date copy of the blockchain, ensuring data consistency and availability.
2. **Transaction Submission**: The node allows users to submit transactions, such as deployments, bids, and leases, to the Akash Network.
3. **Querying Network State**: The node provides an interface for querying the network's state, enabling users to access information about deployments, orders, and account balances.

## How it Works

The [Akash Node][akash-node] plays a critical role in the Akash Network, facilitating interactions with the network and ensuring its proper functioning. Here's a deeper look at how the Akash Node works and its role in the ecosystem:

### Blockchain Synchronization

An Akash Node continuously synchronizes with the network to maintain an up-to-date copy of the blockchain. The node connects to its peers, exchanging and validating new blocks and transactions. The node uses the Tendermint consensus [algorithm][tendermint-consensus], a Byzantine Fault Tolerant (BFT) engine, which enables the network to agree on the state of the blockchain and achieve finality efficiently.

### Transaction Validation and Propagation

The Akash Node is responsible for validating and propagating transactions. When the node receives a transaction, it checks the transaction's validity, ensuring it meets the network's rules and requirements. If the transaction is valid, the node adds it to the mempool (a temporary storage area for pending transactions) and propagates the transaction to its [peers][tendermint-core-documentation].

### Block Creation

Validator nodes in the Akash Network participate in block creation. Validators propose new blocks that contain transactions from the mempool. The validators then participate in the consensus process to reach an agreement on the block's validity. Once the consensus is reached, the new block is appended to the blockchain, and the node updates its local copy [accordingly][tendermint-core-documentation].

### Gossip Protocol

The Akash Node uses a gossip protocol to efficiently communicate with its [peers][gossip-protocol-in-tendermint]. The gossip protocol allows nodes to share information about the network's state, such as new transactions and blocks. By propagating this information through the network, the gossip protocol ensures that all nodes have a consistent view of the blockchain [state][gossip-protocol-in-tendermint].

### Querying Network State

The Akash Node maintains an indexed view of the blockchain state, enabling users to query information such as account balances, deployment statuses, and order books. The node uses the [Application Blockchain Interface (ABCI)][application-blockchain-interface], a protocol that allows the blockchain layer to interact with the application layer. This interface enables the node to process and respond to user queries, providing insights into the network's state.

### Governance and Staking

Akash Nodes, particularly validator nodes, also participate in governance and staking processes. Validators stake Akash Tokens (AKT) as collateral to secure their position in the network. These nodes participate in the governance process by voting on proposals that can affect the network's parameters, upgrades, and other important decisions.

In conclusion, the Akash Node is an integral component of the Akash Network, responsible for synchronizing the blockchain, validating and propagating transactions, creating blocks, and enabling users to query the network's state. The node also plays a role in the governance and staking processes, ensuring the proper functioning and decentralization of the [network][akash-network-official-website].

[akash-node]: https://github.com/akash-network/node
[akash-network-official-website]: https://akash.network/
[tendermint-consensus]: https://tendermint.com/core/
[tendermint-core-documentation]: https://docs.tendermint.com/
[gossip-protocol-in-tendermint]: https://github.com/tendermint/spec/tree/master/spec
[application-blockchain-interface]: https://docs.tendermint.com/master/spec/abci/
