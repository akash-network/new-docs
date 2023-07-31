---
categories: ["Guides"]
tags: ["CLI"]
weight: 5
title: "Deploy using the CLI"
linkTitle: "Deploy using the CLI"
description: "Here's a step-by-step guide to deploy an application on the Akash Network using the Command Line Interface (CLI)."
---

This guide will walk you through installing the Akash CLI, creating and funding an account on an Akash network, and deploying a single-tier web application.

The  command-line client is used to interact with deployments. The `provider-services` command prefix and additional command syntax covered in this guide are utilized to query, launch, and update your applications.

Here's the list of steps we will be doing:
1. [Install Akash CLI](#install-the-akash-cli)
2. [Create an account](#create-an-account) 
3. [Fund your account](#fund-your-account)
4. [Configure your network](#configure-your-network) 
5. [Create your configuration](#create-your-configuration)
6. [Create your certificate](#create-your-certificate)
7. [Create your deployment](#create-your-deployment)
8. [View bids from providers](#view-your-bids)
9. [Create a lease](#create-a-lease)
10. [Send the manifest](#send-the-manifest)
11. [Update the deployment](#update-the-deployment)
12. [Close the deployment](#close-deployment)

## **Install the Akash CLI**

Select a tab below to view instructions for MacOS, Linux, or compiling from source.

{{< tabpane text=true >}} 
{{% tab "MacOS"  %}}
### MacOS

The simplest way to install Akash is using Homebrew using:

```bash
brew untap ovrclk/tap 
brew tap akash-network/tap
brew install akash-provider-services
```

If you do not have homebrew, follow the below steps for installing the Akash Binary.

#### Download Akash Binary

These commands will retrieve the latest, stable version of the Akash software, store the version in a local variable, and install that version.

```bash
cd ~/Downloads

#NOTE that this download may take several minutes to complete
curl -sfL https://raw.githubusercontent.com/akash-network/provider/main/install.sh | bash
```

#### Move the Akash Binary

Move the binary file into a directory included in your path

```bash
sudo mv ./bin/provider-services /usr/local/bin
```

#### Verify Akash Installation

Verify the installation by using a simple command to check the Akash version

```bash
provider-services version
```

Expect/Example Output

```bash
v0.2.0
```
{{% /tab %}}
{{% tab "Linux"  %}}
The simplest way to install Akash is using Homebrew using:

```bash
brew untap ovrclk/tap
brew tap akash-network/tap
brew install akash-provider-services
```

If you do not have homebrew, follow the below steps for installing the Akash Binary.

#### Download Akash Binary

These commands will retrieve the latest, stable version of the Akash software\_**,**\_ store the version in a local variable, and install that version.

```bash
cd ~

apt install jq -y

apt install unzip -y

curl -sfL https://raw.githubusercontent.com/akash-network/provider/main/install.sh | bash
```

#### Add Akash Install Location to User’s Path

Add the software’s install location to the user’s path for easy use of Akash commands.

**NOTE:** Below we provide the steps to add the Akash install directory to a user’s path on a Linux Ubuntu server. Please take a look at a guide for your operating system and how to add a directory to a user’s path.

Open the user’s path file in an editor:

```bash
vi /etc/environment
```

View within text editor prior to the update:

```bash
PATH="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin"
```

Add the following directory, which is the Akash install location, to `PATH`. In this example the active user is root. If logged in as another username, replace /root with your current/home directory.

```bash
/root/bin
```

View within the text editor following the update:

```bash
PATH="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin:/root/bin"
```

### Make the Path Active in the Current Session

```bash
. /etc/environment
```

### Verify Akash Install

Display the version of Akash software installed. This confirms the software installed and that the new user path addition worked.

```bash
provider-services version
```

Expected/Example Result

```bash
v0.2.0
```
{{< /tab >}}
{{% tab "Source"  %}}
### From Source

Installing Akash suite from source:

```bash
$ go get -d github.com/akash-network/provider
$ cd $GOPATH/src/github.com/akash-network/provider
$ AKASH_NET="https://raw.githubusercontent.com/ovrclk/net/master/mainnet"
$ AKASH_VERSION="$(curl -s https://api.github.com/repos/akash-network/provider/releases/latest | jq -r '.tag_name')"
$ git checkout "v$AKASH_VERSION"
$ make deps-install
$ make install
```

Akash is developed and tested with [golang 1.16+](https://golang.org/). Building requires a working [golang](https://golang.org/) installation, a properly set `GOPATH`, and `$GOPATH/bin` present in `$PATH`.

Once you have the dependencies properly setup, download and build `akash` using `make install`
{{< /tab >}}
{{< /tabpane >}}

## **Create an Account**

Configure the name of your key. The command below will set the name of your key to `myWallet`, run the below command and replace `myWallet` with a name of your choice:

```bash
AKASH_KEY_NAME=myWallet
```

Verify you have the shell variables set up . The below command should return the name you've used:

```bash
echo $AKASH_KEY_NAME
```

We now need to point Akash to where the keys are stored for your configuration. To do this we will set the AKASH\_KEYRING\_BACKEND environmental variable.

```bash
AKASH_KEYRING_BACKEND=os
```

Copy and paste this command into Terminal to create an Akash account:

```bash
provider-services keys add $AKASH_KEY_NAME
```

Read the output and save your mnemonic phrase is a safe place. Let's set a Shell Variable in Terminal `AKASH_ACCOUNT_ADDRESS` to save your account address for later.

```bash
export AKASH_ACCOUNT_ADDRESS="$(provider-services keys show $AKASH_KEY_NAME -a)"

echo $AKASH_ACCOUNT_ADDRESS
```

Note that if you close your Terminal window this variable will not be saved.

## **Fund your Account**

A minimum deposit of 5 AKT is required to deploy on Akash, and a small transaction fee is applied to deployment leases paid by the account used to deploy. There are two ways to get funds into your account, buy tokens, and join the Akash community to receive free tokens from the Akash Faucet.

## **Configure your Network**

First configure the base URL (`$AKASH_NET`) for the Akash Network; copy and paste the command below:

```bash
AKASH_NET="https://raw.githubusercontent.com/akash-network/net/master/mainnet"
```

### Version

Next configure the version of the Akash Network `AKASH_VERSION`; copy and paste the command below:

```bash
AKASH_VERSION="$(curl -s https://api.github.com/repos/akash-network/provider/releases/latest | jq -r '.tag_name')"
```

### Chain ID

The akash CLI will recogonize `AKASH_CHAIN_ID` environment variable when exported to the shell.

```bash
export AKASH_CHAIN_ID="$(curl -s "$AKASH_NET/chain-id.txt")"
```

### Network Node

You need to select a node on the network to connect to, using an RPC endpoint. To configure the`AKASH_NODE` environment variable use this export command:

```bash
export AKASH_NODE="$(curl -s "$AKASH_NET/rpc-nodes.txt" | shuf -n 1)"
```

### Confirm your network variables are set up

Your values may differ depending on the network you're connecting to.

```bash
echo $AKASH_NODE $AKASH_CHAIN_ID $AKASH_KEYRING_BACKEND
```

You should see something similar to:

`http://135.181.60.250:26657 akashnet-2 os`

### Set Additional Environment Variables

Set the below set of environment variables to ensure smooth operations

| Variable               | Description                                                                               | Recommended Value |
| ---------------------- | ----------------------------------------------------------------------------------------- | ----------------- |
| AKASH\_GAS             | Gas limit to set per-transaction; set to "auto" to calculate sufficient gas automatically | `auto`            |
| AKASH\_GAS\_ADJUSTMENT | Adjustment factor to be multiplied against the estimate returned by the tx simulation     | `1.15`            |
| AKASH\_GAS\_PRICES     | Gas prices in decimal format to determine the transaction fee                             | `0.025uakt`       |
| AKASH\_SIGN\_MODE      | Signature mode                                                                            | `amino-json`      |

```
export AKASH_GAS=auto
export AKASH_GAS_ADJUSTMENT=1.25
export AKASH_GAS_PRICES=0.025uakt
export AKASH_SIGN_MODE=amino-json
```

### Check your Account Balance

Check your account has sufficient balance by running:

```bash
provider-services query bank balances --node $AKASH_NODE $AKASH_ACCOUNT_ADDRESS
```

You should see a response similar to:

```
balances:
- amount: "93000637"
  denom: uakt
pagination:
  next_key: null
  total: "0"
```

If you don't have a balance, please see the [funding guide](https://github.com/ovrclk/docs/tree/b65f668b212ad1976fb976ad84a9104a9af29770/guides/wallet/funding.md). Please note the balance indicated is denominated in uAKT (AKT x 10^-6), in the above example, the account has a balance of _93 AKT_. We're now setup to deploy.

{{% alert color="info" %}}
Your account must have a minimum balance of 5 AKT to create a deployment. This 5 AKT funds the escrow account associated with the deployment and is used to pay the provider for their services. It is recommended you have more than this minimum balance to pay for transaction fees. For more information on escrow accounts, see [here](https://github.com/ovrclk/docs/blob/master/guides/cli/detailed-steps/broken-reference/README.md)
{{% /alert %}}

## **Create your Configuration**

Create a deployment configuration [deploy.yaml](https://github.com/ovrclk/docs/tree/b65f668b212ad1976fb976ad84a9104a9af29770/guides/deploy/deploy.yml) to deploy the `ovrclk/lunie-light` for [Lunie Light](https://github.com/ovrclk/lunie-light) Node app container using [SDL](https://github.com/ovrclk/docs/tree/b65f668b212ad1976fb976ad84a9104a9af29770/sdl/README.md).

You can use cURL to download the file:

```
curl -s https://raw.githubusercontent.com/ovrclk/docs/master/guides/deploy/deploy.yml > deploy.yml
```

### Modify your Configuration

You may use the sample deployment file as-is or modify it for your own needs as described in our [SDL (Stack Definition Language)](https://github.com/ovrclk/docs/blob/master/sdl/README.md) documentation. A typical modification would be to reference your own image instead of our demo app image.

#### EXAMPLE CONFIGURATION:

```bash
cat > deploy.yaml <<EOF
---
version: "2.0"

services:
  web:
    image: ovrclk/lunie-light
    expose:
      - port: 3000
        as: 80
        to:
          - global: true

profiles:
  compute:
    web:
      resources:
        cpu:
          units: 0.1
        memory:
          size: 512Mi
        storage:
          size: 512Mi
  placement:
    westcoast:
      attributes:
        host: akash
      signedBy:
        anyOf:
          - "akash1365yvmc4s7awdyj3n2sav7xfx76adc6dnmlx63"
      pricing:
        web: 
          denom: uakt
          amount: 1000

deployment:
  web:
    westcoast:
      profile: web
      count: 1

EOF
```

## **Create your Certificate**

In this step we will create a local certificate and then store the certification on the blockchain

* NOTE - for those familiar with previous Akash CLI versions the following commands for cert creation have changed. We believe the new command sets in this version make steps more clear.
* Ensure that prior steps in this guide have been completed and that you have a funded wallet before attempting certificate creation.
* **Your certificate needs to be created only once per account** and can be used across all deployments.

#### Generate Cert

* Note: If it errors with `Error: certificate error: cannot overwrite certificate`, then add `--overwrite` should you want to overwrite the cert. Normally you can ignore that error and proceed with publishing the cert (next step).

```bash
provider-services tx cert generate client --from $AKASH_KEY_NAME
```

#### Publish Cert to the Blockchain

```bash
provider-services tx cert publish client --from $AKASH_KEY_NAME
```

## **Create your Deployment**

### CPU Support

Only x86\_64 processors are officially supported for Akash deployments. This may change in the future and when ARM processors are supported it will be announced and documented.

### Akash Deployment

To deploy on Akash, run:

```
provider-services tx deployment create deploy.yml --from $AKASH_KEY_NAME 
```

You should see a response similar to:

```json
{
  "height":"140325",
  "txhash":"2AF4A01B9C3DE12CC4094A95E9D0474875DFE24FD088BB443238AC06E36D98EA",
  "codespace":"",
  "code":0,
  "data":"0A130A116372656174652D6465706C6F796D656E74",
  "raw_log":"[{\"events\":[{\"type\":\"akash.v1\",\"attributes\":[{\"key\":\"module\",\"value\":\"deployment\"},{\"key\":\"action\",\"value\":\"deployment-created\"},{\"key\":\"version\",\"value\":\"2b86f778de8cc9df415490efa162c58e7a0c297fbac9cdb8d6c6600eda56f17e\"},{\"key\":\"owner\",\"value\":\"akash1vn06ycjjnvsvl639fet9lajjctuturrtx7fvuj\"},{\"key\":\"dseq\",\"value\":\"140324\"},{\"key\":\"module\",\"value\":\"market\"},{\"key\":\"action\",\"value\":\"order-created\"},{\"key\":\"owner\",\"value\":\"akash1vn06ycjjnvsvl639fet9lajjctuturrtx7fvuj\"},{\"key\":\"dseq\",\"value\":\"140324\"},{\"key\":\"gseq\",\"value\":\"1\"},{\"key\":\"oseq\",\"value\":\"1\"}]},{\"type\":\"message\",\"attributes\":[{\"key\":\"action\",\"value\":\"create-deployment\"},{\"key\":\"sender\",\"value\":\"akash1vn06ycjjnvsvl639fet9lajjctuturrtx7fvuj\"},{\"key\":\"sender\",\"value\":\"akash1vn06ycjjnvsvl639fet9lajjctuturrtx7fvuj\"}]},{\"type\":\"transfer\",\"attributes\":[{\"key\":\"recipient\",\"value\":\"akash17xpfvakm2amg962yls6f84z3kell8c5lazw8j8\"},{\"key\":\"sender\",\"value\":\"akash1vn06ycjjnvsvl639fet9lajjctuturrtx7fvuj\"},{\"key\":\"amount\",\"value\":\"5000uakt\"},{\"key\":\"recipient\",\"value\":\"akash14pphss726thpwws3yc458hggufynm9x77l4l2u\"},{\"key\":\"sender\",\"value\":\"akash1vn06ycjjnvsvl639fet9lajjctuturrtx7fvuj\"},{\"key\":\"amount\",\"value\":\"5000000uakt\"}]}]}]",
  "logs":[
    {
      "msg_index":0,
      "log":"",
      "events":[
        {
          "type":"akash.v1",
          "attributes":[
            {
              "key":"module",
              "value":"deployment"
            },
            {
              "key":"action",
              "value":"deployment-created"
            },
            {
              "key":"version",
              "value":"2b86f778de8cc9df415490efa162c58e7a0c297fbac9cdb8d6c6600eda56f17e"
            },
            {
              "key":"owner",
              "value":"akash1vn06ycjjnvsvl639fet9lajjctuturrtx7fvuj"
            },
            {
              "key":"dseq",
              "value":"140324"
            },
            {
              "key":"module",
              "value":"market"
            },
            {
              "key":"action",
              "value":"order-created"
            },
            {
              "key":"owner",
              "value":"akash1vn06ycjjnvsvl639fet9lajjctuturrtx7fvuj"
            },
            {
              "key":"dseq",
              "value":"140324"
            },
            {
              "key":"gseq",
              "value":"1"
            },
            {
              "key":"oseq",
              "value":"1"
            }
          ]
        },
        {
          "type":"message",
          "attributes":[
            {
              "key":"action",
              "value":"create-deployment"
            },
            {
              "key":"sender",
              "value":"akash1vn06ycjjnvsvl639fet9lajjctuturrtx7fvuj"
            },
            {
              "key":"sender",
              "value":"akash1vn06ycjjnvsvl639fet9lajjctuturrtx7fvuj"
            }
          ]
        },
        {
          "type":"transfer",
          "attributes":[
            {
              "key":"recipient",
              "value":"akash17xpfvakm2amg962yls6f84z3kell8c5lazw8j8"
            },
            {
              "key":"sender",
              "value":"akash1vn06ycjjnvsvl639fet9lajjctuturrtx7fvuj"
            },
            {
              "key":"amount",
              "value":"5000uakt"
            },
            {
              "key":"recipient",
              "value":"akash14pphss726thpwws3yc458hggufynm9x77l4l2u"
            },
            {
              "key":"sender",
              "value":"akash1vn06ycjjnvsvl639fet9lajjctuturrtx7fvuj"
            },
            {
              "key":"amount",
              "value":"5000000uakt"
            }
          ]
        }
      ]
    }
  ],
  "info":"",
  "gas_wanted":"100000",
  "gas_used":"94653",
  "tx":null,
  "timestamp":""
}
```

### Find your Deployment

Find the Deployment Sequence (DSEQ) in the deployment you just created. You will need to replace the AKASH\_DSEQ with the number from your deployment to configure a shell variable.

```bash
export AKASH_DSEQ=CHANGETHIS
```

Now set the Order Sequence (OSEQ) and Group Sequence (GSEQ). Note that if this is your first time deploying on Akash, OSEQ and GSEQ will be 1.

```bash
AKASH_OSEQ=1
AKASH_GSEQ=1
```

Verify we have the right values populated by running:

```bash
echo $AKASH_DSEQ $AKASH_OSEQ $AKASH_GSEQ
```

## **View your Bids**

After a short time, you should see bids from providers for this deployment with the following command:

```bash
provider-services query market bid list --owner=$AKASH_ACCOUNT_ADDRESS --node $AKASH_NODE --dseq $AKASH_DSEQ --state=open
```

### Choose a Provider

Note that there are bids from multiple different providers. In this case, both providers happen to be willing to accept a price of _1 uAKT_. This means that the lease can be created using _1 uAKT_ or _0.000001 AKT_ per block to execute the container. You should see a response similar to:

```
bids:
- bid:
    bid_id:
      dseq: "140324"
      gseq: 1
      oseq: 1
      owner: akash1vn06ycjjnvsvl639fet9lajjctuturrtx7fvuj
      provider: akash10cl5rm0cqnpj45knzakpa4cnvn5amzwp4lhcal
    created_at: "140326"
    price:
      amount: "1"
      denom: uakt
    state: open
  escrow_account:
    balance:
      amount: "50000000"
      denom: uakt
    id:
      scope: bid
      xid: akash1vn06ycjjnvsvl639fet9lajjctuturrtx7fvuj/140324/1/1/akash10cl5rm0cqnpj45knzakpa4cnvn5amzwp4lhcal
    owner: akash10cl5rm0cqnpj45knzakpa4cnvn5amzwp4lhcal
    settled_at: "140326"
    state: open
    transferred:
      amount: "0"
      denom: uakt
- bid:
    bid_id:
      dseq: "140324"
      gseq: 1
      oseq: 1
      owner: akash1vn06ycjjnvsvl639fet9lajjctuturrtx7fvuj
      provider: akash1f6gmtjpx4r8qda9nxjwq26fp5mcjyqmaq5m6j7
    created_at: "140326"
    price:
      amount: "1"
      denom: uakt
    state: open
  escrow_account:
    balance:
      amount: "50000000"
      denom: uakt
    id:
      scope: bid
      xid: akash1vn06ycjjnvsvl639fet9lajjctuturrtx7fvuj/140324/1/1/akash1f6gmtjpx4r8qda9nxjwq26fp5mcjyqmaq5m6j7
    owner: akash1f6gmtjpx4r8qda9nxjwq26fp5mcjyqmaq5m6j7
    settled_at: "140326"
    state: open
    transferred:
      amount: "0"
      denom: uakt
```

For this example, we will choose `akash10cl5rm0cqnpj45knzakpa4cnvn5amzwp4lhcal` Run this command to set the provider shell variable:

```bash
AKASH_PROVIDER=akash10cl5rm0cqnpj45knzakpa4cnvn5amzwp4lhcal
```

Verify we have the right value populated by running:

```bash
echo $AKASH_PROVIDER
```

## **Create a Lease**

Create a lease for the bid from the chosen provider above by running this command:

```bash
provider-services tx market lease create --dseq $AKASH_DSEQ --provider $AKASH_PROVIDER --from $AKASH_KEY_NAME
```

### Confirm the Lease

You can check the status of your lease by running:

```bash
provider-services query market lease list --owner $AKASH_ACCOUNT_ADDRESS --node $AKASH_NODE --dseq $AKASH_DSEQ
```

Note the bids will close automatically after 5 minutes, and you may get the response:

```bash
bid not open
```

If this happens, close your deployment and open a new deployment again. To close your deployment run this command:

```bash
provider-services tx deployment close --dseq $AKASH_DSEQ  --owner $AKASH_ACCOUNT_ADDRESS --from $AKASH_KEY_NAME 
```

If your lease was successful you should see a response that ends with:

```bash
    state: active
```

{{% alert color="info" %}}
Please note that once the lease is created, the provider will begin debiting your deployment's escrow account, even if you have not completed the deployment process by uploading the manifest in the following step.
{{% /alert %}}

## **Send the Manifest**

Upload the manifest using the values from above step:

```
provider-services send-manifest deploy.yml --dseq $AKASH_DSEQ --provider $AKASH_PROVIDER --from $AKASH_KEY_NAME
```

### Confirm the URL

Now that the manifest is uploaded, your image is deployed. You can retrieve the access details by running the below:

```bash
provider-services lease-status --dseq $AKASH_DSEQ --from $AKASH_KEY_NAME --provider $AKASH_PROVIDER
```

You should see a response similar to:

```json
{
  "services": {
    "web": {
      "name": "web",
      "available": 1,
      "total": 1,
      "uris": [
        "rga3h05jetf9h3p6dbk62m19ck.ingress.ewr1p0.mainnet.akashian.io"
      ],
      "observed_generation": 1,
      "replicas": 1,
      "updated_replicas": 1,
      "ready_replicas": 1,
      "available_replicas": 1
    }
  },
  "forwarded_ports": {}
}
```

You can access the application by visiting the hostnames mapped to your deployment. Look for a URL/URI and copy it to your web browser.

### View your logs

You can view your application logs to debug issues or watch progress like so:

```bash
provider-services lease-logs \
  --dseq "$AKASH_DSEQ" \
  --provider "$AKASH_PROVIDER" \
  --from "$AKASH_KEY_NAME"
```

## **Update the Deployment**

### Update the Manifest

Update the deploy.yml manifest file with the desired change.

_**NOTE:**_\*\* Not all attributes of the manifest file are eligible for deployment update. If the hardware specs of the manifest are updated (I.e. CPU count), a re-deployment of the workload is necessary. Other attributes, such as deployment image and funding, are eligible for updates.

### Issue Transaction for On Chain Update

```bash
provider-services tx deployment update deploy.yml --dseq $AKASH_DSEQ --from $AKASH_KEY_NAME 
```

### Send Updated Manifest to Provider

```bash
provider-services send-manifest deploy.yml --dseq $AKASH_DSEQ --provider $AKASH_PROVIDER --from $AKASH_KEY_NAME
```

## **Close Deployment**

### Close the Deployment

Should you need to close the deployment, follow this step.

```bash
provider-services tx deployment close --from $AKASH_KEY_NAME
```

