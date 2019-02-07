---
title: Uso de la extensión de máquina virtual de Docker para Azure | Microsoft Docs
description: Información sobre cómo usar la extensión de máquina virtual de Docker para implementar de forma rápida y segura un entorno de Docker en Azure mediante las plantillas de Resource Manager y la CLI de Azure
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
ms.assetid: 936d67d7-6921-4275-bf11-1e0115e66b7f
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/18/2017
ms.author: cynthn
ms.openlocfilehash: 1bc250ac70e48a548d393c3bc6025868948dc022
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/04/2019
ms.locfileid: "55699166"
---
# <a name="create-a-docker-environment-in-azure-using-the-docker-vm-extension"></a>Creación de un entorno de Docker para Azure mediante la extensión de máquina virtual de Docker

Docker es una conocida plataforma de creación de imágenes y administración de contenedores que permite trabajar rápidamente con contenedores en Linux. En Azure, hay diversas maneras de implementar Docker según sus necesidades. Este artículo se centra en el uso de la extensión de máquina virtual de Docker y las plantillas de Azure Resource Manager con la CLI de Azure. 

> [!WARNING]
> La extensión de máquina virtual de Azure Docker para Linux está en desuso y se retirará en noviembre de 2018.
> La extensión simplemente instala Docker, por lo que las alternativas, como cloud-init o la extensión de script personalizado, son una mejor manera de instalar la versión de Docker de su elección. Para obtener más información sobre cómo usar cloud-init, consulte [Personalización de una máquina virtual Linux en el primer arranque](tutorial-automate-vm-deployment.md).

