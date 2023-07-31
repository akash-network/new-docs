---
title: "Environment Setup"
linkTitle: "Environment Setup"
categories: ["Getting Started", "Guides"]
tags: []
weight: 1
---

Add the following to your `~/.bashrc` or `~/.zshrc` to setup an Akash ready environment on your machine:

```sh
export AKASH_KEY_NAME=example # Change this to whatever name you want for your key
export AKASH_KEYRING_BACKEND=os
export AKASH_ACCOUNT_ADDRESS="$(./bin/akash keys show $AKASH_KEY_NAME -a)"
export AKASH_NET="https://raw.githubusercontent.com/akash-network/net/master/mainnet"
export AKASH_VERSION="$(curl -s "$AKASH_NET/version.txt")"
export AKASH_CHAIN_ID="$(curl -s "$AKASH_NET/chain-id.txt")"
export AKASH_NODE="https://akash-rpc.polkachu.com:443"  # Choose whichever node you prefer
export AKASH_HOME="$(realpath ~/.akash)"

# Set the below set of environment variables to ensure smooth operation
export AKASH_GAS=auto
export AKASH_GAS_ADJUSTMENT=1.25
export AKASH_GAS_PRICES=0.025uakt
export AKASH_SIGN_MODE=amino-json
```

After having these variables as part of your session you can start by creating an account.
```sh
provider-services keys add $AKASH_KEY_NAME
```

A minimum deposit of 5 AKT is required to deploy on Akash, and a small transaction fee is applied to deployment leases paid by the account used to deploy.
There are two ways to get funds into your account, buy tokens, and join the Akash community to receive free tokens from the Akash Faucet.