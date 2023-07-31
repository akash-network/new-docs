---
title: "Your First Deployment"
linkTitle: "Your First Deployment"
categories: ["Getting Started", "Guides"]
weight: 2
---

To deploy on the Akash Network you will need to create a local certificate and then store the certification on the blockchain.
To do this run the following commands:
```bash
provider-services tx cert generate client --from $AKASH_KEY_NAME
provider-services tx cert publish client --from $AKASH_KEY_NAME
```
{{% alert title="Warning" color="warning" %}}
Ensure that prior steps in this guide have been completed and that you have a funded wallet before attempting certificate creation.
Your certificate needs to be created only once per account and can be used across all deployments.
{{% /alert %}}

{{% alert title="Note" color="info" %}}
If you run into `Error: certificate error: cannot overwrite certificate`, then add `--overwrite` should you want to overwrite the cert.
Normally you can ignore that error and proceed with publishing the cert.
{{% /alert %}}

You are now ready to create your first deployment on Akash Network.
The deployment process can be split into 3 sub-processes.
Those are, **deployment creation**, **lease creation** and **manifest submission**.

## Deployment Creation
We will download a premade deployment file from the official repository and create our deployment from there.
```bash
curl -s https://raw.githubusercontent.com/ovrclk/docs/master/guides/deploy/deploy.yml > deploy.yml

# Create a deployment from deploy.yml downloaded previously. Use jq to store the DSEQ from the output.
DEPLOYMENT_DSEQ=$(provider-services tx deployment create deploy.yml --from $AKASH_KEY_NAME | jq -c '.logs[].events[].attributes | unique[] | select(.key == "dseq").value')
```

## Lease Creation

The first step in creating a lease is to get the list of open bids on the previous deployment.
Once you have that list you can choose your preferred provider where you'll send your workload to.
You can choose your provider based on an unilimited amount of attributes. These include, but are not limited to, `region`, `audited`, `cpu`, CPU architecture and network speed.
```bash
# Use the DSEQ to query for bids on the deployment.
provider-services query market bid list --owner=$AKASH_ACCOUNT_ADDRESS --node $AKASH_NODE --dseq $DEPLOYMENT_DSEQ --state=open

```