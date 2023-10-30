# php-info

Clone your repository to your development machine and cd to the repository directory.
```sh
oc new-app php:7.3~https://github.com/tu-repo-php
```
```sh
curl http://php-info-csr-0003.apps.cluster-ds596.ds596.sandbox709.opentlc.com/info.php -s | grep 'HOSTNAME '

for i in $(seq 10); do curl http://php-info-csr-0003.apps.cluster-ds596.ds596.sandbox709.opentlc.com/info.php -s | grep 'HOSTNAME '; done
```
