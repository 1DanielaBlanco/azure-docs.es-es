---
title: 'Tutorial: Integración de Azure Active Directory con Mercell | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Mercell.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: bb94c288-2ed4-4683-acde-62474292df29
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 04efbeddebccb4b0c23b499d24fa6ee1d352b84a
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/13/2019
ms.locfileid: "56167345"
---
# <a name="tutorial-azure-active-directory-integration-with-mercell"></a>Tutorial: Integración de Azure Active Directory con Mercell

En este tutorial, aprenderá a integrar Mercell con Azure Active Directory (Azure AD).

La integración de Mercell con Azure AD le proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Mercell.
- Puede permitir que los usuarios inicien sesión automáticamente en Mercell (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Mercell, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en Mercell

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. El escenario descrito en este tutorial consta de dos bloques de creación principales:

1. Adición de Mercell desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-mercell-from-the-gallery"></a>Adición de Mercell desde la galería
Para configurar la integración de Mercell en Azure AD, deberá agregar Mercell desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Mercell desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **Mercell**, seleccione **Mercell** en el panel de resultados y, luego, haga clic en el botón **Agregar** para agregar la aplicación.

    ![Mercell en la lista de resultados](./media/mercell-tutorial/tutorial_mercell_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, configurará y probará el inicio de sesión único de Azure AD con Mercell con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Mercell para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Mercell.

Para configurar y probar el inicio de sesión único de Azure AD con Mercell, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para que los usuarios puedan usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**, para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de Mercell](#create-a-mercell-test-user)**: para tener un homólogo de Britta Simon en Mercell que esté vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**, para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y lo configurará en la aplicación Mercell.

**Para configurar el inicio de sesión único de Azure AD con Mercell, realice los pasos siguientes:**

1. En Azure Portal, en la página de integración de la aplicación **Mercell**, haga clic en **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.

    ![Cuadro de diálogo Inicio de sesión único](./media/mercell-tutorial/tutorial_mercell_samlbase.png)

3. En la sección **Dominio y direcciones URL de Mercell**, lleve a cabo los pasos siguientes:

    ![Información acerca del inicio de sesión único de dominio y direcciones URL de Mercell](./media/mercell-tutorial/tutorial_mercell_url.png)

    En el cuadro de texto **Identificador**, escriba la dirección URL: `https://my.mercell.com/`

4. En la sección **Certificado de firma de SAML** , haga clic en el botón Copiar para copiar la **dirección URL de metadatos de federación de la aplicación** y péguela en el Bloc de notas.
    
    ![Configurar inicio de sesión único](./media/mercell-tutorial/tutorial_metadataurl.png)
     
5. Haga clic en el botón **Guardar** .

    ![Botón Configurar inicio de sesión único](./media/mercell-tutorial/tutorial_general_400.png)

6. Para configurar el inicio de sesión único en **Mercell**, tiene que enviar la **dirección URL de metadatos de federación de la aplicación** generada al [equipo de soporte técnico de Mercell](mailto:webmaster@mercell.com). Dicho equipo lo configura para establecer la conexión de SSO de SAML correctamente en ambos lados.

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

   ![Creación de un usuario de prueba de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel izquierdo de Azure Portal, haga clic en el botón **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/mercell-tutorial/create_aaduser_01.png)

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y, luego, haga clic en **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/mercell-tutorial/create_aaduser_02.png)

3. En la parte superior del cuadro de diálogo **Todos los usuarios**, haga clic en **Agregar** para abrir el cuadro de diálogo **Agregar**.

    ![Botón Agregar](./media/mercell-tutorial/create_aaduser_03.png)

4. En el cuadro de diálogo **Usuario** , realice los pasos siguientes:

    ![Cuadro de diálogo Usuario](./media/mercell-tutorial/create_aaduser_04.png)

     a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    d. Haga clic en **Create**(Crear).
 
### <a name="create-a-mercell-test-user"></a>Crear un usuario de prueba de Mercell

El objetivo de esta sección es crear un usuario llamado Britta Simon en Mercell. Mercell admite el aprovisionamiento Just-In-Time, que está habilitado de forma predeterminada. No hay ningún elemento de acción para usted en esta sección. Al intentar obtener acceso a Mercell, se crea un nuevo usuario, en caso de que no exista.
>[!Note]
>Si necesita crear manualmente un usuario, póngase en contacto con el  [equipo de soporte técnico de Mercell](mailto:webmaster@mercell.com).

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Mercell.

![Asignación de rol de usuario][200] 

**Para asignar el usuario Britta Simon a Mercell, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, navegue a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Mercell**.

    ![Vínculo a Mercell en la lista de aplicaciones](./media/mercell-tutorial/tutorial_mercell_app.png)  

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de Mercell en el panel de acceso, debería iniciar sesión automáticamente en su aplicación Mercell.
Para más información sobre el Panel de acceso, consulte la [introducción al Panel de acceso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/mercell-tutorial/tutorial_general_01.png
[2]: ./media/mercell-tutorial/tutorial_general_02.png
[3]: ./media/mercell-tutorial/tutorial_general_03.png
[4]: ./media/mercell-tutorial/tutorial_general_04.png

[100]: ./media/mercell-tutorial/tutorial_general_100.png

[200]: ./media/mercell-tutorial/tutorial_general_200.png
[201]: ./media/mercell-tutorial/tutorial_general_201.png
[202]: ./media/mercell-tutorial/tutorial_general_202.png
[203]: ./media/mercell-tutorial/tutorial_general_203.png

