---
author: PatAltimore
ms.service: active-directory-b2c
ms.topic: include
ms.date: 11/03/2016
ms.author: patricka
ms.openlocfilehash: bff2543ec48c66c10db697650def0077e3de28be
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50133980"
---
Para habilitar en la aplicación el restablecimiento de contraseña específica se usa una directiva de **restablecimiento de contraseña**. Tenga en cuenta que la opción de restablecimiento de contraseña de todos los inquilinos se especifica [aquí](../articles/active-directory-b2c/active-directory-b2c-reference-sspr.md). Esta directiva describe las experiencias de los consumidores durante el restablecimiento de contraseña y el contenido de los tokens que recibirá la aplicación al finalizarlo correctamente.

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](active-directory-b2c-portal-navigate-b2c-service.md)]

En la sección de directivas de configuración, seleccione **Directivas de restablecimiento de contraseña** y haga clic en **+ Agregar**.

![Seleccionar directivas de registro o inicio de sesión y hacer clic en el botón Agregar](media/active-directory-b2c-create-password-reset-policy/add-b2c-password-reset-policy.png)

Escriba un **nombre** de directiva al que la aplicación haga referencia. Por ejemplo, escriba: `SSPR`.

Seleccione **Proveedores de identidades** y active la opción **Reset password using email address** (Restablecer contraseña mediante la dirección de correo electrónico). Haga clic en **OK**.

![Seleccionar restablecer contraseña mediante la dirección de correo electrónico como proveedor de identidades y hacer clic en el botón Aceptar](media/active-directory-b2c-create-password-reset-policy/add-b2c-password-reset-identity-providers.png)

Seleccione **Notificaciones de aplicación**. Aquí puede elegir las notificaciones que quiere que se devuelvan en los tokens de autorización a su aplicación después de una experiencia de restablecimiento de contraseña correcta. Por ejemplo, seleccione **Id. de objeto del usuario**.

![Seleccionar algunas notificaciones de aplicación y hacer clic en el botón Aceptar](media/active-directory-b2c-create-password-reset-policy/add-b2c-password-reset-application-claims.png)

Haga clic en **Crear** para agregar la directiva. La directiva aparece como **B2C_1_SSPR**. El prefijo **B2C_1_** se anexa al nombre.

Para abrir la directiva, seleccione **B2C_1_SSPR**. Compruebe la configuración especificada en la tabla y haga clic en **Ejecutar ahora**.

![Seleccionar la directiva y ejecutarla](media/active-directory-b2c-create-password-reset-policy/run-b2c-password-reset-policy.png)

| Configuración      | Valor  |
| ------------ | ------ |
| **Aplicaciones** | Aplicación B2C de Contoso |
| **Seleccionar dirección URL de respuesta** | `https://localhost:44316/` |

Se abrirá una nueva pestaña del explorador y podrá comprobar la experiencia del usuario de restablecimiento de contraseña en su aplicación.

> [!NOTE]
> Se tarda hasta un minuto en que la creación de directivas y las actualizaciones surtan efecto.
>
