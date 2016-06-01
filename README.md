# oas-ci-server

Descargue el paquete desde la pestaña de "releases".

Ayuda a instalar el servidor de CI de la OAS.

![Docker Swarm](http://image.slidesharecdn.com/introtoswarm-containerclusterservice-150329142626-conversion-gate01/95/swarm-a-docker-clustering-system-4-638.jpg)

<sub>&copy; Sriram Natarajan / T-Labs http://www.slideshare.net/snrism/swarm-container-cluster-service</sub>

Este proyecto configura los requerimientos para tener Docker Swarm en la OAS y configura Drone para utilizar este Swarm.

![Drone](http://blog.drone.io/images/drone-with-elixir_build-explained.png)

![Drone](http://blog.drone.io/images/drone-with-elixir_containers.png)

<sub>&copy; http://blog.drone.io/post/drone-with-elixir/</sub>

Es posible crear más agentes de CI instalando el paquete: https://github.com/andresvia/oas-ci-agent

Existe un ambiente de pruebas automatizado con Vagrant que instala todo lo necesario en: https://github.com/andresvia/oas-ci

## ¿Cómo usar esto?

 - Abrir [gogs](http://gogs-server:3000/) en el navegador y crear un usuario.
 - Abrir [drone](http://drone-server:8000/) en el navegador y conectar el usuario recién creado en gogs
   - Necesitará agregar la información del docker swarm en drone utilice las llaves de "client" en `/var/lib/docker-swarm-ca/` como dirección utilice la IP local de docker `172.17.0.1` y el puerto de swarm `3376` de la siguiente manera: `tcp://172.17.0.1:3376/` utilice la utilidad [drone cli](http://readme.drone.io/devs/cli/) para hacer esto.

Ejemplo:

```
DOCKER_HOST=tcp://172.17.0.1:3376 DOCKER_TLS_VERIFY=1 DOCKER_CERT_PATH=/var/lib/docker-swarm-ca/client/ DRONE_SERVER=http://127.0.0.1:8000/ DRONE_TOKEN='llave-de-drone-copiada-del-ui' /usr/local/bin/drone node create
```

## Notas de seguridad

 - El servidor oas-ci-server no requiere los archivos de CA para funcionar. Si desea, puede conservar estas llaves en un lugar mas seguro.

## Notas sobre backups

 - Los archivos a los que se le deben hacer copias de seguridad son:
  - Llaves del CA: `/var/lib/docker-swarm-certs`
  - Base de datos de drone: `/var/lib/drone`
  - Base de datos de gogs: `/var/lib/gogs`

## Integración con VCS

En el archivo `/etc/drone/dronerc` se configura el entorno de ejecución de Drone, un archivo de ejemplo es incluído en el paquete sin embargo debe configurarse para las necesidades específicas de la OAS. Por ejemplo en GitHub hay que crear una aplicación y copiar la llave de api y la llave secreta.

Lea la [documentación sobre Drone al respecto](http://readme.drone.io/setup/overview/).

Este paquete también hace la instalación base de Gogs, la cuál se debe completar en [un navegador](http://gogs-server:3000/).

## Puertos

 - Para comunicación (encriptada con TLS) entre Docker Swarm (servicio systemd swarm-manage) y Docker Hosts (servicio systemd docker) puerto TCP/2376
 - Para comunicación (encriptada con TLS) entre Drone (servicio systemd drone) y el Docker Swarm TCP/3376 (ambos se encuentran en el mismo host)
 - Para acceso web a Drone TCP/8000
 - Para acceso web a Gogs TCP/3000

## Seguridad

 - Las reglas exactaas para hacer funcionar con `selinux enforcing` se desconocen, si planea habilitar `selinux enforcing` realice un audit antes de hacerlo para dar los permisos de SELINUX que las aplicaciones necesitan.
