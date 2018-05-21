---
title: Errores de no disponibilidad de la SKU de Azure| Microsoft Docs
description: Describe cómo solucionar problemas de la no disponibilidad de la SKU durante la implementación.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 03/09/2018
ms.author: tomfitz
ms.openlocfilehash: 490c912a6abd6570c9bc74de8b86a516a8e6f807
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/20/2018
---
# <a name="resolve-errors-for-sku-not-available"></a>Resolución de los errores de no disponibilidad de la SKU

Este artículo describe cómo resolver el error **SkuNotAvailable**.

## <a name="symptom"></a>Síntoma

Al implementar un recurso (normalmente una máquina virtual), puede recibir el siguiente código y mensaje de error:

```
Code: SkuNotAvailable
Message: The requested tier for resource '<resource>' is currently not available in location '<location>' 
for subscription '<subscriptionID>'. Please try another tier or deploy to a different location.
```

## <a name="cause"></a>Causa

Recibirá este error si la SKU del recurso que ha seleccionado (como, por ejemplo, el tamaño de máquina virtual) no está disponible para la ubicación seleccionada.

## <a name="solution-1---powershell"></a>Solución 1: PowerShell

Para determinar qué SKU están disponibles en una región, use el comando [Get AzureRmComputeResourceSku](/powershell/module/azurerm.compute/get-azurermcomputeresourcesku). Filtre los resultados por ubicación. Debe tener la versión más reciente de PowerShell para que funcione este comando.

```powershell
Get-AzureRmComputeResourceSku | where {$_.Locations -icontains "southcentralus"}
```

Los resultados incluyen una lista de SKU para la ubicación y las restricciones de esas SKU.

```powershell
ResourceType                Name      Locations Restriction                      Capability Value
------------                ----      --------- -----------                      ---------- -----
availabilitySets         Classic southcentralus             MaximumPlatformFaultDomainCount     3
availabilitySets         Aligned southcentralus             MaximumPlatformFaultDomainCount     3
virtualMachines      Standard_A0 southcentralus
virtualMachines      Standard_A1 southcentralus
virtualMachines      Standard_A2 southcentralus
```

## <a name="solution-2---azure-cli"></a>Solución 2: CLI de Azure

Para determinar qué SKU están disponibles en una región, use el comando `az vm list-skus`. Después, puede usar `grep` o una utilidad similar para filtrar la salida.

```bash
$ az vm list-skus --output table
ResourceType      Locations           Name                    Capabilities                       Tier      Size           Restrictions
----------------  ------------------  ----------------------  ---------------------------------  --------  -------------  ---------------------------
availabilitySets  eastus              Classic                 MaximumPlatformFaultDomainCount=3
avilabilitySets   eastus              Aligned                 MaximumPlatformFaultDomainCount=3
availabilitySets  eastus2             Classic                 MaximumPlatformFaultDomainCount=3
availabilitySets  eastus2             Aligned                 MaximumPlatformFaultDomainCount=3
availabilitySets  westus              Classic                 MaximumPlatformFaultDomainCount=3
availabilitySets  westus              Aligned                 MaximumPlatformFaultDomainCount=3
availabilitySets  centralus           Classic                 MaximumPlatformFaultDomainCount=3
availabilitySets  centralus           Aligned                 MaximumPlatformFaultDomainCount=3
```

## <a name="solution-3---azure-portal"></a>Solución 3: Azure Portal

Para determinar qué SKU están disponibles en una región, use el [portal](https://portal.azure.com). Inicie sesión en el portal y agregue un nuevo recurso a través de la interfaz. A medida que establezca los valores, verá las SKU disponibles para ese recurso. No tiene que completar la implementación.

![SKU disponibles](./media/resource-manager-sku-not-available-errors/view-sku.png)

## <a name="solution-4---rest"></a>Solución 4: REST

Para determinar qué SKU están disponibles en una región, use la API REST para máquinas virtuales. Envíe la solicitud siguiente:

```HTTP 
GET
https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Compute/skus?api-version=2016-03-30
```

Se devuelven las SKU y las regiones disponibles en el formato siguiente:

```json
{
  "value": [
    {
      "resourceType": "virtualMachines",
      "name": "Standard_A0",
      "tier": "Standard",
      "size": "A0",
      "locations": [
        "eastus"
      ],
      "restrictions": []
    },
    {
      "resourceType": "virtualMachines",
      "name": "Standard_A1",
      "tier": "Standard",
      "size": "A1",
      "locations": [
        "eastus"
      ],
      "restrictions": []
    },
    ...
  ]
}
```

Si no puede encontrar una SKU adecuada en esa región ni en ninguna región alternativa que satisfaga las necesidades de su negocio, envíe una [solicitud de SKU](https://aka.ms/skurestriction) al soporte técnico de Azure.
