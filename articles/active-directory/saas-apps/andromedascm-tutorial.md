---
title: 'Tutorial: Integración de Azure Active Directory con Andromeda | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Andromeda.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 7a142c86-ca0c-4915-b1d8-124c08c3e3d8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1fd26129a6ab8fb6082f9465be71eadcafa292db
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/13/2019
ms.locfileid: "56165204"
---
# <a name="tutorial-azure-active-directory-integration-with-andromeda"></a>Tutorial: Integración de Azure Active Directory con Andromeda

En este tutorial, aprenderá a integrar Andromeda con Azure Active Directory (Azure AD).

La integración de Andromeda con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Andromeda.
- Puede permitir que los usuarios inicien sesión automáticamente en Andromeda (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Andromeda, se necesitan los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción que permita el inicio de sesión único en Andromeda

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. El escenario descrito en este tutorial consta de dos bloques de creación principales:

1. Adición de Andromeda desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-andromeda-from-the-gallery"></a>Adición de Andromeda desde la galería
Para configurar la integración de Andromeda en Azure AD, será preciso que agregue Andromeda desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Andromeda desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **Andromeda**, seleccione **Andromeda** en el panel de resultados y, luego, haga clic en el botón **Agregar** para agregar la aplicación.

    ![Andromeda en la lista de resultados](./media/andromedascm-tutorial/tutorial_andromedascm_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, configurará y probará el inicio de sesión único de Azure AD con Andromeda con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Andromeda para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Andromeda.

Para configurar y probar el inicio de sesión único de Azure AD con Andromeda, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para que los usuarios puedan usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**, para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Andromeda](#create-an-andromeda-test-user)**: para tener un homólogo de Britta Simon en Andromeda que esté vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**, para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y lo configurará en la aplicación Andromeda.

**Para configurar el inicio de sesión único de Azure AD con Andromeda, realice los pasos siguientes:**

1. En la página de integración de aplicaciones **Andromeda** de Azure Portal, haga clic en **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Cuadro de diálogo Inicio de sesión único](./media/andromedascm-tutorial/tutorial_andromedascm_samlbase.png)

3. En la sección **Dominio y direcciones URL de Andromeda**, realice los siguientes pasos si quiere configurar la aplicación en el modo iniciado por **IDP**:

    ![Información de dominio y direcciones URL de inicio de sesión único de Andromeda](./media/andromedascm-tutorial/tutorial_andromedascm_url.png)

     a. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón: `https://<tenantURL>.ngcxpress.com/`

    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://<tenantURL>.ngcxpress.com/SAMLConsumer.aspx`.

4. Active **Mostrar configuración avanzada de URL** y siga estos pasos si desea configurar la aplicación en el modo iniciado por **SP**:

    ![Información de dominio y direcciones URL de inicio de sesión único de Andromeda](./media/andromedascm-tutorial/tutorial_andromedascm_url1.png)

    En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<tenantURL>.ngcxpress.com/SAMLLogon.aspx`.
     
    > [!NOTE] 
    > El valor anterior no es real. El valor se actualizará con la dirección URL de inicio de sesión, la dirección URL de respuesta y el identificador reales, que se explican más adelante en el tutorial.

5. La aplicación Andromeda espera las aserciones de SAML en un formato específico. Configure las siguientes notificaciones para esta aplicación. Puede administrar los valores de estos atributos en la sección **Atributos de usuario** de la página de integración de aplicaciones. La siguiente captura de pantalla le muestra un ejemplo de esto.
    
    ![Configurar el atributo de inicio de sesión único](./media/andromedascm-tutorial/tutorial_andromedascm_attribute.png)

    > [!Important]
    > Desactive las definiciones de espacio de nombres al realizar esta configuración.
    
6. En la sección **Atributos de usuario** del cuadro de diálogo **Inicio de sesión único**, configure el atributo token de SAML como muestra la imagen y siga estos pasos:
    
    | Nombre del atributo | Valor de atributo |
    | -------------- | -------------------- |    
    | role        | Rol específico de la aplicación |
    | Tipo        | Tipo de aplicación |
    | company       | CompanyName    |

    > [!NOTE]
    > Estos valores no son reales. Se facilitan solo con fines de demostración, por lo que debe usar los roles de su organización.

     a. Haga clic en **Agregar atributo** para abrir el cuadro de diálogo **Agregar atributo**.

    ![Configurar la adición del inicio de sesión único](./media/andromedascm-tutorial/tutorial_attribute_04.png)

    ![Configurar la adición del atributo del inicio de sesión único](./media/andromedascm-tutorial/tutorial_attribute_05.png)

    b. En el cuadro de texto **Nombre**, escriba el nombre que se muestra para la fila.

    c. En la lista **Valor**, seleccione el atributo que se muestra para esa fila.

    d. Deje **Espacio de nombres** en blanco.
    
    e. Haga clic en **Aceptar**.

7. En la sección **Certificado de firma de SAML**, haga clic en **Certificado (Base64)** y, luego, guarde el archivo de certificado en el equipo.

    ![Vínculo de descarga del certificado](./media/andromedascm-tutorial/tutorial_andromedascm_certificate.png) 

8. Haga clic en el botón **Guardar** .

    ![Botón Configurar inicio de sesión único](./media/andromedascm-tutorial/tutorial_general_400.png)
    
9. En la sección **Configuración de Andromeda**, haga clic en **Configurar Andromeda** para abrir la ventana **Configurar inicio de sesión**. Copie la **dirección URL de servicio de inicio de sesión único de SAML** de la sección **Referencia rápida**.

    ![Configuración de Andromeda](./media/andromedascm-tutorial/tutorial_andromedascm_configure.png)

10. Inicie sesión como administrador en el sitio de la empresa Andromeda.

11. En la parte superior de la barra de menús, haga clic en **Admin** (Administrador) y vaya a **Administration** (Administración).

    ![Administración de Andromeda](./media/andromedascm-tutorial/tutorial_andromedascm_admin.png)

12. En el lado izquierdo de la barra de herramientas, en la sección **Interfaces** (Interfaces), haga clic en **SAML Configuration** (Configuración de SAML).

    ![SAML de Andromeda](./media/andromedascm-tutorial/tutorial_andromedascm_saml.png)

13. En la página **SAML Configuration** (Configuración de SAML), realice los siguientes pasos:

    ![Configuración de Andromeda](./media/andromedascm-tutorial/tutorial_andromedascm_config.png)

     a. Marque **Enable SSO with SAML** (Habilitar SSO con SAML).

    b. En la sección **Andromeda Information** (Información de Andromeda), copie el valor **SP Identity** (Identidad de SP) y péguelo en el cuadro de texto **Identificador** de la sección **Dominio y direcciones URL de Andromeda**.

    c. Copie el valor de **Consumer URL** (Dirección URL de consumidor) y péguelo en el cuadro de texto **Reply URL** (URL de respuesta) de la sección **Andromeda Domain and URLs** (Dominio y direcciones URL de Andromeda).

    d. Copie el valor de **Logon URL** (Dirección URL de registro) y péguelo en el cuadro de texto **Sign-on URL** (Dirección URL de inicio de sesión) de la sección **Andromeda Domain and URLs** (Dominio y direcciones URL de Andromeda).

    e. En la sección **SAML Identity Provider** (Proveedor de identidades SAML), escriba el nombre del proveedor de identidades.

    f. En el cuadro de texto **Single Sign On End Point** (Punto de conexión del servicio de inicio de sesión único), pegue el valor de **Dirección URL del servicio de inicio de sesión único de SAML** que copió de Azure Portal.

    g. Abra el **certificado codificado en Base64** descargado desde Azure Portal en el Bloc de notas y péguelo en el cuadro de texto **X 509 Certificate** (Certificado X509).
    
    h. Asigne los siguientes atributos con su respectivo valor para facilitar el inicio de sesión único desde Azure AD. El atributo **User ID** (Id. de usuario) es necesario para iniciar sesión. Para el aprovisionamiento, es necesario proporcionar los valores **Email** (Correo electrónico), **Company** (Compañía), **UserType** (Tipo de usuario) y **Role** (Rol). En esta sección, se define la asignación de atributos (nombre y valores) que se corresponden con los que se establecen en Azure Portal.

    ![Asignación de atributos de Andromeda](./media/andromedascm-tutorial/tutorial_andromedascm_attbmap.png)

    i. Haga clic en **Save**(Guardar).

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más aquí sobre la característica de documentación insertada: [Documentación insertada de Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

   ![Creación de un usuario de prueba de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel izquierdo de Azure Portal, haga clic en el botón **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/andromedascm-tutorial/create_aaduser_01.png)

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y, luego, haga clic en **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/andromedascm-tutorial/create_aaduser_02.png)

3. En la parte superior del cuadro de diálogo **Todos los usuarios**, haga clic en **Agregar** para abrir el cuadro de diálogo **Agregar**.

    ![Botón Agregar](./media/andromedascm-tutorial/create_aaduser_03.png)

4. En el cuadro de diálogo **Usuario** , realice los pasos siguientes:

    ![Cuadro de diálogo Usuario](./media/andromedascm-tutorial/create_aaduser_04.png)

     a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    d. Haga clic en **Create**(Crear).
 
### <a name="create-an-andromeda-test-user"></a>Creación de un usuario de prueba de Andromeda

El objetivo de esta sección es crear un usuario de prueba llamado Britta Simon en Andromeda. Andromeda admite el aprovisionamiento Just-In-Time, que está habilitado de forma predeterminada. No hay ningún elemento de acción para usted en esta sección. Durante un intento de acceder a Andromeda, se crea un usuario, en caso de que no exista.

>[!Note]
>Si necesita crear manualmente un usuario, es preciso que se ponga contacto con el [equipo de soporte técnico de Andromeda](https://www.ngcsoftware.com/support/).

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Andromeda.

![Asignación de rol de usuario][200] 

**Para asignar el usuario Britta Simon a Andromeda, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, navegue a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Andromeda**.

    ![Vínculo a Andromeda en la lista de aplicaciones](./media/andromedascm-tutorial/tutorial_andromedascm_app.png)  

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de Andromeda en el panel de acceso, debería iniciar sesión automáticamente en su aplicación Andromeda.
Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/andromedascm-tutorial/tutorial_general_01.png
[2]: ./media/andromedascm-tutorial/tutorial_general_02.png
[3]: ./media/andromedascm-tutorial/tutorial_general_03.png
[4]: ./media/andromedascm-tutorial/tutorial_general_04.png

[100]: ./media/andromedascm-tutorial/tutorial_general_100.png

[200]: ./media/andromedascm-tutorial/tutorial_general_200.png
[201]: ./media/andromedascm-tutorial/tutorial_general_201.png
[202]: ./media/andromedascm-tutorial/tutorial_general_202.png
[203]: ./media/andromedascm-tutorial/tutorial_general_203.png
