---
title: 'Guía de inicio rápido: creación de una máquina virtual Windows con Azure PowerShell | Microsoft Docs'
description: En esta guía de inicio rápido, aprenderá a usar Azure PowerShell para crear una máquina virtual Windows.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/04/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 00a96d01cf5b09d8f379f87de03ce6e6b5194d88
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/22/2019
ms.locfileid: "54431179"
---
# <a name="quickstart-create-a-windows-virtual-machine-in-azure-with-powershell"></a>Inicio rápido: Creación de una máquina virtual Windows en Azure con PowerShell

El módulo de Azure PowerShell se usa para crear y administrar recursos de Azure desde la línea de comandos de PowerShell o en scripts. En esta guía de inicio rápido se muestra cómo usar el módulo de Azure PowerShell para implementar una máquina virtual de Azure que ejecuta Windows Server 2016. Para ver la máquina virtual en acción, conéctese a la máquina virtual mediante RDP e instale al servidor web IIS.

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

## <a name="launch-azure-cloud-shell"></a>Inicio de Azure Cloud Shell

Azure Cloud Shell es un shell interactivo gratuito que puede usar para ejecutar los pasos de este artículo. Tiene las herramientas comunes de Azure preinstaladas y configuradas para usarlas en la cuenta. 

Para abrir Cloud Shell, seleccione **Pruébelo** en la esquina superior derecha de un bloque de código. También puede ir a [https://shell.azure.com/powershell](https://shell.azure.com/powershell) para iniciar Cloud Shell en una pestaña independiente del explorador. Seleccione **Copiar** para copiar los bloques de código, péguelos en Cloud Shell y, luego, presione Entrar para ejecutarlos.

Si decide instalar y usar PowerShell localmente, para este tutorial se requiere la versión 5.7.0 del módulo de Azure PowerShell o cualquier versión posterior. Ejecute `Get-Module -ListAvailable AzureRM` para encontrar la versión. Si necesita actualizarla, consulte [Instalación del módulo de Azure PowerShell](/powershell/azure/azurerm/install-azurerm-ps). Si PowerShell se ejecuta localmente, también debe ejecutar `Connect-AzureRmAccount` para crear una conexión con Azure.

## <a name="create-resource-group"></a>Creación de un grupo de recursos

Cree un grupo de recursos de Azure con [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Un grupo de recursos es un contenedor lógico en el que se implementan y se administran los recursos de Azure.

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-virtual-machine"></a>Crear máquina virtual

Cree una máquina virtual con [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). Proporcione nombres para cada uno de los recursos y el cmdlet [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) los creará si aún no existen.

Cuando se le solicite, proporcione un nombre de usuario y una contraseña que se usarán como credenciales de inicio de sesión para la máquina virtual:

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVM" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -OpenPorts 80,3389
```

## <a name="connect-to-virtual-machine"></a>Conexión a la máquina virtual

Finalizada la implementación, conéctese a la máquina virtual mediante RDP. Para ver la máquina virtual en acción, se instala el servidor web IIS.

Para ver la dirección IP pública de la máquina virtual, use el cmdlet [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress):

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName "myResourceGroup" | Select "IpAddress"
```

Use el comando siguiente para crear una sesión de Escritorio remoto desde el equipo local. Reemplace la dirección IP por la dirección IP pública de la máquina virtual. 

```powershell
mstsc /v:publicIpAddress
```

En la ventana **Seguridad de Windows**, seleccione **Más opciones** y, después, **Usar otra cuenta**. Escriba el nombre de usuario como **localhost**\\*username*, escriba la contraseña que creó para la máquina virtual y luego haga clic en **Aceptar**.

Puede recibir una advertencia de certificado durante el proceso de inicio de sesión. Haga clic en **Sí** o en **Continuar** para crear la conexión.

## <a name="install-web-server"></a>Instalación del servidor web

Para ver la máquina virtual en acción, instale el servidor web IIS. Abra un símbolo del sistema de PowerShell en la máquina virtual y ejecute el siguiente comando:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

Cuando haya terminado, cierre la conexión RDP con la máquina virtual.

## <a name="view-the-web-server-in-action"></a>Visualización del servidor web en acción

Con IIS instalado y el puerto 80 abierto en la máquina virtual desde Internet, use el explorador web que prefiera para ver la página principal predeterminada de IIS. Use la dirección IP pública de la máquina virtual que obtuvo en el paso anterior. En el ejemplo siguiente se muestra el sitio web de IIS predeterminado:

![Sitio predeterminado de IIS](./media/quick-create-powershell/default-iis-website.png)

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando ya no los necesite, puede usar el cmdlet [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) para quitar el grupo de recursos, la máquina virtual y todos los recursos relacionados:

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Pasos siguientes

En esta guía de inicio rápido, implementará una máquina virtual sencilla, abrirá un puerto de red para el tráfico web e instalará un servidor web básico. Para más información acerca de las máquinas virtuales de Azure, continúe con el tutorial de máquinas virtuales Windows.

> [!div class="nextstepaction"]
> [Tutoriales de máquinas virtuales Windows de Azure](./tutorial-manage-vm.md)
