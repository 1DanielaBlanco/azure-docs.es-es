---
title: Preguntas más frecuentes sobre informes de Azure Active Directory | Microsoft Docs
description: Preguntas más frecuentes sobre informes de Azure Active Directory.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
ms.assetid: 534da0b1-7858-4167-9986-7a62fbd10439
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: compliance-reports
ms.date: 05/10/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 2ceaf4ecf7da78377c52135b7de5191c1dc3dd28
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/19/2018
ms.locfileid: "36232141"
---
# <a name="azure-active-directory-reporting-faq"></a>Preguntas más frecuentes sobre informes de Azure Active Directory

Este artículo incluye respuestas a preguntas más frecuentes sobre los informes de Azure Active Directory (Azure AD). Para más información, consulte [Informes de Azure Active Directory](active-directory-reporting-azure-portal.md). 

**P.: Estoy usando las API de punto de conexión https://graph.windows.net/&lt;tenant-name&gt;/reports/ para extraer informes de uso de aplicaciones integradas y auditoría de Azure AD en los sistemas de notificación mediante programación. ¿A cuál debo cambiar?**

**R.:** Busque nuestra [documentación de referencia de API](https://developer.microsoft.com/graph/) para ver cómo puede usar las nuevas API para tener acceso a [informes de actividad](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal). Este punto de conexión tiene dos informes (auditoría e inicios de sesión) que proporcionan todos los datos que se obtuvo en el punto de conexión de la anterior API. Este nuevo punto de conexión también tiene un informe de inicios de sesión con la licencia Premium de Azure AD que puede usar para obtener información del uso de aplicaciones y de dispositivos, y de inicio de sesión de usuarios.


--- 

**P.: ¿Estoy usando las API de punto de conexión https://graph.windows.net/&lt;tenant-name&gt;/reports/ para extraer informes de seguridad de Azure AD (tipos específicos de detecciones, como las credenciales perdidas o los inicios de sesión desde direcciones IP anónimas) en nuestros sistemas de informes mediante programación. ¿A cuál debo cambiar?**

**R:** Puede utilizar la [API de eventos de riesgo Identity Protection](active-directory-identityprotection-graph-getting-started.md) para acceder a las detecciones de seguridad a través de Microsoft Graph. Este nuevo formato proporciona mayor flexibilidad en el modo en que permite consultar los datos, con filtrado avanzado, selección de campos y mucho más, y normaliza los eventos de riesgo en un tipo para facilitar la integración en SIEM y otras herramientas de recolección de datos. Dado que los datos están en un formato diferente, no puede sustituir una nueva consulta para las consultas anteriores. Sin embargo, [la nueva API utiliza Microsoft Graph](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityriskevent), que es el estándar de Microsoft para estas API como Office 365 o Azure AD. Así, el trabajo necesario puede dilatar sus inversiones actuales en MS Graph o ayudarle a comenzar la transición a esta nueva plataforma estándar.

--- 

**P: ¿Cuál es la retención de datos de los registros de actividad (auditoría e inicios de sesión) en Azure Portal?** 

**R.:** Consulte [durante cuánto tiempo se almacenan los datos recopilados?](active-directory-reporting-retention.md#q-for-how-long-is-the-collected-data-stored) en una respuesta a esta pregunta.

--- 

**P: ¿Cuánto tiempo debo esperar para poder ver los datos de actividad después de haber completado una tarea?**

**R:** Los registros de actividad de auditoría tienen una latencia que oscila entre 15 minutos y una hora. Los registros de actividad de inicio de sesión pueden tardar desde 15 minutos hasta 2 horas para algunos registros.

---

**P: ¿Necesito ser administrador global para consultar los inicios de sesión de actividad en Azure Portal o para obtener datos a través de la API?**

**R:** No. Para obtener datos de informes en Azure Portal o a través de la API, debe ser **Lector de seguridad**, **Administrador de seguridad** o **Administrador global**.

---

**P: ¿Puedo obtener información del registro de actividad de Office 365 a través de Azure Portal?**

**R:** Aunque los registros de actividad de Office 365 y Azure AD comparten mucho de los recursos del directorio, si desea obtener una vista completa de los registros de actividad de Office 365, debe ir al Centro de administración de Office 365 para obtener la información del registro de actividad de Office 365.

---


**P: ¿Qué API utilizo para obtener información sobre los registros de actividad de Office 365?**

**R:** Use las API de Administración de Office 365 para acceder a los [registros de actividad de Office 365 a través de una API](https://msdn.microsoft.com/office-365/office-365-managment-apis-overview).

---

**P: ¿Cuántos registros puedo descargar de Azure Portal?**

**R:** Puede descargar hasta 5000 registros de Azure Portal. Los registros se ordenan a partir de los *más recientes* y, de forma predeterminada, se obtienen los últimos 5000 registros. Para tamaños de registro superiores a 5000, elija las condiciones de filtro y haga clic en el botón *Script* de Azure Portal. Se descargará un script de PowerShell que puede ejecutar para generar un archivo CSV de todos los registros que coinciden con las condiciones de filtro. Debe ejecutar el script de PowerShell en *modo de administrador*. Para más información, consulte [Descarga de actividades de inicio de sesión](active-directory-reporting-activity-sign-ins.md#download-sign-in-activities).

---

**P: ¿Cuántos registros puedo consultar a través de las API de actividades?**

**R:** Puede consultar hasta un millón de registros (si no usa el operador TOP, que ordena los registros a partir del más reciente). Si usa el operador "top", puede consultar hasta 500 000 registros. Puede encontrar consultas de ejemplo sobre cómo usar la API [aquí](active-directory-reporting-api-getting-started.md).

---

**P: ¿Cómo puedo obtener una licencia Premium?**

**R:** Vea [Introducción a Azure Active Directory Premium](fundamentals/active-directory-get-started-premium.md) para obtener una respuesta a esta pregunta.

---

**P: ¿Cuándo se deberían ver los datos de actividades después de obtener una licencia Premium?**

**R:** Si dispone ya de datos de actividades como una licencia gratuita, entonces puede ver los mismos datos. Si no tiene ningún dato, tardará uno o dos días.

---

**P: ¿Puedo ver los datos del último mes después de obtener una licencia de Azure AD Premium?**

**R:** Si ha cambiado recientemente a una versión Premium (incluida una versión de prueba), inicialmente, puede ver datos de hasta siete días. Cuando se acumulan datos, verá hasta 30 días.

---

**P: Hay un evento de riesgo en Identity Protection, pero no veo el inicio de sesión correspondiente en todos los inicios de sesión. ¿Es normal?**

**R.:** Sí, Identity Protection evalúa el riesgo de todos los flujos de autenticación si es interactivo o no interactivo. Sin embargo, el informe únicamente de todos los inicios de sesión muestra los inicios de sesión interactivos.

---

**P: ¿Cómo puedo descargar el informe Usuarios marcados en riesgo en Azure Portal?**

**R:**: La opción para descargar el informe *Usuarios marcados en riesgo* se agregará próximamente.

---

**P: ¿Cómo puedo sé por qué un inicio de sesión o un usuario se marcó como en riesgo en Azure Portal?**

**R:**: Los clientes de la edición Premium pueden obtener más información sobre los eventos de riesgos subyacentes haciendo clic en el usuario en "Usuarios marcados en riesgo" o haciendo clic en "Inicios de sesión en riesgo". Los clientes de edición gratuita y básica pueden ver los inicios de sesión y usuarios en riesgo sin la información de eventos de riesgo subyacentes.

---

**P: ¿Cómo se calculan las direcciones IP en los inicios de sesión y en el informe de inicios de sesión de riesgo?**

**R:** Las direcciones IP se emiten de forma que no haya ninguna conexión definitiva entre una dirección IP y donde se encuentre físicamente el equipo con esa dirección. Esto se complica por factores tales como proveedores de dispositivos móviles y VPN que emiten direcciones IP desde grupos centrales, a menudo muy lejos de donde se usa realmente el dispositivo del cliente. Dado lo anterior, la conversión de la dirección IP en una ubicación física es un esfuerzo notable basado en seguimientos, datos de registro, búsquedas inversas y otra información. 

---

**P: ¿Qué significa el evento de riesgo "Inicio de sesión con riesgo adicional detectado"?**

**R.:** Para proporcionarle un detalle de todos los inicios de sesión con riesgo en su entorno, mostramos el evento de riesgo "Inicio de sesión con riesgo adicional detectado" en los inicios de sesión debido a detecciones exclusivas para los suscriptores de Azure AD Identity Protection.

---
