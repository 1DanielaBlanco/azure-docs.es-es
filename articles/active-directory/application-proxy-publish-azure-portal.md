---
title: Publicación de aplicaciones mediante el proxy de aplicación de Azure AD | Microsoft Docs
description: Publique aplicaciones locales en la nube con el proxy de aplicación de Azure AD en Azure Portal.
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
ms.date: 12/06/2017
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: b9750212925294dec2220e6ef3e6e19d3bc7c3c6
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/11/2018
---
# <a name="publish-applications-using-azure-ad-application-proxy"></a>Publicación de aplicaciones mediante el proxy de aplicación de Azure AD

El proxy de aplicación de Azure Active Directory (AD) permite publicar aplicaciones locales para acceder a ellas a través de Internet y poder admitir trabajadores remotos. Puede publicar estas aplicaciones a través de Azure Portal para proporcionar acceso remoto seguro desde fuera de la red.

Este artículo le guiará por los pasos necesarios para publicar una aplicación local con el proxy de aplicación. Después de completar este artículo, los usuarios podrán acceder a la aplicación de forma remota. Entonces, estará listo para configurar características adicionales para la aplicación como el inicio de sesión único, información personalizada o requisitos de seguridad.

Si no está familiarizado con el proxy de aplicación, obtenga más información sobre esta característica con el artículo [Provisión de acceso remoto seguro a aplicaciones locales](manage-apps/application-proxy.md).


## <a name="publish-an-on-premises-app-for-remote-access"></a>Publicación de una aplicación local para el acceso remoto

Siga estos pasos para publicar aplicaciones con el proxy de aplicación. Si aún no ha descargado y configurado un conector para su organización, vaya a [Introducción al proxy de aplicación](active-directory-application-proxy-enable.md) e instale el conector primero y, a continuación, publique la aplicación.

> [!TIP]
> Si va a probar el proxy de aplicación por primera vez, elija una aplicación que esté configurada para la autenticación basada en contraseña. El proxy de aplicación es compatible con otros tipos de autenticación, pero las aplicaciones basadas en contraseña son las que se ponen más fácilmente a pleno rendimiento de manera rápida. 

