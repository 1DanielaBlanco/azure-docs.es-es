---
title: Administrar certificados de federación en Azure AD | Microsoft Docs
description: Aprenda a personalizar la fecha de expiración de los certificados de federación y a renovar certificados que expiran pronto.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/09/2017
ms.author: barbkess
ms.reviewer: jeedes
ms.openlocfilehash: 5b4e2694a9b7e67643cb1e3b3cbc99fab940bd9d
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/23/2018
ms.locfileid: "36337103"
---
# <a name="manage-certificates-for-federated-single-sign-on-in-azure-active-directory"></a>Administrar certificados para inicio de sesión único federado en Azure Active Directory
En este artículo se aborda información y preguntas comunes relacionadas con los certificados que crea Azure Active Directory (Azure AD) para establecer el inicio de sesión único (SSO) federado para las aplicaciones SaaS. Estas aplicaciones se agregan desde la galería de aplicaciones de Azure AD o mediante una plantilla de aplicación ajena a la galería. Configure la aplicación mediante la opción de inicio de sesión único federado.

Este artículo solo es relevante para las aplicaciones que están configuradas para usar el inicio de sesión único de Azure AD mediante la federación de SAML, como se muestra en el ejemplo siguiente:

![Inicio de sesión único de Azure AD ](./media/manage-certificates-for-federated-single-sign-on/saml_sso.PNG)

## <a name="auto-generated-certificate-for-gallery-and-non-gallery-applications"></a>Generación automática de certificado para aplicaciones de la galería y ajenas a la misma
Al agregar una nueva aplicación desde la galería y configurar el inicio de sesión basado en SAML, Azure AD genera un certificado para la aplicación con una validez de tres años. Puede descargar este certificado desde la sección **Certificado de firma SAML**. Para las aplicaciones de la galería, en esta sección es posible que se muestre una opción para descargar el certificado o los metadatos, según el requisito de la aplicación.

![Inicio de sesión único de Azure AD](./media/manage-certificates-for-federated-single-sign-on/saml_certificate_download.png)

## <a name="customize-the-expiration-date-for-your-federation-certificate-and-roll-it-over-to-a-new-certificate"></a>Personalizar la fecha de expiración para el certificado de federación y sustituirlo por un certificado nuevo
De forma predeterminada, los certificados se establecen para que expiren después de tres años. Puede elegir otra fecha de expiración para el certificado siguiendo los pasos que se indican a continuación.
En las capturas de pantalla se usa Salesforce como ejemplo, pero estos pasos se pueden aplicar a cualquier aplicación SaaS federada.

1. En [Azure Portal](https://aad.portal.azure.com), haga clic en **Aplicación empresarial** en el panel izquierdo y, después, en **Nueva aplicación** en la página **Información general**:

   ![Apertura del asistente para configuración de SSO](./media/manage-certificates-for-federated-single-sign-on/enterprise_application_new_application.png)

2. Busque la aplicación de la galería y después seleccione la que quiere agregar. Si no encuentra la aplicación necesaria, agregue una mediante la opción **Aplicación situada fuera de la galería**. Esta característica solo está disponible en la SKU Azure AD Premium (P1 y P2).

    ![Inicio de sesión único de Azure AD](./media/manage-certificates-for-federated-single-sign-on/add_gallery_application.png)

3. Haga clic en el vínculo **Inicio de sesión único** del panel izquierdo y cambie **Modo de inicio de sesión único** a **Inicio de sesión basado en SAML**. Este paso genera un certificado de tres años para la aplicación.

4. Para crear un certificado nuevo, haga clic en el vínculo **Crear nuevo certificado** de la sección **Certificado de firma de SAML**.

    ![Generar un certificado nuevo](./media/manage-certificates-for-federated-single-sign-on/create_new_certficate.png)

5. El vínculo **Crear un nuevo certificado** abre el control de calendario. Puede establecer cualquier fecha y hora hasta tres años a partir de la fecha actual. La fecha y hora seleccionadas son la fecha y hora de expiración nuevas del nuevo certificado. Haga clic en **Save**(Guardar).

    ![Descarga y carga del certificado](./media/manage-certificates-for-federated-single-sign-on/certifcate_date_selection.PNG)

6. Ahora el nuevo certificado está disponible para su descarga. Haga clic en el vínculo **Certificado** para descargarlo. En este momento, el certificado no está activo. Cuando quiera sustituir este certificado, active la casilla **Make new certificate active** (Activar nuevo certificado) y haga clic en **Guardar**. Desde ese momento, Azure AD empieza a usar el nuevo certificado para firmar la respuesta.

7.  Para obtener información sobre cómo cargar el certificado en su aplicación SaaS particular, haga clic en el vínculo **View application configuration tutorial** (Ver tutorial de configuración de la aplicación).

## <a name="certificate-expiration-notification-email"></a>Correo electrónico de notificación de expiración del certificado

Azure AD enviará una notificación por correo electrónico 60, 30 y 7 días antes de que expire el certificado SAML. Para especificar la dirección de correo electrónico a la que se debe enviar la notificación:

- En la página Inicio de sesión único de la aplicación Azure Active Directory, vaya al campo Notificación por correo electrónico.
- Escriba la dirección de correo electrónico que debe recibir el correo electrónico de notificación de expiración de certificado. De forma predeterminada, este campo usa la dirección de correo electrónico del administrador que agregó la aplicación.

Recibirá el correo electrónico de notificación de aadnotification@microsoft.com. Para evitar que el correo electrónico vaya a la ubicación de correo no deseado, asegúrese de agregar este correo electrónico a los contactos. 

## <a name="renew-a-certificate-that-will-soon-expire"></a>Renovar un certificado que expirará pronto
Los pasos de renovación siguientes no deben suponer un tiempo de inactividad considerable para los usuarios. En las capturas de pantalla de esta sección se incluye Salesforce como ejemplo, pero estos pasos se pueden aplicar a cualquier aplicación SaaS federada.

1. En la página **Inicio de sesión único** de la aplicación **Azure Active Directory**, genere el nuevo certificado para la aplicación. Puede hacerlo si hace clic en el vínculo **Crear nuevo certificado** de la sección **Certificado de firma de SAML**.

    ![Generar un certificado nuevo](./media/manage-certificates-for-federated-single-sign-on/create_new_certficate.png)

2. Seleccione la fecha y hora de expiración que quiere para el nuevo certificado y haga clic en **Guardar**.

3. Descargue el certificado en la opción **Certificado de firma de SAML**. Cargue el nuevo certificado en la pantalla de configuración de inicio de sesión único de la aplicación SaaS. Para obtener información sobre cómo hacer esto para su aplicación SaaS particular, haga clic en el vínculo **View application configuration tutorial** (Ver tutorial de configuración de la aplicación).
   
4. Para activar el nuevo certificado en Azure AD, active la casilla **Make new certificate active** (Activar nuevo certificado) y haga clic en el botón **Guardar** en la parte superior de la página. De este modo se sustituye el nuevo certificado en Azure AD. El estado del certificado cambia de **Nuevo** a **Activo**. Desde ese momento, Azure AD empieza a usar el nuevo certificado para firmar la respuesta. 
   
    ![Generar un certificado nuevo](./media/manage-certificates-for-federated-single-sign-on/new_certificate_download.png)

## <a name="related-articles"></a>Artículos relacionados
* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](../saas-apps/tutorial-list.md)
* [Índice de artículos sobre la administración de aplicaciones en Azure Active Directory](../active-directory-apps-index.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](what-is-single-sign-on.md)
* [Solución de problemas de inicio de sesión único basado en SAML](../develop/active-directory-saml-debugging.md)
