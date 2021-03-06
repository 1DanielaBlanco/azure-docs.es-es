---
title: Montaje de un volumen secreto en Azure Container Instances
description: Más información acerca de cómo montar un volumen secreto para almacenar información confidencial para que accedan a ella las instancias de Container Instances
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 07/19/2018
ms.author: danlep
ms.openlocfilehash: 9aa80cf3cb02237cea11e370151eda8c67c7b10e
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/08/2018
ms.locfileid: "48856758"
---
# <a name="mount-a-secret-volume-in-azure-container-instances"></a>Montaje de un volumen secreto en Azure Container Instances

Puede usar un volumen *secreto* para suministrar información confidencial en los contenedores de un grupo de contenedores. El volumen *secreto* almacena los secretos en los archivos del volumen, accesibles para los contenedores del grupo de contenedores. Mediante el almacenamiento de secretos en un volumen *secreto*, puede evitar agregar datos confidenciales, como claves SSH o credenciales de la base de datos, en el código de la aplicación.

Todos los volúmenes *secreto* están respaldados por [tmpfs][tmpfs], un sistema de archivos basado en RAM; su contenido nunca se escribe en el almacenamiento no volátil.

> [!NOTE]
> Los volúmenes *secretos* están restringidos actualmente a los contenedores Linux. Obtenga información sobre cómo pasar variables de entorno seguras para contenedores Windows y Linux en [Establecimiento de variables de entorno](container-instances-environment-variables.md). Aunque estamos trabajando para traer todas las características a los contenedores Windows, puede encontrar diferencias en la plataforma actual en la [disponibilidad de cuotas y regiones de Azure Container Instances](container-instances-quotas.md).

## <a name="mount-secret-volume---azure-cli"></a>Montaje de un volumen secreto: CLI de Azure

Para implementar un contenedor con uno o más secretos mediante la CLI de Azure, incluya los parámetros `--secrets` y `--secrets-mount-path` en el comando [az container create][az-container-create]. En este ejemplo se monta un volumen *secreto* que consta de dos secretos, "mysecret1" y "mysecret2", en `/mnt/secrets`:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name secret-volume-demo \
    --image microsoft/aci-helloworld \
    --secrets mysecret1="My first secret FOO" mysecret2="My second secret BAR" \
    --secrets-mount-path /mnt/secrets
```

La siguiente salida de [az container exec][az-container-exec] muestra la apertura de un shell en el contenedor en ejecución, que enumera los archivos incluidos en el volumen secreto y luego muestra su contenido:

```console
$ az container exec --resource-group myResourceGroup --name secret-volume-demo --exec-command "/bin/sh"
/usr/src/app # ls -1 /mnt/secrets
mysecret1
mysecret2
/usr/src/app # cat /mnt/secrets/mysecret1
My first secret FOO
/usr/src/app # cat /mnt/secrets/mysecret2
My second secret BAR
/usr/src/app # exit
Bye.
```

## <a name="mount-secret-volume---yaml"></a>Montaje de un volumen secreto: YAML

También puede implementar grupos de contenedores con la CLI de Azure y una [plantilla YAML](container-instances-multi-container-yaml.md). La implementación mediante la plantilla YAML es el método preferido al implementar grupos de contenedores que constan de varios contenedores.

Cuando se implementa con una plantilla YAML, los valores secretos deben estar **codificados en Base64** en la plantilla. Aunque los valores del secreto aparecen en texto sin formato dentro de los archivos en el contenedor.

La siguiente plantilla YAML define un grupo de contenedores con un solo contenedor que monta un volumen *secreto* en `/mnt/secrets`. El volumen secreto tiene dos secretos, "mysecret1" y "mysecret2".

```yaml
apiVersion: '2018-06-01'
location: eastus
name: secret-volume-demo
properties:
  containers:
  - name: aci-tutorial-app
    properties:
      environmentVariables: []
      image: microsoft/aci-helloworld:latest
      ports: []
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      volumeMounts:
      - mountPath: /mnt/secrets
        name: secretvolume1
  osType: Linux
  restartPolicy: Always
  volumes:
  - name: secretvolume1
    secret:
      mysecret1: TXkgZmlyc3Qgc2VjcmV0IEZPTwo=
      mysecret2: TXkgc2Vjb25kIHNlY3JldCBCQVIK
tags: {}
type: Microsoft.ContainerInstance/containerGroups
```

Para implementar con la plantilla YAML, guarde el anterior código de YAML en un archivo denominado `deploy-aci.yaml` y luego ejecute el comando [az container create] [ az-container-create] con el parámetro `--file`:

```azurecli-interactive
# Deploy with YAML template
az container create --resource-group myResourceGroup --file deploy-aci.yaml
```

## <a name="mount-secret-volume---resource-manager"></a>Montaje de un volumen secreto: Resource Manager

Además de la implementación de CLI y YAML, puede implementar un grupo de contenedores con la [plantilla de Azure Resource Manager](/azure/templates/microsoft.containerinstance/containergroups).

En primer lugar, rellene la matriz `volumes` en la sección `properties` del grupo de contenedores de la plantilla. Cuando se implementa con una plantilla de Resource Manager, los valores secretos deben estar **codificados en Base64** en la plantilla. Aunque los valores del secreto aparecen en texto sin formato dentro de los archivos en el contenedor.

A continuación, para cada contenedor del grupo de contenedores en el que quiera montar el volumen *secreto*, rellene la matriz `volumeMounts` en la sección `properties` de la definición del contenedor.

La siguiente plantilla de Resource Manager define un grupo de contenedores con un solo contenedor que monta un volumen *secreto* en `/mnt/secrets`. El volumen secreto tiene dos secretos, "mysecret1" y "mysecret2".

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-secret.json --> [!code-json[volume-secret](~/azure-docs-json-samples/container-instances/aci-deploy-volume-secret.json)]

Para implementar con la plantilla de Resource Manager, guarde el anterior código JSON en un archivo denominado `deploy-aci.json` y luego ejecute el comando [az group deployment create] [ az-group-deployment-create] con el parámetro `--template-file`:

```azurecli-interactive
# Deploy with Resource Manager template
az group deployment create --resource-group myResourceGroup --template-file deploy-aci.json
```

## <a name="next-steps"></a>Pasos siguientes

### <a name="volumes"></a>Volúmenes

Aprenda a montar otros tipos de volúmenes en Azure Container Instances:

* [Montaje de un recurso compartido de archivos de Azure en Azure Container Instances](container-instances-volume-azure-files.md)
* [Montaje de un volumen de emptyDir en Azure Container Instances](container-instances-volume-emptydir.md)
* [Montaje de un volumen de gitRepo en Azure Container Instances](container-instances-volume-gitrepo.md)

### <a name="secure-environment-variables"></a>Variables de entorno seguras

Otro método para especificar información confidencial en contenedores (incluidos los contenedores Windows) es a través del uso de [variables de entorno seguras](container-instances-environment-variables.md#secure-values).

<!-- LINKS - External -->
[tmpfs]: https://wikipedia.org/wiki/Tmpfs

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-exec]: /cli/azure/container#az-container-exec
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
