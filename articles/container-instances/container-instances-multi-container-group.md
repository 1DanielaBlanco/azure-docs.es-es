---
title: Implementación de grupos de varios contenedores en Azure Container Instances
description: Obtenga información sobre cómo implementar un grupo de contenedores con varios contenedores en Azure Container Instances.
services: container-instances
author: neilpeterson
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 06/08/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: db3f616d85c21f01c751fd82532289593a6e7e45
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/07/2018
ms.locfileid: "34850576"
---
# <a name="deploy-a-container-group"></a>Implementación de un grupo de contenedores

Azure Container Instances admite la implementación de varios contenedores en un solo host mediante un [grupo de contenedores](container-instances-container-groups.md). Esto es útil cuando se crea un sidecar de aplicación para el registro, la supervisión o cualquier otra configuración donde un servicio necesita un segundo proceso asociado.

Hay dos métodos para implementar grupos con varios contenedores mediante la CLI de Azure:

* Implementación de plantillas de Resource Manager (este artículo)
* [Implementación de archivo YAML](container-instances-multi-container-yaml.md)

La implementación con una plantilla de Resource Manager se recomienda cuando se necesita implementar recursos adicionales de un servicio de Azure adicionales (por ejemplo, un recurso compartido de Azure Files) en el momento de la implementación de una instancia de un contenedor. Dada la naturaleza más concisa del formato YAML, la implementación con un archivo YAML se recomienda cuando la implementación incluye *solo* instancias de contenedor.

> [!NOTE]
> Los grupos de varios contenedores están restringidos actualmente a los contenedores Linux. Aunque estamos trabajando para traer todas las características a los contenedores Windows, puede encontrar diferencias en la plataforma actual en la [disponibilidad de cuotas y regiones en Azure Container Instances](container-instances-quotas.md).

## <a name="configure-the-template"></a>Configuración de la plantilla

Las secciones de este artículo recorren la ejecución de una configuración de sidecar de varios contenedores sencilla mediante la implementación de una plantilla de Azure Resource Manager.

Para empezar, cree un archivo llamado `azuredeploy.json` y copie el siguiente código JSON en él.

Esta plantilla de Resource Manager define un grupo de contenedores con dos contenedores, una dirección IP pública y dos puertos expuestos. El primer contenedor del grupo ejecuta una aplicación accesible desde Internet. El segundo contenedor, el sidecar, realiza una solicitud HTTP a la aplicación web principal a través de la red local del grupo.

```JSON
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "containerGroupName": {
      "type": "string",
      "defaultValue": "myContainerGroup",
      "metadata": {
        "description": "Container Group name."
      }
    }
  },
  "variables": {
    "container1name": "aci-tutorial-app",
    "container1image": "microsoft/aci-helloworld:latest",
    "container2name": "aci-tutorial-sidecar",
    "container2image": "microsoft/aci-tutorial-sidecar"
  },
  "resources": [
    {
      "name": "[parameters('containerGroupName')]",
      "type": "Microsoft.ContainerInstance/containerGroups",
      "apiVersion": "2018-04-01",
      "location": "[resourceGroup().location]",
      "properties": {
        "containers": [
          {
            "name": "[variables('container1name')]",
            "properties": {
              "image": "[variables('container1image')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              },
              "ports": [
                {
                  "port": 80
                },
                {
                  "port": 8080
                }
              ]
            }
          },
          {
            "name": "[variables('container2name')]",
            "properties": {
              "image": "[variables('container2image')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              }
            }
          }
        ],
        "osType": "Linux",
        "ipAddress": {
          "type": "Public",
          "ports": [
            {
              "protocol": "tcp",
              "port": "80"
            },
            {
                "protocol": "tcp",
                "port": "8080"
            }
          ]
        }
      }
    }
  ],
  "outputs": {
    "containerIPv4Address": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.ContainerInstance/containerGroups/', parameters('containerGroupName'))).ipAddress.ip]"
    }
  }
}
```

