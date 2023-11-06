# SCC - Security Context Constraints

Ejemplo de uso para ejecutar un pod en Openshift como `root`.

Test pod deployment.

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
		securityContext:
		  privileged: True
        resources:
          limits:
            cpu: 50m
            memory: 100Mi
          requests:
            cpu: 10m
            memory: 50Mi
        command: ["sleep", "infinity"]
```

Importante: Aca lo improtante ee el `securityContext`:
```yaml
    securityContext:
		privileged: True
```