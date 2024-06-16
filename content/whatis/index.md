---
menus: header
title: What is a StorageClass?
weigth: 1
showMetadata: false
---

Kubernetes, the popular container orchestration platform, provides a robust and flexible system for managing containerized applications. One of its powerful features is the ability to manage ephemeral and persistent storage using an interface called StorageClass, specified by the SIG Storage in the Container Storage Interface (CSI) specification.

## Kubernetes StorageClass
To make a storage provider available to Kubernetes, a so-called StorageClass is used. A StorageClass contains multiple configuration values to describe characteristics such as storage capacity and performance. When creating a volume (ephemeral or persistent), the PV is configured (implicitly or explicitly) using the StorageClass of choice, and the underlying storage provider will take care of provisioning and the volume’s lifecycle.

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: my-storage-class
provisioner: csi.vendorname.tld
parameters:
  csi.storage.k8s.io/fstype: ext4
reclaimPolicy: Delete
volumeBindingMode: Immediate
allowVolumeExpansion: true
```

CSI stands for Container Storage Interface and is the official Kubernetes standard for implementing storage providers to interact with. Imagine it to be a set of required and optional operations that can be executed on a volume (like provisioning, snapshotting, deleting). With the main benefit being that the same CSI driver may be used to implement multiple storage classes with different volume characteristics, such as performance, latency, quality-of-service (QoS), backup policies, access permissions, and more.

## Container Storage Interface (CSI)
The Container Storage Interface (CSI) is a standard for exposing storage systems to containerized workloads on Kubernetes. CSI allows vendors to develop plugins for their storage solutions that integrate seamlessly with Kubernetes.

The CSI defines a set of features that can be executed. Some features are mandatory, some are optional.

- **Dynamic Provisioning** provides the option to automatically provision volumes when requested through a Persistent Volume Claim.
- **[Snapshotting](https://kubernetes-csi.github.io/docs/snapshot-restore-feature.html)** provides the option to create snapshots of the current storage state through Kubernetes.
- **[Clonening](https://kubernetes-csi.github.io/docs/volume-cloning.html)** provides the option to create clones of a volume through Kubernetes.
- **[Expanding](https://kubernetes-csi.github.io/docs/volume-expansion.html)** provides the option to expand an existing volume through Kubernetes.
- **[Topoloy Aware](https://kubernetes-csi.github.io/docs/topology.html)** provides the option to define locations like racks, data centers, hosts, and more.
- **[Storage Capacity Tracking](https://kubernetes-csi.github.io/docs/storage-capacity-tracking.html)** provides Kubernetes with the option to understand where to provision a volume and deploy a pod to prevent potentially running out of space.

## Using StorageClass with PersistentVolumeClaim

To use a StorageClass, you need to reference it in a PersistentVolumeClaim (PVC). Here is an example of a PVC that uses the above StorageClass:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: my-persistent-volume-claim
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 50Gi
  storageClassName: my-storage-class
```
