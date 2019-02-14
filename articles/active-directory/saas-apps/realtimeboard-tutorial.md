---
title: 'Tutorial: Integración de Azure Active Directory con RealtimeBoard | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y RealtimeBoard.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: a37fc1c0-4bae-4173-989b-00de53a0076f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0cb658275ad65aaca0a4873c2a80b472b954e8db
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/13/2019
ms.locfileid: "56192700"
---
# <a name="tutorial-azure-active-directory-integration-with-realtimeboard"></a>Tutorial: Integración de Azure Active Directory con RealtimeBoard

En este tutorial, aprenderá a integrar RealtimeBoard con Azure Active Directory (Azure AD).

La integración de RealtimeBoard con Azure AD le proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a RealtimeBoard.
- Puede permitir que los usuarios inicien sesión automáticamente en RealtimeBoard (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con RealtimeBoard, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en RealtimeBoard

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. El escenario descrito en este tutorial consta de dos bloques de creación principales:

1. Agregación de RealtimeBoard desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-realtimeboard-from-the-gallery"></a>Agregación de RealtimeBoard desde la galería
Para configurar la integración de RealtimeBoard en Azure AD, será preciso agregar RealtimeBoard desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar RealtimeBoard desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **RealtimeBoard**, seleccione **RealtimeBoard** en el panel de resultados y, luego, haga clic en el botón **Agregar** para agregar la aplicación.

    ![RealtimeBoard en la lista de resultados](./media/realtimeboard-tutorial/tutorial_realtimeboard_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, configurará y probará el inicio de sesión único de Azure AD con RealtimeBoard con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de RealtimeBoard para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de RealtimeBoard.

Para establecer la relación de vínculo, en RealtimeBoard, asigne el valor de **nombre de usuario** de Azure AD como valor de **Nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con RealtimeBoard, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para que los usuarios puedan usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**, para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de RealtimeBoard](#create-a-realtimeboard-test-user)**: para tener un homólogo de Britta Simon en RealtimeBoard que esté vinculado a la representación del usuario en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**, para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y lo configurará en la aplicación RealtimeBoard.

**Para configurar el inicio de sesión único de Azure AD con RealtimeBoard, realice los pasos siguientes:**

1. En Azure Portal, en la página de integración de la aplicación **RealtimeBoard**, haga clic en **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Cuadro de diálogo Inicio de sesión único](./media/realtimeboard-tutorial/tutorial_realtimeboard_samlbase.png)

3. Vaya a la sección **Dominio y direcciones URL de RealtimeBoard**, si quiere configurar la aplicación en modo iniciado por **IDP**:

    ![Información de dominio y direcciones URL de inicio de sesión único de RealtimeBoard](./media/realtimeboard-tutorial/tutorial_realtimeboard_url.png)

    En el cuadro de texto **Identificador**, escriba una dirección URL como: `https://realtimeboard.com/`

4. Active **Mostrar configuración avanzada de URL**, si desea volver a configurar la aplicación en modo iniciado por **SP**:

    ![Configurar inicio de sesión único](./media/realtimeboard-tutorial/tutorial_realtimeboard_url2.png)

    En el cuadro de texto **URL de inicio de sesión**, escriba una URL como: `https://realtimeboard.com/sso/saml`

5. En la sección **Certificado de firma de SAML**, haga clic en **XML de metadatos** y luego guarde el archivo de metadatos en el equipo.

    ![Vínculo de descarga del certificado](./media/realtimeboard-tutorial/tutorial_realtimeboard_certificate.png) 

6. Haga clic en el botón **Guardar** .

    ![Botón Configurar inicio de sesión único](./media/realtimeboard-tutorial/tutorial_general_400.png)

7. Para configurar el inicio de sesión único en **RealtimeBoard**, siga las [instrucciones de RealtimeBoard](https://help.realtimeboard.com/support/solutions/articles/11000023465-saml-based-single-sign-on-) y use los datos del **XML de metadatos** descargado.

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más aquí sobre la característica de documentación insertada: [Documentación insertada de Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

   ![Creación de un usuario de prueba de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel izquierdo de Azure Portal, haga clic en el botón **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/realtimeboard-tutorial/create_aaduser_01.png)

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y, luego, haga clic en **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/realtimeboard-tutorial/create_aaduser_02.png)

3. En la parte superior del cuadro de diálogo **Todos los usuarios**, haga clic en **Agregar** para abrir el cuadro de diálogo **Agregar**.

    ![Botón Agregar](./media/realtimeboard-tutorial/create_aaduser_03.png)

4. En el cuadro de diálogo **Usuario** , realice los pasos siguientes:

    ![Cuadro de diálogo Usuario](./media/realtimeboard-tutorial/create_aaduser_04.png)

     a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    d. Haga clic en **Create**(Crear).
 
### <a name="create-a-realtimeboard-test-user"></a>Creación de un usuario de prueba de RealtimeBoard

El objetivo de esta sección es crear un usuario de prueba llamado Britta Simon en RealtimeBoard. RealtimeBoard admite el aprovisionamiento just-in-time, que está habilitado de forma predeterminada.

No hay ningún elemento de acción para usted en esta sección. Si el usuario no existe en RealtimeBoard, se crea uno nuevo cuando se intenta acceder a RealtimeBoard.

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a RealtimeBoard.

![Asignación de rol de usuario][200] 

**Para asignar a Britta Simon a RealtimeBoard, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, navegue a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **RealtimeBoard**.

    ![Vínculo a RealtimeBoard en la lista de aplicaciones](./media/realtimeboard-tutorial/tutorial_realtimeboard_app.png)  

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de RealtimeBoard en el Panel de acceso, debería iniciar sesión automáticamente en la aplicación RealtimeBoard.
Para más información sobre el Panel de acceso, consulte la [introducción al Panel de acceso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/realtimeboard-tutorial/tutorial_general_01.png
[2]: ./media/realtimeboard-tutorial/tutorial_general_02.png
[3]: ./media/realtimeboard-tutorial/tutorial_general_03.png
[4]: ./media/realtimeboard-tutorial/tutorial_general_04.png

[100]: ./media/realtimeboard-tutorial/tutorial_general_100.png

[200]: ./media/realtimeboard-tutorial/tutorial_general_200.png
[201]: ./media/realtimeboard-tutorial/tutorial_general_201.png
[202]: ./media/realtimeboard-tutorial/tutorial_general_202.png
[203]: ./media/realtimeboard-tutorial/tutorial_general_203.png

