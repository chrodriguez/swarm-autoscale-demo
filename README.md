# Autoscaling con Docker Swarm

Para poder armar el laboratorio, usaremos el modo swarm de docker. Para ello, se
debe tener instalado docker en versiones superiores a 1.13.

## Poner docker en modo swarm

Para convertir nuestra PC en modo swarm, corremos:

```
docker swarm init
```

> Podemos verificar el modo en el que nuestro daemon docker se encuenta
> funcionando usando `docker info`

> Para dejar el modo swarm, se debe usar `docker swarm leave`

## Correr la demo

La demo provista consta de los siguientes servicios:

* [Aplicación web](https://github.com/docker/dockercloud-hello-world): Implementa un
servidor web que nos indica el nombre del host (contenedor) donde corre el
servicio.
* [Load balancer](https://github.com/docker/dockercloud-haproxy/tree/master):
HAProxy que se integra con el cluster swarm para balancear la
  aplicación servida. Es quien atiende en el puerto 80 y se reconfigura cuando
se escala la aplicación web.
* [Orbiter](https://github.com/gianarb/orbiter): es un servicio de autoescalado
  en swarm. Atiende peticiones que permiten cambiar la escala de un servicio en
swarm.
* [Prometheus](): lleva las estadísticas de conexiones entrantes 

Entendiendo esto, veremos como iniciar nuestro stack en swarm:

```
docker stack deploy -c docker-compose.yml scaling-demo
```

## Verificamos el estado

Con swarm podemos ver el estado de los servicios de nuestro stack de la siguiente
forma:

```
docker stack services scaling-demo
```

En esta vista podemos verificar la cantidad de réplicas de cada servicio. La
configuración del auto escalado, considera que la cantidad de peticiones
servidas en los últimos 30 segundos sean hasta 10 por cada backend. Si ese valor
se excede, se genera una alerta que creará un nuevo backend. Esto será hasta que
se satisfaga la condición mencionada.

## Generando tráfico

Podemos generar tráfico con un simple script en bash:

```
while true; do curl -s localhost | grep 'My hostname'; done
```

Con este script podemos generar suficientes conexiones para romper la condición
necesaria para realizar el escalamiento.
La misma salida, debería empezar a alternar nombres debido al escalamiento y el
comportamiento de la aplicación **Hello World**.

El mismo stack provee dos servicios para comprobar las alertas, como así las
métricas que generan las alertas:

Podemos verificar cómo cambia la escala de nuestra aplicación usando:

```
watch docker stack services scaling-demo
```

### Alertas

Las alertas se podrán observar accediendo a http://127.0.0.1:8080

El producto mostrará cada vez que una alerta se lanza. Las alertas son las
responsables del escalamiento hacia arriba o abajo.

### Métricas que generan alertas

La consola de Prometheus, nos pemite observar los mismos valores que tomarán las
condiciones que generan alertas. Para ello, podemos acceder a
http://127.0.0.1:9090 y escribir nosotros las expresiones en cuestión que son:

* _Cantidad de requerimientos de los últimos 30 segundos:_ `sum(rate(haproxy_frontend_http_requests_total[30s]))`
* _Cantidad de backends del load balancer:_ `sum(rate(haproxy_frontend_http_requests_total[30s]))`
