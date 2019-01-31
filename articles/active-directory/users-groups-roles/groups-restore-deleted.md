---
title: Restauración de un grupo eliminado de Office 365 en Azure Active Directory | Microsoft Docs
description: Restauración de un grupo eliminado, visualización de grupos restaurables y eliminación permanente de un grupo en Azure Active Directory
services: active-directory
documentationcenter: ''
author: eross-msft
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: quickstart
ms.date: 08/28/2017
ms.author: lizross
ms.reviewer: krbain
ms.custom: it-pro
ms.openlocfilehash: ef18897d00e29a21dd602dc7213706bfc30a68bc
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/29/2019
ms.locfileid: "55179385"
---
# <a name="restore-a-deleted-office-365-group-in-azure-active-directory"></a>Restauración de un grupo eliminado de Office 365 en Azure Active Directory

Cuando se elimina un grupo de Office 365 en Azure Active Directory (Azure AD), el grupo eliminado queda retenido y deja de estar visible durante 30 días a contar desde la fecha de eliminación. Este comportamiento es tal para que el grupo y su contenido se puedan restaurar en caso necesario. Esta funcionalidad está restringida exclusivamente a los grupos de Office 365 en Azure AD. No está disponible para los grupos de seguridad ni los grupos de distribución.

> [!NOTE]
> No utilice `Remove-MsolGroup` porque purga el grupo de forma permanente. Utilice siempre `Remove-AzureADMSGroup` para eliminar un grupo de O365.

Los permisos necesarios para restaurar un grupo pueden ser cualquiera de los siguientes:

Rol | Permisos
--------- | ---------
Administrador de la compañía, Soporte para asociados de nivel 2 y Administradores de servicio de Intune | Puede restaurar cualquier grupo eliminado de Office 365
Administrador de cuenta de usuario y Soporte para asociados de nivel 1 | Puede restaurar cualquier grupo eliminado de Office 365, excepto los asignados al rol Administrador de la compañía
Usuario | Puede restaurar cualquier grupo eliminado de Office 365 de su propiedad


## <a name="view-the-deleted-office-365-groups-that-are-available-to-restore"></a>Visualización de grupos eliminados de Office 365 disponibles para restauración

Los cmdlets siguientes se pueden usar para ver los grupos eliminados y comprobar que todavía no se purgaron los que le interesan. Estos cmdlets son parte del [módulo de Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureAD/). Puede encontrar más información sobre este módulo en el artículo sobre la [versión 2 de Azure Active Directory PowerShell](/powershell/azure/install-adv2?view=azureadps-2.0).

1.  Ejecute el cmdlet siguiente para mostrar todos los grupos eliminados de Office 365 existentes en el inquilino que todavía se pueden restaurar.
   
  ```
  Get-AzureADMSDeletedGroup
  ```

2.  Como alternativa, si conoce el id. de objeto de un grupo específico (y puede obtenerlo del cmdlet del paso 1), ejecute el cmdlet siguiente para comprobar que el grupo eliminado específico todavía no se purgó de forma permanente.
  
  ```
  Get-AzureADMSDeletedGroup –Id <objectId>
  ```

## <a name="how-to-restore-your-deleted-office-365-group"></a>Procedimientos para restaurar grupos eliminados de Office 365
Una vez que haya comprobado que el grupo todavía se puede restaurar, siga uno de los pasos siguientes para restaurar el grupo eliminado. Si el grupo contiene documentos, sitios de SP u otros objetos persistentes, el proceso completo de restauración de un grupo y su contenido podría demorar hasta 24 horas.

1.  Ejecute el cmdlet siguiente para restaurar el grupo y su contenido.
 
 ```
 Restore-AzureADMSDeletedDirectoryObject –Id <objectId>
 ``` 

Como alternativa, puede ejecutar el cmdlet siguiente para quitar el grupo eliminado de forma permanente.
 ```
 Remove-AzureADMSDeletedDirectoryObject –Id <objectId>
 ```

## <a name="how-do-you-know-this-worked"></a>¿Cómo se puede saber si funcionó?

Para comprobar que restauró correctamente un grupo de Office 365, ejecute el cmdlet `Get-AzureADGroup –ObjectId <objectId>` para mostrar información sobre el grupo. Una vez que la solicitud de restauración se complete:

- El grupo aparece en la barra de navegación izquierda de Exchange
- El plan del grupo aparecerá en Planner
- Los sitios de SharePoint y su contenido estarán disponibles
- Se podrá tener acceso al grupo desde cualquiera de los puntos de conexión de Exchange y otras cargas de trabajo de Office 365 compatibles con los grupos de Office 365

## <a name="next-steps"></a>Pasos siguientes
En estos artículos se proporciona información adicional sobre los grupos de Azure Active Directory.

* [Consulta de los grupos existentes](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Administración de la configuración de un grupo](../fundamentals/active-directory-groups-settings-azure-portal.md)
* [Administrar miembros de un grupo](../fundamentals/active-directory-groups-members-azure-portal.md)
* [Administrar la pertenencia a grupos](../fundamentals/active-directory-groups-membership-azure-portal.md)
* [Administrar reglas dinámicas de los usuarios de un grupo](groups-dynamic-membership.md)
