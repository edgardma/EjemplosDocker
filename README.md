# Manual de Docker

## Instalación

Primero, no se debe tener instalado `docker` en la PC, si se tiene, desinstalar dicho software. Con la siguiente sentencia se puede desinstalar dicho software:

```shell
for pkg in docker.io docker-doc docker-compose docker-compose-v2 podman-docker containerd runc; do sudo apt-get remove $pkg; done
```

Seguidamente, para instalar `docker` en Ubuntu, se debe seguir las siguientes sentencias:

```shell
## Actualizar el repositorio
sudo apt update

## Instalar los siguientes paquetes
sudo apt-get install ca-certificates curl gnupg lsb-release

# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

## Actualizar el repositorio
sudo apt update

## Instalar los paquetes de docker
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

## Probar la instalación
sudo docker run hello-world
```

*Fuente: [Install Docker Engine on Ubuntu | Docker Documentation](https://docs.docker.com/engine/install/ubuntu/)*

## Sentencias básicas

A continuación se listan las sentencias mas usadas en `Docker` (para el caso de Windows quitar la sentencia `sudo` en el caso de ser usadas fuera del WSL):

```bash
## Listar el contenido:
sudo docker container ls -a

## Listar las imágenes:
sudo docker image ls
sudo docker ps -a

## Crear una imagen con un nombre específico:
sudo docker run --name hola hello-world

## Listar las imágenes:
sudo docker image ls
sudo docker images

## Ejecutar un contenedor con NGinx:
sudo docker run -p 88:80 nginx

## Para ejecutar un contenedor sin log en la consola:
sudo docker run -p 88:80 -d nginx

## Para ver el log de un contenedor:
sudo docker logs [ID_CONTENEDOR]

## Para ver el log de un contenedor en línea:
sudo docker logs [ID_CONTENEDOR] -f

## Ejecutar una sentencia dentro del contenedor:
sudo docker exec -it [ID_CONTENEDOR] nginx -h
sudo docker exec -it [ID_CONTENEDOR] nginx -V

## Entrar a la consola del contenedor:
sudo docker exec -it [ID_CONTENEDOR] bash

## También funciona:
sudo docker exec -i -t [ID_CONTENEDOR] /bin/bash

## Parar la ejecución de un contenedor:
sudo docker stop [ID_CONTENEDOR]/[NOMBRE_CONTENEDOR]

## Iniciar la ejecución de un contedor:
sudo docker start [ID_CONTENEDOR]/[NOMBRE_CONTENEDOR]

## Descargar la última versión de la imagen de Alpine:
sudo docker pull alpine
```

## Ejemplos de contenedores Docker:

### Oracle XE 11g

Para tener un contenedor con Oracle XE 11g ejecutar las siguientes sentencias (*probado tanto Windows como en Ubuntu*): 

```shell
## Windows
docker run -d -p 49161:1521 oracleinanutshell/oracle-xe-11g

## Ubuntu
sudo docker run -d -p 49161:1521 oracleinanutshell/oracle-xe-11g
```

Para ejecutar el contenedor, primero ejecutar la sentencia `docker ps -a` y copiar el nombre del contenedor, luego, ejecutar la siguiente sentencia:

```shell
## Windows
docker start [NOMBRE_CONTEDOR]

## Ubuntu
sudo docker start [NOMBRE_CONTEDOR]
```

Y para conectarse a la base de datos:

```plsql
hostname: localhost
port: 49161
sid: xe
username: system
password: oracle
```

*Fuente: [Docker Hub](https://hub.docker.com/r/oracleinanutshell/oracle-xe-11g)*

### Oracle 18c

*Nota: la imagen pesa 5.89GB*

Clonar el siguiente repositorio (desde Windows se puede usar WSL2):

```shell
git clone https://github.com/oracle/docker-images.git
```

Ir a la carpeta `/docker-images/OracleDatabase/SingleInstance/dockerfiles`

```shell
cd /docker-images/OracleDatabase/SingleInstance/dockerfiles
```

Bajar la imagen, para lo cual ejecutar la siguiente sentencia:

```shell
./buildDockerImage.sh -v 18.4.0 -x -i
```

Para verificar, ejecutar:

```shell
docker images
```

Para ejecutar el contenedor, se debe ejecutar la siguiente sentencia:

```shell
docker run --name OracleXE18 --shm-size=1g -p 49162:1521 -p 9080:8080 -e ORACLE_PWD=PasswordOracle12 oracle/database:18.4.0-xe
```

*Fuente: [docker-images/README.md at main · oracle/docker-images · GitHub](https://github.com/oracle/docker-images/blob/main/OracleDatabase/SingleInstance/README.md)*

### MySQL Server

Para tener un contenedor con una la versión comunitaria mas reciente de MySQL Server, ejecutar la siguiente sentencia (*probado tanto Windows como en Ubuntu*):

```shell
sudo docker run -d -p 33060:3306 --name mysql-db -e MYSQL_ROOT_PASSWORD=secret mysql
```

Para el caso de una Macbook M1, ejecutar la siguiente sentencia:

```shell
## Pull
docker pull arm64v8/mysql
## Crear
docker run -p 33060:3306 --name mysql-db -e MYSQL_ROOT_PASSWORD=secret -d arm64v8/mysql
```

*Fuente: [Docker](https://hub.docker.com/r/arm64v8/mysql)*

En donde:

- `-d`: para ejecutar el contenedor en segundo plano.

- `--name`: el nombre del contenedor.

- `-e`: Para pasar parámetros de configuración del contenedor, en este caso, la clave.

- `-p`: El puerto para acceder desde la máquina local.

- `mysql-db`: Es el nombre de la imagen que ha descargado para crear el contenedor.

Para conectar al servidor desde la máquina local, se puede usar los siguientes parámetros:

```
hostname: localhost
port: 33060
username: root
password: secret
```

*Fuente:* 

* *[Docker Hub](https://hub.docker.com/r/mysql/mysql-server)*

* *[Como crear un contenedor con Docker-Mysql y persistir la información](https://platzi.com/tutoriales/1432-docker/3268-como-crear-un-contenedor-con-docker-mysql-y-persistir-la-informacion/)*

* *[Práctica 5. Creación de un contenedor Docker con MySQL Server](https://josejuansanchez.org/bd/practica-05/index.html)*

### SQL Server Express 2019

Para tener un contenedor con una la versión Express de SQL Server Express 2019, ejecutar la siguiente sentencia (*probado tanto Windows como en Ubuntu*):

```bash
sudo docker run -d -p 1433:1433 --name sqlserver-db -e "ACCEPT_EULA=Y" -e "SA_PASSWORD=secret" -e "MSSQL_PID=Express" mcr.microsoft.com/mssql/server:2019-latest
```

En donde:

- `-d`: para ejecutar el contenedor en segundo plano.

- `--name`: el nombre del contenedor.

- `-e`: Para pasar parámetros de configuración del contenedor, en este caso, la clave.

- `-p`: El puerto para acceder desde la máquina local (*he probado con un puerto distinto al `1433` pero he tenido problemas al momento de conectarme con las herramientas cliente por lo que he mantenido ese puerto*).

- `sqlserver-db`: Es el nombre de la imagen que ha descargado para crear el contenedor.

Para conectar al servidor desde la máquina local, se puede usar los siguientes parámetros (se ha probado tanto `SSMS` y el `Azure Data Studio`):

```
hostname: localhost
port: 1433
username: sa
password: secret
```

*Fuente:*

* *[Docker Hub](https://hub.docker.com/_/microsoft-mssql-server)*

### PostgreSQL

Para tener un contenedor con una versión de PostgreSQL, ejecutar la siguiente sentencia (*probado tanto Windows como en Ubuntu*):

```bash
sudo docker run -d -p 54320:5432 --name posgre-db -e POSTGRES_PASSWORD=secret postgres
```

Para instalar una versión específica, se puede especificar la versión que se necesita, por ejemplo:

```bash
sudo docker run -d -p 54320:5432 --name posgre-db -e POSTGRES_PASSWORD=secret postgres:15.3
```

Si necesitan instalar PostgreSQL con el complemento PostGIS, se puede ejecutar la siguiente sentencia (el puerto por defecto se ha cambiado al `54321`):

```bash
sudo docker run -p 54321:5432 --name posgis-db -e POSTGRES_PASSWORD=secret -d postgis/postgis:15-3.3
```

Con la sentencia `SELECT postgis_full_version();` se puede validar si se tiene instalado el complemento.

En donde:

- `-d`: para ejecutar el contenedor en segundo plano.

- `--name`: el nombre del contenedor.

- `-e`: Para pasar parámetros de configuración del contenedor, en este caso, la clave (luego de la propiedad `POSTGRES_PASSWORD`).

- `-p`: El puerto para acceder desde la máquina local (*he probado con un puerto distinto al `54320/54321` pero he tenido problemas al momento de conectarme con algunas herramientas cliente*).

- `posgre-db / posgis-db`: Es el nombre de la imagen que ha descargado para crear el contenedor.

Para conectar al servidor desde la máquina local, se puede usar los siguientes parámetros:

```
hostname: localhost
port: 54320
username: postgres
password: secret
```

Para conectarse desde la línea de comando desde Ubuntu, se debe instalar lo siguiente:

```shell
sudo apt install postgresql-client-common

sudo apt install postgresql-client
```

Y para conectarse al servidor, utilizar la siguiente sentencia:

```shell
psql -h 127.0.0.1 -p 54320 -U postgres postgres
```

Para validar se puede ejecutar la sentencia `\dt` o `SELECT version();`.

*Fuente:*

- *[Docker Hub](https://hub.docker.com/_/postgres)*

## MongoDB

Para tener un contenedor con MongoDB se debe ejecutar la siguiente sentencia:

```shell
## Mac M1
sudo docker run -p 27017:27017 --name mongodb-desa -d arm64v8/mongo:6.0.5-jammy
```

## GeoServer

Para tener un contenedor con `GeoServer` se debe ejecutar la siguiente sentencia:

```bash
sudo docker pull docker.osgeo.org/geoserver:2.23.1

sudo docker run -d -p 8081:8080 --name geoserver docker.osgeo.org/geoserver:2.23.1
```

Para validar, se puede ingresar en un navegador a la siguiente ruta `http://localhost:8081/geoserver` e ingresar con el usuario y clave por defecto `admin:geoserver`

*Fuente:*

* *[GitHub - geoserver/docker: GeoServer docker image](https://github.com/geoserver/docker)*

## GitLab

Para tener un contenedor con GitLab se debe ejecutar las siguientes sentencias:

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

Finalmente, ingresar nuevamente a la ruta ingresando el usuario `root` con su nueva clave.

## SonarQube

Para tener un contenedor con SobarQube se debe ejecutar las siguientes sentencias:

```bash
docker pull sonarqube:community

sudo docker run -p 9000:9000 --name sonardes -d sonarqube:lts-community
```
