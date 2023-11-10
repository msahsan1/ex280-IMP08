# Persistent Storage Overview

## Verifying the Dynamic Provisioned Storage
```sh
oc get storageclass
```
The output identifies the default storage class. If a storage class exists, then persistent volumes are created dynamically to match persistent volume claims. A persistent volume claim that does not specify a storage class uses the default storage class.

## Deploying Dynamically Provisioned Storage
To add a volume to an application, use the `oc set volumes` command:
```
oc set volumes deployment/example-application \
--add \
--name example-pv-storage \
--type pvc \
--claim-class nfs-storage \
--claim-mode rwo \
--claim-size 15Gi \ 
--mount-path /var/lib/example-app \
--claim-name example-pv-claim
```
The command creates a persistent volume claim resource and adds it to the application as a
volume within the pod.

The following YAML example specifies a persistent volume claim.
To create a PersistentVolumeClaim API object:
```yaml
---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
name: example-pv-claim
labels:
app: example-application
spec:
accessModes:
- ReadWriteOnce
resources:
requests:
storage: 15Gi
```
- PersistentVolumeClaim: Indicates that it is a persistent volume claim.
- name: The name to use in the claimName field of the persistentVolumeClaim element in the volumes section of a deployment manifest.
- accessMpodes: The storage class provisioner must provide this access mode. If persistent volumes are created statically, then an eligible persistent volume must provide this access mode.
- storage: The storage class will create a persistent volume matching this size request. If persistent volumes are created statically, then an eligible persistent volume must be at least the requested size.


## Deleting Persistent Volume Claims
To delete a volume, use the `oc delete` command to delete the persistent volume claim. The
storage class will reclaim the volume after the PVC is removed.
```
oc delete pvc/example-pvc-storage
```