---
categories: ["Providers"]
tags: ["Helm"]
weight: 4
title: "Enable IP Leasing"
linkTitle: "Enable IP Leasing"
description: Lease Internet Protocol v4 addresses from Providers on Akash Network
---

In this guide we detail the enablement of IP Leases on a pre-existing Akash provider.

Please be aware of the following prerequisites prior to getting started:

> _**NOTE**_ - IP Leases enablement is an optional step for Akash providers.  Some providers may not have available public IP address pools and/or other requirements for enabling this feature.

## **Prerequisites**

* Provider IP Leases enablement is only supported for Akash providers built using [Helm Charts]({{< ref "build/helm.md" >}})
* Available pool of unallocated public IP addresses

## **Sections in this Guide**

* [Akash Provider Update](#akash-provider-update)
* [IP Operator](#ip-operator)
* [Create the MetalLB Namespace](#create-the-metallb-namespace)
* [MetalLB Install](#metallb-install)
* [Enable strictARP in kube-proxy](#enable-strictarp-in-kube-proxy)
* [Additional notes on the IP Operator](#additional-notes-on-the-ip-operator)

## **Akash Provider Update**

Update your pre-existing Akash provider to support IP Leases.

### Attribute Update

Update your provider to advertise the following attribute.  This attribute can be used (by users deploying on Akash) to select providers supporting the IP Lease.

```
- key: ip-lease
  value: true
```

### Command Template

```
helm upgrade akash-provider akash/provider -n akash-services -f provider.yaml --set ipoperator=true
```

### Expected/Example Output

```
root@node1:~/provider# helm upgrade akash-provider akash/provider -n akash-services -f provider.yaml --set ipoperator=true
Release "akash-provider" has been upgraded. Happy Helming!
NAME: akash-provider
LAST DEPLOYED: Wed Aug 10 20:35:10 2022
NAMESPACE: akash-services
STATUS: deployed
REVISION: 2
TEST SUITE: None
```

## **IP Operator**

Create the necessary IP Operator for IP Leases provider enablement.

### Command Template

* Replace `<provider-address>` with the address of your provider

```sh
helm install akash-ip-operator akash/akash-ip-operator -n akash-services --set provider_address=<provider-address>
```

### Example Command

```sh
helm install akash-ip-operator akash/akash-ip-operator -n akash-services --set provider_address=akash1hwmenz63dp59uve5ytea09suwgr47y3rn9902y
```

### Example Output

```sh
helm install akash-ip-operator akash/akash-ip-operator -n akash-services --set provider_address=akash1hwmenz63dp59uve5ytea09suwgr47y3rn9902y

NAME: akash-ip-operator
LAST DEPLOYED: Wed Aug 10 20:43:38 2022
NAMESPACE: akash-services
STATUS: deployed
REVISION: 1
TEST SUITE: None
```

## **Create the MetalLB Namespace**

Issue the following command to create the necessary MetalLB namespace:

```
kubectl create ns metallb-system
```

## **MetalLB Install**

In this guide we present paths to install MetalLB both via Helm Charts and Kubespray.  Please follow only the path applicable, ideal to your environment.

Sections within this guide:

* [New MetalLB Deployment via Helm](#option-1-deploy-metallb-with-helm)
* [New MetalLB Deployment via Kubespray](#option-2-deploy-metallb-using-kubespray)
* [Migration of MetalLB Version 0.12.X to 0.13.x](#migrating-metallb-0.12.x-to-0.13.x)

### Option 1: Deploy MetalLB with Helm

> _**NOTE**_ - If you plan to upgrade your MetalLB Helm-Chart in the future, ensure the [metallb release notes](https://metallb.universe.tf/release-notes/) are followed.

```sh
helm repo add metallb https://metallb.github.io/metallb

helm -n metallb-system install metallb metallb/metallb --version 0.13.7
```

### Expose your MetalLB Controller to the Akash IP Operator

```sh
kubectl -n metallb-system expose deployment metallb-controller --name=controller --overrides='{"spec":{"ports":[{"protocol":"TCP","name":"monitoring","port":7472}]}}'
```

### Apply your MetalLB Config

> _**NOTE**_ - make sure you replace the example addresses with your IP ranges

```sh
cat <<EOF | kubectl apply -f -
apiVersion: metallb.io/v1beta1
kind: IPAddressPool
metadata:
  name: default
  namespace: metallb-system
spec:
  addresses:
  - 144.217.30.192/28
  - 198.50.185.112/28
  - 66.70.218.96/28
  - 194.28.98.217/32
  - 194.28.98.219-194.28.98.222
  autoAssign: true
  avoidBuggyIPs: false
EOF
```

> If you are using MetalLB with a kubernetes version that enforces [Pod Security Admission](https://kubernetes.io/docs/concepts/security/pod-security-admission/) (which is beta in k8s 1.23), make sure to label the metallb-system namespace with the following labels:

```yaml
  labels:
    pod-security.kubernetes.io/enforce: privileged
    pod-security.kubernetes.io/audit: privileged
    pod-security.kubernetes.io/warn: privileged
```

## Option 2: Deploy MetalLB using Kubespray

Based on MetalLB via Kubespray guidance documented [here](https://github.com/kubernetes-sigs/kubespray/blob/v2.20.0/docs/metallb.md)

The Kubespray flags provided bellow should go into your Provider's Kubespray inventory file and under the vars section.  Our reference Provider Kubespray inventory file - used during initial Provider Kubernetes cluster build.

```yaml
# akash provider needs metallb pool name set to `default` - https://github.com/ovrclk/provider-services/blob/v0.1.0-rc13/cluster/kube/metallb/client.go#L43
metallb_pool_name: default
metallb_enabled: true
metallb_speaker_enabled: true
#metallb_avoid_buggy_ips: true
metallb_protocol: layer2
kube_proxy_strict_arp: true

# set your IP ranges here
metallb_ip_range:
  - 144.217.30.192/28
  - 198.50.185.112/28
  - 66.70.218.96/28
```

### Expose your MetalLB Controller to the Akash IP Operator

* Kubespray your cluster with this config and then expose your MetalLB controller for the Akash IP Operator access it

```sh
kubectl -n metallb-system expose deployment controller --overrides='{"spec":{"ports":[{"protocol":"TCP","name":"monitoring","port":7472}]}}'
```

### Migrating MetalLB 0.12.X to 0.13.X

> _**Perform the following only if you have upgraded your MetalLB from 0.12 (or prior) to 0.13 (or higher) version.**_

> Based on [https://metallb.universe.tf/configuration/migration\_to\_crds/](https://metallb.universe.tf/configuration/migration\_to\_crds/)

1\). Save the old v0.12 configmap-based metallb config to a `config.yaml` file

```sh
kubectl -n metallb-system get cm config -o yaml > config.yaml
```

2\). Use this command to migrate it to v0.13 (CRD-based format) - (`resources.yaml`)

```sh
docker run -d -v $(pwd):/var/input quay.io/metallb/configmaptocrs
```

This will produce a new config file - `resources.yaml`.

3\). Apply the new config

```sh
kubectl apply -f resources.yaml
```

4\). Remove the old config

```sh
kubectl -n metallb-system delete cm config
```

#### Example of Legacy v0.12 Metallb Configmap Based Config

* `config.yaml`

```yaml
apiVersion: v1
data:
  config: |
    address-pools:
    - name: default
      protocol: layer2
      addresses:
      - 194.28.98.216/29
kind: ConfigMap
metadata:
  annotations:
    kubectl.kubernetes.io/last-applied-configuration: |
      {"apiVersion":"v1","data":{"config":"address-pools:\n- name: default\n  protocol: layer2\n  addresses:\n  - 194.28.98.216/29\n"},"kind":"ConfigMap","metadata":{"annotations":{},"name":"config","namespace":"metallb-system"}}
  creationTimestamp: "2023-02-20T10:35:36Z"
  name: config
  namespace: metallb-system
  resourceVersion: "150026"
  uid: fd35767f-2f35-4792-b4fd-01b1d9ce2cb8
```

#### Example of New metallb v0.13 CRD Based Config

* `resources.yaml`

```yaml
# This was autogenerated by MetalLB's custom resource generator.
apiVersion: metallb.io/v1beta1
kind: IPAddressPool
metadata:
  creationTimestamp: null
  name: default
  namespace: metallb-system
spec:
  addresses:
  - 194.28.98.216/29
status: {}
---
apiVersion: metallb.io/v1beta1
kind: L2Advertisement
metadata:
  creationTimestamp: null
  name: l2advertisement1
  namespace: metallb-system
spec:
  ipAddressPools:
  - default
status: {}
---
```

## **Enable strictARP in kube-proxy**

If you’re using kube-proxy in IPVS mode, since Kubernetes v1.14.2 you have to enable strict ARP mode.

> _**NOTE**_ - this is not needed if you’re using kube-router as service-proxy because it is enabling strict ARP by default.

Achieve this by patching your kube-proxy config in current cluster:

```sh
# see what changes would be made, returns nonzero returncode if different
kubectl get configmap kube-proxy -n kube-system -o yaml | \
sed -e "s/strictARP: false/strictARP: true/" | \
kubectl diff -f - -n kube-system

# actually apply the changes, returns nonzero returncode on errors only
kubectl get configmap kube-proxy -n kube-system -o yaml | \
sed -e "s/strictARP: false/strictARP: true/" | \
kubectl apply -f - -n kube-system
```

* If using kubespray for your cluster deployment, make sure to add the following variable:

```sh
kube_proxy_strict_arp: true
```

## **Additional notes on the IP Operator**

If running non-Helm-based Akash Provider, then make sure to set the following&#x20;

```sh
AKASH_IP_OPERATOR=true
```

* Alternatively this could be passed in via CLI argument

```sh
provider-services run --ip-operator=true
```

* Additional ensure that the Akash provider IP operator is running (`provider-services ip-operator`)

