---
title: Administración de un clúster de Azure Kubernetes con una interfaz de usuario web
description: Uso del panel de Kubernetes en AKS
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 02/24/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: ab137c8397f747ba07475910cd4461d88951d6be
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
---
# <a name="kubernetes-dashboard-with-azure-kubernetes-service-aks"></a>Panel de Kubernetes con Azure Kubernetes Service (AKS)

La CLI de Azure puede utilizarse para iniciar el panel de Kubernetes. Este documento le guía por el inicio del panel de Kubernetes con la CLI de Azure y también a través de algunas operaciones básicas del panel. Para obtener más información sobre el panel de Kubernetes, vea [Kubernetes Web UI Dashboard][kubernetes-dashboard] (Panel de la interfaz de usuario web de Kubernetes).

## <a name="before-you-begin"></a>Antes de empezar

En los pasos que se detallan en este documento se asume que se ha creado un clúster de AKS y que ha establecido una conexión kubectl con dicho clúster. Si necesita estos elementos, vea la [Guía de inicio rápido de AKS][aks-quickstart].

También es preciso que esté instalada y configurada la versión 2.0.27 de la CLI de Azure u otra posterior. Para saber qué versión tiene, ejecute el comando az --version. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure][install-azure-cli].

## <a name="start-kubernetes-dashboard"></a>Inicio del panel de Kubernetes

Utilice el comando `az aks browse` para iniciar el panel de Kubernetes. Cuando ejecute este comando, reemplace el nombre de clúster y de grupo de recursos.

```azurecli
az aks browse --resource-group myResourceGroup --name myAKSCluster
```

Este comando crea a un proxy entre el sistema de desarrollo y la API de Kubernetes y abre un explorador web en el panel de Kubernetes.

## <a name="run-an-application"></a>Ejecución de una aplicación

En el panel de Kubernetes, haga clic en el botón **Create** (Crear) de la ventana derecha superior. Asigne el nombre de la implementación `nginx` y escriba `nginx:latest` para el nombre de las imágenes. En **Service** (Servicio), seleccione **External** (Externo) y escriba `80` para el puerto y el puerto de destino.

Cuando esté listo, haga clic en **Deploy** (Implementar) para crear la implementación.

![Cuadro de diálogo de creación del servicio de Kubernetes](./media/container-service-kubernetes-ui/create-deployment.png)

## <a name="view-the-application"></a>Visualización de la aplicación

El estado de la aplicación puede verse en el panel de Kubernetes. Una vez que se ejecuta la aplicación, cada componente tiene una casilla verde junto a ella.

![Pod de Kubernetes](./media/container-service-kubernetes-ui/complete-deployment.png)

Para más información sobre los pods de aplicación, haga clic en **Pod** en el menú izquierdo y, luego, seleccione el pod **NGINX**. Aquí puede ver información específica del pod como el consumo de recursos.

![Recursos de Kubernetes](./media/container-service-kubernetes-ui/running-pods.png)

Para buscar la dirección IP de la aplicación, haga clic en **Services** (Servicios) en el menú izquierdo y, luego, seleccione el servicio **NGINX**.

![vista de nginx](./media/container-service-kubernetes-ui/nginx-service.png)

## <a name="edit-the-application"></a>Edición de aplicación

Además de crear y visualizar las aplicaciones, el panel de Kubernetes puede utilizarse para editar y actualizar las implementaciones de aplicaciones.

Para editar una implementación, haga clic en **Deployments** (Implementaciones) en el menú izquierdo y, luego, seleccione la implementación **NGINX**. Por último, seleccione **Edit** (Editar) en la barra de navegación superior derecha.

![Edición de Kubernetes](./media/container-service-kubernetes-ui/view-deployment.png)

Busque el valor `spec.replica`, que debe ser 1, y cambie este valor a 3. Si lo hace, el número de réplicas de la implementación NGINX aumenta de 1 a 3.

Seleccione **Update** (Actualizar) cuando esté listo.

![Edición de Kubernetes](./media/container-service-kubernetes-ui/edit-deployment.png)

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre el panel de Kubernetes, vea la documentación de Kubernetes.

> [!div class="nextstepaction"]
> [Kubernetes Web UI Dashboard][kubernetes-dashboard] (Panel de la interfaz de usuario web de Kubernetes)

<!-- LINKS - external -->
[kubernetes-dashboard]: https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[install-azure-cli]: /cli/azure/install-azure-cli