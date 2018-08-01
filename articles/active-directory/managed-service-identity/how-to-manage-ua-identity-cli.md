---
title: Administración de una instancia de Managed Service Identity (MSI) asignada por el usuario mediante la CLI de Azure
description: Instrucciones paso a paso sobre cómo crear, enumerar y eliminar una instancia de Managed Service Identity asignada por el usuario mediante la CLI de Azure.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/16/2018
ms.author: daveba
ms.openlocfilehash: 5deaace49bfff994defc06a5f60597add6affc0b
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/20/2018
ms.locfileid: "39188155"
---
# <a name="create-list-or-delete-a-user-assigned-identity-using-the-azure-cli"></a>Creación, enumeración o eliminación de una identidad asignada por el usuario mediante la CLI de Azure

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

La identidad de servicio administrada proporciona a los servicios de Azure una identidad administrada en Azure Active Directory. Puede usar esta identidad para autenticar a cualquier servicio que admita la autenticación de Azure AD, sin necesidad de tener credenciales en el código. 

En este artículo, aprenderá a crear, enumerar y eliminar una identidad asignada por el usuario mediante la CLI de Azure.

## <a name="prerequisites"></a>Requisitos previos

- Si no está familiarizado con Managed Service Identity, consulte la [sección de introducción](overview.md). **No olvide revisar la [diferencia entre una identidad asignada por el sistema y una asignada por el usuario](overview.md#how-does-it-work)**.
- Si aún no tiene una cuenta de Azure, [regístrese para una cuenta gratuita](https://azure.microsoft.com/free/) antes de continuar.
- Para llevar a cabo las operaciones de administración de este artículo, su cuenta debe tener las siguientes asignaciones de roles:
    - Rol [Colaborador de identidad administrada](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) para crear, leer (listar), actualizar y eliminar una identidad asignada por el usuario.
    - Rol [Operador de identidad administrada](/azure/role-based-access-control/built-in-roles#managed-identity-operator) para leer (listar) las propiedades de una identidad asignada por el usuario.
- Para ejecutar los ejemplos de script de la CLI, tiene tres opciones:
    - Usar [Azure Cloud Shell](../../cloud-shell/overview.md) desde Azure Portal (consulte la sección siguiente).
    - Usar Azure Cloud Shell integrado a través del botón "Pruébelo", situado en la esquina superior derecha de cada bloque de código.
    - [Instalar la versión más reciente de CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 o posterior), si prefiere usar una consola local de CLI. Inicie sesión en Azure con `az login`, usando una cuenta asociada a la suscripción de Azure en la que desearía implementar la identidad asignada por el usuario.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-user-assigned-managed-identity"></a>Creación de una identidad administrada asignada por el usuario 

Para crear una identidad asignada por el usuario, use el comando [az identity create](/cli/azure/identity#az-identity-create). El parámetro `-g` especifica el grupo de recursos donde se creará la identidad asignada por el usuario, mientras que el parámetro `-n` especifica su nombre. Reemplace los valores de los parámetros `<RESOURCE GROUP>` y `<USER ASSIGNED IDENTITY NAME>` con sus propios valores:

[!INCLUDE[ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

 ```azurecli-interactive
az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-identities"></a>Enumeración de identidades asignadas por el usuario

Para enumerar las identidades asignadas por el usuario, use el comando [az identity list](/cli/azure/identity#az-identity-list). Reemplace `<RESOURCE GROUP>` por su propio valor:

```azurecli-interactive
az identity list -g <RESOURCE GROUP>
```
En la respuesta JSON, a las identidades de usuario se devuelve el valor `"Microsoft.ManagedIdentity/userAssignedIdentities"` para la clave `type`.

`"type": "Microsoft.ManagedIdentity/userAssignedIdentities"`

## <a name="delete-a-user-assigned-identity"></a>Eliminación de una identidad asignada por el usuario

Para eliminar una identidad asignada por el usuario, use el comando [az identity delete](/cli/azure/identity#az-identity-delete).  El parámetro -n especifica el nombre, y el parámetro -g especifica el grupo de recursos donde se creó la identidad asignada por el usuario. Reemplace los valores de los parámetros `<USER ASSIGNED IDENTITY NAME>` y `<RESOURCE GROUP>` con sus propios valores:

 ```azurecli-interactive
az identity delete -n <USER ASSIGNED IDENTITY NAME> -g <RESOURCE GROUP>
```
> [!NOTE]
> La eliminación de una identidad asignada por el usuario no quitará la referencia de ningún recurso al que se haya asignado. Quite esas máquinas virtuales y VMSS con el comando `az vm/vmss identity remove`.

## <a name="related-content"></a>Contenido relacionado

Para obtener una lista completa de comandos de identidad de la CLI de Azure, consulte [az identity](/cli/azure/identity).

Para obtener información sobre cómo asignar una identidad asignada por el usuario a una VM de Azure, consulte [Configuración de Managed Service Identity (MSI) con una máquina virtual con la CLI de Azure](qs-configure-cli-windows-vm.md#user-assigned-identity)


 