Para usar un registro de imagen de contenedor privado, agregue un objeto al documento JSON con el formato siguiente. Para ver una implementación de ejemplo de esta configuración, consulte el documento [Referencia de plantilla de Resource Manager de ACI][template-reference].

```JSON
"imageRegistryCredentials": [
  {
    "server": "[parameters('imageRegistryLoginServer')]",
    "username": "[parameters('imageRegistryUsername')]",
    "password": "[parameters('imageRegistryPassword')]"
  }
]
```

## <a name="deploy-the-template"></a>Implementación de la plantilla

Cree un grupo de recursos con el comando [az group create][az-group-create].

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Implemente la plantilla con el comando [az group deployment create][az-group-deployment-create].

```azurecli-interactive
az group deployment create --resource-group myResourceGroup --template-file azuredeploy.json
```

Al cabo de unos segundos, debe recibir una respuesta inicial de Azure.

## <a name="view-deployment-state"></a>Visualización del estado de la implementación

Para ver el estado de la implementación, use el siguiente comando [az container show][az-container-show]:

```azurecli-interactive
az container show --resource-group myResourceGroup --name myContainerGroup --output table
```

Si desea ver la aplicación en ejecución, vaya a su dirección IP en su explorador. Por ejemplo, la dirección IP es `52.168.26.124` en esta salida de ejemplo:

```bash
Name              ResourceGroup    ProvisioningState    Image                                                           IP:ports               CPU/Memory       OsType    Location
----------------  ---------------  -------------------  --------------------------------------------------------------  ---------------------  ---------------  --------  ----------
myContainerGroup  myResourceGroup  Succeeded            microsoft/aci-helloworld:latest,microsoft/aci-tutorial-sidecar  52.168.26.124:80,8080  1.0 core/1.5 gb  Linux     westus
```

## <a name="view-logs"></a>Ver registros

Visualice la salida del registro de un contenedor con el comando [az container logs][az-container-logs]. El argumento `--container-name` especifica el contenedor del que se van a extraer registros. En este ejemplo, se especifica el primer contenedor.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name myContainerGroup --container-name aci-tutorial-app
```

Salida:

```bash
listening on port 80
::1 - - [09/Jan/2018:23:17:48 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [09/Jan/2018:23:17:51 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [09/Jan/2018:23:17:54 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
```

Para ver los registros para el contenedor sidecar, ejecute el mismo comando especificando el segundo nombre del contenedor.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name myContainerGroup --container-name aci-tutorial-sidecar
```

Salida:

```bash
Every 3s: curl -I http://localhost                          2018-01-09 23:25:11

  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0  1663    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
HTTP/1.1 200 OK
X-Powered-By: Express
Accept-Ranges: bytes
Cache-Control: public, max-age=0
Last-Modified: Wed, 29 Nov 2017 06:40:40 GMT
ETag: W/"67f-16006818640"
Content-Type: text/html; charset=UTF-8
Content-Length: 1663
Date: Tue, 09 Jan 2018 23:25:11 GMT
Connection: keep-alive
```

Como puede ver, el sidecar realiza periódicamente una solicitud HTTP a la aplicación web principal a través de la red local del grupo para asegurarse de que se está ejecutando. Este ejemplo de sidecar podría ampliarse para desencadenar una alerta si recibe un código de respuesta HTTP distinto de 200 OK.

## <a name="next-steps"></a>Pasos siguientes

En este artículo se explican los pasos necesarios para implementar una instancia de contenedor de Azure de varios contenedores. Para obtener una experiencia integral de Azure Container Instances, consulte el tutorial de Azure Container Instances.

> [!div class="nextstepaction"]
> [Tutorial de Azure Container Instances][aci-tutorial]

<!-- LINKS - Internal -->
[aci-tutorial]: ./container-instances-tutorial-prepare-app.md
[az-container-logs]: /cli/azure/container#az_container_logs
[az-container-show]: /cli/azure/container#az_container_show
[az-group-create]: /cli/azure/group#az_group_create
[az-group-deployment-create]: /cli/azure/group/deployment#az_group_deployment_create
[template-reference]: https://docs.microsoft.com/azure/templates/microsoft.containerinstance/containergroups
