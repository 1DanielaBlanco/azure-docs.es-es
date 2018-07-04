---
title: ¿Qué son los controles de acceso en el acceso condicional de Azure Active Directory? | Microsoft Docs
description: Aprenda cómo funcionan los controles de acceso en el acceso condicional de Azure Active Directory.
services: active-directory
keywords: acceso condicional a aplicaciones, acceso condicional con Azure AD, acceso seguro a recursos de empresa, directivas de acceso condicional
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.component: protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/13/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: dc1e3fd3984d0ccbfb0565a3216ec907894e88ea
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/27/2018
ms.locfileid: "37028955"
---
# <a name="what-are-access-controls-in-azure-active-directory-conditional-access"></a>¿Qué son los controles de acceso en el acceso condicional de Azure Active Directory? 

Con el [acceso condicional de Azure Active Directory (Azure AD)](active-directory-conditional-access-azure-portal.md), puede controlar el modo en que los usuarios autorizados acceden a las aplicaciones en la nube. En una directiva de acceso condicional, definirá la respuesta ("haga esto") al motivo para desencadenarla ("cuando esto suceda"). 

![Control](./media/active-directory-conditional-access-controls/10.png)


En el contexto del acceso condicional, 

- "**Cuando esto suceda**" son las **condiciones** y

- "**Entonces haga esto**" son los **controles de acceso**.


La combinación de una declaración de condición con los controles representa una directiva de acceso condicional.

![Control](./media/active-directory-conditional-access-controls/61.png)

Cada control es un requisito que la persona o el sistema que inicia sesión deben cumplir, o una restricción en lo que el usuario puede hacer tras iniciar sesión. 

Existen dos tipos de controles: 

- **Controles de concesión**: para obtener acceso

- **Controles de sesión**: para restringir el acceso dentro de una sesión

En este tema se explican los distintos controles disponibles en el acceso condicional de Azure AD. 

## <a name="grant-controls"></a>Controles de concesión

Con los controles de concesión, puede bloquear el acceso por completo o permitirlo con requisitos adicionales seleccionando los controles que desee. Con varios controles, puede requerir:

- Que todos los controles se cumplan (*Y*) 
- Que un control seleccionado se cumpla (*O*)

![Control](./media/active-directory-conditional-access-controls/17.png)



### <a name="multi-factor-authentication"></a>Multi-Factor Authentication

Puede utilizar este control para requerir la autenticación multifactor para tener acceso a la aplicación en la nube especificada. Este control es compatible con los siguientes proveedores multifactor: 

- Azure Multi-Factor Authentication 

- Un proveedor de autenticación multifactor de terceros, en combinación con Servicios de federación de Active Directory (AD FS).
 
La autenticación multifactor ayuda a proteger los recursos del acceso por parte de un usuario no autorizado que podría haber conseguido las credenciales principales de un usuario válido.



### <a name="compliant-device"></a>Dispositivos compatible

