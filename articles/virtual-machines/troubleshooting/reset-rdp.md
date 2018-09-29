---
title: Restablecimiento de la contraseña o la configuración de Escritorio remoto en una VM Windows | Microsoft Docs
description: Aprenda a restablecer la contraseña de una cuenta o los servicios de Escritorio remoto en una máquina virtual Windows mediante Azure Portal o Azure PowerShell.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 45c69812-d3e4-48de-a98d-39a0f5675777
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 03/23/2018
ms.author: cynthn
ms.openlocfilehash: a8db7ef82136bae51c99bcfd2a4743e09ebf5712
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/27/2018
ms.locfileid: "47412965"
---
# <a name="how-to-reset-the-remote-desktop-service-or-its-login-password-in-a-windows-vm"></a>Restablecimiento del servicio Escritorio remoto o la contraseña de inicio de sesión en una VM con Windows
Si no puede conectarse a una máquina virtual Windows, puede restablecer la contraseña de administrador local o la configuración de servicio de Escritorio remoto (no se admite en controladores de dominio de Windows). Puede usar el Portal de Azure o la extensión de acceso de máquina virtual en Azure PowerShell para restablecer la contraseña. Una vez que haya iniciado sesión en la máquina virtual, debe restablecer la contraseña para ese usuario.  
Si usa Azure PowerShell, asegúrese de tener [instalado y configurado el módulo de PowerShell más reciente](/powershell/azure/overview) y haber iniciado sesión en su suscripción de Azure. También puede [realizar estos pasos para máquinas virtuales creadas con el modelo de implementación clásica](https://docs.microsoft.com/azure/virtual-machines/windows/classic/reset-rdp).

## <a name="ways-to-reset-configuration-or-credentials"></a>Métodos para restablecer la configuración o las credenciales
Puede restablecer los servicios y las credenciales de Escritorio remoto de varias maneras diferentes, dependiendo de sus necesidades:

- [Restablecimiento mediante Azure Portal](#azure-portal)
- [Restablecimiento mediante Azure PowerShell](#vmaccess-extension-and-powershell)

## <a name="azure-portal"></a>Azure Portal
Para expandir los menús del portal, haga clic en las tres barras en la esquina superior izquierda y, a continuación, en **Máquinas virtuales**:

![Busque la máquina virtual de Azure.](./media/reset-rdp/Portal-Select-VM.png)

### <a name="reset-the-local-administrator-account-password"></a>**Restablecer la contraseña de cuenta de administración local**

Seleccione la máquina virtual Windows, haga clic en **Soporte y solución de problemas** > **Restablecer contraseña**. Se mostrará la hoja de restablecimiento de contraseña:

![Página Restablecimiento de contraseña](./media/reset-rdp/Portal-RM-PW-Reset-Windows.png)

Escriba el nombre de usuario y una nueva contraseña nueva; después, haga clic en **Actualizar**. Trate de conectarse de nuevo a la máquina virtual.

### <a name="reset-the-remote-desktop-service-configuration"></a>**Restablecer la configuración de servicio de Escritorio remoto**

Seleccione la máquina virtual Windows, haga clic en **Soporte y solución de problemas** > **Restablecer contraseña**. Se mostrará la hoja de restablecimiento de contraseña. 

![Restablecer configuración de RDP](./media/reset-rdp/Portal-RM-RDP-Reset.png)

Seleccione **Restablecer solo configuración** en el menú desplegable y, a continuación, haga clic en **Actualizar**. Trate de conectarse de nuevo a la máquina virtual.


## <a name="vmaccess-extension-and-powershell"></a>Extensión VMAccess y PowerShell
Asegúrese de tener [instalado y configurado el módulo de PowerShell más reciente](/powershell/azure/overview) y haber iniciado sesión en su suscripción de Azure con el cmdlet `Connect-AzureRmAccount`.

### <a name="reset-the-local-administrator-account-password"></a>**Restablecer la contraseña de cuenta de administración local**
Restablezca la contraseña de administrador o el nombre de usuario con el comando de PowerShell [Set-AzureRmVMAccessExtension](/powershell/module/azurerm.compute/set-azurermvmaccessextension). El valor de typeHandlerVersion debe ser 2.0 o superior, porque la versión 1 está en desuso. 

```powershell
$SubID = "<SUBSCRIPTION ID>" 
$RgName = "<RESOURCE GROUP NAME>" 
$VmName = "<VM NAME>" 
$Location = "<LOCATION>" 
 
Connect-AzureRmAccount 
Select-AzureRMSubscription -SubscriptionId $SubID 
Set-AzureRmVMAccessExtension -ResourceGroupName $RgName -Location $Location -VMName $VmName -Credential (get-credential) -typeHandlerVersion "2.0" -Name VMAccessAgent 
```

> [!NOTE] 
> Si escribe un nombre distinto al de la cuenta de administrador local actual en la máquina virtual, la extensión VMAccess agregará una cuenta de administrador local con ese nombre y asignará la contraseña especificada a esa cuenta. Si la cuenta de administrador local en la máquina virtual existe, restablecerá la contraseña y, si la cuenta está deshabilitada, la extensión VMAccess la habilitará.

### <a name="reset-the-remote-desktop-service-configuration"></a>**Restablecer la configuración de servicio de Escritorio remoto**
Restablezca el acceso remoto a la máquina virtual con el cmdlet de PowerShell [Set-AzureRmVMAccessExtension](/powershell/module/azurerm.compute/set-azurermvmaccessextension). En el ejemplo siguiente se habilita se restablece la extensión de acceso llamada `myVMAccess` en la máquina virtual denominada `myVM` en el grupo de recursos `myResourceGroup`:

```powershell
Set-AzureRmVMAccessExtension -ResourceGroupName "myResoureGroup" -VMName "myVM" -Name "myVMAccess" -Location WestUS -typeHandlerVersion "2.0" -ForceRerun
```

> [!TIP]
> En cualquier momento, una VM puede tener un solo agente de acceso a VM. Para establecer las propiedades del agente de acceso de la máquina virtual correctamente, puede usarse la opción `-ForceRerun`. Al usar `-ForceRerun`, procure usar el mismo nombre para el agente de acceso de máquina virtual establecido en los comandos anteriores.

Si sigue sin poder conectarse remotamente a la máquina virtual, consulte más pasos que puede tratar de realizar en [Solución de problemas de conexiones del Escritorio remoto a una máquina virtual de Azure con Windows](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Si pierde la conexión con el controlador de dominio de Windows, deberá restaurarla a partir de una copia de seguridad del controlador de dominio.

## <a name="next-steps"></a>Pasos siguientes
Si la extensión de acceso de la máquina virtual de Azure no responde y no puede restablecer la contraseña, puede [restablecer la contraseña de Windows local sin conexión](../windows/reset-local-password-without-agent.md). Este método es un proceso más avanzado y requiere que se conecte el disco duro virtual de la máquina virtual problemática a otra máquina virtual. Primero siga los pasos descritos en este artículo e intente solamente el método de restablecimiento de contraseña sin conexión como último recurso.

[Características y extensiones de las máquinas virtuales de Azure](../extensions/features-windows.md)

[Conexión a una máquina virtual de Azure con RDP o SSH](http://msdn.microsoft.com/library/azure/dn535788.aspx)

[Solucionar problemas de conexiones de Escritorio remoto a una máquina virtual de Azure basada en Windows](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

