# oas-ci-server

Descargue el paquete desde la pestaña de "releases".

Ayuda a instalar el servidor de CI de la OAS.

![Docker Swarm](http://image.slidesharecdn.com/introtoswarm-containerclusterservice-150329142626-conversion-gate01/95/swarm-a-docker-clustering-system-4-638.jpg)

<sub>&copy; Sriram Natarajan / T-Labs http://www.slideshare.net/snrism/swarm-container-cluster-service</sub>

Este proyecto configura los requerimientos para tener Docker Swarm en la OAS y configura Drone para utilizar este Swarm.

![Drone](http://blog.drone.io/images/drone-with-elixir_build-explained.png)

![Drone](http://blog.drone.io/images/drone-with-elixir_containers.png)

<sub>&copy; http://blog.drone.io/post/drone-with-elixir/</sub>

Para completar la implementación instale también https://github.com/andresvia/oas-ci-agent antes de continuar.

## Integración con VCS

En el archivo `/etc/drone/dronerc` se configura el entorno de ejecución de Drone, un archivo de ejemplo es incluído en el paquete sin embargo debe configurarse para las necesidades específicas de la OAS.

Lea la documentación sobre Drone al respecto http://readme.drone.io/setup/overview/ .
