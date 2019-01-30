---
title: 'Tutorial: Integración de Azure Active Directory con IMPAC Risk Manager | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory e IMPAC Risk Manager.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 4d77390e-898c-4258-a562-a1181dfe2880
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: jeedes
ms.openlocfilehash: ca0ea482b1cfb2f7af962ae1b7537f79bb60a62b
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/23/2019
ms.locfileid: "54823154"
---
# <a name="tutorial-azure-active-directory-integration-with-impac-risk-manager"></a>Tutorial: Integración de Azure Active Directory con IMPAC Risk Manager

En este tutorial, aprenderá a integrar IMPAC Risk Manager con Azure Active Directory (Azure AD).

La integración de IMPAC Risk Manager con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a IMPAC Risk Manager.
- Puede habilitar que los usuarios inicien sesión automáticamente en IMPAC Risk Manager (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con IMPAC Risk Manager, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en IMPAC Risk Manager

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. El escenario descrito en este tutorial consta de dos bloques de creación principales:

1. Adición de IMPAC Risk Manager desde la galería
1. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-impac-risk-manager-from-the-gallery"></a>Adición de IMPAC Risk Manager desde la galería
Para configurar la integración de IMPAC Risk Manager en Azure AD, será preciso que agregue IMPAC Risk Manager desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar IMPAC Risk Manager desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

1. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
1. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

1. En el cuadro de búsqueda, escriba **IMPAC Risk Manager**, seleccione **IMPAC Risk Manager** en el panel de resultados y, luego, haga clic en el botón **Agregar** para agregar la aplicación.

    ![IMPAC Risk Manager en la lista de resultados](./media/impacriskmanager-tutorial/tutorial_impacriskmanager_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con IMPAC Risk Manager utilizando un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de IMPAC Risk Manager para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de IMPAC Risk Manager.

Para establecer la relación de vínculo, en IMPAC Risk Manager, asigne el valor de **nombre de usuario** de Azure AD como valor de **Nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con IMPAC Risk Manager, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para que los usuarios puedan usar esta característica.
1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**, para probar el inicio de sesión único de Azure AD con Britta Simon.
1. **[Creación de un usuario de prueba en IMPAC Risk Manager](#create-a-impac-risk-manager-test-user)**: para tener un homólogo de Britta Simon en IMPAC Risk Manager que esté vinculado a su representación en Azure AD.
1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**, para permitir que Britta Simon use el inicio de sesión único de Azure AD.
1. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y lo configurará en la aplicación IMPAC Risk Manager.

**Para configurar el inicio de sesión único de Azure AD con IMPAC Risk Manager, siga estos pasos:**

1. En Azure Portal, en la página de integración de la aplicación **IMPAC Risk Manager**, haga clic en **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

1. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Cuadro de diálogo Inicio de sesión único](./media/impacriskmanager-tutorial/tutorial_impacriskmanager_samlbase.png)

1. En la sección **Dominio y direcciones URL de IMPAC Risk Manager**, realice los siguientes pasos si desea configurar la aplicación en el modo iniciado por IDP:

    ![Información de dominio y direcciones URL de inicio de sesión único de IMPAC Risk Manager](./media/impacriskmanager-tutorial/tutorial_impacriskmanager_url_new.png)

     a. En el cuadro de texto **Identificador**, escriba un valor proporcionado por IMPAC.

    b. En el cuadro de texto **URL de respuesta** , escriba una dirección URL con el siguiente patrón:
    | Entorno | Patrón de dirección URL |
    | ---------------|--------------- |    
    | Para producción |`https://www.riskmanager.co.nz/DotNet/SSOv2/AssertionConsumerService.aspx?client=<ClientSuffix>`|
    | Para ensayo y entrenamiento  |`https://staging.riskmanager.co.nz/DotNet/SSOv2/AssertionConsumerService.aspx?client=<ClientSuffix>`|
    | Para desarrollo  |`https://dev.riskmanager.co.nz/DotNet/SSOv2/AssertionConsumerService.aspx?client=<ClientSuffix>`|
    | Para control de calidad |`https://QA.riskmanager.co.nz/DotNet/SSOv2/AssertionConsumerService.aspx?client=<ClientSuffix>`|
    | Para pruebas |`https://test.riskmanager.co.nz/DotNet/SSOv2/AssertionConsumerService.aspx?client=<ClientSuffix>`|

1. Active **Mostrar configuración avanzada de URL** y siga estos pasos si desea configurar la aplicación en el modo iniciado por **SP**:

    ![Información de dominio y direcciones URL de inicio de sesión único de IMPAC Risk Manager](./media/impacriskmanager-tutorial/tutorial_impacriskmanager_url1_new.png)

    En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón:
    | Entorno | Patrón de dirección URL |
    | ---------------|--------------- |    
    | Para producción |`https://www.riskmanager.co.nz/SSOv2/<ClientSuffix>`|
    | Para ensayo y entrenamiento  |`https://staging.riskmanager.co.nz/SSOv2/<ClientSuffix>`|
    | Para desarrollo  |`https://dev.riskmanager.co.nz/SSOv2/<ClientSuffix>`|
    | Para control de calidad |`https://QA.riskmanager.co.nz/SSOv2/<ClientSuffix>`|
    | Para pruebas |`https://test.riskmanager.co.nz/SSOv2/<ClientSuffix>`|

    > [!NOTE] 
    > Estos valores no son reales. Actualice estos valores con los valores reales de Identificador, URL de respuesta y URL de inicio de sesión. Póngase en contacto con el [equipo de soporte de cliente de IMPAC Risk Manager](mailto:rmsupport@Impac.co.nz) para obtener estos valores.

1. En la sección **Certificado de firma de SAML**, haga clic en **Certificado (Base64)** y, luego, guarde el archivo de certificado en el equipo.

    ![Vínculo de descarga del certificado](./media/impacriskmanager-tutorial/tutorial_impacriskmanager_certificate.png) 

1. Haga clic en el botón **Guardar** .

    ![Botón Configurar inicio de sesión único](./media/impacriskmanager-tutorial/tutorial_general_400.png)
    
1. En la sección **Configuración de IMPAC Risk Manager**, haga clic en **Configurar IMPAC Risk Manager** para abrir la ventana **Configurar inicio de sesión**. Copie la **dirección URL del servicio de inicio de sesión único de SAML, el identificador de entidad de SAML** y la **dirección URL de cierre de sesión** de la sección **Referencia rápida**.

    ![Configurar inicio de sesión único](./media/impacriskmanager-tutorial/tutorial_impacriskmanager_configure.png)

1. Para configurar el inicio de sesión único en **IMPAC Risk Manager**, es preciso enviar el **Certificado (Base64)** descargado, la **dirección URL de cierre de sesión, el identificador de identidad de SAML** y la **dirección URL del servicio de inicio de sesión único de SAML** al [equipo de soporte técnico de IMPAC Risk Manager](mailto:rmsupport@Impac.co.nz). Dicho equipo lo configura para establecer la conexión de SSO de SAML correctamente en ambos lados.

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más aquí sobre la característica de documentación insertada: [Documentación insertada de Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

   ![Creación de un usuario de prueba de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel izquierdo de Azure Portal, haga clic en el botón **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/impacriskmanager-tutorial/create_aaduser_01.png)

1. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y, luego, haga clic en **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/impacriskmanager-tutorial/create_aaduser_02.png)

1. En la parte superior del cuadro de diálogo **Todos los usuarios**, haga clic en **Agregar** para abrir el cuadro de diálogo **Agregar**.

    ![Botón Agregar](./media/impacriskmanager-tutorial/create_aaduser_03.png)

1. En el cuadro de diálogo **Usuario** , realice los pasos siguientes:

    ![Cuadro de diálogo Usuario](./media/impacriskmanager-tutorial/create_aaduser_04.png)

     a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    d. Haga clic en **Create**(Crear).
 
### <a name="create-a-impac-risk-manager-test-user"></a>Creación de un usuario de prueba de IMPAC Risk Manager

En esta sección, se crea un usuario denominado Britta Simon en IMPAC Risk Manager. Trabaje con el  [equipo de soporte técnico de IMPAC Risk Manager](mailto:rmsupport@Impac.co.nz) para agregar los usuarios en la plataforma de IMPAC Risk Manager. Los usuarios se tienen que crear y activar antes de usar el inicio de sesión único. 

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a IMPAC Risk Manager.

![Asignación de rol de usuario][200] 

**Para asignar Britta Simon a IMPAC Risk Manager, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, navegue a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

1. En la lista de aplicaciones, seleccione **IMPAC Risk Manager**.

    ![Vínculo a IMPAC Risk Manager en la lista de aplicaciones](./media/impacriskmanager-tutorial/tutorial_impacriskmanager_app.png)  

1. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

1. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

1. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

1. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de IMPAC Risk Manager en el panel de acceso, debería iniciar sesión automáticamente en la aplicación IMPAC Risk Manager.
Para más información sobre el Panel de acceso, consulte la [introducción al Panel de acceso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/impacriskmanager-tutorial/tutorial_general_01.png
[2]: ./media/impacriskmanager-tutorial/tutorial_general_02.png
[3]: ./media/impacriskmanager-tutorial/tutorial_general_03.png
[4]: ./media/impacriskmanager-tutorial/tutorial_general_04.png

[100]: ./media/impacriskmanager-tutorial/tutorial_general_100.png

[200]: ./media/impacriskmanager-tutorial/tutorial_general_200.png
[201]: ./media/impacriskmanager-tutorial/tutorial_general_201.png
[202]: ./media/impacriskmanager-tutorial/tutorial_general_202.png
[203]: ./media/impacriskmanager-tutorial/tutorial_general_203.png

