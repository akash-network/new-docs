---
categories: ["Providers"]
tags: ["Kubernetes", "Helm"]
weight: 1
title: "Build using Helm"
linkTitle: "Build using Helm"
---

## 1. Kubernetes Configurations

Create Provider namespaces on your Kubernetes cluster.

Run these commands from a Kubernetes master node which has kubectl access to cluster.

```bash
kubectl create ns akash-services
kubectl label ns akash-services akash.network/name=akash-services akash.network=true

kubectl create ns ingress-nginx
kubectl label ns ingress-nginx app.kubernetes.io/name=ingress-nginx app.kubernetes.io/instance=ingress-nginx

kubectl create ns lease
kubectl label ns lease akash.network=true
```

## 2. Export Provider Wallet

In this section we will export the pre-existing, funded wallet to store the private key in a local file.  To conduct the commands in this section the Akash CLI must be installed which is detailed in this [guide ](../../../guides/cli/detailed-steps/)(STEP 1 only).

The wallet used will be used for the following purposes:

* Pay for provider transaction gas fees
* Pay for bid collateral which is discussed further in this section

{{% alert title="Important" color="info" %}}
Make sure to create a new Akash account for the provider and do not reuse an account used for deployment purposes.  Bids will not be generated from your provider if the deployment orders are created with the same key as the provider.
{{% /alert %}}

### List Available Keys

* Print the key names available in the local OS keychain for use in the subsequent step

```
provider-services keys list
```

Example output:

```yaml
- name: ""
  type: local
  address: akash1<redacted>
  pubkey: '{"@type":"/cosmos.crypto.secp256k1.PubKey","key":"<redacted>"}'
  mnemonic: ""
- name: mykey
  type: local
  address: akash1<redacted>
  pubkey: '{"@type":"/cosmos.crypto.secp256k1.PubKey","key":"<redacted>"}'
  mnemonic: ""
```

### Export Private Key to Local File

* The key-name can be any name of your choice
* Note the passphrase used to protect the private key as it will be used in future steps

{{% alert title="Note" color="info" %}}
The passhprase MUST be at least 8 characters long. Otherwise provider will encounter `failed to decrypt private key: ciphertext decryption failed error` when `keys import` is executed.
{{% /alert %}}

#### Export Provider Key

```
cd ~

provider-services keys export mykey1
```

Example output:

```
Enter passphrase to encrypt the exported key:                          
Enter keyring passphrase:                                              
-----BEGIN TENDERMINT PRIVATE KEY-----
kdf: bcrypt
salt: REDACTED
type: secp256k1

REDACTED
-----END TENDERMINT PRIVATE KEY-----
```

#### Create key.pem and Copy Output Into File

* Copy the contents of the prior step into the `key.pem` file

{{% alert title="Note" color="info" %}}
File should contain only what's between `-----BEGIN TENDERMINT PRIVATE KEY-----` and  `-----END TENDERMINT PRIVATE KEY-----` (including the `BEGIN` and `END` lines):
{{% /alert %}}

Expected file contents

```
-----BEGIN TENDERMINT PRIVATE KEY-----
kdf: bcrypt
salt: REDACTED
type: secp256k1

REDACTED
-----END TENDERMINT PRIVATE KEY-----
```

## 4. Installing Helm

