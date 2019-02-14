---
title: 'Tutorial: Integración de Azure Active Directory con MOVEit Transfer: Azure AD integration | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y MOVEit Transfer - Azure AD integration.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 8ff7102d-be73-4888-ae81-d8e3d01dd534
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b50bf623046094509170b5b5efc091013499b51b
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/13/2019
ms.locfileid: "56169843"
---
# <a name="tutorial-azure-active-directory-integration-with-moveit-transfer---azure-ad-integration"></a>Tutorial: Integración de Azure Active Directory con MOVEit Transfer: Azure AD integration

En este tutorial, obtendrá información sobre cómo integrar MOVEit Transfer - Azure AD integration con Azure Active Directory (Azure AD).

La integración de MOVEit Transfer - Azure AD integration con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a MOVEit Transfer - Azure AD integration.
- Puede habilitar que los usuarios inicien sesión automáticamente en MOVEit Transfer - Azure AD integration (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con MOVEit Transfer - Azure AD integration, se necesitan los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en MOVEit Transfer - Azure AD integration

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. El escenario descrito en este tutorial consta de dos bloques de creación principales:

1. Agregación de MOVEit Transfer - Azure AD integration desde la galería
1. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-moveit-transfer---azure-ad-integration-from-the-gallery"></a>Agregación de MOVEit Transfer - Azure AD integration desde la galería
Para configurar la integración de MOVEit Transfer - Azure AD integration en Azure AD, deberá agregar MOVEit Transfer - Azure AD integration desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar MOVEit Transfer - Azure AD integration desde la galería, siga estos pasos:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

1. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
1. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

1. En el cuadro de búsqueda, escriba **MOVEit Transfer - Azure AD integration**, seleccione **MOVEit Transfer - Azure AD integration** en el panel de resultados y, a continuación, haga clic en el botón **Agregar** para agregar la aplicación.

    ![MOVEit Transfer - Azure AD integration en la lista de resultados](./media/moveittransfer-tutorial/tutorial_moveittransfer_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, configurará y probará el inicio de sesión único de Azure AD con MOVEit Transfer - Azure AD integration con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de MOVEit Transfer - Azure AD integration para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de MOVEit Transfer - Azure AD integration.

Para establecer la relación de vínculo, en MOVEit Transfer - Azure AD integration, asigne el valor de **nombre de usuario** de Azure AD como valor de **Nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con MOVEit Transfer - Azure AD integration, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para que los usuarios puedan usar esta característica.
1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**, para probar el inicio de sesión único de Azure AD con Britta Simon.
1. **[Creación de un usuario de prueba en MOVEit Transfer - Azure AD integration](#create-a-moveit-transfer---azure-ad-integration-test-user)**: para tener un homólogo de Britta Simon en MOVEit Transfer - Azure AD integration que esté vinculado a la representación del usuario en Azure AD.
1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**, para permitir que Britta Simon use el inicio de sesión único de Azure AD.
1. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, se habilita el inicio de sesión único de Azure AD en Azure Portal y se configura el inicio de sesión único en la aplicación MOVEit Transfer - Azure AD integration.

**Para configurar el inicio de sesión único de Azure AD con MOVEit Transfer - Azure AD integration, siga estos pasos:**

1. En la página de integración de la aplicación **MOVEit Transfer - Azure AD integration** de Azure Portal, haga clic en **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

1. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Cuadro de diálogo Inicio de sesión único](./media/moveittransfer-tutorial/tutorial_moveittransfer_samlbase.png)

1. En la sección **Dominio y direcciones URL de MOVEit Transfer - Azure AD integration** realice los siguientes pasos:

    ![Configurar inicio de sesión único](./media/moveittransfer-tutorial/tutorial_moveittransfer_url.png)

     a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://contoso.com`.

    b. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón: `https://contoso.com/<tenatid>`

    c. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://contoso.com/<tenatid>/SAML/SSO/HTTP-Post`.    
     
    > [!NOTE] 
    > Estos valores no son reales. Actualice estos valores con los valores reales de Identificador, URL de respuesta y URL de inicio de sesión. Puede hacer referencia a estos valores más tarde en la sección **Dirección URL de metadatos del proveedor de servicios** o póngase en contacto con el [equipo de soporte técnico de MOVEit Transfer - Azure AD integration](https://community.ipswitch.com/s/support) para obtener estos valores.

1. En la sección **Certificado de firma de SAML**, haga clic en **XML de metadatos** y luego guarde el archivo de metadatos en el equipo.

    ![Vínculo de descarga del certificado](./media/moveittransfer-tutorial/tutorial_moveittransfer_certificate.png) 

1. Haga clic en el botón **Guardar** .

    ![Botón Configurar inicio de sesión único](./media/moveittransfer-tutorial/tutorial_general_400.png)
    
1. Inicie la sesión en el inquilino de MOVEit Transfer como administrador.

1. En la barra de navegación de la izquierda, haga clic en **Settings**(Configuración).

    ![Sección Configuración en la aplicación](./media/moveittransfer-tutorial/tutorial_moveittransfer_000.png)

1. Haga clic en el vínculo **Single Signon** (Inicio de sesión único) que se encuentra en **Security Policies -> User Auth** (Directivas de seguridad -> Autenticación de usuario).

    ![Directivas de seguridad en la aplicación](./media/moveittransfer-tutorial/tutorial_moveittransfer_001.png)

1. Haga clic en el vínculo Metadata URL (Dirección URL de metadatos) para descargar el documento de metadatos.

    ![Dirección URL de metadatos del proveedor de servicios](./media/moveittransfer-tutorial/tutorial_moveittransfer_002.png)
    
    * Compruebe que **entityID** (identificador de entidad) coincide con **Identifier** (identificador) en la sección **Dominio y direcciones URL de MOVEit Transfer - Azure AD integration**.
    * Compruebe que la dirección URL de la ubicación de **AssertionConsumerService** coincide con **REPLY URL** (dirección URL de respuesta) en la sección **Dominio y direcciones URL de MOVEit Transfer - Azure AD integration**.
    
    ![Configuración del inicio de sesión único en la aplicación](./media/moveittransfer-tutorial/tutorial_moveittransfer_007.png)

1. Haga clic en el botón **Add Identity Provider** (Agregar proveedor de identidades) para agregar un nuevo proveedor de identidad federada.

    ![Agregación del proveedor de identidades](./media/moveittransfer-tutorial/tutorial_moveittransfer_003.png)

1. Haga clic en **Browse...** (Examinar...) para seleccionar el archivo de metadatos que descargó de Azure Portal y, después, haga clic en **Add Identity Provider** (Agregar proveedor de identidades) para cargar el archivo descargado.

    ![Proveedor de identidades de SAML](./media/moveittransfer-tutorial/tutorial_moveittransfer_004.png)

1. Seleccione "**Yes**" (Sí) en la opción **Enabled** (Habilitado) de la página **Edit Federated Identity Provider Settings** (Editar configuración de proveedor de identidades federada) y haga clic en **Save** (Guardar).

    ![Configuración del proveedor de identidades federadas](./media/moveittransfer-tutorial/tutorial_moveittransfer_005.png)

1. En la página **Edit Federated Identity Provider Settings** (Editar configuración del proveedor de identidades federadas), realice las siguientes acciones:
    
    ![Edición de la configuración del proveedor de identidades federadas](./media/moveittransfer-tutorial/tutorial_moveittransfer_006.png)
    
     a. Seleccione **SAML NameID** en **Login name** (Nombre de inicio de sesión).
    
    b. Seleccione **Other** (Otros) como **Full name** (Nombre completo) y en el cuadro de texto **Attribute name** (Nombre de atributo) coloque el valor: `http://schemas.microsoft.com/identity/claims/displayname`.
    
    c. Seleccione **Other** (Otros) como **Email** (Correo electrónico) y en el cuadro de texto **Attribute name** (Nombre de atributo) coloque el valor: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.
    
    d. Seleccione **Yes** (Sí) en **Auto-create account on signon** (Crear automáticamente cuenta al iniciar sesión).
    
    e. Haga clic en el botón **Guardar** .

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más aquí sobre la característica de documentación insertada: [Documentación insertada de Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

   ![Creación de un usuario de prueba de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel izquierdo de Azure Portal, haga clic en el botón **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/moveittransfer-tutorial/create_aaduser_01.png)

1. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y, luego, haga clic en **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/moveittransfer-tutorial/create_aaduser_02.png)

1. En la parte superior del cuadro de diálogo **Todos los usuarios**, haga clic en **Agregar** para abrir el cuadro de diálogo **Agregar**.

    ![Botón Agregar](./media/moveittransfer-tutorial/create_aaduser_03.png)

1. En el cuadro de diálogo **Usuario** , realice los pasos siguientes:

    ![Cuadro de diálogo Usuario](./media/moveittransfer-tutorial/create_aaduser_04.png)

     a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    d. Haga clic en **Create**(Crear).
 
### <a name="create-a-moveit-transfer---azure-ad-integration-test-user"></a>Creación de un usuario de prueba de MOVEit Transfer - Azure AD integration

El objetivo de esta sección es crear un usuario llamado Britta Simon en MOVEit Transfer - Azure AD integration. MOVEit Transfer - Azure AD integration admite el aprovisionamiento Just-In-Time que ha habilitado. No hay ningún elemento de acción para usted en esta sección. Al intentar acceder a MOVEit Transfer - Azure AD integration se creará un nuevo usuario, en caso de que no exista.

>[!NOTE]
>Si necesita crear un usuario manualmente, es preciso que se ponga en contacto con el [equipo de soporte técnico de MOVEit Transfer - Azure AD integration](https://community.ipswitch.com/s/support).

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a MOVEit Transfer - Azure AD integration.

![Asignación de rol de usuario][200] 

**Para asignar a Britta Simon a MOVEit Transfer - Azure AD integration, siga estos pasos:**

1. En Azure Portal, abra la vista de aplicaciones, navegue a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

1. En la lista de aplicaciones, seleccione **MOVEit Transfer - Azure AD integration**.

    ![Vínculo a MOVEit Transfer - Azure AD integration en la lista de aplicaciones](./media/moveittransfer-tutorial/tutorial_moveittransfer_app.png)  

1. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

1. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

1. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

1. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

El objetivo de esta sección es probar la configuración del inicio de sesión único de Azure AD mediante el panel de acceso.

Al hacer clic en el icono de MOVEit Transfer - Azure AD integration en el panel de acceso, debería iniciar sesión automáticamente en la aplicación MOVEit Transfer - Azure AD integration. 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)


<!--Image references-->

[1]: ./media/moveittransfer-tutorial/tutorial_general_01.png
[2]: ./media/moveittransfer-tutorial/tutorial_general_02.png
[3]: ./media/moveittransfer-tutorial/tutorial_general_03.png
[4]: ./media/moveittransfer-tutorial/tutorial_general_04.png

[100]: ./media/moveittransfer-tutorial/tutorial_general_100.png

[200]: ./media/moveittransfer-tutorial/tutorial_general_200.png
[201]: ./media/moveittransfer-tutorial/tutorial_general_201.png
[202]: ./media/moveittransfer-tutorial/tutorial_general_202.png
[203]: ./media/moveittransfer-tutorial/tutorial_general_203.png

