# Ejemplo de ejecución de Kubernetes localmente:

### Pre-requisitos

Este código se ha probado sobre el siguiente software:

* Windows 10 Enterprise (21H1)
  
  * WSL 2
  
  * Ubuntu 20.04.3

* Docker Desktop v4.1.1
  
  * Kubernetes v1.21.5

### Ejecutar el aplicativo go

En la carpeta `src/` existe un archivo `main.go` que contiene el siguiente código en lenguaje `go` de Google:

```go
package main

import (
    "fmt"
    "log"
    "net/http"
    "net"
    "os"
)

func main() {
    port := "8080"
    if fromEnv := os.Getenv("PORT"); fromEnv != "" {
        port = fromEnv
    }

    server := http.NewServeMux()
    server.HandleFunc("/", hello)

    log.Printf("Server listening on port %s", port)
    err := http.ListenAndServe(":"+port, server)
    log.Fatal(err)
}

func hello(w http.ResponseWriter, r *http.Request) {
    log.Printf("Serving request: %s", r.URL.Path)
    host, _ := os.Hostname()
    fmt.Fprintf(w, "Hola Mundo!\n")
    fmt.Fprintf(w, "Version: 1.0.0\n")
    fmt.Fprintf(w, "Hostname: %s\n", host)

    addrs, _ := net.InterfaceAddrs()
    for _, a := range addrs {
        if ipnet, ok := a.(*net.IPNet); ok && !ipnet.IP.IsLoopback() {
            if ipnet.IP.To4() != nil {
                fmt.Fprintf(w, "IP Address: %s\n", ipnet.IP.String())
            }
        }
    }
}
```

Ejecutar la siguiente sentencia para validar que el código se ejecuta sin problemas:

```
go run main.go
```

Posteriormente, ir a un navegador e ir a la ruta `http://localhost:8080/` y revisar si muestra la aplicación.

### Crear una imagen

En la carpeta `src/` existe un archivo `Dockerfile` que contiene lo siguiente:

```dockerfile
FROM golang:1.11-alpine
ADD . /go/src/hello-app
RUN go install hello-app

FROM alpine:latest
COPY --from=0 /go/bin/hello-app .
ENV PORT 8080
CMD ["./hello-app"]
```

Con la siguiente sentencia, se creará una imagen con el nombre `emarquez/api-hello`

```
docker build -t emarquez/api-hello src/
```

### Crear un contenedor en base a la imagen creada previamente

Con la siguiente sentencia creamos e iniciamos un contenedor con la imagen que creamos anteriormente:

```
docker run --name api-hello -d -p 85:8080 emarquez/api-hello
```

Posteriormente, ir a un navegador e ir a la ruta `http://localhost:85/` y revisar si muestra la aplicación.

### Crear un 'pod' en Kubernetes

En la carpeta `k8s` se tiene el archivo `pod.yaml` que tiene el siguiente contenido:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: api-hello-pod
  labels:
    app: api-hello
spec:
  containers:
  - name: api-hello
    image: emarquez/api-hello:latest
    ports:
    - containerPort: 8080
    imagePullPolicy: Never
```

> *NOTA*: Se debe indicar que la línea `imagePullPolicy: Never` se tuvo que adicionar ya que daba error al querer crear un `pod` en Kubernetes en modo local, el error que mostraba el pod era el siguiente:
> 
> `Failed to pull image rpc error: code = Unknown desc = Error response from daemon: pull access denied for emarquez/api-hello, repository does not exist or may require 'docker login': denied: requested access to the resource is denied`

Con la siguiente sentencia creamos un `pod` en Kubernetes:

```
kubectl apply -f k8s/pod.yaml
```

Con la sentencia, revisamos el `pod` creado:

```
kubectl get pods
```

El cual debe dar el siguiente resultado:

```
NAME            READY   STATUS    RESTARTS   AGE
api-hello-pod   1/1     Running   0          3s
```

Seguidamente, con el siguiente comando revisamos el log del contendor en el `pod`:

```
kubectl logs api-hello-pod
```

Esta sentencia, devolverá un resultado parecido a este:

```
2021/10/23 02:58:21 Server listening on port 8080
```

Con esto, se comprobó que no hay problemas con la creación de un `pod`, entonces procederemos a eliminar el `pod`:

```
kubectl delete pod api-hello-pod
```

### Crear un 'Deployment' en Kubernetes

En la carpeta `k8s` se tiene el archivo `deployment.yaml` que tiene el siguiente contenido:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: api-hello-deployment
  labels:
    app: api-hello
spec:
  replicas: 10
  selector:
    matchLabels:
      app: api-hello
  template:
    metadata:
      labels:
        app: api-hello
    spec:
      containers:
      - name: api-hello
        image: emarquez/api-hello:latest
        ports:
        - containerPort: 8080
        imagePullPolicy: Never
```