Puede configurar directivas de acceso condicional basadas en dispositivos. El objetivo de una directiva de acceso condicional basada en dispositivos es conceder acceso a los recursos configurados solo desde [dispositivos administrados](active-directory-conditional-access-policy-connected-applications.md#managed-devices). Requerir el uso de un dispositivo compatible es una de las opciones que tiene para definir qué es un dispositivo administrado. Si esta opción está seleccionada, la directiva de acceso condicional concede acceso a los intentos que se realizan con dispositivos [registrados](device-management-introduction.md) en Azure Active Directory y marcados como compatibles por Intune (en cualquier sistema operativo del dispositivo) o por un sistema MDM de terceros en dispositivos con Windows 10. No se pueden utilizar sistemas MDM de terceros con otros sistemas operativos que no sean Windows 10.

Para más información, consulte [Configuración de directivas de acceso condicional basadas en dispositivos de Azure Active Directory](active-directory-conditional-access-policy-connected-applications.md).

### <a name="hybrid-azure-ad-joined-device"></a>Dispositivo unido a Azure AD híbrido

Requerir un dispositivo que esté unido a un dominio de Azure AD híbrido es otra opción que puede usar para configurar directivas de acceso condicional basadas en dispositivos. Este requisito hace referencia a equipos de escritorio, equipos portátiles y tabletas de empresa con Windows que están unidos a una instancia local de Active Directory. Si se selecciona esta opción, la directiva de acceso condicional concede acceso a los intentos de acceso realizados con dispositivos unidos a su Active Directory local y su Azure Active Directory.  

Para más información, consulte [Configuración de directivas de acceso condicional basadas en dispositivos de Azure Active Directory](active-directory-conditional-access-policy-connected-applications.md).





### <a name="approved-client-app"></a>Aplicaciones cliente aprobadas

Dado que los empleados emplean dispositivos móviles tanto para las tareas de trabajo como para uso personal, debe tener la capacidad de proteger los datos de la empresa a los que se accede con dispositivos, incluso en el caso en el que no estén administrados por el usuario.
Puede usar [directivas de protección de aplicaciones de Intune](https://docs.microsoft.com/intune/app-protection-policy) para ayudar a proteger los datos de su compañía independientes de cualquier solución de administración de dispositivos móviles (MDM).


Con las aplicaciones cliente aprobadas, puede requerir que una aplicación cliente que intente obtener acceso a las aplicaciones en la nube admita [directivas de protección de aplicaciones de Intune](https://docs.microsoft.com/intune/app-protection-policy). Por ejemplo, puede restringir el acceso a Exchange Online para la aplicación Outlook. Una directiva de acceso condicional que requiere aplicaciones cliente aprobadas se conoce también como [directiva de acceso condicional basado en aplicaciones](active-directory-conditional-access-mam.md). Para una lista de las aplicaciones cliente admitidas, consulte el [requisito de las aplicaciones cliente aprobadas](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement).


### <a name="terms-of-use"></a>Términos de uso

Puede requerir a un usuario en el inquilino que consienta las condiciones de uso antes de concederle acceso a un recurso. Como administrador, puede configurar y personalizar las condiciones de uso mediante la carga de un documento PDF. Si un usuario se encuentra en el ámbito de control, el acceso a una aplicación solo se concede si se han aceptado las condiciones de uso. 


### <a name="custom-controls"></a>Controles personalizados 

También puede crear controles personalizados en acceso condicional que redirigen a los usuarios a un servicio compatible para satisfacer más requisitos fuera de Azure Active Directory. Esto le permite utilizar ciertos proveedores externos de autenticación multifactor y comprobación para exigir reglas de acceso condicional o para crear su propio servicio personalizado. Para satisfacer este control, el explorador de un usuario se redirige al servicio externo, lleva a cabo todas las actividades de autenticación y validación necesarias y luego se vuelve a redirigir a Azure Active Directory. Si el usuario se autenticó o validó correctamente, dicho usuario continúa en el flujo de acceso condicional. 

## <a name="custom-controls"></a>Controles personalizados

Los controles personalizados son una funcionalidad de la edición Azure Active Directory Premium P2. Al utilizar controles personalizados, los usuarios se redirigen a un servicio compatible para satisfacer más requisitos fuera de Azure Active Directory. Para satisfacer este control, el explorador de un usuario se redirige al servicio externo, lleva a cabo todas las actividades de autenticación y validación necesarias y luego se vuelve a redirigir a Azure Active Directory. Azure Active Directory comprueba la respuesta y, si el usuario se autenticó o validó correctamente, dicho usuario continuará en el flujo de acceso condicional.

Estos controles permiten el uso de ciertos servicios externos o personalizados como controles de acceso condicional y generalmente amplían las funcionalidades de acceso condicional.

Los proveedores que actualmente ofrecen un servicio compatible incluyen:

- [Duo Security](https://duo.com/docs/azure-ca)

- RSA

- [Trusona](https://www.trusona.com/docs/azure-ad-integration-guide)

Para más información sobre esos servicios, póngase en contacto con los proveedores directamente.

### <a name="creating-custom-controls"></a>Creación de controles personalizados

Para crear un control personalizado, primero debe ponerse en contacto con el proveedor que desea utilizar. Cada proveedor que no es Microsoft tiene su propio proceso y requisitos para registrarse, suscribirse o pasar a formar parte del servicio e indicar que usted desea integrarse con el acceso condicional. En ese momento, el proveedor le proporcionará un bloque de datos en formato JSON. Estos datos permiten al proveedor y al acceso condicional trabajar conjuntamente para su inquilino, crean el nuevo control y definen cómo el acceso condicional puede indicar si los usuarios han realizado correctamente la comprobación con el proveedor.

Copie los datos JSON y, a continuación, péguelos en el cuadro de texto relacionado. No realice ningún cambio en JSON a menos que entienda explícitamente dicho cambio. Realizar cualquier cambio podría interrumpir la conexión entre el proveedor y Microsoft y dejarle tanto a usted como a sus usuarios fuera de sus cuentas.

La opción para crear un control personalizado está en la sección **Administrar** de la página **Acceso condicional**.

![Control](./media/active-directory-conditional-access-controls/82.png)

Al hacer clic en **Nuevo control personalizado** se abre una hoja con un cuadro de texto para los datos JSON del control.  


![Control](./media/active-directory-conditional-access-controls/81.png)


### <a name="deleting-custom-controls"></a>Eliminación de controles personalizados

Para eliminar un control personalizado, primero debe asegurarse de que no se está utilizando en ninguna directiva de acceso condicional. Cuando haya terminado:

1. Vaya a la lista Controles personalizados.

2. Haga clic en ...  

3. Seleccione **Eliminar**.

### <a name="editing-custom-controls"></a>Edición de controles personalizados

Para editar un control personalizado, debe eliminar el control actual y crear un nuevo control con la información actualizada.




## <a name="session-controls"></a>Controles de sesión

Los controles de sesión permiten limitar la experiencia desde una aplicación en la nube. Los controles de sesión son aplicados por aplicaciones en la nube y se basan en información adicional sobre la sesión proporcionada por Azure AD a la aplicación.

![Control](./media/active-directory-conditional-access-controls/31.png)

### <a name="use-app-enforced-restrictions"></a>Usar restricciones que exige la aplicación

Puede usar este control para requerir que Azure AD transmita la información del dispositivo a la aplicación en la nube. Esto ayuda a la aplicación en la nube a saber si el usuario procede de un dispositivo compatible o un dispositivo unido al dominio. Actualmente este control solo se admite con SharePoint como aplicación en la nube. SharePoint usa la información del dispositivo para proporcionar a los usuarios una experiencia completa o limitada según el estado del dispositivo.
Para más información sobre cómo requerir el acceso limitado con SharePoint consulte [Controlar el acceso desde dispositivos no administrados](https://aka.ms/spolimitedaccessdocs).



## <a name="next-steps"></a>Pasos siguientes

- Si quiere saber cómo configurar una directiva de acceso condicional, consulte [Requerir MFA para aplicaciones específicas con acceso condicional a Azure Active Directory](active-directory-conditional-access-app-based-mfa.md).

- Si está listo para configurar directivas de acceso condicional para su entorno, consulte [Procedimientos recomendados para el acceso condicional en Azure Active Directory](active-directory-conditional-access-best-practices.md). 
