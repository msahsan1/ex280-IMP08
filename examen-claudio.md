# Examen Claudio

1- Crear un Identity Provider con el nombre LTAexcram, con los usuario nico, claudio, gustavos con la contraseña redhat para los tres. Los usuarios nico y claudio tengan permisos de cluster-admin para todo el cluster que pertenezcan al grupo siadmin.

2- De ahora en mas utilizar el usuario nico.

3- Se nos informo que hay un proyecto, con el Display Name "Test DO280", donde el cliente nos informa que contrato un servidor web para que le proporcione datos pero nunca pudo ingresar al sitio web. Pasado los 10 minutos de analisis puede preguntarle al boludo que implemento el servicio que cagada supuestamente se mando y por lo que puede ser que no funcione dicho web server y ver si hay posibilidades de solucionarlo sin tener que borrarlo y crear uno nuevo.

4- Darle para el usuario gustavos permiso de usuario basico al proyecto dotest1, y este usuario debe pertenecer al grupo noadmin.

5- Seguir con el SUPERUSUARIO nico. Dicho proyecto dotest1 se encontraba funcionando pero un guachin, que se fue de la empresa porque no le daban buen aumento, saboteo el servicio segun nos comento el servicio de inteligencia de los antikkk.

6- Por un tema presupuestario se nos pidio que modifiquemos un maximo de 4 replicas y limitar los recursos de memoria y cpu, lo dejo a tu eleccion (se me llenaron las bolas pensando que poner).

7- Se nos pidio ahora que en vez de prefijado las replicas, use 1 solo de minimo y que pueda aumentar hasta 5, por consumo de CPU y que escale al 60% del consumo.

8- Crear una ruta segura edge y que le generemos las credenciales con los siguientes datos:
/C=AR/ST=CABA/L=Flores/O=MSDOS/CN=info-php.example.com

9- Crear un nuevo proyecto con una nueva app de ejemplo de apache, crearle un volumen de 1Gb y asignarselo en /tmp. Este proyecto debe correr en un nodo en particular a eleccion (no en un master). Usar labels.

10- Crear un secreto anexo=pompeya, que esté en el namespace correspondiente para esto, y de ahi mandarlo al env de un contenedor.