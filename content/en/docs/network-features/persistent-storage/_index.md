---
categories: ["Concepts"]
tags: []
title: "Persistent Storage"
linkTitle: "Persistent Storage"
---

Akash persistent storage allows deployment data to persist through the lifetime of a lease.
The provider creates a volume on disk that is mounted into the deployment.
This functionality closely mimics typical container persistent storage.

## Limitations

**Persistent storage only persists during the lease.**
The storage is lost when:

* The deployment is migrated to a different provider.
* The deployment’s lease is closed. Even when relaunched onto the same provider, storage will not persist across leases.
* Shared volumes are not currently supported. If a SDL defines a single profile with a persistent storage definition - and that profile is then used by multiple services - individual, unique volumes will be created per service.

{{% alert title="Note" color="info" %}}
When planning to use persistent storage in a deployment, take into account the network (between the storage nodes) as a factor which will cause the latency, causing slower disk throughput / IOPS.
This might not be suitable for heavy IOPS applications such as a Solana validator.
{{% /alert %}}
## Deployment Specifications

Note that currently only a single persistent volume is allowed/supported per service definition in the Akash SDL.
It is not possible to mount multiple persistent volumes in a service.

## Troubleshooting

### Possible Deployment Issues and Recommendations

#### Provider Slow or Over Utilized disks

_Issue_  - Slow/over utilized disks OR networking issue impacting distributed storage system (Ceph)&#x20;

_Solution_ -  always use providers with beta3 class fast storage and change to a new provider if you experience issues

#### Persistent Storage for Deployment Full

_Issue_ - persistent storage allocated to the deployment reaches capacity

_Solution_ - either use fast ephemeral storage so the pod will automatically restart once it gets full or allocate more disk space when for the persistent storage.  Continue to watch and clean the disk or redeploy the pod once persistent storage gets full.

### Hostname Conflict - May Cause Manifest Send Errors

If the hostname defined in the accept field is already in use within the Akash provider, a conflict occurs if another deployment attempts to launch with the same hostname.  This could occur within our testnet environment if multiple people are attempting to use the same SDL and deploy to the same provider.  Consider changing the accept field to a unique hostname (I.e. \<myname>.locahost) if you receive an error in send of the manifest to the provider.

```
grafana:
  image: grafana/grafana
  expose:
    - port: 3000
  as: 80
  to:
    - global: true
  accept:
    - webdistest.localhost
```