# Creacion usuarios en OCP

## Dependencias
- Htpasswd
```sh
sudo yum install httpd-tools
```


## Identity Provider

1. Logueamos a OCP con usuario ocn privilegios de `cluster-admin`.

2. Creamos un archivo htpasswd (si no hay otro creado en OCP)
```sh
touch ARCHIVO-USUAIROS
```
3. Creamos los usuarios que queremos con el siguiente comando.
```sh
htpasswd -Bb ARCHIVO-USUAIROS USUARIO1 PASSWD
```
> Nota: El flag -Bb fuerza un encriptado sobre la contraseña en el archivo que creamos anteriormente

Tambien se puede usar el flag -c para crear el archivo htpasswd y agregar el primer usuario. De seguir usando el flag se reeamplazara por el anterior.
 ```sh
 htpasswd -c Bb ARCHIVO-USUAIROS USUARIO1 PASSWD
 ```

4. Una vez agragados todos los usuarios al archivo htpasswd, creamos un secreto en el NS openshift-config con el archivo de usuarios.
```sh
oc create secret generic secreto-htpasswd --from-file=htpasswd=ARCHIVO-USUARIOS -n openshift-config
```
5. Creamos el reemplazo de la configuracion sobre el protocolo OAUTH de OCP.
```sh
oc replace -f - <<API
apiVersion: config.openshift.io/v1
kind: OAuth
metadata:
  name: cluster
spec:
  identityProviders:
  - name: Local Password
    mappingMethod: claim
    type: HTPasswd
    htpasswd:
      fileData:
        name: secreto-htpasswd
API
```
O tambien se puede editar el recurso Oauth.
```sh
oc edit oauth
```
Y agregamos lo siguiente en indentityProviders (puede haber más de uno):
```yaml
spec:
  identityProviders:
  - name: Local Password
    mappingMethod: claim
    type: HTPasswd
    htpasswd:
      fileData:
        name: secreto-htpasswd
```

6. Verificar

Esperar unos minutos a que se regeneren los pods del namespace openshift-authentication para que haga efecto la configuración.
```sh
oc get pods -n openshift-authentication
```

## RBAC
1. Creamos un grupo para nuestros usuarios.
```sh
oc adm groups new my_new-admins
```
2. Agregamos los usuarios al grupo creado anteriormente.
```sh
oc adm groups add-users my_new-admins usuuario
```
3. Damos permisos al grupo de usuarios.
```sh
oc adm policy add-cluster-role-to-group cluster-admin my_new-admins
```
4. Verificar
Logear a Openshift con los usuairos creados, y en este caso verificar con `oc get nodes` tener privilegios de cluster admin. Para la consola web, ver de tener la opción de Compute para ver los nodos.