# Instalar Kubernetes

## Pre-requisitos

- Ubuntu 20.04.3

- Docker

## Linux

Agregar la siguiente llave:

```
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
```

Agregar el repositorio:

```
sudo apt-add-repository "deb http://apt.kubernetes.io/ kubernetes-xenial main"
```

Instalar los siguientes paquetes:

```
sudo apt-get install kubelet kubeadm kubectl
```

Marcar los paquetes instalados:

```
sudo apt-mark hold kubelet kubeadm kubectl
```

Ejecutar las siguiente sentencia:

```
sudo systemctl daemon-reload
```

> En el caso de tener problemas con `Swap` ejecutar la siguiente sentencia:
> 
> ```
> sudo swapoff -a
> ```

Configurar el servicio en el servidor principal:

```
sudo kubeadm init --pod-network-cidr=10.244.0.0/16
```

Revisar si el servicio `kubelet` este funcionando correctamente:

```
sudo systemctl status kubelet
```

Finalmente, si todo se ha ejecutado sin problemas, ejecutar la siguientes sentencias:

```
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

Y verificamos los `namespaces` de Kubernetes:

```
kubectl get namespaces
```

 El cual debe devolver un resultado similar al siguiente:

```
NAME              STATUS   AGE
default           Active   2d1h
kube-node-lease   Active   2d1h
kube-public       Active   2d1h
kube-system       Active   2d1h
```

## Posibles errores:

Primero, verificar el estado del servicio `kubelet` con las siguientes sentencias:

```
sudo systemctl status kubelet
```

Si el servicio no se ha inciado correctamente, intentar iniciarlo con la siguiente sentencia:

```
sudo systemctl restart kubelet
```

Para revisar el log de ejecución del servicio, ejecutar la siguiente sentencia:

```
sudo journalctl -xeu kubelet
```

En el caso de que el log tenga un problema con la ruta de inicio de `Docker`, primero, resetear `kubeadm`:

```
sudo kubeadm reset
```

Luego, crear o editar el siguiente archivo:

```
sudo vim /etc/docker/daemon.json
```

Y adicionar el siguientes líneas:

```json
{
  "exec-opts": ["native.cgroupdriver=systemd"]
}
```

Posteriormente, reiniciar de `Docker`:

```
sudo systemctl restart docker
```

Y finalmente, configurar el servicio en el servidor principal:

```
sudo kubeadm init --pod-network-cidr=10.244.0.0/16
```

> Si al momento de reiniciar el equipo el servicio `kubelet` no funciona, ejecutar los siguientes sentencias:
> 
> ```
> sudo swapoff -a
> sudo systemctl daemon-reload
> ```


