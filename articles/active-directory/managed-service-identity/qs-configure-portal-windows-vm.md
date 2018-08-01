---
title: Configuración de Managed Service Identity en una máquina virtual de Azure mediante Azure Portal
description: Instrucciones paso a paso para configurar una identidad de servicio administrada en una máquina virtual de Azure, mediante Azure Portal.
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
ms.date: 09/19/2017
ms.author: daveba
ms.openlocfilehash: 6ba090065b18a44cc1f01a62eefb5dcf52bcf356
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/23/2018
ms.locfileid: "39213272"
---
# <a name="configure-a-vm-managed-service-identity-using-the-azure-portal"></a>Configuración de una identidad de servicio administrada en una máquina virtual con Azure Portal

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Managed Service Identity proporciona a los servicios de Azure una identidad administrada automáticamente en Azure Active Directory. Puede usar esta identidad para autenticar a cualquier servicio que admita la autenticación de Azure AD, sin necesidad de tener credenciales en el código. 

En este artículo, aprenderá a habilitar y deshabilitar la identidad asignada por el sistema para una máquina virtual de Azure, mediante Azure Portal. La asignación y eliminación de identidades asignadas por el usuario desde máquinas virtuales de Azure no se admiten actualmente mediante Azure Portal.

> [!NOTE]
> Actualmente, las operaciones de identidades asignadas por el usuario no se admiten mediante Azure Portal. Compruebe si hay actualizaciones. 

## <a name="prerequisites"></a>Requisitos previos

- Si no está familiarizado con Managed Service Identity, consulte la [sección de introducción](overview.md).
- Si aún no tiene una cuenta de Azure, [regístrese para una cuenta gratuita](https://azure.microsoft.com/free/) antes de continuar.
- Para llevar a cabo las operaciones de administración en este artículo, su cuenta debe tener la siguiente asignación de roles:
    - [Colaborador de máquina virtual](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) para habilitar y quitar la identidad asignada por el sistema de una máquina virtual de Azure.

## <a name="managed-service-identity-during-creation-of-an-azure-vm"></a>Managed Service Identity durante la creación de un conjunto de escalado de una máquina virtual de Azure

Actualmente, la creación de máquinas virtuales mediante Azure Portal no admite operaciones de Managed Service Identity. En su lugar, para crear una máquina virtual consulte uno de los siguientes artículos de la guía de inicio rápido de creación de máquinas virtuales:

- [Creación de una máquina virtual Windows desde Azure Portal](../../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Creación de una máquina virtuales Linux desde Azure Portal](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)  

A continuación, pase a la siguiente sección, donde encontrará información detallada sobre la habilitación de Managed Service Identity en la máquina virtual.

## <a name="enable-managed-service-identity-on-an-existing-azure-vm"></a>Habilitación de Managed Service Identity en una máquina virtual existente de Azure

Para habilitar la identidad asignada por el sistema en una máquina virtual que se aprovisionó originalmente sin ella, siga estos pasos:

1. Inicie sesión en [Azure Portal](https://portal.azure.com) con una cuenta asociada a la suscripción de Azure que contiene la máquina virtual.

2. Navegue a la máquina virtual que desee y seleccione la página "Configuración".

3. Para habilitar la identidad asignada por el sistema en la máquina virtual, seleccione "Sí" en "Managed Service Identity" y, luego, haga clic en **Guardar**. Esta operación puede tardar 60 segundos o más en completarse:

   > [!NOTE]
   > La incorporación de una identidad asignada por el usuario a una máquina virtual no se admite a través de Azure Portal.

   ![Captura de pantalla de la página de configuración](../managed-service-identity/media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade.png)  

## <a name="remove-managed-service-identity-from-an-azure-vm"></a>Eliminación de Managed Service Identity de una máquina virtual de Azure

Si tiene una máquina virtual que ya no necesita la identidad asignada por el sistema:

1. Inicie sesión en [Azure Portal](https://portal.azure.com) con una cuenta asociada a la suscripción de Azure que contiene la máquina virtual. 

2. Navegue a la máquina virtual que desee y seleccione la página "Configuración".

3. Para deshabilitar la identidad asignada por el sistema en la máquina virtual, seleccione "No" en "Managed Service Identity" y, luego, haga clic en Guardar. Esta operación puede tardar 60 segundos o más en completarse:

    > [!NOTE]
    > La incorporación de una identidad asignada por el usuario a una máquina virtual no se admite a través de Azure Portal.

   ![Captura de pantalla de la página de configuración](../managed-service-identity/media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade-disable.png)  

## <a name="related-content"></a>Contenido relacionado

- Para obtener información sobre Managed Service Identity, consulte [Información general](overview.md).

## <a name="next-steps"></a>Pasos siguientes

- Con Azure Portal, dé a la identidad de servicio administrada de una máquina virtual de Azure [acceso a otro recurso de Azure](howto-assign-access-portal.md).