Install Helm on a Kubernetes Master Node or the host machine from where you are accessing the cluster.
You can check the [installation guide](https://helm.sh/docs/intro/install/).
After installing Helm, add the akash helm repository to your system.
```bash
# Remove any potential prior repo instances
helm repo remove akash
helm repo add akash https://akash-network.github.io/helm-charts
```

## 5. Domain Name Review

Add DNS (type A) records for your Akash Provider related domains on your DNS hosting provider.

### Akash Provider Domain Records

* Replace yourdomain.com with your own domain name
* DNS (type A) records should point to public IP address of a single Kubernetes worker node of your choice

```
*.ingress.yourdomain.com

provider.yourdomain.com
```

{{% alert title="Warning" color="warning" %}}
Do not use Cloudflare or any other TLS proxy solution for your Provider DNS A records.
{{% /alert %}}

Instead of the multiple DNS A records for worker nodes, consider using CNAME DNS records such as the example provided below.
CNAME use allows ease of management and introduces higher availability.

> `*.ingress 300 IN CNAME nodes.yourdomain.com.`\
> `nodes 300 IN A x.x.x.x`\
> `nodes 300 IN A x.x.x.x`\
> `nodes 300 IN A x.x.x.x`\
> `provider 300 IN CNAME nodes.yourdomain.com.`

## 6. Provider Build via Helm Chart

In this section the Akash Provider will be installed and customized via the use of Helm Charts.

{{% alert title="Note" color="info" %}}
When the Helm Chart is installed the Provider instance/details will be created on the blockchain and your provider will be registered in the Akash open cloud marketplace.
The associated transaction for Provider creation is detailed [here](https://github.com/ovrclk/docs/blob/master/cli/akash\_tx\_provider\_create.md).
{{% /alert %}}

### Environment Variables

* Declare the following environment variables for Helm to use
* Replace the variables with your own settings

```bash
# Set akash provider address that starts with 'akash1'.
# This allows the akash-provider to decrypt the key.
export ACCOUNT_ADDRESS=akash1XXXX

# Set the password you have entered upon akash keys export > key.pem
export KEY_PASSWORD=12341234

# Set your domain. Register DNS A and wildcard address as specified in previous step, i.e. `provider.yourdomain.com` DNS A record and `*.ingress.yourdomain.com` DNS wildcard record.
export DOMAIN=yourdomain.com

# Set the Akash RPC node for your provider to use.
export NODE=http://akash-node-1:26657
```

{{% alert title="Warning" color="warning" %}}
Domain should be a publicly accessible DNS name dedicated for your provider use such as yourdomain.com.
The domain specified in this variable will be used by Helm during the Provider chart install process to produce the `provider.yourdomain.com` sub-domain name and the `ingress.yourdomain.com` sub-domain name.
The domain specified will also be used by Helm during the Ingress Controller install steps coming up in this guide.
Once your provider is up and running the `*.ingress.yourdomain.com` URI will be used for web app deployments such as `abc123.ingress.yourdomain.com`.
{{% /alert %}}

{{% alert title="Note" color="info" %}}
If you are going to deploy Akash RPC Node using Helm-Charts then set the node to http://akash-node-1:26657 It is recommended that you install your own Akash RPC node.
Follow [this guide](../../../akash-nodes/akash-node-via-helm-charts/) to do so.
{{% /alert %}}

### Provider Withdraw Period

Akash providers may dictate how often they withdraw funds consumed by active deployments/tenants escrow accounts

Few things to consider regarding the provider withdraw period:
* The default withdraw setting in the Helm Charts is one (1) hour
* An advantage of the one hour default setting is assurance that a deployment may not breach the escrow account dramatically. If the withdraw period were set to 12 hours instead - the deployment could exhaust the amount in escrow in one hour (for example) but the provider would not calculate this until many hours later and the deployment would essentially operate for free in the interim.
* A disadvantage of frequent withdraws is the possibility of losing profitability based on fees incurred by the providers withdraw transactions. If the provider hosts primarily low resource workloads, it is very possible that fees could exceed deployment cost/profit.

#### OPTIONAL - Update the Provider Withdraw Period

If it is desired to change the withdrawal period from the default one hour setting, update the `withdrawalperiod` setting in the provider.yaml file created subsequently in this section.
In the example the Provider Build section of this doc the withdrawal period has been set to 12 hours.  Please adjust as preferred.

### Provider Build Prep

Ensure you are applying the latest version of subsequent Helm Charts install/upgrade steps
```
helm repo update
```

### Create a provider.yaml File

Issue the following command to build your Akash Provider and update the following keys for your unique use case:
* `region`
* `organization`

Optional Parameters - the following parameters may be added at the same level as `from` and `key` if you which to advertise your support email address and company website URL.
* `email`
* `website`

```
cd ~

mkdir provider

cd provider

cat > provider.yaml << EOF
---
from: "$ACCOUNT_ADDRESS"
key: "$(cat ~/key.pem | openssl base64 -A)"
keysecret: "$(echo $KEY_PASSWORD | openssl base64 -A)"
domain: "$DOMAIN"
node: "$NODE"
withdrawalperiod: 12h
attributes:
- key: region
  value: "<YOUR REGION>"   # set your region here, e.g. "us-west"
- key: host
  value: akash
- key: tier
  value: community
- key: organization
  value: "<YOUR ORG>"      # set your organization name here
EOF
```

#### Verification of provider.yaml File
Issue the following commands to verify the `provider.yaml` file created in previous steps

```
cd ~/provider

cat provider.yaml
```

### Provider Bid Defaults

When a provider is created the default bid engine settings are used.
If desired these settings could be updated and added to the `provider.yaml` file.
But we would recommend initially using the default values.

{{% alert title="Note" color="info" %}}
The `bidpricestoragescale` value will get overridden by `-f provider-storage.yaml` covered in [Provider Persistent Storage](../helm-based-provider-persistent-storage-enablement/) documentation.
{{% /alert %}}

{{% alert title="Note" color="info" %}}
If you want to use a shellScript bid price strategy, pass the bid price script via `bidpricescript` variable detailed in the [bid pricing script doc](../akash-provider-bid-pricing/).
This will automatically suppress all `bidprice<cpu|memory|endpoint|storage>scale` settings.
{{% /alert %}}

```
bidpricecpuscale: "0.004" # cpu pricing scale in uakt per millicpu
bidpricememoryscale: "0.0016" # memory pricing scale in uakt per megabyte
bidpriceendpointscale: "0" # endpoint pricing scale in uakt per endpoint
bidpricestoragescale: "0.00016" # storage pricing scale in uakt per megabyte
```

### Install the Provider Helm Chart

```bash
helm install akash-provider akash/provider -n akash-services -f provider.yaml
```
Expected output of Provider Helm install:
```
NAME: akash-provider
LAST DEPLOYED: Thu Apr 28 18:58:10 2022
NAMESPACE: akash-services
STATUS: deployed
REVISION: 1
TEST SUITE: None
```

Verify the provider is successfully running by executing the following command:
```bash
kubectl get pods -n akash-services
```
You should expect a Pod running similar to:
```
root@node1:~# kubectl get pods -n akash-services

NAME                              READY   STATUS    RESTARTS   AGE
akash-provider-6d7c455dfb-qkf5z   1/1     Running   0          4m37s
```

{{% alert title="Troubleshoot" color="danger" %}}
If your Akash Provider pod status displays `init:0/1` for a prolonged period of time, use the following command to view Init container logs.
Often the Provider may have a RPC issue and this should be revealed in these logs.
RPC issues may be caused by an incorrect declaration in the NODE variable declaration issued previously in this section.
Or possibly your custom RPC node is not in sync.
```
kubectl -n akash-services logs -l app=akash-provider -c init --tail 200 -f
```
{{% /alert %}}

### Helm Chart Uninstall Process

Should a need arise to uninstall the Helm Chart and attempt the process anew, the following step can be used.
Only conduct this step if there is a problem with Akash Provider Helm Chart install.
This Helm uninstall technique can be used for this or any subsequent chart installs.
Following this step - if needed - start the Provider Helm Chart install anew via the prior step in this page.

```bash
helm uninstall akash-provider -n akash-services
```

## 7. (Optional) Provider Bid Customization

If there is a desire to manipulate the provider bid engine, include the `--set bidpricescript` switch.
The pricing script used in this switch is detailed in the [Akash Provider Bid Pricing](../akash-provider-bid-pricing/) section of this guide.
Edit the `price_script_generic.sh` file detailed in [Akash Provider Bid Pricing](../akash-provider-bid-pricing/) with preferred resource pricing level

{{% alert title="Note" color="info" %}}
When the provider deployment is created the bid script should return the price in under 5 seconds.
If the script does not execute in this time period the following error message will be seen in the provider pod logs.
Such a report would suggest that there is an error/issue with script customizations that should be reviewed.
Following review and adjustment, uninstall the provider deployment (via helm uninstall) and reinstall.
{{% /alert %}}

Example Bid/Price Script syntax:
```
cd provider

wget https://raw.githubusercontent.com/akash-network/helm-charts/main/charts/akash-provider/scripts/price_script_generic.sh

# adjust target prices to your needs in price_script_generic.sh file
helm upgrade --install akash-provider akash/provider -n akash-services -f provider.yaml --set bidpricescript="$(cat price_script_generic.sh | openssl base64 -A)"
```

## 8. Hostname Operator

Run the following command to build the Kubernetes Hostname Operator

{{% alert title="Note" color="info" %}}
If a need arises to use a different software version other than the one defined in the Chart.yaml Helm file - include the following switch.
In most circumstances this should not be necessary.
* `--set image.tag=<image-name>`
* Example: `--set image.tag=0.1.0`
{{% /alert %}}

```
helm install akash-hostname-operator akash/akash-hostname-operator -n akash-services
```

Expected output:
```
NAME: akash-hostname-operator
LAST DEPLOYED: Thu Apr 28 19:06:30 2022
NAMESPACE: akash-services
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
1. Get the application URL by running these commands:
export POD_NAME=$(kubectl get pods --namespace akash-services -l "app.kubernetes.io/name=hostname-operator,app.kubernetes.io/instance=hostname-operator" -o jsonpath="{.items[0].metadata.name}")
export CONTAINER_PORT=$(kubectl get pod --namespace akash-services $POD_NAME -o jsonpath="{.spec.containers[0].ports[0].containerPort}")
echo "Visit http://127.0.0.1:8080 to use your application"
kubectl --namespace akash-services port-forward $POD_NAME 8080:$CONTAINER_PORT
```

Confirm the operator is running successfully by running `kubectl get pods -n akash-services`.

## 9. Ingress Controller

To allow traffic to your provider you need to install an Ingress Controller.
Akash providers use NGINX Ingress Controller by default.

```
helm install akash-ingress akash/akash-ingress -n ingress-nginx --set domain=$DOMAIN
```

You can enhance your provider by enabling monitoring.
**Quasarch** has a [guide](http://www.quasarch.cloud/blog/akash-provider-ingress-observability) on how you can integrate your Ingress Controller with Prometheus and Grafana to deploy a sophisticated observability stack.

{{% alert title="Note" color="info" %}}
The following firewall rules are applicable to internet-facing Kubernetes components.
### Akash Provider
```
8443/tcp - for manifest uploads
```

### Akash Ingress Controller
```
80/tcp - for web app deployments
443/tcp - for web app deployments
30000-32767/tcp - for Kubernetes node port range for deployments
30000-32767/udp - for Kubernetes node port range for deployments
```
{{% /alert %}}