1. Inicie sesión como administrador en [Azure Portal](https://portal.azure.com/).
2. Seleccione **Azure Active Directory** > **Aplicaciones empresariales** > **Nueva aplicación**.

  ![Adición de una aplicación empresarial](./media/application-proxy-publish-azure-portal/add-app.png)

3. Seleccione **Todo** y, después, seleccione **Aplicación local**.  

  ![Adición de su propia aplicación](./media/application-proxy-publish-azure-portal/add-your-own.png)

4. Especifique la siguiente información acerca de la aplicación:

   - **Nombre**: nombre de la aplicación que va a aparecer en el panel de acceso y en Azure Portal. 

   - **URL interna**: dirección URL que se usa para obtener acceso a la aplicación desde la red privada. Puede especificar una ruta de acceso específica en el servidor back-end para publicar, mientras que el resto del servidor no se publica. De esta forma, puede publicar sitios diferentes en el mismo servidor como aplicaciones diferentes y dar a cada uno un nombre y unas reglas de acceso propios.

     > [!TIP]
     > Si publica una ruta de acceso, asegúrese de que incluye todas las imágenes, los scripts y las hojas de estilos necesarias para la aplicación. Por ejemplo, si la aplicación se encuentra en https://yourapp/app y usa las imágenes que se encuentran en https://yourapp/media, debe publicar https://yourapp/ como la ruta de acceso. Esta dirección URL interna no tiene que ser la página de inicio que verán los usuarios. Para más información, consulte [Establecimiento de una página principal personalizada para aplicaciones publicadas mediante el proxy de aplicación de Azure AD](application-proxy-office365-app-launcher.md).

   - **Dirección URL externa**: la dirección a la que irán los usuarios para tener acceso a la aplicación desde fuera de la red. Si no desea usar el dominio del proxy de aplicación predeterminado, lea sobre el [uso de dominios personalizados en el proxy de aplicación de Azure AD](active-directory-application-proxy-custom-domains.md).
   - **Autenticación previa**: la forma en que el proxy de aplicación comprueba los usuarios antes de concederles acceso a la aplicación. 

     - Azure Active Directory: el proxy de la aplicación redirige a los usuarios para que inicien sesión en Azure AD, que autentica sus permisos para el directorio y la aplicación. Se recomienda mantener esta opción como predeterminada, para que puede aprovechar las características de seguridad de Azure AD como el acceso condicional y Multi-Factor Authentication.
     - Acceso directo: los usuarios no tienen que autenticarse en Azure Active Directory para tener acceso a la aplicación. Esto no impide que pueda configurar los requisitos de autenticación en el back-end.
   - **Grupo de conectores**: los conectores procesan el acceso remoto a la aplicación, y los grupos de conectores le ayudan a organizar los conectores y las aplicaciones por región, red o finalidad. Si no tiene ningún grupo de conectores creado todavía, la aplicación se asigna al **predeterminado**.

>[!NOTE]
>Si la aplicación usa websockets para conectarse, asegúrese de que tiene la versión del conector 1.5.612.0 o superior con compatibilidad de websocket y de que el grupo de conectores asignado solo utiliza estos conectores.

   ![Configuración de la aplicación](./media/application-proxy-publish-azure-portal/configure-app.png)
5. Si es necesario, configure opciones adicionales. En la mayoría de las aplicaciones, debe mantener esta configuración en su estado predeterminado. 
   - **Tiempo de espera de las aplicaciones de back-end**: establezca este valor en **Largo** solo si la aplicación es lenta en autenticarse y conectarse. 
   - **Traducir URL en encabezados**: mantenga este valor como **Sí** a menos que la aplicación requiera el encabezado de host original en la solicitud de autenticación.
   - **Translate URLs in Application Body** (Traducir URL en el cuerpo de la aplicación): mantenga este valor como **No** a menos que tenga vínculos HTML codificados a otras aplicaciones locales y no use dominios personalizados. Para más información, consulte sobre la [traducción de vínculos con el proxy de aplicación](application-proxy-link-translation.md).
   
   ![Configuración de la aplicación](./media/application-proxy-publish-azure-portal/additional-settings.png)

6. Seleccione **Agregar**.


## <a name="add-a-test-user"></a>Adición de un usuario de prueba 

Para probar que la aplicación se publicó correctamente, agregue una cuenta de usuario de prueba. Compruebe que esta cuenta ya tiene permisos para acceder a la aplicación desde dentro de la red corporativa.

1. En la hoja de inicio rápido, seleccione **Assign a user for testing** (Asignar un usuario de prueba).

  ![Asignar un usuario de prueba](./media/application-proxy-publish-azure-portal/assign-user.png)

2. En la hoja Usuarios y grupos, seleccione **Agregar**.

  ![Agregar un usuario o grupo](./media/application-proxy-publish-azure-portal/add-user.png)

3. En la hoja para agregar asignación, seleccione **Usuarios y grupos** y elija luego la cuenta que va a agregar. 
4. Seleccione **Asignar**.

## <a name="test-your-published-app"></a>Prueba de la aplicación publicada

En el explorador, navegue a la dirección URL externa que configuró durante el paso de publicación. Debe ver la pantalla de inicio y poder iniciar sesión con la cuenta de prueba que configuró.

![Prueba de la aplicación publicada](./media/application-proxy-publish-azure-portal/test-app.png)


## <a name="next-steps"></a>Pasos siguientes
- [Descargue conectores](active-directory-application-proxy-enable.md) y [cree grupos de conectores](active-directory-application-proxy-connectors-azure-portal.md) para publicar aplicaciones en ubicaciones y redes independientes.

- [Configure el inicio de sesión único](application-proxy-sso-azure-portal.md) para la aplicación publicada recientemente.
