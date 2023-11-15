# Manaje quotas

## Applying Quotas

The following table describes some resources that a quota can restrict by their count or number:

| Resource Name | Quota Description |
| - | - |
| pods | Total number of pods |
| replicationcontrollers | Total number of replication controllers |
| services | Total number of services |
| secrets | Total number of secrets |
| persistentvolumeclaims | Total number of persistent volume claims |

| Resource Name | Quota Description |
| - | - |
| requests.cpu | Total CPU use across all containers |
| requests.memory | Total memory use across all containers |
| requests.storage | Total storage requests by containers across all persistent volume claims |

To create a resource quota is by using the `oc create quota` command, for example:
```sh
oc create quota dev-quota --hard services=10,cpu=1300m,memory=1.5Gi
```

### Manifiesto ejemplo
```yaml
apiVersion: v1
kind: ResourceQuota
metadata:
  name: dev-quota
spec:
  hard:
    services: "10"
    cpu: "1300m"
    memory: "1.5Gi"
```

## Applying Quotas to Multiple Projects

The ClusterResourceQuota resource is created at cluster level, similar to a persistent volume, and specifies resource constraints that apply to multiple projects.
```sh
oc create clusterresourcequota NAME --project-label-selector=key=value [--hard=RESOURCE=QUANTITY]... [flags]
```
Example:
```sh
oc create clusterresourcequota limit-bob \
--project-annotation-selector=openshift.io/requester=user-bob \
--hard=pods=10,services=5
```
