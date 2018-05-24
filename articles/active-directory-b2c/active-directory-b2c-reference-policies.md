---
title: 'Azure Active Directory B2C: directivas integradas | Microsoft Docs'
description: Tema sobre el marco de directiva extensible de Azure Active Directory B2C y sobre cómo crear distintos tipos de directiva
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 01/26/2017
ms.author: davidmu
ms.openlocfilehash: 424186a0acfe17cd7cb96f3ba7f8201e8b2b38ec
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/04/2018
ms.locfileid: "33200355"
---
# <a name="azure-active-directory-b2c-built-in-policies"></a>Azure Active Directory B2C: directivas integradas


El marco de directiva extensible de Azure Active Directory (Azure AD) B2C es la fortaleza esencial del servicio. Las directivas describen totalmente las experiencias de identidad del consumidor como el registro, el inicio de sesión y la edición de perfil. Por ejemplo, una directiva de registro le permite controlar comportamientos configurando los siguientes valores:

* Tipos de cuenta (cuentas sociales como Facebook, o cuentas locales como direcciones de correo electrónico) que los consumidores pueden usar para registrarse en la aplicación
* Atributos (por ejemplo, nombre, código postal, número de calzado, etc.) que se recopilarán del consumidor durante el registro
* Uso de Azure Multi-Factor Authentication
* La apariencia de todas las páginas de registro
* Información (que se manifiesta como notificaciones en un token) que recibe la aplicación cuando se completa la ejecución de la directiva.

Puede crear varias directivas de diferentes tipos en su inquilino y usarlas en sus aplicaciones según sea necesario. Las directivas se pueden volver a usar en todas las aplicaciones. Esta flexibilidad permite a los desarrolladores definir y modificar experiencias de identidad de consumidor con cambios mínimos o ningún cambio en su código.

Las directivas están disponibles para usarlas mediante una interfaz de usuario sencilla. Su aplicación desencadena una directiva mediante una solicitud de autenticación HTTP estándar (pasando un parámetro de directiva en la solicitud) y recibe un token personalizado como respuesta. Por ejemplo, la única diferencia que hay entre las solicitudes que invocan una directiva de registro y las que invocan una directiva de inicio de sesión es el nombre de la directiva que se usa en el parámetro de cadena de consulta "p":

```

https://login.microsoftonline.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siup                                       // Your sign-up policy

```

```

https://login.microsoftonline.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siin                                       // Your sign-in policy

```

## <a name="create-a-sign-up-or-sign-in-policy"></a>Creación de una directiva de registro o de inicio de sesión

Esta directiva controla las experiencias de registro y de inicio de sesión del cliente con una sola configuración. A los consumidores se les lleva por la ruta correcta (registro o inicio de sesión) según el contexto. También describe el contenido de los tokens que recibirá la aplicación cuando el registro o el inicio de sesión sean correctos.  **Aquí puede encontrar**código de ejemplo de la [directiva de registro o de inicio de sesión](active-directory-b2c-devquickstarts-web-dotnet-susi.md).  Le recomendamos que use esta directiva en vez de una directiva de **registro** y una directiva de **inicio de sesión**.  

[!INCLUDE [active-directory-b2c-create-sign-in-sign-up-policy](../../includes/active-directory-b2c-create-sign-in-sign-up-policy.md)]

## <a name="create-a-sign-up-policy"></a>Creación de una directiva de registro

[!INCLUDE [active-directory-b2c-create-sign-up-policy](../../includes/active-directory-b2c-create-sign-up-policy.md)]

## <a name="create-a-sign-in-policy"></a>Uso de una directiva de inicio de sesión

[!INCLUDE [active-directory-b2c-create-sign-in-policy](../../includes/active-directory-b2c-create-sign-in-policy.md)]

## <a name="create-a-profile-editing-policy"></a>Creación de una directiva de edición de perfil

[!INCLUDE [active-directory-b2c-create-profile-editing-policy](../../includes/active-directory-b2c-create-profile-editing-policy.md)]

## <a name="create-a-password-reset-policy"></a>Crear una directiva de restablecimiento de contraseña

[!INCLUDE [active-directory-b2c-create-password-reset-policy](../../includes/active-directory-b2c-create-password-reset-policy.md)]

## <a name="preview-policies"></a>Directivas de versión preliminar

Según se publican nuevas características, puede que algunas no estén disponibles en las directivas existentes.  Tenemos previsto reemplazar las versiones anteriores con la versión más reciente del mismo tipo una vez que estas directivas tengan disponibilidad general.  Las directivas existentes no cambiarán y para sacar provecho de estas nuevas características tendrá que crear nuevas directivas.

## <a name="frequently-asked-questions"></a>Preguntas más frecuentes

### <a name="how-do-i-link-a-sign-up-or-sign-in-policy-with-a-password-reset-policy"></a>¿Cómo puedo vincular una directiva de inicio de sesión o de registro con una directiva de restablecimiento de contraseña?
Al crear una directiva de **inicio de sesión o de registro** (con cuentas locales), verá el vínculo **¿Olvidó la contraseña?** en la primera página de la experiencia. Al hacer clic en este vínculo, no se desencadena automáticamente ninguna directiva de restablecimiento de contraseña, 

sino que se devuelve a la aplicación el código de error **`AADB2C90118`**. La aplicación debe controlar este código de error invocando una directiva de restablecimiento de contraseña específica. Para obtener más información, consulte un [ejemplo en el que se muestra el método para vincular directivas](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-DotNet-SUSI).

### <a name="should-i-use-a-sign-up-or-sign-in-policy-or-a-sign-up-policy-and-a-sign-in-policy"></a>¿Debo usar una directiva de inicio de sesión o de registro o una directiva de inicio de sesión y una directiva de registro?
Le recomendamos que use una directiva de **inicio de sesión o de registro** en vez de una directiva de **inicio de sesión** y una directiva de **registro**.  

La directiva de **inicio de sesión o de registro** tiene más funcionalidades que la directiva de **inicio de sesión**. También le permite usar la personalización de la interfaz de usuario de la página y tiene una mejor compatibilidad con la localización. 

La directiva de **inicio de sesión** se recomienda si no necesita localizar las directivas, solo necesita unas funcionalidades de personalización secundarias para la personalización de marca y quiere que el restablecimiento de contraseña esté integrado en ella.

## <a name="next-steps"></a>Pasos siguientes
* [Configuración de token, sesión e inicio de sesión único](active-directory-b2c-token-session-sso.md)
* [Deshabilitación de la comprobación de correos electrónicos durante la suscripción de consumidores](active-directory-b2c-reference-disable-ev.md)

