# Ejemplos de contenedores Docker:

## Oracle XE 11g

Para tener un contenedor con Oracle XE 11g ejecutar las siguientes sentencias (probado tanto Windows como en Ubuntu): 

```shell
docker run -d -p 49161:1521 oracleinanutshell/oracle-xe-11g
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


