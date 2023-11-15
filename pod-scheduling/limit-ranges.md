# Applying Limit Ranges


```sh
oc explain limitRange.spec.limits
```
```
KIND:     LimitRange
VERSION:  v1

RESOURCE: limits <[]Object>

DESCRIPTION:
     Limits is the list of LimitRangeItem objects that are enforced.

     LimitRangeItem defines a min/max usage limit for any resource that matches
     on kind.

FIELDS:
   default	<map[string]string>
     Default resource requirement limit value by resource name if resource limit is omitted.

   defaultRequest	<map[string]string>
     DefaultRequest is the default resource requirement request value by
     resource name if resource request is omitted.

   max	<map[string]string>
     Max usage constraints on this kind by resource name.

   maxLimitRequestRatio	<map[string]string>
     MaxLimitRequestRatio if specified, the named resource must have a request and limit that are both non-zero where limit divided by request is less than or equal to the enumerated value; this represents the max burst for the named resource.

   min	<map[string]string>
     Min usage constraints on this kind by resource name.

   type	<string> -required-
     Type of resource that this limit applies to.
```

### Manifiesto ejemplo

```yaml
apiVersion: "v1"
kind: "LimitRange"
metadata:
  name: "resource-limits" 
spec:
  limits:
    - type: "Pod" 
      max:
        cpu: "2"
        memory: "1Gi"
      min:
        cpu: "200m"
        memory: "6Mi"
    - type: "Container" 
      max:
        cpu: "2"
        memory: "1Gi"
      min:
        cpu: "100m"
        memory: "4Mi"
      default: 
        cpu: "300m"
        memory: "200Mi"
      defaultRequest: 
        cpu: "200m"
        memory: "100Mi"
      maxLimitRequestRatio: 
        cpu: "10"
    - type: openshift.io/Image 
      max:
        storage: 1Gi
    - type: openshift.io/ImageStream 
      max:
        openshift.io/image-tags: 20
        openshift.io/images: 30
    - type: "PersistentVolumeClaim" 
      min:
        storage: "2Gi"
      max:
        storage: "50Gi"
```