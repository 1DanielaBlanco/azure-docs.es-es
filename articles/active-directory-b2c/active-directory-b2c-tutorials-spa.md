---
title: 'Tutorial: Habilitación de la autenticación de una aplicación de una sola página con cuentas mediante Azure Active Directory B2C | Microsoft Docs'
description: Tutorial acerca de cómo usar Azure Active Directory B2C para proporcionar inicio de sesión de usuario a una aplicación de una sola página (JavaScript).
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.author: davidmu
ms.date: 3/02/2018
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.component: B2C
ms.openlocfilehash: 4953cb0db428de19268cdd90661f7818b06b6945
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/31/2018
ms.locfileid: "43343869"
---
# <a name="tutorial-enable-single-page-app-authentication-with-accounts-using-azure-active-directory-b2c"></a>Tutorial: Habilitación de la autenticación de una aplicación de una sola página con cuentas mediante Azure Active Directory B2C

En este tutorial se muestra cómo usar Azure Active Directory (Azure AD) B2C para registrar e iniciar sesión con usuarios en una aplicación de una sola página (SPA). Azure AD B2C permite que las aplicaciones puedan autenticarse en cuentas de las redes sociales, cuentas de empresa y cuentas de Azure Active Directory mediante protocolos estándar abiertos.

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Registrar una aplicación de ejemplo con una sola página en un directorio de Azure AD B2C.
> * Crear directivas para el registro, el inicio de sesión, la edición de un perfil y el restablecimiento de contraseña.
> * Configurar la aplicación de ejemplo para que use el directorio de Azure AD B2C.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Requisitos previos

