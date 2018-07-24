---
title: Entidad de servicio del clúster de Azure Kubernetes
description: Cree y administre una entidad de servicio de Azure Active Directory para un clúster de Kubernetes en AKS
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: get-started-article
ms.date: 04/19/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 4dbb8b7abf6da77115d0e1d12621ec20ec60d174
ms.sourcegitcommit: 04fc1781fe897ed1c21765865b73f941287e222f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/13/2018
ms.locfileid: "39035207"
---
# <a name="service-principals-with-azure-kubernetes-service-aks"></a>Entidades de servicio con Azure Kubernetes Service (AKS)

Un clúster de AKS requiere una [entidad de servicio de Azure Active Directory][aad-service-principal] para interactuar con las API de Azure. La entidad de servicio se necesita para crear y administrar dinámicamente recursos como [Azure Load Balancer][azure-load-balancer-overview].

Este artículo muestra distintas opciones para configurar una entidad de servicio para un clúster de Kubernetes en AKS.

## <a name="before-you-begin"></a>Antes de empezar


Para crear una entidad de servicio de Azure AD, es preciso tener los permisos suficientes para registrar una aplicación en un inquilino de Azure AD y asignarle un rol en una suscripción. Si no tiene los permisos necesarios, es posible que tenga que pedir al administrador de Azure AD o de la suscripción que asigne los permisos necesarios o que crear previamente una entidad de servicio para el clúster de Kubernetes.

También es preciso que esté instalada y configurada la versión 2.0.27 de la CLI de Azure u otra posterior. Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure][install-azure-cli].

## <a name="create-sp-with-aks-cluster"></a>Creación de entidades de servicio con un clúster de AKS

Al implementar un clúster de AKS clúster con el comando `az aks create`, tiene la opción de que se genere automáticamente una entidad de servicio.

En el siguiente ejemplo, se crea un clúster de AKS y, como no se ha especificado una entidad de servicio existente, se crea una para el clúster. Para completar esta operación, la cuenta debe tener los derechos apropiados para crear un servicio principal.

```azurecli-interactive
az aks create --name myAKSCluster --resource-group myResourceGroup --generate-ssh-keys
```

## <a name="use-an-existing-sp"></a>Uso de una entidad de servicio existente

Con un clúster AKS se pueden usar las entidades de servicio de Azure AD existentes o se puede crear una previamente. Esto resulta útil cuando se implementa un clúster desde Azure Portal, donde hay que proporcionar la información de la entidad de servicio. Cuando se usa una entidad de servicio existente, el secreto de cliente debe configurarse como una contraseña.

## <a name="pre-create-a-new-sp"></a>Creación previa de una entidad de servicio nueva

Para crear la entidad de servicio con la CLI de Azure, utilice el comando [az ad sp create-for-rbac][az-ad-sp-create].

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

La salida es similar a la siguiente. Tome nota de `appId` y `password`. Estos valores se usan al crear un clúster de AKS.

```json
{
  "appId": "7248f250-0000-0000-0000-dbdeb8400d85",
  "displayName": "azure-cli-2017-10-15-02-20-15",
  "name": "http://azure-cli-2017-10-15-02-20-15",
  "password": "77851d2c-0000-0000-0000-cb3ebc97975a",
  "tenant": "72f988bf-0000-0000-0000-2d7cd011db47"
}
```

## <a name="use-an-existing-sp"></a>Uso de una entidad de servicio existente

Si usa una entidad de servicio creada previamente, incluya `appId` y `password` como valores de los argumentos del comando `az aks create`.

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myAKSCluster --service-principal <appId> --client-secret <password>
```

Si va a implementar un clúster de AKS mediante Azure Portal, escriba el valor `appId` en el campo **Identificador de cliente de la entidad de servicio** y el valor `password` en el campo **Service principal client secret** (Secreto de cliente de la entidad de servicio) en el formulario de configuración del clúster de AKS.

![Imagen de la exploración hasta Azure Vote](media/container-service-kubernetes-service-principal/sp-portal.png)

## <a name="additional-considerations"></a>Consideraciones adicionales

Cuando trabaje con entidades de servicio de AKS y Azure AD, tenga en cuenta lo siguiente.

* La entidad de servicio para Kubernetes forma parte de la configuración del clúster. Sin embargo, no use la identidad para implementar el clúster.
* Cada entidad de servicio está asociada a una aplicación de Azure AD. La entidad de servicio de un clúster de Kubernetes puede asociarse con cualquier nombre de aplicación de Azure AD válido (por ejemplo, `https://www.contoso.org/example`). La dirección URL de la aplicación no tiene por qué ser un punto de conexión real.
* Al especificar el valor de **Identificador de cliente** de la entidad de servicio, use el valor de `appId` (como se muestra en este artículo) o la entidad de servicio correspondiente `name` (por ejemplo, `https://www.contoso.org/example`).
* En las máquinas virtuales principal y nodo del clúster de Kubernetes, las credenciales de la entidad de servicio se almacenan en el archivo `/etc/kubernetes/azure.json`.
* Cuando use el comando `az aks create` para generar la entidad de servicio automáticamente, sus credenciales se escriben en el archivo `~/.azure/aksServicePrincipal.json` de la máquina que se usa para ejecutar el comando.
* Al eliminar un clúster de AKS creado mediante `az aks create`, no se elimina la entidad de servicio que se creó automáticamente. Para eliminar la entidad de servicio, primero hay que obtener el identificador para el servicio principal con [az ad app list][az-ad-app-list]. En el ejemplo siguiente se consulta el clúster denominado *myAKSCluster* y, a continuación, se elimina el identificador de aplicación con [az ad app delete][az-ad-app-delete]. Reemplace estos nombres con sus propios valores:

    ```azurecli-interactive
    az ad app list --query "[?displayName=='myAKSCluster'].{Name:displayName,Id:appId}" --output table
    az ad app delete --id <appId>
    ```

## <a name="next-steps"></a>Pasos siguientes

Para más información acerca de las entidades de servicio de Azure Active Directory, consulte la documentación de las aplicaciones de Azure AD.

> [!div class="nextstepaction"]
> [Objetos de aplicación y de entidad de servicio][service-principal]

<!-- LINKS - internal -->
[aad-service-principal]: ../active-directory/develop/active-directory-application-objects.md
[acr-intro]: ../container-registry/container-registry-intro.md
[az-ad-sp-create]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
[azure-load-balancer-overview]: ../load-balancer/load-balancer-overview.md
[install-azure-cli]: /cli/azure/install-azure-cli
[service-principal]: ../active-directory/develop/active-directory-application-objects.md
[user-defined-routes]: ../load-balancer/load-balancer-overview.md
[az-ad-app-list]: /cli/azure/ad/app#az-ad-app-list
[az-ad-app-delete]: /cli/azure/ad/app#az-ad-app-delete
