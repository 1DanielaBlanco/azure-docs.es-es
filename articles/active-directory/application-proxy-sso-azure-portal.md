---
title: Inicio de sesión único en aplicaciones mediante el proxy de aplicación de Azure AD | Microsoft Docs
description: Active el inicio de sesión único para sus aplicaciones locales publicadas mediante el proxy de aplicación de Azure AD en Azure Portal.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 77e7288060abd2577867023a777eb3bdd4331e88
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/11/2018
---
# <a name="password-vaulting-for-single-sign-on-with-application-proxy"></a>Almacén de contraseñas para el inicio de sesión único con el proxy de aplicación

El proxy de aplicación de Azure Active Directory le ayuda a mejorar la productividad mediante la publicación de aplicaciones locales de manera que los empleados remotos puedan tener acceso seguro a estas. En Azure Portal, puede configurar también el inicio de sesión único (SSO) para estas aplicaciones. Los usuarios solo necesitan autenticarse con Azure AD y podrán acceder a la aplicación de empresa sin necesidad de volver a iniciar sesión.

El proxy de aplicación admite varios [modos de inicio de sesión único](manage-apps/application-proxy-single-sign-on.md). El inicio de sesión basado en contraseña se ha creado para aplicaciones que usan una combinación de nombre de usuario/contraseña para la autenticación. Al configurar el inicio de sesión basado en contraseña para su aplicación, los usuarios tendrán que iniciar sesión en la aplicación local una vez. Después de eso, Azure Active Directory almacena la información de inicio de sesión y la proporciona automáticamente a la aplicación cuando los usuarios acceden a ella de forma remota. 

Debe haber publicado y probado ya la aplicación con el proxy de aplicación. Si no es así, siga los pasos de [Publish applications using Azure AD Application Proxy](manage-apps/application-proxy-publish-azure-portal.md) (Publicación de aplicaciones mediante el proxy de aplicación de Azure AD) y luego vuelva a este punto. 

## <a name="set-up-password-vaulting-for-your-application"></a>Configuración del almacenamiento de contraseñas para la aplicación

1. Inicie sesión en [Azure Portal](https://portal.azure.com) como administrador.
2. Seleccione **Azure Active Directory** > **Aplicaciones empresariales** > **Todas las aplicaciones**.
3. En la lista, seleccione la aplicación para la que desea configurar el SSO.  
4. Seleccione **Inicio de sesión único**.

   ![Seleccionar inicio de sesión único](./media/application-proxy-sso-azure-portal/select-sso.png)

5. Para el modo SSO, elija **Inicio de sesión basado en contraseña**.
6. Para la URL de inicio de sesión, escriba la dirección URL de la página donde los usuarios escriben su nombre de usuario y contraseña para iniciar sesión fuera de la red corporativa. Debe ser la dirección URL externa que creó al publicar la aplicación a través del proxy de aplicación. 

   ![Elegir inicio de sesión basado en contraseña y escribir la URL](./media/application-proxy-sso-azure-portal/password-sso.png)

7. Seleccione **Guardar**.

<!-- Need to repro?
7. The page should tell you that a sign-in form was successfully detected at the provided URL. If it doesn't, select **Configure [your app name] Password Single Sign-on Settings** and choose **Manually detect sign-in fields**. Follow the instructions to point out where the sign-in credentials go. 
-->

## <a name="test-your-app"></a>Prueba de la aplicación

Vaya a la dirección URL externa configurada para obtener acceso remoto a la aplicación. Inicie sesión con sus credenciales para esa aplicación (o las credenciales para la cuenta de prueba que configuró con acceso). Una vez que inicie sesión correctamente, debería poder salir de la aplicación y volver sin necesidad de volver a especificar sus credenciales. 

## <a name="next-steps"></a>Pasos siguientes

- Obtenga información sobre otras maneras de implementar el [inicio de sesión único con el proxy de aplicación](manage-apps/application-proxy-single-sign-on.md)
- Más información sobre [Consideraciones de seguridad al obtener acceso a aplicaciones de forma remota con el Proxy de aplicación de Azure AD](application-proxy-security-considerations.md)