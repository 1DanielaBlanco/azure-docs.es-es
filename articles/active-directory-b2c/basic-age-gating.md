---
title: Usar el acceso según la edad en Azure Active Directory B2C | Microsoft Docs
description: Obtenga información acerca de cómo identificar a los menores mediante la aplicación.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/29/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 9a9a86d445deaea4872615f443ad53f76638a758
ms.sourcegitcommit: 715813af8cde40407bd3332dd922a918de46a91a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2018
ms.locfileid: "47056529"
---
#<a name="using-age-gating-in-azure-ad-b2c"></a>Usar el acceso según la edad en Azure AD B2C

>[!IMPORTANT]
>Esta característica se encuentra en versión preliminar privada.  Consulte nuestro [blog de servicio](https://blogs.msdn.microsoft.com/azureadb2c/) para obtener más información cuando esté disponible o póngase en contacto con AADB2CPreview@microsoft.com.  NO use esta opción en los directorios de producción hasta que esté disponible para el público en general, ya que el uso de estas nuevas características puede provocar la pérdida de datos y cambios inesperados en el comportamiento.  
>

##<a name="age-gating"></a>Acceso según la edad
La opción de acceso según la edad le permite usar Azure AD B2C para identificar a los menores en la aplicación.  Puede bloquear al usuario para que no inicie sesión en la aplicación o permitir que acceda a la misma mediante solicitudes adicionales que identifiquen el grupo de edad del usuario y el estado de la autorización parental.  

>[!NOTE]
>Se puede realizar el seguimiento de la autorización parental en un atributo de usuario denominado `consentProvidedForMinor`.  Puede actualizar esta propiedad mediante Graph API, que utilizará este campo al actualizar `legalAgeGroupClassification`.
>

##<a name="setting-up-your-directory-for-age-gating"></a>Configurar el directorio para el acceso según la edad
Para poder usar el acceso según la edad en un flujo de usuario, debe configurar el directorio para que admita propiedades adicionales. Esta operación se puede hacer a través de `Properties` en el menú (que estará disponible solo si usted forma parte de la versión preliminar privada).  
1. En la extensión de Azure AD B2C, haga clic en la opción **Propiedades** del inquilino, en el menú de la izquierda.
2. En la sección **Acceso según la edad**, haga clic en el botón **Configurar**.
3. Espere a que finalice la operación y el directorio tendrá configurado el acceso según la edad.

##<a name="enabling-age-gating-in-your-user-flow"></a>Habilitar el acceso según la edad en el flujo de usuario
Una vez que el directorio esté configurado para usar el acceso según la edad, puede usar esta característica en los flujos de usuario de la versión preliminar.  Esta característica requiere cambios que la hacen incompatible con los tipos de flujos de usuario existentes.  Para habilitar el acceso según la edad, realice los pasos siguientes:
1. Cree un flujo de usuario de la versión preliminar.
2. Una vez creado, vaya a **Propiedades** en el menú.
3. En la sección **Acceso según la edad**, haga clic en el botón de alternancia para habilitar la característica.
4. A continuación, puede elegir cómo quiere administrar los usuarios que se identifican como menores.

##<a name="what-does-enabling-age-gating-do"></a>¿Qué funciones tiene el acceso según la edad?
Una vez habilitado el acceso según la edad en el flujo de usuario, la experiencia de usuario cambia.  Al hacer el registro, se solicita a los usuarios su fecha de nacimiento y país de residencia junto con los atributos de usuario que se configuraron en el flujo de usuarios.  Al iniciar sesión, los usuarios que todavía no hayan especificado la fecha de nacimiento y el país de residencia deberán proporcionar esta información la próxima vez que inicien sesión.  A partir de estos dos valores, Azure AD B2C identificará si el usuario es un menor y actualizará el campo `ageGroup`, cuyo valor puede ser `null`, `Undefined`, `Minor`, `Adult` y `NotAdult`.  A continuación, se usan los campos `ageGroup` y `consentProvidedForMinor` para calcular `legalAgeGroupClassification`. 

##<a name="age-gating-options"></a>Opciones del acceso según la edad
Puede elegir que Azure AD B2C bloquee a menores que no tengan autorización parental, o permitirles el acceso y que la aplicación decida qué hacer con ellos.  

###<a name="allowing-minors-without-parental-consent"></a>Permitir el acceso a menores sin autorización parental
Para los flujos de usuarios que permitan el registro, el inicio de sesión o ambos, puede optar por permitir el acceso de menores sin autorización a la aplicación.  En el caso de los menores sin autorización parental, se les permite iniciar sesión o registrarse de manera normal, y Azure AD B2C emite un token de identificación con la notificación `legalAgeGroupClassification`.  Al usar esta notificación, puede elegir la experiencia que tienen estos usuarios, como la necesidad de que consigan una autorización parental (y así actualizar el campo `consentProvidedForMinor`).

###<a name="blocking-minors-without-parental-consent"></a>Bloquear a menores sin autorización parental
Para los flujos de usuarios que permitan el registro, el inicio de sesión o ambos, puede optar por bloquear el acceso de menores sin autorización a la aplicación.  Hay dos opciones para administrar los usuarios bloqueados en Azure AD B2C:
* Enviar datos JSON de vuelta a la aplicación: esta opción enviará una respuesta a la aplicación indicando que se bloqueó un menor.
* Mostrar una página de error: se mostrará al usuario una página que le informe que no puede acceder a la aplicación.