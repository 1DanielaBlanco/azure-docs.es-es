---
title: 'Tutorial: Integración de Azure Active Directory con KnowBe4 Security Awareness Training | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y KnowBe4 Security Awareness Training.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: b80d2212-cc5f-4adb-836c-570640810c39
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2017
ms.author: jeedes
ms.openlocfilehash: c27af4e7bc88f24b76310336859740d8795f7cbe
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/02/2018
ms.locfileid: "39449280"
---
# <a name="tutorial-azure-active-directory-integration-with-knowbe4-security-awareness-training"></a>Tutorial: Integración de Azure Active Directory con KnowBe4 Security Awareness Training

En este tutorial, aprenderá a integrar KnowBe4 Security Awareness Training con Azure Active Directory (Azure AD).

La integración de KnowBe4 Security Awareness Training con Azure AD le proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a KnowBe4 Security Awareness Training.
- Puede habilitar a los usuarios para que inicien sesión automáticamente en KnowBe4 Security Awareness Training (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con KnowBe4 Security Awareness Training, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción de KnowBe4 Security Awareness Training con el inicio de sesión único habilitado

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. El escenario descrito en este tutorial consta de dos bloques de creación principales:

1. Incorporación de KnowBe4 Security Awareness Training desde la galería
1. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-knowbe4-security-awareness-training-from-the-gallery"></a>Incorporación de KnowBe4 Security Awareness Training desde la galería
Para configurar la integración de KnowBe4 Security Awareness Training en Azure AD, es preciso agregar KnowBe4 Security Awareness Training desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar KnowBe4 Security Awareness Training desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

1. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
1. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

1. En el cuadro de búsqueda, escriba  **KnowBe4 Security Awareness Training**, seleccione  **KnowBe4 Security Awareness Training** desde el panel de resultados, a continuación, haga clic en el botón **Agregar** para agregar el aplicación.

    ![KnowBe4 Security Awareness Training en la lista de resultados](./media/knowbe4-tutorial/tutorial_knowbe4_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, configurará y probará el inicio de sesión único de Azure AD con KnowBe4 Security Awareness Training con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de KnowBe4 Security Awareness Training para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de KnowBe4 Security Awareness Training.

Para establecer la relación de vínculo, en KnowBe4 Security Awareness Training, asigne el valor de **nombre de usuario** de Azure AD como valor de **Nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con KnowBe4 Security Awareness Training, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para que los usuarios puedan usar esta característica.
1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**, para probar el inicio de sesión único de Azure AD con Britta Simon.
1. **[Creación de un usuario de prueba de KnowBe4 Security Awareness Training](#create-a-knowbe4-security-awareness-training-test-user)**: para tener un homólogo de Britta Simon en KnowBe4 Security Awareness Training que esté vinculado a la representación del usuario en Azure AD.
1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**, para permitir que Britta Simon use el inicio de sesión único de Azure AD.
1. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y configurará el inicio de sesión único en la aplicación KnowBe4 Security Awareness Training.

**Para configurar el inicio de sesión único de Azure AD con KnowBe4 Security Awareness Training, realice los pasos siguientes:**

1. En Azure Portal, en la página de integración de la aplicación **KnowBe4 Security Awareness Training**, haga clic en **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

1. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Cuadro de diálogo Inicio de sesión único](./media/knowbe4-tutorial/tutorial_knowbe4_samlbase.png)

1. En la sección **Dominio y direcciones URL de KnowBe4 Security Awareness Training**, lleve a cabo los pasos siguientes:

    ![Información acerca del inicio de sesión único de dominio y direcciones URL de KnowBe4 Security Awareness Training](./media/knowbe4-tutorial/tutorial_knowbe4_url.png)

    a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<companyname>.KnowBe4.com/auth/saml/<instancename>`.

    > [!NOTE] 
    > El valor de la dirección URL de inicio de sesión no es real. Actualícelo con la dirección URL de inicio de sesión real. Póngase en contacto con el [equipo de soporte técnico de KnowBe4 Security Awareness Training](mailto:support@KnowBe4.com) para obtener dicho valor. 

    b. En el cuadro de texto **Identificador**, escriba el valor de cadena: `KnowBe4`

    > [!NOTE]
    >Distingue mayúsculas de minúsculas

1. En la sección **Certificado de firma de SAML**, haga clic en **Certificado (sin procesar)** y, a continuación, guarde el archivo de certificado en el equipo.

    ![Vínculo de descarga del certificado](./media/knowbe4-tutorial/tutorial_knowbe4_certificate.png) 

1. Haga clic en el botón **Guardar** .

    ![Botón Configurar inicio de sesión único](./media/knowbe4-tutorial/tutorial_general_400.png)

1. En la sección **Configuración de KnowBe4 Security Awareness Training**, haga clic en **Configurar KnowBe4 Security Awareness Training** para abrir la ventana **Configurar inicio de sesión**. Copie la **URL del servicio de inicio de sesión único de SAML, el identificador de entidad de SAML y la dirección URL de cierre de sesión** de la sección **Referencia rápida**.

    ![Configuración de KnowBe4 Security Awareness Training](./media/knowbe4-tutorial/tutorial_knowbe4_configure.png) 

1. Para configurar el inicio de sesión único en **KnowBe4 Security Awareness Training**, es preciso enviar los valores descargados de **Certificado (sin procesar)**, **Sign-Out URL (Dirección URL de cierre de sesión), SAML Entity ID (Identificador de entidad de SAML) y SAML Single Sign-On Service URL (Dirección URL del servicio de inicio de sesión único de SAML)** al [equipo de soporte técnico de KnowBe4 Security Awareness Training ](mailto:support@KnowBe4.com).

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más sobre la característica de documentación insertada aquí: [Vista previa: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

   ![Creación de un usuario de prueba de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel izquierdo de Azure Portal, haga clic en el botón **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/knowbe4-tutorial/create_aaduser_01.png)

1. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y, luego, haga clic en **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/knowbe4-tutorial/create_aaduser_02.png)

1. En la parte superior del cuadro de diálogo **Todos los usuarios**, haga clic en **Agregar** para abrir el cuadro de diálogo **Agregar**.

    ![Botón Agregar](./media/knowbe4-tutorial/create_aaduser_03.png)

1. En el cuadro de diálogo **Usuario** , realice los pasos siguientes:

    ![Cuadro de diálogo Usuario](./media/knowbe4-tutorial/create_aaduser_04.png)

    a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    d. Haga clic en **Create**(Crear).
 
### <a name="create-a-knowbe4-security-awareness-training-test-user"></a>Creación de un usuario de prueba de KnowBe4 Security Awareness Training

El objetivo de esta sección es crear un usuario llamado Britta Simon en KnowBe4 Security Awareness Training. KnowBe4 Security Awareness Training admite el aprovisionamiento Just-In-Time, que está habilitado de forma predeterminada.

No hay ningún elemento de acción para usted en esta sección. Al intentar acceder a KnowBe4 Security Awareness Training, si aún no existe, se crea un usuario. 

>[!NOTE]
>Si necesita crear un usuario manualmente, es preciso que se ponga en contacto con el [equipo de soporte técnico de KnowBe4 Security Awareness Training](mailto:support@KnowBe4.com).

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, concederá acceso a Britta Simon a nowBe4 Security Awareness Training para que use el inicio de sesión único de Azure.

![Asignación de rol de usuario][200] 

**Para asignar a Britta Simon a KnowBe4 Security Awareness Training, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, navegue a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

1. En la lista de aplicaciones, seleccione **KnowBe4 Security Awareness Training**.

    ![KnowBe4 Security Awareness Training en la lista de aplicaciones](./media/knowbe4-tutorial/tutorial_knowbe4_app.png)  

1. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

1. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

1. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

1. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

El objetivo de esta sección es probar la configuración del inicio de sesión único de Azure AD mediante el panel de acceso.
  
Al hacer clic en el icono de KnowBe4 Security Awareness Training en el panel de acceso, debería iniciar sesión automáticamente en la aplicación KnowBe4 Security Awareness Training. 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/knowbe4-tutorial/tutorial_general_01.png
[2]: ./media/knowbe4-tutorial/tutorial_general_02.png
[3]: ./media/knowbe4-tutorial/tutorial_general_03.png
[4]: ./media/knowbe4-tutorial/tutorial_general_04.png

[100]: ./media/knowbe4-tutorial/tutorial_general_100.png

[200]: ./media/knowbe4-tutorial/tutorial_general_200.png
[201]: ./media/knowbe4-tutorial/tutorial_general_201.png
[202]: ./media/knowbe4-tutorial/tutorial_general_202.png
[203]: ./media/knowbe4-tutorial/tutorial_general_203.png