* Cree su propio [directorio de Azure AD B2C](active-directory-b2c-get-started.md)
* Instalar [Visual Studio 2017](https://www.visualstudio.com/downloads/) con la carga de trabajo de **ASP.NET y desarrollo web**.
* [SDK de .NET Core 2.0.0](https://www.microsoft.com/net/core) o posterior
* Instalar [Node.js](https://nodejs.org/en/download/)

## <a name="register-single-page-app"></a>Registro de la aplicación de una sola página

Las aplicaciones deben [registrarse](../active-directory/develop/developer-glossary.md#application-registration) en el directorio para que puedan recibir [tokens de acceso](../active-directory/develop/developer-glossary.md#access-token) de Azure Active Directory. El registro de una aplicación crea un [identificador de la aplicación](../active-directory/develop/developer-glossary.md#application-id-client-id) en el directorio. 

Inicie sesión en [Azure Portal](https://portal.azure.com/) como administrador global del directorio de Azure AD B2C.

[!INCLUDE [active-directory-b2c-switch-b2c-tenant](../../includes/active-directory-b2c-switch-b2c-tenant.md)]

1. Seleccione **Azure AD B2C** en la lista de servicios de Azure Portal. 

2. En la configuración de B2C, haga clic en **Aplicaciones** y luego en **Agregar**. 

    Para registrar la aplicación web de ejemplo en el directorio, utilice la siguiente configuración:
    
    ![Incorporación de una nueva aplicación](media/active-directory-b2c-tutorials-spa/spa-registration.png)
    
    | Configuración      | Valor sugerido  | Descripción                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Nombre** | My sample single page app | Escriba un **Nombre** que describa la aplicación a los consumidores. | 
    | **Incluir aplicación web o API web** | SÍ | Seleccione **Sí** si desea una aplicación de una sola página. |
    | **Permitir flujo implícito** | SÍ | Seleccione **Sí**, ya que la aplicación utiliza el [inicio de sesión con OpenID Connect](active-directory-b2c-reference-oidc.md). |
    | **URL de respuesta** | `http://localhost:6420` | Las direcciones URL de respuesta son puntos de conexión en los que Azure AD B2C devolverá los tokens que su aplicación solicite. En este tutorial, el ejemplo se ejecuta localmente (localhost) y escucha en el puerto 6420. |
    | **Incluir cliente nativo** | Sin  | Como es una aplicación de una sola página, no un cliente nativo, seleccione No. |
    
3. Haga clic en **Crear** para registrar la aplicación.

Las aplicaciones registradas aparecen en la lista de aplicaciones del directorio de Azure AD B2C. Seleccione la aplicación de una sola página en la lista. Se muestra el panel de propiedades de la aplicación de una sola página registrada.

![Propiedades de aplicación de una sola página](./media/active-directory-b2c-tutorials-spa/b2c-spa-properties.png)

Tome nota del **Id. del cliente de aplicación**. El identificador identifica la aplicación de forma exclusiva y será necesario al configurar la aplicación más adelante en el tutorial.

## <a name="create-policies"></a>Creación de directivas

Una directiva de Azure AD B2C define los flujos de trabajo de usuario. Por ejemplo, el registro, el inicio de sesión, el cambio de contraseñas y la edición de perfiles son flujos de trabajo comunes.

### <a name="create-a-sign-up-or-sign-in-policy"></a>Creación de una directiva de registro o de inicio de sesión

Para registrar a usuarios y que estos tengan acceso a la aplicación web, cree una **directiva de registro o de inicio de sesión**.

1. En la página del portal de Azure AD B2C, seleccione **Directivas de inicio de sesión o de registro** y haga clic en **Agregar**.

    Para configurar la directiva, utilice la siguiente configuración:

    ![Incorporación de directiva de registro o de inicio de sesión](media/active-directory-b2c-tutorials-web-app/add-susi-policy.png)

    | Configuración      | Valor sugerido  | Descripción                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Nombre** | SiUpIn | Escriba un **Nombre** para la directiva. El nombre de la directiva tiene el prefijo **B2C_1_**. En el código de ejemplo, se utiliza el nombre completo de la directiva **B2C_1_SiUpIn**. | 
    | **Proveedor de identidades** | Registro por correo electrónico | El proveedor de identidades que se usa para identificar al usuario de forma exclusiva. |
    | **Atributos de registro** | Nombre para mostrar y Código postal | Seleccione los atributos que se van a recopilar del usuario durante el registro. |
    | **Notificaciones de la aplicación** | Nombre para mostrar, Código postal, El usuario es nuevo, Identificador de objeto del usuario | Seleccione las [notificaciones](../active-directory/develop/developer-glossary.md#claim) que desea incluir en el [token de acceso](../active-directory/develop/developer-glossary.md#access-token). |

2. Haga clic en **Crear** para crear la directiva. 

### <a name="create-a-profile-editing-policy"></a>Creación de una directiva de edición de perfil

Para permitir que los usuarios restablezcan la información de su perfil de usuario por su cuenta, cree una **directiva de edición de perfil**.

1. En la página del portal de Azure AD B2C, seleccione **Directivas de edición de perfil** y haga clic en **Agregar**.

    Para configurar la directiva, utilice la siguiente configuración:

    | Configuración      | Valor sugerido  | Descripción                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Nombre** | SiPe | Escriba un **Nombre** para la directiva. El nombre de la directiva tiene el prefijo **B2C_1_**. En el código de ejemplo, se utiliza el nombre completo de la directiva **B2C_1_SiPe**. | 
    | **Proveedor de identidades** | Inicio de sesión en una cuenta local | El proveedor de identidades que se usa para identificar al usuario de forma exclusiva. |
    | **Atributos de perfil** | Nombre para mostrar y Código postal | Seleccione los atributos que los usuarios pueden modificar durante la edición de un perfil. |
    | **Notificaciones de la aplicación** | Nombre para mostrar, código postal, identificador de objeto del usuario | Seleccione las [notificaciones](../active-directory/develop/developer-glossary.md#claim) que desea incluir en el [token de acceso](../active-directory/develop/developer-glossary.md#access-token) después de una edición de perfil correcta. |

2. Haga clic en **Crear** para crear la directiva. 

### <a name="create-a-password-reset-policy"></a>Crear una directiva de restablecimiento de contraseña

Para habilitar en su aplicación el restablecimiento de contraseña, deberá crear una **directiva de restablecimiento de contraseña**. Esta directiva describe la experiencia del consumidor durante el restablecimiento de contraseña y el contenido de los tokens que recibe la aplicación al finalizar correctamente.

1. En la página del portal de Azure AD B2C, seleccione **Directivas de restablecimiento de contraseña** y haga clic en **Agregar**.

    Para configurar la directiva, utilice la siguiente configuración.

    | Configuración      | Valor sugerido  | Descripción                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **Nombre** | SSPR | Escriba un **Nombre** para la directiva. El nombre de la directiva tiene el prefijo **B2C_1_**. En el código de ejemplo, se utiliza el nombre completo de la directiva **B2C_1_SSPR**. | 
    | **Proveedor de identidades** | Restablecer la contraseña mediante la dirección de correo electrónico | Es el proveedor de identidades que se usa para identificar al usuario de forma exclusiva. |
    | **Notificaciones de la aplicación** | Identificador de objeto del usuario | Seleccione las [notificaciones](../active-directory/develop/developer-glossary.md#claim) que desea incluir en el [token de acceso](../active-directory/develop/developer-glossary.md#access-token) después de un restablecimiento de contraseña correcto. |

2. Haga clic en **Crear** para crear la directiva. 

## <a name="update-single-page-app-code"></a>Actualización del código de la aplicación de una sola página

Ahora que ha registrado una aplicación y ha creado directivas, debe configurar la aplicación para que use el directorio de Azure AD B2C. En este tutorial, configurará una aplicación JavaScript SPA de ejemplo que puede descargar desde GitHub. 

[Descargue un archivo zip](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip) o clone la aplicación web de ejemplo desde GitHub.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```
La aplicación de ejemplo muestra cómo una aplicación de una sola página puede usar Azure AD B2C para el registro del usuario, el inicio de sesión y llamar a una API web protegida. Debe cambiar la aplicación para que use el registro de aplicaciones del directorio y configurar las directivas que ha creado. 

Para cambiar la configuración de la aplicación:

1. Abra el archivo `index.html` en la aplicación de una sola página de Node.js de ejemplo.
2. Configure el ejemplo con la información de registro del directorio de Azure AD B2C. Cambie las siguientes líneas de código (no olvide reemplazar los valores con los nombres del directorio y las API):

    ```javascript
    // The current application coordinates were pre-registered in a B2C directory.
    var applicationConfig = {
        clientID: '<Application ID for your SPA obtained from portal app registration>',
        authority: "https://fabrikamb2c.b2clogin.com/tfp/fabrikamb2c.onmicrosoft.com/B2C_1_SiUpIn",
        b2cScopes: ["https://fabrikamb2c.onmicrosoft.com/demoapi/demo.read"],
        webApi: 'https://fabrikamb2chello.azurewebsites.net/hello',
    };
    ```

    El nombre de la directiva que se ha utilizado en este tutorial es **B2C_1_SiUpIn**. Si usa otro nombre de directiva, utilice el nombre de la directiva en el valor `authority`.

## <a name="run-the-sample"></a>Ejecución del ejemplo

1. Inicie un símbolo del sistema de Node.js.
2. Cambie al directorio que contiene el ejemplo de Node.js. Por ejemplo, `cd c:\active-directory-b2c-javascript-msal-singlepageapp`.
3. Ejecute los comandos siguientes:
    ```
    npm install && npm update
    node server.js
    ```

    La ventana de la consola muestra el número de puerto de donde se hospeda la aplicación.
    
    ```
    Listening on port 6420...
    ```

4. Use un explorador para ir a la dirección `http://localhost:6420` y ver la aplicación.

La aplicación de ejemplo es compatible con el registro, el inicio de sesión, la edición de un perfil y el restablecimiento de contraseña. Este tutorial ilustra cómo un usuario se registra en la aplicación mediante una dirección de correo electrónico. Puede explorar los demás escenarios por su cuenta.

### <a name="sign-up-using-an-email-address"></a>Registro con una dirección de correo electrónico

1. Haga clic en **Login** (Iniciar sesión) para iniciar sesión como usuario de la aplicación SPA. Aquí se utiliza la directiva **B2C_1_SiUpIn** que ha definido en un paso anterior.

2. Azure AD B2C presenta una página de inicio de sesión con un vínculo de registro. Como no tiene aún una cuenta, haga clic en el vínculo **Registrarse ahora**. 

3. El flujo de trabajo del registro presenta una página para recopilar y verificar la identidad del usuario con una dirección de correo electrónico. El flujo de trabajo de registro también recopila la contraseña del usuario y los atributos requeridos definidos en la directiva.

    Utilice una dirección de correo electrónico válida y valídela mediante el código de verificación. Establezca una contraseña. Especifique valores para los atributos solicitados. 

    ![Flujo de trabajo de registro](media/active-directory-b2c-tutorials-desktop-app/sign-up-workflow.png)

4. Haga clic en **Crear** para crear una cuenta local en el directorio de Azure AD B2C.

Ahora el usuario puede utilizar su dirección de correo electrónico para iniciar sesión y usar la aplicación SPA.

> [!NOTE]
> Tras iniciar sesión, la aplicación muestra el error "permisos insuficientes". Este error aparece porque está intentando acceder a un recurso desde el directorio de la demostración. Como el token de acceso solamente es válido para su directorio de Azure AD, la llamada API no tiene autorización. Continúe en el siguiente tutorial para crear una API web protegida para el directorio. 

## <a name="clean-up-resources"></a>Limpieza de recursos

Puede usar el directorio de Azure AD B2C si tiene previsto probar otros tutoriales de Azure AD B2C. Cuando ya no sea necesario, puede [eliminar el directorio de Azure AD B2C](active-directory-b2c-faqs.md#how-do-i-delete-my-azure-ad-b2c-tenant).

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido a crear a un directorio de Azure AD B2C, a crear directivas y a actualizar la aplicación de ejemplo de una sola página para que utilice el directorio de Azure AD B2C. En el siguiente tutorial aprenderá a registrar, configurar y llamar a una API web protegida desde una aplicación de escritorio.

> [!div class="nextstepaction"]
> 
