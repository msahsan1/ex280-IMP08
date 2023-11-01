# Prueba balanceo minikube.

## Instalación de ingress
Usamos repo helm de bitnami.

https://artifacthub.io/packages/helm/bitnami/nginx-ingress-controller
```bash
helm install my-release oci://registry-1.docker.io/bitnamicharts/nginx-ingress-controller --set service.type=NodePort
```
Verificar con estos comandos:
```bash
export HTTP_NODE_PORT=$(kubectl --namespace default get services -o jsonpath="{.spec.ports[0].nodePort}" my-release-nginx-ingress-controller)
export HTTPS_NODE_PORT=$(kubectl --namespace default get services -o jsonpath="{.spec.ports[1].nodePort}" my-release-nginx-ingress-controller)
export NODE_IP=$(kubectl --namespace default get nodes -o jsonpath="{.items[0].status.addresses[1].address}")

echo "Visit http://$NODE_IP:$HTTP_NODE_PORT to access your application via HTTP."
echo "Visit https://$NODE_IP:$HTTPS_NODE_PORT to access your application via HTTPS."
```
Crear ingress.
```bash
kubectl apply -f ingress.yaml
```
Ver IP.
```bash
k get ingress
NAME              CLASS    HOSTS              ADDRESS        PORTS   AGE
example           nginx    minikube           192.168.49.2   80      18m
```
Editar archivo /etc/hosts, y agregar la línea IP -> minikube.
```bash
192.168.49.2      minikube
```
Tomar nota del puerto expuesto.
```bash
k get svc
NAME                                                  TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)                      AGE
my-release-nginx-ingress-controller                   NodePort    10.110.106.248   <none>        80:30917/TCP,443:31221/TCP   22m
```
Verificar funcionamiento.
```bash
curl -s minikube:30917/ | jq "".HOSTNAME
```

## Prueba de ingress
Desplegamos el servicio.
```bash
kubectl create deployment httpenv --image jpetazzo/httpenv -n default
kubectl scale deployment httpenv --replicas=10
kubectl expose deployment httpenv --port=8888
```
Generamos 100 request al servicio.
```bash
for i in $(seq 100); do curl -s 10.99.85.244:8888 | jq "".HOSTNAME; done > salida.txt
```
Contamos la cantidad de request en cada pod.
```bash
cat salida.txt | sort | uniq -c
      5 "httpenv-769fb55cb-2ccxn"
     13 "httpenv-769fb55cb-4577t"
      8 "httpenv-769fb55cb-jnkpj"
      9 "httpenv-769fb55cb-km7t8"
     13 "httpenv-769fb55cb-kmv9r"
     15 "httpenv-769fb55cb-ph8gg"
     11 "httpenv-769fb55cb-prjc8"
      9 "httpenv-769fb55cb-r7tt8"
      8 "httpenv-769fb55cb-t9sdn"
      9 "httpenv-769fb55cb-vclwp"
```
Para verificar la configuración de iptables.
```bash
vagrant@ubuntu2204:~$ sudo iptables -t nat -nL KUBE-SERVICES | grep 10.99.85.244
KUBE-SVC-6ZVPY37LGINYSSPR  tcp  --  0.0.0.0/0            10.99.85.244         /* default/httpenv cluster IP */ tcp dpt:8888

vagrant@ubuntu2204:~$ sudo iptables -t nat -nL KUBE-SVC-6ZVPY37LGINYSSPR
Chain KUBE-SVC-6ZVPY37LGINYSSPR (1 references)
target     prot opt source               destination         
KUBE-MARK-MASQ  tcp  -- !10.244.0.0/16        10.99.85.244         /* default/httpenv cluster IP */ tcp dpt:8888
KUBE-SEP-GPWR3APAVUJOODXU  all  --  0.0.0.0/0            0.0.0.0/0            /* default/httpenv -> 172.17.0.10:8888 */ statistic mode random probability 0.10000000009
KUBE-SEP-KM6UOTYZLF7TEV7H  all  --  0.0.0.0/0            0.0.0.0/0            /* default/httpenv -> 172.17.0.11:8888 */ statistic mode random probability 0.11111111101
KUBE-SEP-VFCTLT47P42BCVL6  all  --  0.0.0.0/0            0.0.0.0/0            /* default/httpenv -> 172.17.0.12:8888 */ statistic mode random probability 0.12500000000
KUBE-SEP-L7ZI5M7XEOWH4WYR  all  --  0.0.0.0/0            0.0.0.0/0            /* default/httpenv -> 172.17.0.3:8888 */ statistic mode random probability 0.14285714272
KUBE-SEP-5QIKPJG63YBA7NIY  all  --  0.0.0.0/0            0.0.0.0/0            /* default/httpenv -> 172.17.0.4:8888 */ statistic mode random probability 0.16666666651
KUBE-SEP-GTJMOL4XFHER6IB6  all  --  0.0.0.0/0            0.0.0.0/0            /* default/httpenv -> 172.17.0.5:8888 */ statistic mode random probability 0.20000000019
KUBE-SEP-ZKPJHK4EK7KBDKY4  all  --  0.0.0.0/0            0.0.0.0/0            /* default/httpenv -> 172.17.0.6:8888 */ statistic mode random probability 0.25000000000
KUBE-SEP-UBMIIKTDI7KAT5VC  all  --  0.0.0.0/0            0.0.0.0/0            /* default/httpenv -> 172.17.0.7:8888 */ statistic mode random probability 0.33333333349
KUBE-SEP-YMPCBAVS4XKKAHS2  all  --  0.0.0.0/0            0.0.0.0/0            /* default/httpenv -> 172.17.0.8:8888 */ statistic mode random probability 0.50000000000
KUBE-SEP-YGRPELBHAWQZOHR3  all  --  0.0.0.0/0            0.0.0.0/0            /* default/httpenv -> 172.17.0.9:8888 */
```