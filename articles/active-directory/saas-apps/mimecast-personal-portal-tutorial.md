---
title: 'Tutorial: Integración de Azure Active Directory con Mimecast Personal Portal | Microsoft Docs'
description: Obtenga información sobre cómo configurar el inicio de sesión único entre Azure Active Directory y Mimecast Personal Portal.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 345b22be-d87e-45a4-b4c0-70a67eaf9bfd
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/24/2018
ms.author: jeedes
ms.openlocfilehash: 2380fec59e26ada57dac2134efc898d6d466bd57
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/19/2018
ms.locfileid: "36229640"
---
# <a name="tutorial-azure-active-directory-integration-with-mimecast-personal-portal"></a>Tutorial: Integración de Azure Active Directory con Mimecast Personal Portal

En este tutorial, obtendrá información sobre cómo integrar Mimecast Personal Portal con Azure Active Directory (Azure AD).

La integración de Mimecast Personal Portal con Azure AD proporciona las siguientes ventajas:

- En Azure AD puede controlar quién tiene acceso a Mimecast Personal Portal.
- Puede permitir que los usuarios inicien sesión automáticamente en Mimecast Personal Portal (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>requisitos previos

Para configurar la integración de Azure AD con Mimecast Personal Portal, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Un suscripción habilitada para inicio de sesión único en Mimecast Personal Portal

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. El escenario descrito en este tutorial consta de dos bloques de creación principales:

1. Incorporación de Mimecast Personal Portal desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-mimecast-personal-portal-from-the-gallery"></a>Incorporación de Mimecast Personal Portal desde la galería
Para configurar la integración de Mimecast Personal Portal en Azure AD, deberá agregar esta solución desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Mimecast Personal Portal desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **Mimecast Personal Portal**, seleccione **Mimecast Personal Portal** en el panel de resultados y, luego, haga clic en el botón **Agregar** para agregar la aplicación.

    ![Mimecast Personal Portal en la lista de resultados](./media/mimecast-personal-portal-tutorial/tutorial_mimecastpersonalportal_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, configurará y probará el inicio de sesión único de Azure AD con Mimecast Personal Portal con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Mimecast Personal Portal para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Mimecast Personal Portal.

Para configurar y probar el inicio de sesión único de Azure AD con Mimecast Personal Portal, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para que los usuarios puedan usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**, para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Mimecast Personal Portal](#create-a-mimecast-personal-portal-test-user)**: para tener un homólogo de Britta Simon en Mimecast Personal Portal que esté vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**, para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y lo configurará en la aplicación Mimecast Personal Portal.

**Para configurar el inicio de sesión único de Azure AD con Mimecast Personal Portal, realice los pasos siguientes:**

1. En la página de integración de la aplicación **Mimecast Personal Portal** de Azure Portal, haga clic en **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Cuadro de diálogo Inicio de sesión único](./media/mimecast-personal-portal-tutorial/tutorial_mimecastpersonalportal_samlbase.png)

3. En la sección **Dominio y direcciones URL de Mimecast Personal Portal**, lleve a cabo los pasos siguientes:

    ![Información acerca del inicio de sesión único de dominio y direcciones URL de Mimecast Personal Portal](./media/mimecast-personal-portal-tutorial/tutorial_mimecastpersonalportal_url.png)

    a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL: 

    | Region  |  Valor | 
    | --------------- | --------------- | 
    | Europa          | `https://eu-api.mimecast.com/login/saml`|
    | Estados Unidos   | `https://us-api.mimecast.com/login/saml`|
    | Sudáfrica    | `https://za-api.mimecast.com/login/saml`|
    | Australia       | `https://au-api.mimecast.com/login/saml`|
    | Internacional        | `https://jer-api.mimecast.com/login/saml`|

    b. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón:

    | Region  |  Valor | 
    | --------------- | --------------- |
    | Europa          | `https://eu-api.mimecast.com/sso/<accountcode>`|
    | Estados Unidos   | `https://us-api.mimecast.com/sso/<accountcode>`|    
    | Sudáfrica    | `https://za-api.mimecast.com/sso/<accountcode>`|
    | Australia       | `https://au-api.mimecast.com/sso/<accountcode>`|
    | Internacional        | `https://jer-api.mimecast.com/sso/<accountcode>`|

    c. En el cuadro de texto **URL de respuesta**, escriba una dirección URL: 

    | Region  |  Valor | 
    | --------------- | --------------- | 
    | Europa          | `https://eu-api.mimecast.com/login/saml`|
    | Estados Unidos   | `https://us-api.mimecast.com/login/saml`|
    | Sudáfrica    | `https://za-api.mimecast.com/login/saml`|
    | Australia       | `https://au-api.mimecast.com/login/saml`|
    | Internacional        | `https://jer-api.mimecast.com/login/saml`|
    
    > [!NOTE] 
    > El valor del identificador no es real. Actualícelo con el identificador real. Póngase en contacto con el [equipo de soporte de cliente de Mimecast Personal Portal](http://www.mimecast.com/customer-success/technical-support/) para obtener el valor. 

4. En la sección **Certificado de firma de SAML**, haga clic en **Certificado (Base64)** y, luego, guarde el archivo de certificado en el equipo.

    ![Vínculo de descarga del certificado](./media/mimecast-personal-portal-tutorial/tutorial_mimecastpersonalportal_certificate.png) 

5. Haga clic en el botón **Guardar** .

    ![Botón Configurar inicio de sesión único](./media/mimecast-personal-portal-tutorial/tutorial_general_400.png)

6. En la sección **Configuración de Mimecast Personal Portal**, haga clic en **Configurar Mimecast Personal Portal** para abrir la ventana **Configurar el inicio de sesión**. Copie la **URL del servicio de inicio de sesión único de SAML, el identificador de entidad de SAML y la dirección URL de cierre de sesión** de la sección **Referencia rápida**.

    ![Configuración de Mimecast Personal Portal](./media/mimecast-personal-portal-tutorial/tutorial_mimecastpersonalportal_configure.png) 

7. En otra ventana del explorador web, inicie sesión como administrador en el sitio de la compañía de Mimecast Personal Portal.

8. Vaya a **Servicios \> Aplicaciones**.
   
    ![Aplicaciones](./media/mimecast-personal-portal-tutorial/ic794998.png "Aplicaciones")

9. Haga clic en **Authentication Profiles**(Perfiles de autenticación).
   
    ![Authentication Profiles (Perfiles de autenticación)](./media/mimecast-personal-portal-tutorial/ic794999.png "Authentication Profiles (Perfiles de autenticación)")

10. Haga clic en **New Authentication Profile**(Nuevo perfil de autenticación).
   
    ![New Authentication Profile (Nuevo perfil de autenticación)](./media/mimecast-personal-portal-tutorial/ic795000.png "New Authentication Profile (Nuevo perfil de autenticación)")

11. En la sección **Authentication Profile** (Perfil de autenticación), realice estos pasos:
   
    ![Authentication Profile (Perfil de autenticación)](./media/mimecast-personal-portal-tutorial/ic795001.png "Authentication Profile (Perfil de autenticación)")
   
    a. En el cuadro de texto **Description** (Descripción), escriba un nombre para la configuración.
   
    b. Seleccione **Aplicar la autenticación SAML a Mimecast Personal Portal**.
   
    c. Como **Provider** (Proveedor), seleccione **Azure Active Directory**.
   
    d. En el cuadro de texto **Issuer URL** (Dirección URL del emisor), pegue el valor de **SAML Entity ID** (Identificador de entidad de SAML) que copió de Azure Portal.
   
    e. En el cuadro de texto **Login URL** (Dirección URL de inicio de sesión), pegue el valor de **SAML Single Sign-On Service URL** (Dirección URL del servicio de inicio de sesión de SAML) que copió de Azure Portal.
   
    f. En el cuadro de texto **Logout URL** (Dirección URL de cierre de sesión), pegue el valor de **Dirección URL de cierre de sesión** que copió de Azure Portal.

    g. Abra el certificado codificado en **base 64** descargado de Azure Portal en el Bloc de notas, copie su contenido en el Portapapeles y luego péguelo en el cuadro de texto **Certificado de proveedor de identidades (metadatos)**.

    h. Seleccione **Permitir inicio de sesión único**.
   
    i. Haga clic en **Save**(Guardar).

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

   ![Creación de un usuario de prueba de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel izquierdo de Azure Portal, haga clic en el botón **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/mimecast-personal-portal-tutorial/create_aaduser_01.png)

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y, luego, haga clic en **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/mimecast-personal-portal-tutorial/create_aaduser_02.png)

3. En la parte superior del cuadro de diálogo **Todos los usuarios**, haga clic en **Agregar** para abrir el cuadro de diálogo **Agregar**.

    ![Botón Agregar](./media/mimecast-personal-portal-tutorial/create_aaduser_03.png)

4. En el cuadro de diálogo **Usuario** , realice los pasos siguientes:

    ![Cuadro de diálogo Usuario](./media/mimecast-personal-portal-tutorial/create_aaduser_04.png)

    a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    d. Haga clic en **Create**(Crear).
 
### <a name="create-a-mimecast-personal-portal-test-user"></a>Creación de un usuario de prueba de Mimecast Personal Portal

Para permitir que los usuarios de Azure AD inicien sesión en Mimecast Personal Portal, deben aprovisionarse en Mimecast Personal Portal. En el caso de Mimecast Personal Portal, el aprovisionamiento es una tarea manual.

Deberá registrar un dominio para poder crear los usuarios.

**Siga estos pasos para configurar el aprovisionamiento de usuario:**

1. Inicie sesión en **Mimecast Personal Portal** como administrador.

2. Vaya a **Directorios\> Interno**.
   
    ![Directories (Directorios)](./media/mimecast-personal-portal-tutorial/ic795003.png "Directories (Directorios)")

3. Haga clic en **Register New Domain**(Registrar nuevo dominio).
   
    ![Register New Domain (Registrar nuevo dominio)](./media/mimecast-personal-portal-tutorial/ic795004.png "Register New Domain (Registrar nuevo dominio)")

4. Una vez creado el nuevo dominio, haga clic en **New Address**(Nueva dirección).
   
    ![New Address (Nueva dirección)](./media/mimecast-personal-portal-tutorial/ic795005.png "New Address (Nueva dirección)")

5. En el cuadro de diálogo de dirección nueva, lleve a cabo los pasos siguientes para la cuenta de Azure AD válida que desea aprovisionar:
   
    ![Guardar](./media/mimecast-personal-portal-tutorial/ic795006.png "Guardar")
   
    a. En el cuadro de texto **Dirección de correo electrónico**, escriba la **dirección de correo electrónico** del usuario, en este caso **BrittaSimon@contoso.com**.
    
    b. En el cuadro de texto **Nombre global**, escriba el **nombre de usuario** como **BrittaSimon**.

    c. En los cuadros de texto **Contraseña** y **Confirmar contraseña**, escriba la **contraseña** del usuario.
   
    b. Haga clic en **Save**(Guardar).

>[!NOTE]
>Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de Mimecast Personal Portal ofrecida por Mimecast Personal Portal para aprovisionar cuentas de usuario de Azure AD.

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Mimecast Personal Portal.

![Asignación de rol de usuario][200] 

**Para asignar Britta Simon a Mimecast Personal Portal, siga estos pasos:**

1. En Azure Portal, abra la vista de aplicaciones, navegue a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Mimecast Personal Portal**.

    ![Vínculo a Mimecast Personal Portal en la lista de aplicaciones](./media/mimecast-personal-portal-tutorial/tutorial_mimecastpersonalportal_app.png)  

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de Mimecast Personal Portal en el Panel de acceso, debería iniciar sesión automáticamente en su aplicación Mimecast Personal Portal.
Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/mimecast-personal-portal-tutorial/tutorial_general_01.png
[2]: ./media/mimecast-personal-portal-tutorial/tutorial_general_02.png
[3]: ./media/mimecast-personal-portal-tutorial/tutorial_general_03.png
[4]: ./media/mimecast-personal-portal-tutorial/tutorial_general_04.png

[100]: ./media/mimecast-personal-portal-tutorial/tutorial_general_100.png

[200]: ./media/mimecast-personal-portal-tutorial/tutorial_general_200.png
[201]: ./media/mimecast-personal-portal-tutorial/tutorial_general_201.png
[202]: ./media/mimecast-personal-portal-tutorial/tutorial_general_202.png
[203]: ./media/mimecast-personal-portal-tutorial/tutorial_general_203.png