> *NOTA*: Al igual que el punto anterior, la línea `imagePullPolicy: Never` se tuvo que adicionar ya que daba error al querer crear un `pod` en Kubernetes en modo local, el error que mostraba el pod era el siguiente:
> 
> `Failed to pull image rpc error: code = Unknown desc = Error response from daemon: pull access denied for emarquez/api-hello, repository does not exist or may require 'docker login': denied: requested access to the resource is denied`

Con la siguiente sentencia, creamos un despliegue de varios `pods` en Kubernetes, con la sisguiente sentencia:

```
kubectl apply -f k8s/deployment.yaml
```

Luego, ejecutamos la sigueinte sentencia para verificar el estado de los `pods` creados:

```
kubectl get pods
```

Esta sentencia, debe devolver en consola el siguiente resultado:

```
NAME                                    READY   STATUS    RESTARTS   AGE
api-hello-deployment-84b694d899-66s5k   1/1     Running   0          40s
api-hello-deployment-84b694d899-9qhfx   1/1     Running   0          40s
api-hello-deployment-84b694d899-hk2gj   1/1     Running   0          40s
api-hello-deployment-84b694d899-hldkd   1/1     Running   0          40s
api-hello-deployment-84b694d899-kbd5c   1/1     Running   0          40s
api-hello-deployment-84b694d899-l4vz4   1/1     Running   0          40s
api-hello-deployment-84b694d899-l77dn   1/1     Running   0          40s
api-hello-deployment-84b694d899-msbcg   1/1     Running   0          40s
api-hello-deployment-84b694d899-qkwz9   1/1     Running   0          40s
api-hello-deployment-84b694d899-qtckd   1/1     Running   0          40s
```

### Crear un ´Service´en Kubernetes

En la carpeta `k8s` se tiene el archivo `service.yaml` que tiene el siguiente contenido:

```yaml
kind: Service
apiVersion: v1
metadata:
  name: api-hello-service
spec:
  type: LoadBalancer
  selector:
    app: api-hello
  ports:
  - protocol: TCP
    port: 80
    targetPort: 8080
```

Con la siguiente sentencia, creamos el servicio en Kubernetes, con la sisguiente sentencia:

```
kubectl apply -f k8s/service.yaml
```

Luego, ejecutamos la sigueinte sentencia para verificar el estado del servicio creado:

```
kubectl get svc
```

Devolverá un resultado similar al siguiente:

```
NAME                TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
api-hello-service   LoadBalancer   10.103.130.151   localhost     80:30856/TCP   15s
kubernetes          ClusterIP      10.96.0.1        <none>        443/TCP        6h35m
```

Posteriormente, ir a un navegador e ir a la ruta `http://localhost` y revisar si muestra la aplicación.

Para revisar si los `pods` esán siendo balanceados por Kubernetes, podemos eliminar un `pod` con la siguiente sentencia:

```
kubectl delete pod api-hello-deployment-84b694d899-66s5k
```

Y la aplicación debe seguir funcionando sin problemas.



***Fuente:***

[Introduccion a Kubernetes (Practica) - YouTube](https://www.youtube.com/watch?v=6jeCUFNv0XI&t=2264s)


