# Resumen Taints y Tolerations

## Taints
Los taints van al `nodo`, para hacer que aquellos pods que no tengan la toleration asocianda no se ejecuten en el nodo. Por defecto los pods no tienen tolerations.

Hay 3 tipos de tains:
- NoSchedule: No se programa la ejecución de ciertos pods en tal nodo.
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

Para Openshift.
```sh
oc adm taint nodes node1 <key>=<value>:<taint-effect>
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


Para un deployment.
```sh
oc explain deployment.spec.template.spec.tolerations
```
```
FIELDS:
   effect	<string>
     Effect indicates the taint effect to match. Empty means match all taint
     effects. When specified, allowed values are NoSchedule, PreferNoSchedule
     and NoExecute.



   key	<string>
     Key is the taint key that the toleration applies to. Empty means match all
     taint keys. If the key is empty, operator must be Exists; this combination
     means to match all values and all keys.

   operator	<string>
     Operator represents a key's relationship to the value. Valid operators are
     Exists and Equal. Defaults to Equal. Exists is equivalent to wildcard for
     value, so that a pod can tolerate all taints of a particular category.



   tolerationSeconds	<integer>
     TolerationSeconds represents the period of time the toleration (which must
     be of effect NoExecute, otherwise this field is ignored) tolerates the
     taint. By default, it is not set, which means tolerate the taint forever
     (do not evict). Zero and negative values will be treated as 0 (evict
     immediately) by the system.

   value	<string>
     Value is the taint value the toleration matches to. If the operator is
     Exists, the value should be empty, otherwise just a regular string.
```
