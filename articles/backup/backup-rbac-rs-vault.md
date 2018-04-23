---
title: Administración de copias de seguridad con el control de acceso basado en roles de Azure | Microsoft Docs
description: Utilice el control de acceso basado en roles para administrar el acceso a las operaciones de administración de copia de seguridad en el almacén de Recovery Services.
services: backup
documentationcenter: ''
author: trinadhk
manager: shreeshd
editor: ''
ms.assetid: 3bd46b97-4b29-47a5-b5ac-ac174dd36760
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 8/22/2017
ms.author: trinadhk;markgal
ms.openlocfilehash: 442d998d8898dc40ee23ca541d35c340edf64dbd
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2018
---
# <a name="use-role-based-access-control-to-manage-azure-backup-recovery-points"></a>Uso del control de acceso basado en roles para administrar puntos de recuperación de Azure Backup
El control de acceso basado en roles (RBAC) de Azure permite realizar una administración detallada del acceso para Azure. También podrá repartir las tareas entre el equipo y conceder a los usuarios únicamente el nivel de acceso que necesitan para realizar su trabajo.

> [!IMPORTANT]
> Los roles proporcionados por Azure Backup están limitados a las acciones que se pueden realizar en Azure Portal o a los cmdlets de PowerShell del almacén de Recovery Services. Las acciones realizadas en la IU del cliente del agente de Azure Backup, en la IU de System Center Data Protection Manager o en la IU del Azure Backup Server están fuera del control de estos roles.

Azure Backup proporciona 3 roles integrados para controlar las operaciones de administración de copia de seguridad. Más información sobre [roles integrados del control de acceso basado en rol de Azure](../role-based-access-control/built-in-roles.md)

* [Colaborador de copia de seguridad](../role-based-access-control/built-in-roles.md#backup-contributor): Este rol tiene todos los permisos para crear y administrar copias de seguridad, excepto para crear el almacén de Recovery Services y facilitar acceso a otros usuarios. Imagine este rol como administrador de copias de seguridad que puede realizar todas las operaciones de administración de copia de seguridad.
* [Operador de copia de seguridad](../role-based-access-control/built-in-roles.md#backup-operator): Este rol tiene permisos para todo lo que puede hacer un colaborador, excepto quitar copias de seguridad y administrar directivas de copia de seguridad. Este rol es equivalente al de colaborador, salvo que no puede realizar operaciones destructivas, como detener copias de seguridad con la eliminación de datos o quitar el registro de recursos locales.
* [Lector de copia de seguridad](../role-based-access-control/built-in-roles.md#backup-reader): Este rol tiene permisos para ver todas las operaciones de administración de copia de seguridad. Imagine este rol como una persona de supervisión.

Si quiere definir sus propios roles para tener un mayor control, consulte cómo [crear roles personalizados](../role-based-access-control/custom-roles.md) en RBAC de Azure.



## <a name="mapping-backup-built-in-roles-to-backup-management-actions"></a>Asignación de roles integrados de Backup a las acciones de administración de copia de seguridad
En la tabla siguiente se capturan acciones de administración de Backup y el rol RBAC mínimo correspondiente necesario para realizar esa operación.

| Operación de administración | Rol RBAC mínimo necesario |
| --- | --- |
| Crear almacén de Recovery Services | Colaborador en el grupo de recursos del almacén |
| Habilitar la copia de seguridad de VM de Azure | Operador de copia de seguridad en el almacén, colaborador de máquina virtual en VM |
| Copia de seguridad a petición de VM | Operador de copia de seguridad |
| Restaurar VM | Operador de copia de seguridad, colaborador de grupo de recursos donde se implementa la máquina virtual y las redes virtuales |
| Restaurar discos y archivos individuales a partir de la copia de seguridad de VM | Operador de copia de seguridad, colaborador de máquina virtual en VM |
| Crear directiva de copia de seguridad para copia de seguridad de VM de Azure | Colaborador de copia de seguridad |
| Modificar directiva de copia de seguridad de copia de seguridad de VM de Azure | Colaborador de copia de seguridad |
| Eliminar directiva de copia de seguridad de copia de seguridad de VM de Azure | Colaborador de copia de seguridad |
| Detener copia de seguridad (con retención de datos o eliminación de datos) en copia de seguridad de VM | Colaborador de copia de seguridad |
| Registrar Windows Server, cliente o SCDPM local o Azure Backup Server | Operador de copia de seguridad |
| Eliminar Windows Server, cliente o SCDPM local registrado o Azure Backup Server | Colaborador de copia de seguridad |

## <a name="next-steps"></a>Pasos siguientes
* [Control de acceso basado en roles de Azure](../role-based-access-control/role-assignments-portal.md): introducción a RBAC en Azure Portal.
* Aprenda a administrar el acceso con:
  * [PowerShell](../role-based-access-control/role-assignments-powershell.md)
  * [CLI de Azure](../role-based-access-control/role-assignments-cli.md)
  * [API de REST](../role-based-access-control/role-assignments-rest.md)
* [Solución de problemas del control de acceso basado en roles](../role-based-access-control/troubleshooting.md): sugerencias para resolver problemas frecuentes.
