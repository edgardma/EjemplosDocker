# Manual de Docker

## Instalación

Para instalar `docker` en Ubuntu, se debe seguir las siguientes sentencias:

```shell
## Actualizar el repositorio
sudo apt update

## Instalar los siguientes paquetes
sudo apt-get install ca-certificates curl gnupg lsb-release

## Adicionar la llave GPG
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

## Adicionar el repositorio
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

## Actualizar el repositorio
sudo apt update

## Instalar los paquetes de docker
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-compose-plugin

## Probar la instalación
sudo docker run hello-world
```

*Fuente: [Install Docker Engine on Ubuntu | Docker Documentation](https://docs.docker.com/engine/install/ubuntu/)*

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

### MySQL Server

Para tener un contenedor con una la versión comunitaria mas reciente de MySQL Server, ejecutar la siguiente sentencia (*probado tanto Windows como en Ubuntu*):

```shell
sudo docker run -d -p 33060:3306 --name mysql-db -e MYSQL_ROOT_PASSWORD=secret mysql
```

En donde:

- `-d`: para ejecutar el contenedor en segundo plano.

- `--name`: el nombre del contenedor.

- `-e`: Para pasar parámetros de configuración del contenedor, en este caso, la clave.

- `-p`: El puerto para acceder desde la máquina local.

- `mysql`: Es el nombre de la imagen que ha descargado para crear el contenedor.

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