## <a name="azure-docker-vm-extension-overview"></a>Introducción a la extensión de máquina virtual de Docker para Azure
La extensión de máquina virtual de Docker para Azure permite instalar y configurar el demonio de Docker, el cliente de Docker y Docker Compose en la máquina virtual (VM) Linux. Con la extensión de máquina virtual de Docker para Azure, dispone de más control y características que si solo usa Docker Machine o si crea el host de Docker por su cuenta. Gracias a estas características adicionales, como [Docker Compose](https://docs.docker.com/compose/overview/), la extensión de máquina virtual de Docker para Azure es adecuada para entornos de producción o desarrollo más sólidos.

Para más información sobre los diferentes métodos de implementación, incluido el uso de Docker Machine e instancias de Azure Container Service, consulte los artículos siguientes:

* Para crear rápidamente el prototipo de una aplicación, puede crear un solo host de Docker con [Docker Machine](docker-machine.md).
* Para crear entornos escalables listos para la producción que añadan herramientas de administración y programación, puede implementar un clúster de [Kubernetes](../../container-service/kubernetes/index.yml) o [Docker Swarm](../../container-service/dcos-swarm/index.yml) en instancias de Azure Container Service.


## <a name="deploy-a-template-with-the-azure-docker-vm-extension"></a>Implementación de una plantilla con la extensión de máquina virtual de Docker para Azure
Aquí se va a usar una plantilla de inicio rápido existente para crear una máquina virtual Ubuntu que use la extensión de máquina virtual de Docker para Azure para instalar y configurar el host de Docker. Puede ver la plantilla aquí: [Implementación sencilla de una máquina virtual Ubuntu con Docker](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu). Necesita tener instalada la versión más reciente de la [CLI de Azure](/cli/azure/install-az-cli2) y haber iniciado sesión en una cuenta de Azure con [az login](/cli/azure/reference-index).

En primer lugar, cree un grupo de recursos con [az group create](/cli/azure/group). En el ejemplo siguiente, se crea un grupo de recursos denominado *myResourceGroup* en la ubicación *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

A continuación, implemente una VM con [az group deployment create](/cli/azure/group/deployment), que incluye la extensión de VM de Docker para Azure de [esta plantilla de Azure Resource Manager en GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu). Proporcione sus propios valores únicos para *newStorageAccountName*, *adminUsername*, *adminPassword* y *dnsNameForPublicIP*:

```azurecli
az group deployment create --resource-group myResourceGroup \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/docker-simple-on-ubuntu/azuredeploy.json
```

La implementación tarda unos minutos en finalizar.


## <a name="deploy-your-first-nginx-container"></a>Implementación del primer contenedor NGINX
Para ver detalles de la máquina virtual, incluido el nombre DNS, use [az vm show](/cli/azure/vm):

```azurecli
az vm show \
    --resource-group myResourceGroup \
    --name myDockerVM \
    --show-details \
    --query [fqdns] \
    --output tsv
```

SSH al nuevo host Docker. Proporcione su propio nombre de usuario y nombre DNS de los pasos anteriores:

```bash
ssh azureuser@mypublicdns.eastus.cloudapp.azure.com
```

Una vez que haya iniciado sesión en el host de Docker, se va a ejecutar un contenedor NGINX:

```bash
sudo docker run -d -p 80:80 nginx
```

La salida se parece al siguiente ejemplo, donde se descarga la imagen NGINX y se inicia un contenedor:

```bash
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
efd26ecc9548: Pull complete
a3ed95caeb02: Pull complete
a48df1751a97: Pull complete
8ddc2d7beb91: Pull complete
Digest: sha256:2ca2638e55319b7bc0c7d028209ea69b1368e95b01383e66dfe7e4f43780926d
Status: Downloaded newer image for nginx:latest
b6ed109fb743a762ff21a4606dd38d3e5d35aff43fa7f12e8d4ed1d920b0cd74
```

Compruebe el estado de los contenedores que se ejecutan en el host de Docker como se indica a continuación:

```bash
sudo docker ps
```

La salida es similar al ejemplo siguiente, que muestra que se está ejecutando el contenedor NGINX y se está efectuando el reenvío a los puertos TCP 80 y 443:

```bash
CONTAINER ID        IMAGE               COMMAND                  CREATED              STATUS              PORTS                         NAMES
b6ed109fb743        nginx               "nginx -g 'daemon off"   About a minute ago   Up About a minute   0.0.0.0:80->80/tcp, 443/tcp   adoring_payne
```

Para ver el contenedor en acción, abra un explorador web y escriba el nombre DNS del host de Docker:

![Ejecución del contenedor ngnix](./media/dockerextension/nginxrunning.png)

## <a name="azure-docker-vm-extension-template-reference"></a>Referencia sobre la plantilla de la extensión de máquina virtual de Docker para Azure
En el ejemplo anterior, se usa una plantilla de inicio rápido existente. También puede implementar la extensión de máquina virtual de Docker para Azure con sus propias plantillas de Resource Manager. Para hacerlo, agregue lo siguiente a sus plantillas de Resource Manager y defina el elemento `vmName` de la máquina virtual correctamente:

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "[concat(variables('vmName'), '/DockerExtension'))]",
  "apiVersion": "2015-05-01-preview",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
  ],
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "DockerExtension",
    "typeHandlerVersion": "1.*",
    "autoUpgradeMinorVersion": true,
    "settings": {},
    "protectedSettings": {}
  }
}
```

Puede encontrar un tutorial más detallado sobre el uso de plantillas de Resource Manager en [Información general de Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).

## <a name="next-steps"></a>Pasos siguientes
Es posible que desee [configurar el puerto TCP del demonio de Docker](https://docs.docker.com/engine/reference/commandline/dockerd/#/bind-docker-to-another-hostport-or-a-unix-socket), comprender la [seguridad de Docker](https://docs.docker.com/engine/security/security/) o implementar contenedores mediante [Docker Compose](https://docs.docker.com/compose/overview/). Para más información sobre la extensión de máquina virtual de Docker para Azure en sí, consulte el [proyecto de GitHub](https://github.com/Azure/azure-docker-extension/).

Lea más información sobre las opciones de implementación adicionales de Docker en Azure:

* [Uso de una máquina de Docker con el controlador de Azure](docker-machine.md)  
* [Introducción a Docker y Compose para definir y ejecutar una aplicación de contenedores múltiples en una máquina virtual de Azure](docker-compose-quickstart.md).
* [Implementación de un clúster del servicio Contenedor de Azure](../../container-service/dcos-swarm/container-service-deployment.md)

