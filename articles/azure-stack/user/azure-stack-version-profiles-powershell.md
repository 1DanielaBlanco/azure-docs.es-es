---
title: Uso de perfiles de la versión de la API con PowerShell en Azure Stack | Microsoft Docs
description: Obtenga información sobre cómo usar los perfiles de la versión de API con PowerShell en Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: EBAEA4D2-098B-4B5A-A197-2CEA631A1882
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2018
ms.author: sethm
ms.reviewer: sijuman
ms.openlocfilehash: 994893eb73356fde9acc593569dc5fb1c5a0106f
ms.sourcegitcommit: 794bfae2ae34263772d1f214a5a62ac29dcec3d2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/11/2018
ms.locfileid: "44391137"
---
# <a name="use-api-version-profiles-for-powershell-in-azure-stack"></a>Uso de los perfiles de la versión de la API para PowerShell en Azure Stack

*Se aplica a: sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

Los perfiles de versión de la API proporcionan una manera de administrar las diferencias de versión entre Azure y Azure Stack. Un perfil de la versión de la API es un conjunto de módulos de PowerShell AzureRM con versiones específicas de la API. Cada plataforma de la nube tiene un conjunto de perfiles de versión de API compatibles. Por ejemplo, Azure Stack es compatible con una versión de perfil con fecha específica **2017-03-09-perfil**, y Azure es compatible con la versión de perfil **más reciente** de la API. Cuando se instala un perfil, se instalan los módulos de PowerShell AzureRM que se corresponden con el perfil especificado.

 

## <a name="install-the-powershell-module-required-to-use-api-version-profiles"></a>Instale el módulo de PowerShell necesario para usar perfiles de la versión de la API

El módulo **AzureRM.Bootstrapper** que está disponible a través de la Galería de PowerShell proporciona cmdlet de PowerShell que son necesarios para trabajar con perfiles de la versión de API. Use el siguiente cmdlet para instalar el módulo AzureRM.Bootstrapper:

```PowerShell
Install-Module -Name AzureRm.BootStrapper
```

## <a name="install-a-profile"></a>Instalar un perfil

Use el cmdlet **Install-AzureRmProfile** con el perfil de versión **2017-03-09-perfil** de la API para instalar los módulos de AzureRM que necesita Azure Stack. Los módulos de operador de Azure Stack no se instalan con este perfil de la versión de la API. Estos módulos deben instalarse por separado, tal y como se especificó en el paso 3 del artículo [Instalación de PowerShell para Azure Stack](azure-stack-powershell-install.md).

```PowerShell 
Install-AzureRMProfile -Profile 2017-03-09-profile
```
## <a name="install-and-import-modules-in-a-profile"></a>Instalar e importar módulos en un perfil

Use el cmdlet de **AzureRmProfile Use** para instalar e importar módulos que están asociados a un perfil de la versión de la API. Puede importar un solo perfil de la versión de la API en una sesión de PowerShell. Para importar un perfil de versión de la API diferente, debe abrir una nueva sesión de PowerShell. El cmdlet Use-AzureRMProfile ejecuta las siguientes tareas:  
1. Comprueba si están instalados los módulos de PowerShell asociados al perfil de versión de la API especificado en el ámbito actual.  
2. Descarga e instala los módulos si no están ya instalados.   
3. Importa los módulos en la sesión actual de PowerShell. 

```PowerShell
# Installs and imports the specified API version profile into the current PowerShell session.
Use-AzureRmProfile -Profile 2017-03-09-profile -Scope CurrentUser

# Installs and imports the specified API version profile into the current PowerShell session without any prompts
Use-AzureRmProfile -Profile 2017-03-09-profile -Scope CurrentUser -Force
```

Para instalar e importar los módulos seleccionados de AzureRM de un perfil de la versión de la API, ejecute el cmdlet Use-AzureRMProfile con el parámetro **Módulo**:

```PowerShell
# Installs and imports the compute, Storage and Network modules from the specified API version profile into your current PowerShell session.
Use-AzureRmProfile -Profile 2017-03-09-profile -Module AzureRM.Compute, AzureRM.Storage, AzureRM.Network
```

## <a name="get-the-installed-profiles"></a>Obtener los perfiles instalados

Use el cmdlet **Get AzureRmProfile** para obtener una lista disponible de perfiles de versión de la API: 

```PowerShell
# lists all API version profiles provided by the AzureRM.BootStrapper module.
Get-AzureRmProfile -ListAvailable 

# lists the API version profiles which are installed on your machine
Get-AzureRmProfile
```
## <a name="update-profiles"></a>Administrar perfiles

Use el cmdlet **Update-AzureRmProfile** para actualizar los módulos de un perfil de versión de la API a la versión más reciente de los módulos que están disponibles en la PSGallery. Se recomienda ejecutar siempre el cmdlet **Update-AzureRmProfile** cmdlet en una nueva sesión de PowerShell para evitar conflictos al importar módulos. El cmdlet Update-AzureRmProfile ejecuta las siguientes tareas:

1. Comprueba si se instalan las versiones más recientes de módulos en el perfil determinado de versión de API para el ámbito actual.  
2. Le pide que las instale si no están ya instaladas.  
3. Instala e importa los módulos actualizados en la sesión actual de PowerShell.  

```PowerShell
Update-AzureRmProfile -Profile 2017-03-09-profile
```

Para quitar las versiones de los módulos instaladas anteriormente antes de actualizar a la versión más reciente disponible, use el cmdlet Update-AzureRmProfile junto con el parámetro **-RemovePreviousVersions**:

```PowerShell 
Update-AzureRmProfile -Profile 2017-03-09-profile -RemovePreviousVersions
```

Este cmdlet ejecuta las siguientes tareas:  

1. Comprueba si se instalan las versiones más recientes de módulos en el perfil determinado de versión de API para el ámbito actual.  
2. Quita las versiones anteriores de módulos con el perfil de versión de API actual y en la sesión actual de PowerShell.  
4. le pide que instale la versión más reciente.  
5. Instala e importa los módulos actualizados en la sesión actual de PowerShell.  
 
## <a name="uninstall-profiles"></a>Desinstalar perfiles

Use el cmdlet **Uninstall-AzureRmProfile** para desinstalar el perfil de versión de API especificado.

```PowerShell 
Uninstall-AzureRmProfile -Profile 2017-03-09-profile
```

## <a name="next-steps"></a>Pasos siguientes
* [Install PowerShell for Azure Stack](azure-stack-powershell-install.md) (Instalación de PowerShell para Azure Stack)
* [Configuración del entorno de PowerShell del usuario de Azure Stack](azure-stack-powershell-configure-user.md)  
