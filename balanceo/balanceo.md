# Prueba balanceo minikube.

## Instalación de ingress
Usamos repo helm de bitnami.

https://artifacthub.io/packages/helm/bitnami/nginx-ingress-controller
```bash
helm install my-release oci://registry-1.docker.io/bitnamicharts/nginx-ingress-controller
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