Descargue el paquete desde la pestaña de "releases".

# oas-ci-server

Ayuda a instalar el servidor de CI de la OAS.

![Docker Swarm](http://image.slidesharecdn.com/introtoswarm-containerclusterservice-150329142626-conversion-gate01/95/swarm-a-docker-clustering-system-4-638.jpg)

<sub>&copy; Sriram Natarajan / T-Labs http://www.slideshare.net/snrism/swarm-container-cluster-service</sub>

Este proyecto configura los requerimientos para tener Docker Swarm en la OAS y configura Drone para utilizar este Swarm.

![Drone](http://blog.drone.io/images/drone-with-elixir_build-explained.png)

![Drone](http://blog.drone.io/images/drone-with-elixir_containers.png)

<sub>&copy; http://blog.drone.io/post/drone-with-elixir/</sub>

Debe instalar primero agentes de CI instalando el paquete: https://github.com/andresvia/oas-ci-agent

Existe un ambiente de pruebas automatizado con Vagrant que instala todo lo necesario en: https://github.com/andresvia/oas-ci

## ¿Cómo usar esto?

 - Abrir UI de [gogs](http://gogs-server:3000/) en el navegador y crear un usuario.
 - Abrir UI de [drone](http://drone-server:8000/) en el navegador y conectar el usuario recién creado en Gogs.
   - Para agregar la información del Swarm Manage en Drone utilice las llaves de "client" en `/var/lib/docker-ci-swarm-ca/` como dirección utilice la IP local de Docker `172.17.0.1` y el puerto de Swarm Manage `3376` de la siguiente manera: `tcp://172.17.0.1:3376/`, puede utilizar la utilidad [drone cli](http://readme.drone.io/devs/cli/) para hacer esto o bien hacerlo desde el UI de Drone.

Este es un ejemplo utilizando **drone cli** con el servidor de Drone ejecutándose en el mismo host (127.0.0.1:8000) al igual que Swarm Manage (172.17.0.1:3376) y suponiendo las llaves de cliente disponibles en `/var/lib/docker-ci-swarm-ca/` las rutas y las direcciones cambiarán si su servidor CA no es igual que su servidor Drone y servidor Swarm Manage.

```
DOCKER_HOST=tcp://172.17.0.1:3376 \
DOCKER_TLS_VERIFY=1 \
DOCKER_CERT_PATH=/var/lib/docker-ci-swarm-ca/client/ \
DRONE_SERVER=http://127.0.0.1:8000/ \
DRONE_TOKEN='token-del-admin-de-drone-copiada-desde-el-ui-de-drone' \
  /usr/local/bin/drone node create
```

Este es un ejemplo haciéndolo desde el UI de Drone.

![drone add node](http://i.imgur.com/YGSPbUN.png)

Los nodos de CI también se pueden agregar directamente, ignorando por completo el "setup" de Swarm Manage, la dirección en ese caso será el FQDN del nodo y el puerto sería el de Docker directamente (2376) por lo cual la URL de conexión sería: tcp://FQDN_DE_NODO:2376 sin embargo los certificados a usar seguirán siendo los de cliente.

## Notas sobre backups

 - Los archivos a los que se le deben hacer copias de seguridad son:
  - Base de datos de drone: `/var/lib/drone`
  - Base de datos de gogs: `/var/lib/gogs`

## Integración con VCS

En el archivo `/etc/drone/dronerc` se configura el entorno de ejecución de Drone, un archivo de ejemplo es incluído en el paquete y este funcionará si Drone y Gogs se encuentran en el mismo host, si este no es el caso se deberá editar y luego reiniciar el servicio de **drone**.

Lea la [documentación sobre Drone al respecto](http://readme.drone.io/setup/overview/).

Este paquete también hace la instalación base de Gogs, la cuál se debe completar en [un navegador](http://gogs-server:3000/).

Drone utiliza las mismas credenciales de acceso que el sistema de VCS integrado.

## Puertos

 - Para comunicación (encriptada con TLS) entre Swarm Manage (servicio systemd ci-swarm-manage) y Docker (servicio systemd docker) puerto TCP/2376
 - Para comunicación (encriptada con TLS) entre Drone (servicio systemd drone) y Swarm Manage TCP/3376
 - Para comunicación (no encriptada) entre oas-ci-agent y Registry (servicio systemd registry) TCP/5000
 - Para acceso web a Drone (servicio systemd drone) TCP/8000 (no encriptado)
 - Para acceso web a Gogs (servicio systemd gogs) TCP/3000 (no encriptado)
 - Para acceso web a Registry Frontend (servicio systemd registry-frontend) TCP/8080 (no encriptado)

Se recomienda asegurar los puertos (especialmente aquellos no encriptados) usando firewalld limitándolo a las redes segmentadas de confianza y VPNs.

## Seguridad

 - Las reglas exactas para hacer funcionar con `selinux enforcing` se desconocen, si planea habilitar `selinux enforcing` realice un audit antes de hacerlo para otorgar los permisos que las aplicaciones necesitan.
