---
title: 'Tutorial: Integración de Azure Active Directory con Perception United States (no UltiPro) | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Perception United States (no UltiPro).
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: b4a8f026-cb5f-41eb-9680-68eddc33565e
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.openlocfilehash: f4d01495e48d440c09ad62cd69dbf9b11b7a4cc2
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/23/2019
ms.locfileid: "54812410"
---
# <a name="tutorial-azure-active-directory-integration-with-perception-united-states-non-ultipro"></a>Tutorial: Integración de Azure Active Directory con Perception United States (no UltiPro)

En este tutorial, obtendrá información sobre cómo integrar Perception United States (no UltiPro) con Azure Active Directory (Azure AD).

La integración de Perception United States (no UltiPro) con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Perception United States (no UltiPro).
- Puede permitir que los usuarios inicien sesión automáticamente en Perception United States (no UltiPro) (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Perception United States (no UltiPro), necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en Perception United States (no UltiPro)

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. El escenario descrito en este tutorial consta de dos bloques de creación principales:

1. Agregar Perception United States (no UltiPro) desde la galería
1. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-perception-united-states-non-ultipro-from-the-gallery"></a>Agregar Perception United States (no UltiPro) desde la galería
Para configurar la integración de Perception United States (no UltiPro) en Azure AD, deberá agregar Perception United States (no UltiPro) desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Perception United States (no UltiPro) desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

1. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
1. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

1. En el cuadro de búsqueda, escriba **Perception United States (no UltiPro)**, seleccione **Perception United States (no UltiPro)** en el panel de resultados y, a continuación, haga clic en el botón **Agregar** para agregar la aplicación.

    ![Perception United States (no UltiPro) en la lista de resultados](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con Perception United States (no UltiPro) con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Perception United States (no UltiPro) para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Perception United States (no UltiPro).

Para establecer la relación de vínculo, en Perception United States (no UltiPro), asigne el valor de **nombre de usuario** de Azure AD como valor de **Nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con Perception United States (no UltiPro), es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para que los usuarios puedan usar esta característica.
1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**, para probar el inicio de sesión único de Azure AD con Britta Simon.
1. **[Creación de un usuario de prueba de Perception United States (no UltiPro)](#create-a-perception-united-states-non-ultipro-test-user)** : para tener un homólogo de Britta Simon en Perception United States (no UltiPro) que esté vinculado a la representación del usuario en Azure AD.
1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**, para permitir que Britta Simon use el inicio de sesión único de Azure AD.
1. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y lo configurará en la aplicación Perception United States (no UltiPro).

**Para configurar el inicio de sesión único de Azure AD con Perception United States (no UltiPro), realice los pasos siguientes:**

1. En la página de integración de la aplicación **Perception United States (no UltiPro)** de Azure Portal, haga clic en **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

1. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Cuadro de diálogo Inicio de sesión único](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_samlbase.png)

1. En la sección **Dominio y direcciones URL de Perception United States (no UltiPro)**, lleve a cabo los pasos siguientes:

    ![Información de dominio y direcciones URL de inicio de sesión único de Perception United States (no UltiPro)](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_url.png)

     a. En el cuadro de texto **Identificador**, escriba la dirección URL: `https://perception.kanjoya.com/sp`

    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://perception.kanjoya.com/sso?idp=<entity_id>`.

    > [!NOTE] 
    > Este valor no es real. El valor se actualizará con la dirección URL de respuesta real, que se explica más adelante en el tutorial.
 
1. En la sección **Certificado de firma de SAML**, haga clic en **XML de metadatos** y luego guarde el archivo de metadatos en el equipo.

    ![Vínculo de descarga del certificado](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_certificate.png) 

1. Haga clic en el botón **Guardar** .

    ![Botón Configurar inicio de sesión único](./media/perceptionunitedstates-tutorial/tutorial_general_400.png)

1. En la sección **Configuración de Perception United States (no UltiPro)**, haga clic en **Configurar Perception United States (no UltiPro)** para abrir la ventana**Configurar inicio de sesión**. Copie el valor de **Identificador de entidad de SAML** de la **sección Referencia rápida**

     a. La aplicación **Perception United States (no UltiPro)** requiere que el valor de **Identificador de entidad de SAML**, que ha copiado, esté codificado como URI. Para obtener el valor codificado como URI, use el siguiente vínculo: **http://www.url-encode-decode.com/**.

    b. Después de obtener el valor codificado como URI, combínelo con la **Dirección URL de respuesta** según se indica a continuación:

    `https://perception.kanjoya.com/sso?idp=<URI encooded entity_id>`
    
    c. Pegue el valor anterior en el cuadro de texto **Dirección URL de respuesta** en la sección **Dominio y direcciones URL de Perception United States (no UltiPro)**.

    ![Configuración de Perception United States (no UltiPro)](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_configure.png) 

1. En otra ventana del explorador web, inicie sesión en su sitio de la compañía de Perception United States (no UltiPro) como administrador.

1. En la barra de herramientas principal, haga clic en **Account Settings** (Configuración de la cuenta).

    ![Usuario de Perception United States (no UltiPro)](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_user.png)

1. En la página **Account Settings** (Configuración de la cuenta), realice los pasos siguientes:

    ![Usuario de Perception United States (no UltiPro)](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_account.png)

     a. En el cuadro de texto **Company Name** (Nombre de la empresa), escriba el nombre de la **empresa**.
    
    b. En el cuadro de texto **Account Name** (Nombre de la cuenta), escriba el nombre de la **cuenta**.

    c. En el cuadro de texto **Default Reply-To Email** (Correo electrónico de respuesta predeterminado), escriba un **correo electrónico** válido.

    d. Seleccione **SSO Identity Provider** (Proveedor de identidades de SSO) como **SAML 2.0**.

1. En la página **SSO Configuration** (Configuración de SSO), realice los siguientes pasos:

    ![Configuración de SSO de Perception United States (no UltiPro)](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_ssoconfig.png)

     a. Seleccione **SAML NameID Type** (Tipo de identificador de nombre de SAML) como **EMAIL** (correo electrónico).

    b. En el cuadro de texto **SSO Configuration Name** (nombre de configuración de SSO), escriba el nombre de su **configuración**.
    
    c. En el cuadro de texto **Identity Provider Name** (nombre del proveedor de identidades), pegue el valor de **Identificador de entidad de SAML** que ha copiado de Azure Portal. 

    d. En el cuadro de texto **SAML Domain** (dominio de SAML), escriba el dominio, por ejemplo **@contoso.com**.

    e. Haga clic en **Upload Again** (cargar de nuevo) para cargar el archivo **XML de metadatos**.

    f. Haga clic en **Update**(Actualizar).


> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más aquí sobre la característica de documentación insertada: [Documentación insertada de Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

   ![Creación de un usuario de prueba de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel izquierdo de Azure Portal, haga clic en el botón **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/perceptionunitedstates-tutorial/create_aaduser_01.png)

1. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y, luego, haga clic en **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/perceptionunitedstates-tutorial/create_aaduser_02.png)

1. En la parte superior del cuadro de diálogo **Todos los usuarios**, haga clic en **Agregar** para abrir el cuadro de diálogo **Agregar**.

    ![Botón Agregar](./media/perceptionunitedstates-tutorial/create_aaduser_03.png)

1. En el cuadro de diálogo **Usuario** , realice los pasos siguientes:

    ![Cuadro de diálogo Usuario](./media/perceptionunitedstates-tutorial/create_aaduser_04.png)

     a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    d. Haga clic en **Create**(Crear).
  
### <a name="create-a-perception-united-states-non-ultipro-test-user"></a>Creación de un usuario de prueba de Perception United States (no UltiPro)

En esta sección, creará un usuario llamado Britta Simon en Perception United States (no UltiPro). Trabaje con el [equipo de soporte técnico de Perception United States (no UltiPro)](https://www.ultimatesoftware.com/Contact/ContactUs) para agregar los usuarios en la plataforma de Perception United States (no UltiPro).

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Perception United States (no UltiPro).

![Asignación de rol de usuario][200] 

**Para asignar a Britta Simon a Perception United States (no UltiPro), realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, navegue a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

1. En la lista de aplicaciones, seleccione **Perception United States (no UltiPro)**.

    ![Vínculo a Perception United States (no UltiPro) en la lista de aplicaciones](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_app.png)  

1. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

1. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

1. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

1. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de Perception United States (no UltiPro) en el panel de acceso, debería iniciar sesión automáticamente en la aplicación Perception United States (no UltiPro).
Para más información sobre el Panel de acceso, consulte la [introducción al Panel de acceso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/perceptionunitedstates-tutorial/tutorial_general_01.png
[2]: ./media/perceptionunitedstates-tutorial/tutorial_general_02.png
[3]: ./media/perceptionunitedstates-tutorial/tutorial_general_03.png
[4]: ./media/perceptionunitedstates-tutorial/tutorial_general_04.png

[100]: ./media/perceptionunitedstates-tutorial/tutorial_general_100.png

[200]: ./media/perceptionunitedstates-tutorial/tutorial_general_200.png
[201]: ./media/perceptionunitedstates-tutorial/tutorial_general_201.png
[202]: ./media/perceptionunitedstates-tutorial/tutorial_general_202.png
[203]: ./media/perceptionunitedstates-tutorial/tutorial_general_203.png

