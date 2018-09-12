---
title: Configuración del inicio de sesión único en una aplicación de proxy de aplicación | Microsoft Docs
description: Cómo configurar rápidamente el inicio de sesión único en su aplicación de proxy de aplicación
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: 9c353c9b34c3d4a2b21093cc0ae82e638f5ddd01
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/11/2018
ms.locfileid: "44355394"
---
# <a name="how-to-configure-single-sign-on-to-an-application-proxy-application"></a>Configuración del inicio de sesión único en una aplicación de proxy de aplicación

El inicio de sesión único (SSO) permite que los usuarios accedan a una aplicación sin tener que autenticarse varias veces. Permite que se lleve a cabo la autenticación única en la nube, en Azure Active Directory, y que el servicio o el conector suplante al usuario para completar los desafíos de autenticación adicionales de la aplicación.

## <a name="how-to-configure-single-sign-on"></a>Configuración del inicio de sesión único
Para configurar SSO, asegúrese de que la aplicación esté configurada para la autenticación previa a través de Azure Active Directory. Para realizar esta configuración, vaya a **Azure Active Directory** -&gt; **Aplicaciones empresariales** -&gt; **Todas las aplicaciones** -&gt; Su aplicación **-&gt; Proxy de la aplicación**. En esta página, asegúrese de que el campo "Autenticación previa" esté establecido en "Azure Active Directory". 

Para más información sobre los métodos de autenticación previa, consulte el paso 4 del [documento sobre publicación de aplicaciones](application-proxy-publish-azure-portal.md).

   ![Método de autenticación previa en Azure Portal](./media/application-proxy-config-sso-how-to/app-proxy.png)

## <a name="configuring-single-sign-on-modes-for-application-proxy-applications"></a>Configuración de modos de inicio de sesión únicos para aplicaciones de proxy de aplicación
Configure el tipo específico de inicio de sesión único. Los métodos de inicio de sesión se clasifican en función del tipo de autenticación que la aplicación back-end use. Las aplicaciones de proxy de aplicación admiten tres tipos de inicio de sesión:

-   **Inicio de sesión con contraseña**: el inicio de sesión con contraseña sirve para cualquier aplicación que utilice los campos de nombre de usuario y contraseña para iniciar sesión. Los pasos de configuración están en la [documentación sobre la configuración de SSO con contraseña](../active-directory-enterprise-apps-whats-new-azure-portal.md#bring-your-own-password-sso-applications).

-   **Autenticación de Windows integrada**: para las aplicaciones que utilizan la autenticación de Windows integrada (IWA), el inicio de sesión único se habilita a través de la delegación limitada de Kerberos (KCD). Este método concede permiso a los conectores de proxy de aplicación en Active Directory para suplantar a los usuarios, y para enviar y recibir tokens en su nombre. Puede ver más información sobre la configuración de KCD en la [documentación sobre el inicio de sesión único con KCD](application-proxy-configure-single-sign-on-with-kcd.md).

-   **Inicio de sesión basado en el encabezado**: el inicio de sesión basado en el encabezado se habilita a través de una asociación y requiere configuración adicional. Para ver detalles sobre la asociación y las instrucciones paso a paso para configurar el inicio de sesión único para una aplicación que use encabezados para la autenticación, consulte la [documentación de PingAccess para Azure AD](application-proxy-configure-single-sign-on-with-ping-access.md).

Puede encontrar todas estas opciones si va a "Aplicaciones empresariales" en la aplicación y abre la página **Inicio de sesión único** en el menú de la izquierda. Tenga en cuenta que, si la aplicación se creó en el portal antiguo, es posible que no vea todas estas opciones.

En esta página, también verá una opción adicional para el inicio de sesión: Inicio de sesión vinculado. El proxy de aplicación también admite esta opción. Sin embargo, esta opción no agrega el inicio de sesión único a la aplicación. Dicho esto, es posible que ya se haya implementado el inicio de sesión único en la aplicación mediante otro servicio, como los Servicios de federación de Active Directory. 

Esta opción permite que un administrador cree un vínculo a una aplicación que los usuarios ven en primer lugar al acceder a la aplicación. Por ejemplo, si hay una aplicación que está configurada para autenticar a usuarios mediante Servicios de federación de Active Directory 2.0, el administrador puede usar la opción "Inicio de sesión vinculado" para crear un vínculo a ella en el panel de acceso.

## <a name="next-steps"></a>Pasos siguientes
[Proporcionar un inicio de sesión único a las aplicaciones con el proxy de aplicación](application-proxy-configure-single-sign-on-with-kcd.md)
