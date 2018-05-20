---
title: 'Tutorial de Kubernetes en Azure: escalado de una aplicación'
description: 'Tutorial de AKS: escalado de una aplicación'
services: container-service
author: dlepow
manager: jeconnoc
ms.service: container-service
ms.topic: tutorial
ms.date: 02/22/2018
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 7b962ccd8349996cd33cc3960391cba8fce549ad
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/10/2018
---
# <a name="tutorial-scale-application-in-azure-kubernetes-service-aks"></a>Tutorial: Escalado de una aplicación en Azure Kubernetes Service (AKS)

Si ha ido siguiendo los tutoriales, tiene un clúster de Kubernetes en funcionamiento en AKS y ha implementado la aplicación Azure Voting.

En este tutorial, parte cinco de ocho, se escalan horizontalmente los pods en la aplicación y se prueba el escalado automático de pods. También se aprende a escalar el número de nodos de VM de Azure para cambiar la capacidad del clúster de hospedar cargas de trabajo. Las tareas completadas incluyen:

> [!div class="checklist"]
> * Escalado de los nodos de Azure para Kubernetes
> * Escalado manual de pods de Kubernetes
> * Configuración de pods de Autoscale que se ejecuten en el front-end de la aplicación

En tutoriales posteriores se actualiza la aplicación Azure Vote y se configura Log Analytics para supervisar el clúster de Kubernetes.

## <a name="before-you-begin"></a>Antes de empezar

En tutoriales anteriores se ha empaquetado una aplicación en una imagen de contenedor, se ha cargado esta imagen en Azure Container Registry y se ha creado un clúster de Kubernetes. La aplicación se ejecutó después en el clúster de Kubernetes.

Si no ha realizado estos pasos y quiere continuar, vuelva al [tutorial 1: Creación de imágenes de contenedor][aks-tutorial-prepare-app].

## <a name="scale-aks-nodes"></a>Escalado de nodos de AKS

Si creó el clúster de Kubernetes mediante los comandos en el tutorial anterior, este tiene un nodo. Puede ajustar el número de nodos manualmente si tiene previsto usar más o menos cargas de trabajo de contenedor en el clúster.

En el ejemplo siguiente, el número de nodos aumenta a tres en el clúster de Kubernetes denominado *myAKSCluster*. El comando tarda unos minutos en completarse.

```azurecli
az aks scale --resource-group=myResourceGroup --name=myAKSCluster --node-count 3
```

La salida es parecida a esta:

```
"agentPoolProfiles": [
  {
    "count": 3,
    "dnsPrefix": null,
    "fqdn": null,
    "name": "myAKSCluster",
    "osDiskSizeGb": null,
    "osType": "Linux",
    "ports": null,
    "storageProfile": "ManagedDisks",
    "vmSize": "Standard_D2_v2",
    "vnetSubnetId": null
  }
```

## <a name="manually-scale-pods"></a>Escalado manual de pods

Hasta el momento se han implementado el front-end de Azure Vote y la instancia de Redis, cada uno con una única réplica. Para comprobarlo, ejecute el comando [kubectl get][kubectl-get].

```azurecli
kubectl get pods
```

Salida:

```
NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-2549686872-4d2r5   1/1       Running   0          31m
azure-vote-front-848767080-tf34m   1/1       Running   0          31m
```

Cambie manualmente el número de pods en la implementación de `azure-vote-front` mediante el comando [kubectl scale][kubectl-scale]. En este ejemplo el número se aumenta a cinco.

```azurecli
kubectl scale --replicas=5 deployment/azure-vote-front
```

Ejecute [kubectl get pods][kubectl-get] para comprobar que Kubernetes crea los pods. Tras aproximadamente un minuto, se ejecutan los pods adicionales:

```azurecli
kubectl get pods
```

Salida:

```
NAME                                READY     STATUS    RESTARTS   AGE
azure-vote-back-2606967446-nmpcf    1/1       Running   0          15m
azure-vote-front-3309479140-2hfh0   1/1       Running   0          3m
azure-vote-front-3309479140-bzt05   1/1       Running   0          3m
azure-vote-front-3309479140-fvcvm   1/1       Running   0          3m
azure-vote-front-3309479140-hrbf2   1/1       Running   0          15m
azure-vote-front-3309479140-qphz8   1/1       Running   0          3m
```

## <a name="autoscale-pods"></a>Escalado automático de pods

Kubernetes admite el [escalado automático horizontal de pods][kubernetes-hpa] para ajustar el número de pods en una implementación en función del uso de la CPU o de otras métricas de selección.

Para usar el escalador automático, los pods deben tener solicitudes de la CPU y límites definidos. En la implementación de `azure-vote-front`, el contenedor del front-end solicita 0,25 CPU, con un límite de 0,5 CPU. La configuración tiene el siguiente aspecto:

```YAML
resources:
  requests:
     cpu: 250m
  limits:
     cpu: 500m
```

En el ejemplo siguiente se usa el comando [kubectl autoscale][kubectl-autoscale] para escalar automáticamente el número de pods en la implementación de `azure-vote-front`. En este contexto, si el uso de la CPU supera el 50 %, el escalador automático aumenta el número de pods a un máximo de 10.


```azurecli
kubectl autoscale deployment azure-vote-front --cpu-percent=50 --min=3 --max=10
```

Para ver el estado del escalador automático, ejecute el siguiente comando.

```azurecli
kubectl get hpa
```

Salida:

```
NAME               REFERENCE                     TARGETS    MINPODS   MAXPODS   REPLICAS   AGE
azure-vote-front   Deployment/azure-vote-front   0% / 50%   3         10        3          2m
```

Después de unos minutos con carga mínima en la aplicación Azure Vote, el número de réplicas del pod se reduce automáticamente a tres.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, se han usado distintas características de escalado en el clúster de Kubernetes. Las tareas tratadas incluyen:

> [!div class="checklist"]
> * Escalado manual de pods de Kubernetes
> * Configuración de pods de Autoscale que se ejecuten en el front-end de la aplicación
> * Escalado de los nodos de Azure para Kubernetes

Vaya al siguiente tutorial para aprender a actualizar la aplicación en Kubernetes.

> [!div class="nextstepaction"]
> [Actualización de una aplicación en Kubernetes][aks-tutorial-update-app]

<!-- LINKS - external -->
[kubectl-autoscale]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#autoscale
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-scale]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#scale
[kubernetes-hpa]: https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[aks-tutorial-update-app]: ./tutorial-kubernetes-app-update.md
