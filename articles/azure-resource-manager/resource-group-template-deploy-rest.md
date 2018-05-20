---
title: Implementación de recursos con la API de REST y plantilla | Microsoft Docs
description: Use Azure Resource Manager y la API de REST de Resource Manager para implementar recursos en Azure. Los recursos se definen en una plantilla de Resource Manager.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 1d8fbd4c-78b0-425b-ba76-f2b7fd260b45
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/01/2018
ms.author: tomfitz
ms.openlocfilehash: bf2fc2aeb094a828fa1efe6904b897f3a4ab46d8
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
---
# <a name="deploy-resources-with-resource-manager-templates-and-resource-manager-rest-api"></a>Implementación de recursos con las plantillas de Resource Manager y la API de REST de Resource Manager

En este artículo se explica cómo usar la API de REST de Resource Manager con plantillas de Resource Manager para implementar sus recursos en Azure.  

> [!TIP]
> Para obtener ayuda con la depuración de un error durante la implementación, consulte:
> 
> * [Visualización de operaciones de implementación](resource-manager-deployment-operations.md) para obtener información que lo ayude a solucionar el error
> * [Solución de problemas comunes al implementar recursos en Azure con Azure Resource Manager](resource-manager-common-deployment-errors.md) para obtener información sobre cómo resolver errores comunes de implementación
> 
> 

La plantilla puede ser un archivo local o en un archivo externo que está disponible a través de un identificador URI. Cuando la plantilla se encuentra en una cuenta de almacenamiento, puede restringir el acceso a la plantilla y proporcionar un token de firma de acceso compartido (SAS) durante la implementación.

[!INCLUDE [resource-manager-deployments](../../includes/resource-manager-deployments.md)]

## <a name="deploy-with-the-rest-api"></a>Implementación con la API de REST
1. Establezca los [encabezados y parámetros comunes](/rest/api/azure/), incluidos los tokens de autenticación.

2. Si no tiene un grupo de recursos existente, puede crear uno. Especifique el identificador de la suscripción, el nombre del nuevo grupo de recursos y la ubicación que necesita para la solución. Para obtener más información, consulte [Crear un grupo de recursos](/rest/api/resources/resourcegroups/createorupdate).

  ```HTTP
  PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>?api-version=2015-01-01
  {
    "location": "West US",
    "tags": {
      "tagname1": "tagvalue1"
    }
  }
  ```

3. Valide la implementación antes de ejecutarla. Para ello, ejecute la operación de [validación de una implementación de plantilla](/rest/api/resources/deployments/validate). Al probar la implementación, proporcione los parámetros exactamente como lo haría al ejecutar la implementación (como se muestra en el paso siguiente).

4. Cree una implementación. Especifique el identificador de suscripción, el nombre del grupo de recursos, el nombre de la implementación y un vínculo a su plantilla. Para obtener información sobre el archivo de plantilla, consulte [Archivo de parámetros](#parameter-file). Para obtener más información acerca de la API de REST para crear un grupo de recursos, consulte [Creación de una implementación de plantilla](https://docs.microsoft.com/rest/api/resources/deployments#Deployments_CreateOrUpdate). Observe que el **modo** se establece en **Incremental**. Para ejecutar una implementación completa, establezca el **modo** en **Completo**. Tenga cuidado al usar este modo, ya que puede eliminar accidentalmente los recursos que no estén en la plantilla.

  ```HTTP
  PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2015-01-01
  {
    "properties": {
      "templateLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
        "contentVersion": "1.0.0.0"
      },
      "mode": "Incremental",
      "parametersLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
        "contentVersion": "1.0.0.0"
      }
    }
  }
  ```

    Si desea registrar el contenido de la respuesta y el de la solicitud, o ambos, incluya **debugSetting** en la solicitud.

  ```HTTP
  "debugSetting": {
    "detailLevel": "requestContent, responseContent"
  }
  ```

    Puede configurar la cuenta de almacenamiento para utilizar un token de firma de acceso compartido (SAS). Para obtener más información, consulte [Delegating Access with a Shared Access Signature](https://docs.microsoft.com/rest/api/storageservices/delegating-access-with-a-shared-access-signature)(Delegación del acceso con una firma de acceso compartido).

5. Obtenga el estado de la implementación de la plantilla. Para obtener más información, consulte [Obtener información acerca de una implementación de plantilla](/rest/api/resources/deployments/get).

  ```HTTP
  GET https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2015-01-01
  ```

## <a name="redeploy-when-deployment-fails"></a>Nueva implementación cuando se produce un error en la implementación

Si se produce un error en las implementaciones, puede especificar que se vuelva a implementar automáticamente una implementación anterior desde el historial de implementaciones. Para usar esta opción, las implementaciones deben tener nombres únicos para que se puedan identificar en el historial. Si no tienen nombres únicos, la implementación con error en cuestión podría sobrescribir la implementación anteriormente correcta en el historial. Solo se puede usar esta opción con las implementaciones de nivel de raíz. Las implementaciones de una plantilla anidada no están disponibles para volver a implementarse.

Para volver a implementar la última implementación correcta si se produce un error en la actual, use:

```HTTP
"onErrorDeployment": {
  "type": "LastSuccessful",
},
```

Para volver a implementar una implementación específica si se produce un error en la implementación actual, use:

```HTTP
"onErrorDeployment": {
  "type": "SpecificDeployment",
  "deploymentName": "<deploymentname>"
}
```

La implementación especificada debe haberse realizado correctamente.

## <a name="parameter-file"></a>Archivo de parámetros

Si utiliza un archivo de parámetros para pasar los valores de parámetro durante la implementación, tendrá que crear un archivo JSON con un formato similar al del ejemplo siguiente:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "webSiteName": {
            "value": "ExampleSite"
        },
        "webSiteHostingPlanName": {
            "value": "DefaultPlan"
        },
        "webSiteLocation": {
            "value": "West US"
        },
        "adminPassword": {
            "reference": {
               "keyVault": {
                  "id": "/subscriptions/{guid}/resourceGroups/{group-name}/providers/Microsoft.KeyVault/vaults/{vault-name}"
               }, 
               "secretName": "sqlAdminPassword" 
            }   
        }
   }
}
```

El tamaño del archivo de parámetros no puede ser superior a 64 KB.

Si necesita proporcionar un valor confidencial para un parámetro (por ejemplo, una contraseña), agregue ese valor a un almacén de claves. Recupere el almacén de claves durante la implementación, como se muestra en el ejemplo anterior. Para más información, consulte [Paso de valores seguros durante la implementación](resource-manager-keyvault-parameter.md). 

## <a name="next-steps"></a>Pasos siguientes
* Para obtener información sobre el control de operaciones asincrónicas de REST, vea [Seguimiento de las operaciones asincrónicas de Azure](resource-manager-async-operations.md).
* Para ver un ejemplo de cómo implementar los recursos mediante la biblioteca cliente de .NET, consulte [Deploy Azure resources using .NET libraries and a template](../virtual-machines/windows/csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)(Implementación de recursos de Azure mediante bibliotecas de .NET y una plantilla).
* Para definir parámetros de plantilla, consulte [Creación de plantillas](resource-group-authoring-templates.md#parameters).
* Para obtener instrucciones sobre cómo implementar la solución en diferentes entornos, vea [Entornos de desarrollo y pruebas en Microsoft Azure](solution-dev-test-environments.md).
* Para obtener instrucciones sobre cómo las empresas pueden utilizar Resource Manager para administrar eficazmente las suscripciones, vea [Scaffold empresarial de Azure: Gobierno de suscripción prescriptivo](resource-manager-subscription-governance.md).

