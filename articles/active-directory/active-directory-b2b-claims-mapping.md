---
title: Asignación de notificaciones de usuario de colaboración B2B de Azure Active Directory | Microsoft Docs
description: Personalice las notificaciones de usuario que se emiten en el token SAML para los usuarios B2B de Azure Active Directory (Azure AD).
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 04/06/2018
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: bab339a07503a042f459c0933e92e0ed4a4fb27e
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/10/2018
---
# <a name="b2b-collaboration-user-claims-mapping-in-azure-active-directory"></a>Asignación de notificaciones de usuario de colaboración B2B de Azure Active Directory

Azure Active Directory (Azure AD) admite la personalización de las notificaciones emitidas en el token SAML para los usuarios de colaboración B2B. Cuando un usuario se autentique en la aplicación, Azure AD emitirá un token SAML a la aplicación que contiene información (o notificaciones) sobre el usuario que lo identifica de forma única. De forma predeterminada, dicha información incluye el nombre de usuario, la dirección de correo electrónico, el nombre y los apellidos del usuario.

En [Azure Portal](https://portal.azure.com) puede ver o editar las notificaciones que se envían en el token SAML a la aplicación. Para acceder a la configuración, seleccione **Azure Active Directory** > **Aplicaciones empresariales** > la aplicación que está configurada para el inicio de sesión único > **Inicio de sesión único**. Consulte la configuración del token SAML en la sección **Atributos del usuario**.

![Muestra los atributos del token SAML en la interfaz de usuario](media/active-directory-b2b-claims-mapping/view-claims-in-saml-token.png)

Hay dos razones posibles por las que puede que tenga que editar las notificaciones que se emiten en el token SAML:

1. La aplicación requiere un conjunto diferente de URI o valores de notificación.

2. La aplicación requiere que la notificación NameIdentifier tenga un valor que no sea el del nombre principal de usuario (UPN) almacenado en Azure AD.

Para más información acerca de cómo agregar y editar notificaciones, consulte [Personalización de las notificaciones emitidas en el token SAML para aplicaciones empresariales en Azure Active Directory](develop/active-directory-saml-claims-customization.md).

Para los usuarios de colaboración B2B, por motivos de seguridad, se evita realizar la asignación de NameID y UPD entre inquilinos.

## <a name="next-steps"></a>Pasos siguientes

- Para más información acerca de las propiedades de usuario de colaboración B2B, consulte [Propiedades de un usuario de colaboración B2B de Azure Active Directory](active-directory-b2b-user-properties.md).
- Para más información acerca de los tokens de usuario para los usuarios de colaboración B2B, consulte [Información sobre los tokens de usuario de la colaboración B2B de Azure Active Directory](active-directory-b2b-user-token.md).

