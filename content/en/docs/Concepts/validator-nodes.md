---
categories: ["Concepts"]
tags: []
weight: 6
title: "Validator Nodes"
linkTitle: "Validator Nodes"
date: 2023-03-30
description: Validator nodes play a crucial role in maintaining the security and integrity of the Akash Network.
---

Validator nodes play a crucial role in maintaining the security and integrity of the Akash Network.
## Overview

Validator nodes in the Akash ecosystem are responsible for validating transactions, proposing new blocks, and maintaining the consensus state of the network. They achieve this by participating in the consensus algorithm, which is based on the Tendermint Core engine and the Cosmos SDK.

## Key Responsibilities

1. **Block Validation**: Validator nodes verify the transactions included in a block and ensure they adhere to the network's rules and protocol.
2. **Block Propagation**: Validator nodes propagate the validated blocks to other nodes in the network.
3. **Consensus**: Validator nodes participate in the consensus process, using the Tendermint Proof-of-Stake (PoS) algorithm to elect a block proposer and confirm the validity of proposed blocks.
4. **Governance**: Validator nodes take part in the governance process by voting on proposals to modify network parameters, upgrade the protocol, or distribute community funds.

## Validator Rewards and Risks

Validator nodes receive rewards in the form of newly minted AKT tokens and transaction fees for participating in the network. However, they also face potential risks:

- **Uptime risk**: Validators with poor uptime or poor network connectivity might be penalized by having their staked AKT tokens slashed.
- **Double signing risk**: Validators that sign conflicting blocks might have their staked AKT tokens slashed as well.
- **Commission rate risk**: Validators with uncompetitive commission rates might not attract enough delegators and thus have lower voting power in the network.

It's crucial to ensure a secure, stable, and well-maintained validator node to avoid penalties and maximize rewards.