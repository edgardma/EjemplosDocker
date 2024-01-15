# Instructivo para instalación de las herramientas de desarrollo

## GeoServer

Para tener un contenedor con `GeoServer` se debe ejecutar la siguiente sentencia:

```bash
sudo docker pull docker.osgeo.org/geoserver:2.23.1

sudo docker run -d -p 8081:8080 --name geoserver docker.osgeo.org/geoserver:2.23.1
```

Para validar, se puede ingresar en un navegador a la siguiente ruta `http://localhost:8081/geoserver` e ingresar con el usuario y clave `admin:geoserver`

*Fuente:*

* *[GitHub - geoserver/docker: GeoServer docker image](https://github.com/geoserver/docker)*

## GitLab

Para tener un contenedor con GitLab se debe ejecutar la siguiente sentencia:

```bash
sudo docker pull gitlab/gitlab-ce

sudo docker run -p 8083:80 -p 8443:443 --name gitlabdes -d gitlab/gitlab-ce:latest
```

Parar validar, se puede ingresar en un navegador a la siguiente ruta local: `http://localhost:8083/users/sign_in`.

Seguidamente, se debe cambiar la clave por defecto del usuario `root` ejecutando las siguientes sentencias:

```bash
docker exec -it e0c3ba8e /bin/bash

gitlab-rake 'gitlab:password:reset'
```

Ingresar el nombre del usuario, en este caso `root`, luego ingresar la clave y repetir la clave ingresada.

Finalmente, ingresar nuevamente a la ruta ingresando el usuario `roor` con su nueva clave.

## Jenkins

### Pre-requisitos:

- Se debe instalar JDK8 o jdk11 ya sea de Oracle u OpenJDK

- Tener liberado el puerto 8080 (para la instalación por defecto)

### Instalación en Ubuntu:

Se debe ejecutar las siguientes sentencias:

```bash
sudo apt-get update
sudo apt-get upgrade
wget -q -O - https://pkg.jenkins.io/debian/jenkins.io.key | sudo apt-key add -
sudo add-apt-repository "deb https://pkg.jenkins.io/debian binary/"
sudo apt-get update
sudo apt-get install jenkins
```

Validar la ejecución de Jenkins en un navegador a la siguiente ruta `localhost:8080`.

En el caso que Jenkins aún no está activo, ejecutar las siguiente sentencia en la consola:

```bash
sudo service jenkins status
sudo service jenkins start
sudo service jenkins status
```

### Instalar en Docker

Para instalar Jenkins en Docker, se puede usar la siguiente sentencia (tanto para Windows como en Ubuntu) en donde se puede especificar el puerto en donde se ejecutará el servicio, en este caso, el puerto 9080:

```bash
docker run -d -p 50000:50000 -p 9080:9080 -e JENKINS_OPTS="--httpPort=9080" --name jenkinsdocker jenkins/jenkins:lts
```

La primera vez, Docker bajará los archivos necesarios para crear el contenedor, por lo cual, puede tomar su tiempo para concluir. En el caso que ya tenga esos archivos, Docker lo creará mucho mas rápido.

Desde la consola, para acceder al contenido del contenedor, se debe ejecutar la siguiente sentencia:

```bash
docker exec -i -t [id_contendor] /bin/bash
```

Luego, para poder leer el contenido del archivo creado luego de la instalación, se puede ejecutar la siguiente sentencia desde la consola:

```bash
head /var/jenkins_home/secrets/initialAdminPassword
```

Finalmente, validar la ejecución de Jenkins en un navegador local a la siguiente ruta `localhost:9080`.
