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

Para tener un contenedor con Oracle XE 11g ejecutar las siguientes sentencias (probado tanto Windows como en Ubuntu): 

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
