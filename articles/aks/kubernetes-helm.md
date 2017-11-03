---
title: "Implementación de contenedores con Helm en Kubernetes en Azure | Microsoft Docs"
description: "Uso de la herramienta de empaquetado de Helm para implementar contenedores en un clúster de Kubernetes en AKS"
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: aks, azure-container-service
keywords: 
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/24/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: de52c2aad0f74b59970234872dfa3e4136929915
ms.sourcegitcommit: c5eeb0c950a0ba35d0b0953f5d88d3be57960180
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/24/2017
---
# <a name="use-helm-with-azure-container-service-aks"></a>Uso de Helm con Azure Container Service (AKS)

[Helm](https://github.com/kubernetes/helm/) es una herramienta de empaquetado de código abierto que ayuda a instalar y administrar el ciclo de vida de las aplicaciones de Kubernetes. Helm, que funciona de forma similar a los administradores de paquetes de Linux como *APT* y *Yum*, se utiliza para administrar los gráficos de Kubernetes, que son paquetes de recursos de Kubernetes preconfigurados.

Este documento trata la configuración y uso de Helm en un clúster de Kubernetes en AKS.

## <a name="before-you-begin"></a>Antes de empezar

En los pasos que se detallan en este documento se asume que se ha creado un clúster de AKS y que ha establecido una conexión kubectl con dicho clúster. Si necesita estos elementos, vea la [Guía de inicio rápido de AKS](./kubernetes-walkthrough.md).

## <a name="install-helm-cli"></a>Instalación de la CLI de Helm

La CLI de Helm es un cliente que se ejecuta en su sistema de desarrollo, y que permite iniciar, detener y administrar aplicaciones con gráficos de Helm.

Si usa Azure CloudShell, la CLI de Helm ya estará instalada. Para instalar la CLI de Helm en un equipo Mac, use `brew`. Para conocer otras opciones de instalación, vea [Installing Helm](https://github.com/kubernetes/helm/blob/master/docs/install.md) (Instalación de Helm).

```console
brew install kubernetes-helm
```

Salida:

```
==> Downloading https://homebrew.bintray.com/bottles/kubernetes-helm-2.6.2.sierra.bottle.1.tar.gz
######################################################################## 100.0%
==> Pouring kubernetes-helm-2.6.2.sierra.bottle.1.tar.gz
==> Caveats
Bash completion has been installed to:
  /usr/local/etc/bash_completion.d
==> Summary
🍺  /usr/local/Cellar/kubernetes-helm/2.6.2: 50 files, 132.4MB
```

## <a name="configure-helm"></a>Configuración de Helm

El comando [helm init](https://docs.helm.sh/helm/#helm-init) se utiliza para instalar componentes de Helm en un clúster de Kubernetes y realizar configuraciones del cliente. Helm se encuentra preinstalado en los clústeres de AKS, por lo que solo es necesaria la configuración del cliente. Ejecute el siguiente comando para configurar el cliente Helm.

```azurecli-interactive
helm init --client-only
```

Salida:

```
$HELM_HOME has been configured at /Users/neilpeterson/.helm.
Not installing Tiller due to 'client-only' flag having been set
Happy Helming!
```

## <a name="find-helm-charts"></a>Búsqueda de gráficos de Helm

Los gráficos de Helm se usan para implementar aplicaciones en un clúster de Kubernetes. Para buscar los gráficos de Helm creados previamente, use el comando [helm search](https://docs.helm.sh/helm/#helm-search).

```azurecli-interactive
helm search
```

El resulta debe ser similar al siguiente, pero con más gráficos.

```
NAME                            VERSION DESCRIPTION
stable/acs-engine-autoscaler    2.0.0   Scales worker nodes within agent pools
stable/artifactory              6.1.0   Universal Repository Manager supporting all maj...
stable/aws-cluster-autoscaler   0.3.1   Scales worker nodes within autoscaling groups.
stable/buildkite                0.2.0   Agent for Buildkite
stable/centrifugo               2.0.0   Centrifugo is a real-time messaging server.
stable/chaoskube                0.5.0   Chaoskube periodically kills random pods in you...
stable/chronograf               0.3.0   Open-source web application written in Go and R...
stable/cluster-autoscaler       0.2.0   Scales worker nodes within autoscaling groups.
stable/cockroachdb              0.5.0   CockroachDB is a scalable, survivable, strongly...
stable/concourse                0.7.0   Concourse is a simple and scalable CI system.
stable/consul                   0.4.1   Highly available and distributed service discov...
stable/coredns                  0.5.0   CoreDNS is a DNS server that chains middleware ...
stable/coscale                  0.2.0   CoScale Agent
stable/dask-distributed         2.0.0   Distributed computation in Python
stable/datadog                  0.8.0   DataDog Agent
...
```

Para actualizar la lista de gráficos, use el comando [helm repo update](https://docs.helm.sh/helm/#helm-repo-update).

```azurecli-interactive
helm repo update
```

Salida:

```
Hang tight while we grab the latest from your chart repositories...
...Skip local chart repository
...Successfully got an update from the "stable" chart repository
Update Complete. ⎈ Happy Helming!⎈
```

## <a name="run-helm-charts"></a>Ejecución de gráficos de Helm

Para implementar la controladora de entrada NGINX, use el comando [helm install](https://docs.helm.sh/helm/#helm-install).

```azurecli-interactive
helm install stable/nginx-ingress
```

El resultado es similar al siguiente, pero incluye información adicional como instrucciones sobre cómo usar la implementación de Kubernetes.

```
NAME:   tufted-ocelot
LAST DEPLOYED: Thu Oct  5 00:48:04 2017
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/ConfigMap
NAME                                    DATA  AGE
tufted-ocelot-nginx-ingress-controller  1     5s

==> v1/Service
NAME                                         CLUSTER-IP   EXTERNAL-IP  PORT(S)                     AGE
tufted-ocelot-nginx-ingress-controller       10.0.140.10  <pending>    80:30486/TCP,443:31358/TCP  5s
tufted-ocelot-nginx-ingress-default-backend  10.0.34.132  <none>       80/TCP                      5s

==> v1beta1/Deployment
NAME                                         DESIRED  CURRENT  UP-TO-DATE  AVAILABLE  AGE
tufted-ocelot-nginx-ingress-controller       1        1        1           0          5s
tufted-ocelot-nginx-ingress-default-backend  1        1        1           1          5s
...
```

Para más información sobre el uso de la controladora de entrada NGINX con Kubernetes, vea [Controladora de entrada NGINX](https://github.com/kubernetes/ingress/tree/master/controllers/nginx).

## <a name="list-helm-charts"></a>Enumeración de gráficos de Helm

Para ver una lista de gráficos instalados en el clúster, use el comando [helm list](https://docs.helm.sh/helm/#helm-list).

```azurecli-interactive
helm list
```

Salida:

```
NAME            REVISION    UPDATED                     STATUS      CHART               NAMESPACE
bilging-ant     1           Thu Oct  5 00:11:11 2017    DEPLOYED    nginx-ingress-0.8.7 default
```

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre cómo administrar gráficos de Kubernetes, vea la documentación de Helm.

> [!div class="nextstepaction"]
> [Documentación de Helm](https://github.com/kubernetes/helm/blob/master/docs/index.md)