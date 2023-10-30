# Resumen Taints y Tolerations

> Probar comando en Openshift (creo que hat que agregar el adm).

> Probar tolerations en deployment.

## Taints
Los taints van al nodo, para hacer que aque los pods no tengan la toleration asocianda no se ejecuten en el nodo. Por defecto los pods no tienen tolerations.

Hay 3 tipos de tains:
- NoScehdule: No se programa la ejecución de ciertos pods en tal nodo.
- PreferNoSchedule: No se programa la ejecución de ciertos pods en tal nodo, pero no es 100% asegurado.
- NoExecute: No se programa la ejecución de ciertos pods en tal nodo, y se eliminan aquellos que no tengan el toleration asociado.

Para ver lo taints que tiene asociado uno nodo. ejemplo master:
```sh
kubectl describe node kubemaster | grep Taint
Taints:             node-role.kubernetes.io/master:NoSchedule
```

Para aplicar un taint a un nodo:
```sh
kubectl taint nodes node1 <key>=<value>:<taint-effect>
```

## Tolerations
Las tolerations van a los pods para permitir su ejecución en los nodos que tengan determinado taint. 

Para aplicar un toleration a un pod hay que modificar el manifiesto.
```yaml
apiVersion:
kind: Pod
metadata:
    name: myapp-pod
spec:
    containers:
    - name: example-app
      image: image-axample
    
    tolerations:
    - key: "key"
      operato: "Equal"
      value: "value"
      effect: "taint-effect"

```