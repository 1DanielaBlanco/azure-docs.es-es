---
title: 'Tutorial: Integración de Azure Active Directory con PlanMyLeave | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y PlanMyLeave.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: b0d31cbe-7ae2-488b-9cf3-4927391fa744
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/16/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d578367ef3b1f55715841bf51a3baeef28f198e9
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/13/2019
ms.locfileid: "56207048"
---
# <a name="tutorial-azure-active-directory-integration-with-planmyleave"></a>Tutorial: Integración de Azure Active Directory con PlanMyLeave

En este tutorial, obtendrá información sobre cómo integrar PlanMyLeave con Azure Active Directory (Azure AD).

Integrar PlanMyLeave con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a PlanMyLeave.
- Puede permitir que los usuarios inicien sesión automáticamente en PlanMyLeave (inicio de sesión único) con las cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con PlanMyLeave, se necesitan los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en PlanMyLeave

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. El escenario descrito en este tutorial consta de dos bloques de creación principales:

1. Agregar PlanMyLeave desde la galería
1. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-planmyleave-from-the-gallery"></a>Agregar PlanMyLeave desde la galería
Para configurar la integración de PlanMyLeave en Azure AD, deberá agregar PlanMyLeave desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar PlanMyLeave desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

1. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
1. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

1. En el cuadro de búsqueda, escriba **PlanMyLeave**, seleccione **PlanMyLeave** en el panel de resultados y, luego, haga clic en el botón **Agregar** para agregar la aplicación.

    ![PlanMyLeave en la lista de resultados](./media/planmyleave-tutorial/tutorial_planmyleave_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con PlanMyLeave con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de PlanMyLeave para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de PlanMyLeave.

Para establecer la relación de vínculo, en PlanMyLeave, asigne el valor de **nombre de usuario** de Azure AD como valor de **Nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con PlanMyLeave, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para que los usuarios puedan usar esta característica.
1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**, para probar el inicio de sesión único de Azure AD con Britta Simon.
1. **[Creación de un usuario de prueba de PlanMyLeave](#create-a-planmyleave-test-user)**: para tener un homólogo de Britta Simon en PlanMyLeave que esté vinculado a la representación del usuario en Azure AD.
1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**, para permitir que Britta Simon use el inicio de sesión único de Azure AD.
1. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y configurará el inicio de sesión único en la aplicación PlanMyLeave.

**Para configurar el inicio de sesión único de Azure AD con PlanMyLeave, realice los pasos siguientes:**

1. En Azure Portal, en la página de integración de la aplicación **PlanMyLeave**, haga clic en **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

1. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Cuadro de diálogo Inicio de sesión único](./media/planmyleave-tutorial/tutorial_planmyleave_samlbase.png)

1. En la sección **Dominio y direcciones URL de PlanMyLeave**, lleve a cabo los pasos siguientes:

    ![Información sobre dominio y direcciones URL de inicio de sesión único de PlanMyLeave](./media/planmyleave-tutorial/tutorial_planmyleave_url.png)

     a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<company-name>.planmyleave.com/Login.aspx`.

    b. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón: `https://<company-name>.planmyleave.com`

    > [!NOTE] 
    > Estos valores no son reales. Debe actualizarlos con la dirección URL y el identificador reales de inicio de sesión. Póngase en contacto con el [equipo de soporte técnico de PlanMyLeave](mailto:support@planmyleave.com) para obtener estos valores. 
 
1. En la sección **Certificado de firma de SAML**, haga clic en **XML de metadatos** y luego guarde el archivo de metadatos en el equipo.

    ![Vínculo de descarga del certificado](./media/planmyleave-tutorial/tutorial_planmyleave_certificate.png) 

1. Haga clic en el botón **Guardar** .

    ![Botón Configurar inicio de sesión único](./media/planmyleave-tutorial/tutorial_general_400.png)

1. En la sección **Configuración de PlanMyLeave**, haga clic en **Configurar PlanMyLeave** para abrir la ventana **Configurar inicio de sesión único**. Copie la **dirección URL de servicio de inicio de sesión único de SAML** de la sección **Referencia rápida**.

    ![Configuración de PlanMyLeave](./media/planmyleave-tutorial/tutorial_planmyleave_configure.png) 
1. En otra ventana del explorador web, inicie sesión en como administrador en el inquilino de PlanMyLeave.

1. Vaya a **Configuración del sistema**. Después, en la sección **Administración de seguridad**, haga clic en **Company SAML settings** (Configuración de SAML de la empresa).

    ![Configuración del inicio de sesión único en la aplicación](./media/planmyleave-tutorial/tutorial_planmyleave_002.png) 

1. En la sección **SAML Settings** (Configuración de SAML), haga clic en el icono del editor.

    ![Configuración del inicio de sesión único en la aplicación](./media/planmyleave-tutorial/tutorial_planmyleave_003.png)

1. En la sección **Update SAML Settings** (Actualizar configuración de SAML), siga estos pasos:

    ![Configuración del inicio de sesión único en la aplicación](./media/planmyleave-tutorial/tutorial_planmyleave_004.png)

     a.  En el cuadro de texto **Login URL** (Dirección URL de inicio de sesión), pegue la **dirección URL del servicio de inicio de sesión único de SAML** que copió desde Azure Portal.

    b.  Abra los metadatos descargados, copie el valor de **X509Certificate** y, a continuación, péguelo en el cuadro de texto **Certificate** (Certificado).

    c. Establezca "**Is Enable**" en "**Yes**".

    d. Haga clic en **Save**(Guardar). 

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más aquí sobre la característica de documentación insertada: [Documentación insertada de Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

   ![Creación de un usuario de prueba de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel izquierdo de Azure Portal, haga clic en el botón **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/planmyleave-tutorial/create_aaduser_01.png)

1. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y, luego, haga clic en **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/planmyleave-tutorial/create_aaduser_02.png)

1. En la parte superior del cuadro de diálogo **Todos los usuarios**, haga clic en **Agregar** para abrir el cuadro de diálogo **Agregar**.

    ![Botón Agregar](./media/planmyleave-tutorial/create_aaduser_03.png)

1. En el cuadro de diálogo **Usuario** , realice los pasos siguientes:

    ![Cuadro de diálogo Usuario](./media/planmyleave-tutorial/create_aaduser_04.png)

     a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    d. Haga clic en **Create**(Crear).
 
### <a name="create-a-planmyleave-test-user"></a>Creación de un usuario de prueba de PlanMyLeave

El objetivo de esta sección es crear un usuario llamado Britta Simon en PlanMyLeave. PlanMyLeave admite el aprovisionamiento Just-In-Time, que está habilitado de forma predeterminada.

No hay ningún elemento de acción para usted en esta sección. Durante un intento de acceder a PlanMyLeave se creará un nuevo usuario, en caso de que no exista.

> [!NOTE]
> Si necesita crear manualmente un usuario, es preciso que se ponga contacto con el [equipo de soporte técnico de PlanMyLeave](mailto:support@planmyleave.com).

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a PlanMyLeave.

![Asignación de rol de usuario][200] 

**Para asignar a Britta Simon a PlanMyLeave, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, navegue a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

1. En la lista de aplicaciones, seleccione **PlanMyLeave**.

    ![Vínculo a PlanMyLeave en la lista de aplicaciones](./media/planmyleave-tutorial/tutorial_planmyleave_app.png)  

1. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

1. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

1. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

1. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de PlanMyLeave en el panel de acceso, debería iniciar sesión automáticamente en la aplicación de PlanMyLeave.
Para más información sobre el Panel de acceso, consulte la [introducción al Panel de acceso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/planmyleave-tutorial/tutorial_general_01.png
[2]: ./media/planmyleave-tutorial/tutorial_general_02.png
[3]: ./media/planmyleave-tutorial/tutorial_general_03.png
[4]: ./media/planmyleave-tutorial/tutorial_general_04.png

[100]: ./media/planmyleave-tutorial/tutorial_general_100.png

[200]: ./media/planmyleave-tutorial/tutorial_general_200.png
[201]: ./media/planmyleave-tutorial/tutorial_general_201.png
[202]: ./media/planmyleave-tutorial/tutorial_general_202.png
[203]: ./media/planmyleave-tutorial/tutorial_general_203.png

