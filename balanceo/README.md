Prueba balanceo minikube
```bash
kubectl create deployment httpenv --image jpetazzo/httpenv -n default
kubectl scale deployment httpenv --replicas=10
kubectl expose deployment httpenv --port=8888

for i in $(seq 100); do curl -s 10.99.85.244:8888 | jq "".HOSTNAME; done > salida.txt

vagrant@ubuntu2204:~$ cat salida.txt | grep -c httpenv-7bdf7d4584-56c4g
8
vagrant@ubuntu2204:~$ cat salida.txt | grep -c httpenv-7bdf7d4584-6msbp
7
vagrant@ubuntu2204:~$ cat salida.txt | grep -c httpenv-7bdf7d4584-6qqq6
13
vagrant@ubuntu2204:~$ cat salida.txt | grep -c httpenv-7bdf7d4584-7pggx
13
vagrant@ubuntu2204:~$ cat salida.txt | grep -c httpenv-7bdf7d4584-clb45
8
vagrant@ubuntu2204:~$ cat salida.txt | grep -c httpenv-7bdf7d4584-gljpp
7
vagrant@ubuntu2204:~$ cat salida.txt | grep -c httpenv-7bdf7d4584-mdcnb
13
vagrant@ubuntu2204:~$ cat salida.txt | grep -c httpenv-7bdf7d4584-pvx5v
12
vagrant@ubuntu2204:~$ cat salida.txt | grep -c httpenv-7bdf7d4584-svvmv
9
vagrant@ubuntu2204:~$ cat salida.txt | grep -c httpenv-7bdf7d4584-vf5pq
10

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

PROBAR EN EKS O K8S. ¿PARA EL COMANDO IPTABLES HAY QUE ENTRAR A UN NODO WORKER?
