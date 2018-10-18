---
title: 'Script de Azure PowerShell de Azure de ejemplo: creación de una máquina virtual conectando un disco administrado como disco del SO | Microsoft Docs'
description: 'Script de Azure PowerShell de Azure de ejemplo: creación de una máquina virtual conectando un disco administrado como disco del SO'
services: virtual-machines-windows
documentationcenter: virtual-machines
author: ramankum
manager: kavithag
editor: ramankum
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/10/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: d727b33c8b955667e78442387f27b9adecf66798
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/17/2018
ms.locfileid: "49386024"
---
# <a name="create-a-virtual-machine-using-an-existing-managed-os-disk-with-powershell"></a>Creación de una máquina virtual con un disco del SO administrado mediante PowerShell

Este script crea una máquina virtual conectando un disco administrado como disco del SO. Use este script en los anteriores escenarios:
* Creación de una máquina virtual desde un disco del SO administrado que se copió desde un disco administrado de otra suscripción
* Creación de una máquina virtual desde un disco administrado que se creó a partir de un archivo de disco duro virtual especializado 
* Creación de una máquina virtual desde un disco del SO administrado que se creó a partir de una instantánea 

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de ejemplo

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-vm-from-snapshot/create-vm-from-snapshot.ps1 "Create VM from snapshot")]

## <a name="clean-up-deployment"></a>Limpieza de la implementación 

Ejecute el siguiente comando para quitar el grupo de recursos, la máquina virtual y todos los recursos relacionados.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Explicación del script

Este script usa los siguientes comandos para obtener las propiedades del disco administrado, conectar un disco administrado a una nueva máquina virtual y crear una máquina virtual. Cada elemento de la tabla incluye vínculos a la documentación específica del comando.

| Get-Help | Notas |
|---|---|
| [Get-AzureRmDisk](/powershell/module/azurerm.compute/Get-AzureRmDisk) | Obtiene el objeto de disco basado en el nombre y el grupo de recursos de un disco. La propiedad de identificador del objeto de disco devuelto se utiliza para conectar el disco a una nueva máquina virtual. |
| [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig) | Crea una configuración de máquina virtual. Esta configuración incluye diversa información, como el nombre de la máquina virtual, sistema el operativo y las credenciales administrativas. La configuración se utiliza durante la creación de las máquinas virtuales. |
| [Set-AzureRmVMOSDisk](/powershell/module/azurerm.compute/set-azurermvmosdisk) | Conecta un disco administrado mediante la propiedad de identificador del disco como disco del SO a una nueva máquina virtual. |
| [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) | Crea una dirección IP pública. |
| [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) | Crea una interfaz de red. |
| [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) | Cree una máquina virtual. |
|[Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Quita un grupo de recursos y todos los recursos incluidos en él. |

Para las imágenes de Marketplace, utilice [Set-AzureRmVMPlan](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmplan?view=azurermps-6.7.0) para establecer la información del plan
```powershell
Set-AzureRmVMPlan -VM $VirtualMachine -Publisher $Publisher -Product $Product -Name $Bame
```

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre el módulo de Azure PowerShell, consulte la [documentación de Azure PowerShell](/powershell/azure/overview).

Encontrará más ejemplos de scripts de Azure PowerShell de máquina virtual en la [documentación sobre máquinas virtuales Windows de Azure](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
