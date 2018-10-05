---
title: 'Tutorial: Integración de Azure Active Directory con Freshdesk | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y FreshDesk.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: c2a3e5aa-7b5a-4fe4-9285-45dbe6e8efcc
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2018
ms.author: jeedes
ms.reviewer: jeedes
ms.openlocfilehash: ce302db74f831e67b576e4c0001f21473fd7f2e0
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2018
ms.locfileid: "47037531"
---
# <a name="tutorial-azure-active-directory-integration-with-freshdesk"></a>Tutorial: Integración de Azure Active Directory con Freshdesk

En este tutorial, obtendrá información sobre cómo integrar FreshDesk con Azure Active Directory (Azure AD).

La integración de FreshDesk con Azure AD proporciona las siguientes ventajas:

- En Azure AD se puede controlar quién tiene acceso a FreshDesk.
- Puede permitir que los usuarios inicien sesión automáticamente en FreshDesk (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: el nuevo Azure Portal.

Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con FreshDesk, se necesitan los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en FreshDesk

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No debe usar el entorno de producción, a menos que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba.
El escenario descrito en este tutorial consta de dos bloques de creación principales:

1. Agregar FreshDesk desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-freshdesk-from-the-gallery"></a>Agregar FreshDesk desde la galería

Para configurar la integración de FreshDesk en Azure AD, deberá agregar FreshDesk desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar FreshDesk desde la galería, siga estos pasos:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![APLICACIONES][2]

3. Haga clic en el botón **Agregar** situado en la parte superior del cuadro de diálogo.

    ![APLICACIONES][3]

4. En el cuadro de búsqueda, escriba **FreshDesk**. En el panel de resultados, seleccione **FreshDesk** y, a continuación, haga clic en el botón **Agregar** para agregar la aplicación.

    ![Creación de un usuario de prueba de Azure AD](./media/freshdesk-tutorial/tutorial_freshdesk_addfromgallery.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD

En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con FreshDesk con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de FreshDesk para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de FreshDesk.

Esta relación de vínculo se establece mediante la asignación del valor de **Nombre de usuario** en Azure AD como el valor de **Dirección de correo electrónico** en FreshDesk.

Para configurar y probar el inicio de sesión único de Azure AD con FreshDesk, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-sign-on)** : para permitir a los usuarios usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba de FreshDesk](#creating-a-freshdesk-test-user)**: para tener un homólogo de Britta Simon en FreshDesk que esté vinculado a la representación de ella en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y configurará el inicio de sesión único en la aplicación FreshDesk.

**Para configurar el inicio de sesión único de Azure AD con FreshDesk, realice los pasos siguientes:**

1. En Azure Portal, en la página de integración de la aplicación **FreshDesk**, haga clic en **Inicio de sesión único**.

    ![Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo**, seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.

    ![Configurar inicio de sesión único](./media/freshdesk-tutorial/tutorial_freshdesk_samlbase.png)

3. En la sección **FreshDesk Domain and URLs** (Dominios y direcciones URL de FreshDesk), lleve a cabo los pasos siguientes:

    ![Configurar inicio de sesión único](./media/freshdesk-tutorial/tutorial_freshdesk_url.png)

    a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<tenant-name>.freshdesk.com` u otro valor que Freshdesk haya sugerido.

    > [!NOTE]
    > Tenga en cuenta que este no es el valor real. Tiene que actualizar este valor con la dirección URL de inicio de sesión real. Póngase en contacto con el [equipo de soporte técnico de FreshDesk](https://freshdesk.com/helpdesk-software?utm_source=Google-AdWords&utm_medium=Search-IND-Brand&utm_campaign=Search-IND-Brand&utm_term=freshdesk&device=c&gclid=COSH2_LH7NICFVUDvAodBPgBZg) para obtener este valor.

4. La aplicación espera las aserciones de SAML en un formato específico, que requiere que se agreguen asignaciones de atributos personalizados a la configuración de los atributos del token de SAML. La siguiente captura de pantalla le muestra un ejemplo de esto. El valor predeterminado de **Identificador de usuario** es **user.userprincipalname**, pero **FreshDesk** espera que este valor se asigne a la dirección de correo electrónico del usuario. Para ello, puede usar el atributo **user.mail** de la lista o usar el valor de atributo correspondiente en función de la configuración de su organización.

    ![Configurar inicio de sesión único](./media/freshdesk-tutorial/tutorial_attribute.png)

5. En la sección **Certificado de firma de SAML**, haga clic en **Certificado (Base64)** y, luego, guarde el archivo de certificado en el equipo.

    ![Configurar inicio de sesión único](./media/freshdesk-tutorial/tutorial_freshdesk_certificate.png)

    > [!NOTE]
    > Si tiene algún problema, consulte este [vínculo](https://support.freshdesk.com/support/discussions/topics/317543).

6. Haga clic en el botón **Guardar** .

    ![Configurar inicio de sesión único](./media/freshdesk-tutorial/tutorial_general_400.png)

7. Instale **OpenSSL** en su sistema, si aún no lo ha instalado.

8. Abra un **símbolo del sistema** y ejecute los comandos siguientes:

    a. Escriba el valor `openssl x509 -inform DER -in FreshDesk.cer -out certificate.crt` en el símbolo del sistema.

    > [!NOTE]
    > Aquí, **FreshDesk.cer** es el certificado que ha descargado de Azure Portal.

    b. Escriba el valor `openssl x509 -noout -fingerprint -sha256 -inform pem -in certificate.crt` en el símbolo del sistema. 
    
    > [!NOTE]
    > Aquí **certificate.crt** es el certificado de salida que se generó en el paso anterior.

    c. Copie el valor de **Huella digital** y péguelo en el Bloc de notas. Quite los signos de dos puntos de la huella digital para obtener el valor final de la huella digital.

9. En la sección **Configuración de FreshDesk**, haga clic en **Configurar FreshDesk** para abrir la ventana Configurar inicio de sesión. Copie la dirección URL del servicio de inicio de sesión único de SAML y la URL de cierre de sesión de la sección **Referencia rápida**.

    ![Configurar inicio de sesión único](./media/freshdesk-tutorial/tutorial_freshdesk_configure.png)

10. En otra ventana del explorador web, inicie sesión en el sitio de la compañía de Freshdesk como administrador.

11. Seleccione el **icono Configuración** y, en la sección **Seguridad**, siga estos pasos:

    ![Inicio de sesión único](./media/freshdesk-tutorial/IC776770.png "Inicio de sesión único")
  
    a. En **Inicio de sesión único (SSO)**, seleccione **Activado**.

    b. Seleccione **Inicio de sesión único de SAML**.

    c. En el cuadro de texto **URL de inicio de sesión SAML**, pegue el valor de **URL del servicio de inicio de sesión único de SAML** que ha copiado de Azure Portal.

    d. En el cuadro de texto **URL de cierre de sesión**, pegue el valor de **URL de cierre de sesión** que ha copiado de Azure Portal.

    e. En el cuadro de texto **Huella digital del certificado de seguridad**, pegue el valor de **Huella digital** que ha obtenido después de quitar los dos puntos.
  
    f. Haga clic en **Save**(Guardar).

### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

![Creación de un usuario de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo de **Azure Portal**, haga clic en el icono de **Azure Active Directory**.

    ![Creación de un usuario de prueba de Azure AD](./media/freshdesk-tutorial/create_aaduser_01.png) 

2. Vaya a **Usuarios y grupos** y haga clic en **Todos los usuarios** para mostrar la lista de usuarios.

    ![Creación de un usuario de prueba de Azure AD](./media/freshdesk-tutorial/create_aaduser_02.png) 

3. En la parte superior del diálogo, haga clic en **Agregar** para abrir el diálogo **Usuario**.

    ![Creación de un usuario de prueba de Azure AD](./media/freshdesk-tutorial/create_aaduser_03.png) 

4. En la página de diálogo **Usuario**, realice los siguientes pasos:

    ![Creación de un usuario de prueba de Azure AD](./media/freshdesk-tutorial/create_aaduser_04.png) 

    a. En el cuadro de texto **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la **dirección de correo electrónico** de Britta Simon.

    c. Seleccione **Mostrar contraseña** y anote el valor del cuadro **Contraseña**.

    d. Haga clic en **Create**(Crear).

### <a name="creating-a-freshdesk-test-user"></a>Creación de un usuario de prueba de FreshDesk

Para permitir que los usuarios de Azure AD inicien sesión en FreshDesk, deben aprovisionarse en FreshDesk.  
En el caso de FreshDesk, el aprovisionamiento es una tarea manual.

**Para aprovisionar cuentas de usuario, realice estos pasos:**

1. Inicie sesión en su inquilino de **Freshdesk** .

2. En el menú de la parte superior, haga clic en **Administrador**.

   ![Administración](./media/freshdesk-tutorial/IC776772.png "Administración")

3. En la pestaña **Configuración general**, haga clic en **Agentes**.
  
   ![Agentes](./media/freshdesk-tutorial/IC776773.png "Agentes")

4. Haga clic en **Nuevo agente**.

    ![Nuevo agente](./media/freshdesk-tutorial/IC776774.png "Nuevo agente")

5. En el cuadro de diálogo Agent Information (Información de agente), realice los pasos siguientes:

   ![Información sobre agentes](./media/freshdesk-tutorial/IC776775.png "Información sobre agentes")

   a. En el cuadro de texto **Email** (Correo electrónico), escriba la dirección de correo electrónico de la cuenta de Azure AD que quiera aprovisionar.

   b. En el cuadro de texto **Nombre completo** , escriba el nombre de la cuenta de Azure AD que quiera aprovisionar.

   c. En el cuadro de texto **Título** , escriba el título de la cuenta de Azure AD que quiera aprovisionar.

   d. Haga clic en **Save**(Guardar).

    >[!NOTE]
    >El titular de la cuenta de Azure AD recibirá un mensaje de correo electrónico que incluye un vínculo para confirmar la cuenta antes de que se active.
    >
    >[!NOTE]
    >Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de Freshdesk que proporcione Freshdesk para aprovisionar cuentas de usuario de AAD a FreshDesk.

### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a FreshDesk.

![Asignar usuario][200]

**Para asignar Britta Simon a FreshDesk, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, navegue a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201]

2. En la lista de aplicaciones, seleccione **FreshDesk**.

    ![Configurar inicio de sesión único](./media/freshdesk-tutorial/tutorial_freshdesk_app.png) 

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Asignar usuario][202]

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Asignar usuario][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.

### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de FreshDesk en el panel de acceso, debería obtener la página de inicio de sesión e iniciar sesión automáticamente en su aplicación FreshDesk.

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/freshdesk-tutorial/tutorial_general_01.png
[2]: ./media/freshdesk-tutorial/tutorial_general_02.png
[3]: ./media/freshdesk-tutorial/tutorial_general_03.png
[4]: ./media/freshdesk-tutorial/tutorial_general_04.png

[100]: ./media/freshdesk-tutorial/tutorial_general_100.png

[200]: ./media/freshdesk-tutorial/tutorial_general_200.png
[201]: ./media/freshdesk-tutorial/tutorial_general_201.png
[202]: ./media/freshdesk-tutorial/tutorial_general_202.png
[203]: ./media/freshdesk-tutorial/tutorial_general_203.png