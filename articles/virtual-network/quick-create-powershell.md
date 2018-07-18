---
title: 'Guía de inicio rápido: Creación de una red virtual mediante Azure PowerShell | Microsoft Docs'
description: En esta guía de inicio rápido, aprenderá a crear una red virtual mediante Azure Portal. Una red virtual permite que los recursos de Azure, como las máquinas virtuales, se comuniquen entre sí de forma privada y con Internet.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I want to create a virtual network so that virtual machines can communicate with privately with each other and with the internet.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: quickstart
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/09/2018
ms.author: jdial
ms.custom: mvc
ms.openlocfilehash: b8b67b235b54fb5bde738ed5cc1605e08d182a69
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2018
ms.locfileid: "38688092"
---
# <a name="quickstart-create-a-virtual-network-using-powershell"></a>Guía de inicio rápido: Creación de una red virtual mediante PowerShell

Una red virtual permite que los recursos de Azure, como máquinas virtuales (VM), se comuniquen entre sí de forma privada y con Internet. En esta guía de inicio rápido aprenderá a crear una red virtual. Después de crear una red virtual, implementará dos máquinas virtuales en la red virtual. Luego, se conectará a una máquina virtual desde Internet y se comunicará de forma privada entre las dos máquinas virtuales.

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-powershell.md)]

Si decide instalar y usar PowerShell de forma local, para esta guía necesitará la versión 5.4.1 del módulo de AzureRM PowerShell, o cualquier versión posterior. Ejecute ` Get-Module -ListAvailable AzureRM` para ver cuál es la versión instalada. Si necesita actualizarla, consulte [Instalación del módulo de Azure PowerShell](/powershell/azure/install-azurerm-ps). Si PowerShell se ejecuta localmente, también debe ejecutar `Connect-AzureRmAccount` para crear una conexión con Azure.

## <a name="create-a-virtual-network"></a>Creación de una red virtual

Para poder crear una red virtual, debe crear un grupo de recursos que contenga la red virtual. Cree un grupo de recursos con [New-AzureRmResourceGroup](/powershell/module/AzureRM.Resources/New-AzureRmResourceGroup). En el ejemplo siguiente, se crea un grupo de recursos denominado *myResourceGroup* en la ubicación *eastus*.

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

Cree una red virtual con [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork). En el ejemplo siguiente se crea una red virtual predeterminada denominada *myVirtualNetwork* en la ubicación *EastUS*:

```azurepowershell-interactive
$virtualNetwork = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

Los recursos de Azure se implementan en una subred dentro de una red virtual, por lo que no es necesario crear una subred. Cree una configuración de subred con [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig). 

```azurepowershell-interactive
$subnetConfig = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name default `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork
```

Escriba la configuración de subred en la red virtual con el cmdlet [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/Set-AzureRmVirtualNetwork), que crea la subred dentro de la red virtual:

```azurepowershell-interactive
$virtualNetwork | Set-AzureRmVirtualNetwork
```

## <a name="create-virtual-machines"></a>Creación de máquinas virtuales

Cree dos máquinas virtuales en la red virtual:

### <a name="create-the-first-vm"></a>Creación de la primera máquina virtual

Cree una máquina virtual con [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). Cuando ejecute el comando siguiente, se le solicitarán las credenciales. Los valores que especifique se configuran como el nombre de usuario y la contraseña de la máquina virtual. La opción `-AsJob` crea la máquina virtual en segundo plano, así que puede continuar con el siguiente paso.

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroup" `
    -Location "East US" `
    -VirtualNetworkName "myVirtualNetwork" `
    -SubnetName "default" `
    -Name "myVm1" `
    -AsJob
```

Se devuelve un resultado similar al del ejemplo siguiente, y Azure comienza a crear la máquina virtual en segundo plano.

```powershell
Id     Name            PSJobTypeName   State         HasMoreData     Location             Command                  
--     ----            -------------   -----         -----------     --------             -------                  
1      Long Running... AzureLongRun... Running       True            localhost            New-AzureRmVM     
```

### <a name="create-the-second-vm"></a>Creación de la segunda máquina virtual 

Escriba el comando siguiente:

```azurepowershell-interactive
New-AzureRmVm `
  -ResourceGroupName "myResourceGroup" `
  -VirtualNetworkName "myVirtualNetwork" `
  -SubnetName "default" `
  -Name "myVm2"
```

La máquina virtual tarda en crearse unos minutos. No continúe con el paso siguiente hasta que el comando anterior se ejecute y se devuelva la salida a PowerShell.

## <a name="connect-to-a-vm-from-the-internet"></a>Conexión a una máquina virtual desde Internet

Use [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) para devolver la dirección IP pública de una máquina virtual. En el siguiente ejemplo se devuelve la dirección IP pública de la máquina virtual *myVm1*:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVm1 `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Reemplace `<publicIpAddress>` en el siguiente comando, por la dirección IP pública devuelta por el comando anterior y, a continuación, escriba el siguiente comando: 

```
mstsc /v:<publicIpAddress>
```

Se crea un archivo de Protocolo de Escritorio remoto (.rdp) y se descarga en su equipo. Abra el archivo .rdp descargado. Cuando se le pida, seleccione **Conectar**. Escriba el nombre de usuario y la contraseña que especificó al crear la máquina virtual. Puede que deba seleccionar **More choices** (Más opciones) y, luego, **Use a different account** (Usar una cuenta diferente), para especificar las credenciales que escribió al crear la máquina virtual. Seleccione **Aceptar**. Puede recibir una advertencia de certificado durante el proceso de inicio de sesión. Si recibe la advertencia, seleccione **Sí** o **Continuar** para continuar con la conexión.

## <a name="communicate-between-vms"></a>Comunicarse entre máquinas virtuales

En PowerShell, en la máquina virtual *myVm1*, escriba `ping myvm2`. Se produce un error de ping porque se usa el Protocolo de mensajes de control de Internet (ICMP) y, de forma predeterminada, este protocolo no puede atravesar el Firewall de Windows.

Para permitir que *myVm2* haga ping a *myVm1* en un paso posterior, escriba el siguiente comando desde PowerShell, que permite una conexión entrante de ICMP a través del Firewall de Windows:

```powershell
New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
```

Cierre la conexión de Escritorio remoto a *myVm1*. 

Vuelva a realizar los pasos de [Conexión a una máquina virtual desde Internet](#connect-to-a-vm-from-the-internet), pero conéctese a *myVm2*. 

En un símbolo del sistema de la máquina virtual *myVm2*, escriba `ping myvm1`.

Recibe respuestas de *myVm1*, porque permitió ICMP a través del Firewall de Windows en la máquina virtual *myVm1* en un paso anterior.

Cierre la conexión de Escritorio remoto a *myVm2*.

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando ya no lo necesite, puede usar [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) para quitar el grupo de recursos y todos los recursos que contiene:

```azurepowershell-interactive 
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Pasos siguientes

En esta guía de inicio rápido, ha creado una red virtual predeterminada y dos máquinas virtuales. Se ha conectado a una máquina virtual desde Internet y se ha comunicado de forma privada entre las dos máquinas virtuales. Para más información sobre la configuración de red virtual, consulte [Administración de una red virtual](manage-virtual-network.md). 

De forma predeterminada, Azure permite la comunicación privada sin restricciones entre máquinas virtuales, pero solo permite conexiones de Escritorio remoto entrantes a máquinas virtuales Windows desde Internet. Para aprender a permitir o restringir los distintos tipos de comunicación de red tanto hacia las máquinas virtuales como desde estas, diríjase al tutorial [Filtro del tráfico de red](tutorial-filter-network-traffic.md).
