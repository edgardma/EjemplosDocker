# Manual de AKS

AKS - Azure Kubernetes Service

## Setencias principales

- Crear un POD:
  
  ```shell
  kubectl run nginx --image=nginx --restart=Never
  ```

- Ver el estado de un POD:
  
  ```bash
  kubectl get pods
  ```

- Ver toda la configuración de de un POD:
  
  ```shell
  kubectl describe pod ngnix
  ```

- Eliminar un POD:
  
  ```shell
  kubectl delete pod
  ```

- sdada



*Fuente: [Documentación de Azure Kubernetes Service | Microsoft Learn](https://learn.microsoft.com/es-es/azure/aks/)*
