---
title: 'Ejemplo de script de Azure PowerShell: descarga de la plantilla de configuración de dispositivos | Microsoft Docs'
description: Descarga de la plantilla de configuración de dispositivos
services: vpn-gateway
documentationcenter: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.devlang: powershell
ms.topic: sample
ms.date: 04/17/2018
ms.author: alzam
ms.openlocfilehash: 53f7adcfbaaf84f50a6b724ef143b833a5f9bf78
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/24/2018
ms.locfileid: "42887136"
---
# <a name="download-vpn-device-template-using-powershell"></a>Descarga de la plantilla de dispositivos VPN mediante PowerShell

Este script descarga la plantilla de dispositivos VPN para una conexión


```azurepowershell-interactive
# Declare variables
$RG          = "TestRG1"
$GWName      = "VNet1GW"
$Connection  = "VNet1toSite1"

# List the available VPN device models and versions
Get-AzureRmVirtualNetworkGatewaySupportedVpnDevice -Name $GWName -ResourceGroupName $RG

# Download the configuration template for the connection
Get-AzureRmVirtualNetworkGatewayConnectionVpnDeviceConfigScript -Name $Connection -ResourceGroupName $RG `
-DeviceVendor Juniper -DeviceFamily Juniper_SRX_GA -FirmwareVersion Juniper_SRX_12.x_GA
```

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando ya no necesite los recursos que ha creado, utilice el comando [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) para eliminar el grupo de recursos. Se eliminarán el grupo de recursos y todos los recursos que contiene.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name TestRG1
```

## <a name="script-explanation"></a>Explicación del script

Este script usa los siguientes comandos para crear la implementación. Cada elemento de la tabla incluye vínculos a la documentación específica del comando.

| Get-Help | Notas |
|---|---|
| [Get-AzureRmVirtualNetworkGatewaySupportedVpnDevice](/powershell/module/azurerm.network/Get-AzureRmVirtualNetworkGatewaySupportedVpnDevice) | Muestra todos los modelos y las versiones disponibles de dispositivo VPN. |
| [Get-AzureRmVirtualNetworkGatewayConnectionVpnDeviceConfigScript](/powershell/module/azurerm.network/Get-AzureRmVirtualNetworkGatewayConnectionVpnDeviceConfigScript) | Descarga la plantilla de configuración para la conexión. |

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre el módulo de Azure PowerShell, consulte la [documentación de Azure PowerShell](/powershell/azure/overview).
