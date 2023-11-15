# Manaje pod Resources

You can use the oc set resources command to specify resource requests and limits. The following command sets the same requests and limits as the preceding example:
```sh
oc set resources deployment hello-world-nginx \
--requests cpu=10m,memory=20Mi \ 
--limits cpu=80m,memory=100Mi
```

### Manifiesto ejemplo
Ejemplo de un deployment que levanta un pod de test con request y limits seteados:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: test-pod
spec:
  replicas: 1
  selector:
    matchLabels:
      app: test-pod
  template:
    metadata:
      labels:
        app: test-pod
    spec:
      containers:
      - name: busybox-container
        image: busybox
        resources:
          limits:
            cpu: 50m
            memory: 100Mi
          requests:
            cpu: 10m
            memory: 50Mi
        command: ["sleep", "infinity"]
```

Para verificar usar el comando `oc explain`:
```sh
oc explain deployment.spec.template.spec.containers.resources
```
```
KIND:     Deployment
VERSION:  apps/v1

RESOURCE: resources <Object>

DESCRIPTION:
     Compute Resources required by this container. Cannot be updated. More info:
     https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/

     ResourceRequirements describes the compute resource requirements.

FIELDS:
   limits	<map[string]string>
     Limits describes the maximum amount of compute resources allowed. More
     info:
     https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/

   requests	<map[string]string>
     Requests describes the minimum amount of compute resources required. If
     Requests is omitted for a container, it defaults to Limits if that is
     explicitly specified, otherwise to an implementation-defined value. More
     info:
     https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/
```