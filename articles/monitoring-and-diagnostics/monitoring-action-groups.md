---
title: Creación y administración de grupos de acciones en Azure Portal
description: Obtenga información acerca de cómo crear y administrar grupos de acciones en Azure Portal.
author: dkamstra
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/1/2018
ms.author: dukek
ms.component: alerts
ms.openlocfilehash: 63216d56fb3acbb954086fbf026441e69073621e
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/11/2018
ms.locfileid: "35263072"
---
# <a name="create-and-manage-action-groups-in-the-azure-portal"></a>Creación y administración de grupos de acciones en Azure Portal
## <a name="overview"></a>Información general ##
Un grupo de acciones es una colección de las preferencias de notificación que el usuario define. Las alertas de Azure Monitor y Service Health están configuradas para usar un grupo de acciones específico cuando se desencadena la alerta. Varias alertas pueden usar el mismo grupo de acciones o distintos grupos de acciones en función de los requisitos del usuario.

En este artículo se muestra cómo crear y administrar grupos de acciones en el portal de Azure.

Cada acción se compone de las siguientes propiedades:

* **Nombre**: un identificador único dentro del grupo de acciones.  
* **Tipo de acción**: realizar una llamada de voz o enviar un SMS, enviar un correo electrónico, llamar a un webhook, enviar datos a una herramienta ITSM, llamar a una aplicación lógica, enviar una notificación push a la aplicación de Azure o ejecutar un runbook de Automation.
* **Detalles**: el número de teléfono, dirección de correo electrónico o identificador URI del webhook o los detalles de conexión de ITSM.

Para más información sobre el uso de plantillas de Azure Resource Manager para configurar grupos de acciones, consulte [Plantillas de Resource Manager para grupos de acciones](monitoring-create-action-group-with-resource-manager-template.md).

## <a name="create-an-action-group-by-using-the-azure-portal"></a>Creación de un grupo de acciones con Azure Portal ##
1. En el [portal](https://portal.azure.com), seleccione **Monitor**. La hoja **Supervisión** consolida todos los valores y datos de supervisión en una vista.

    ![Servicio "Monitor"](./media/monitoring-action-groups/home-monitor.png)
2. En la sección **Configuración**, seleccione **Grupos de acciones**.

    ![Pestaña "Grupos de acciones"](./media/monitoring-action-groups/action-groups-blade.png)
3. Seleccione **Agregar grupo de acciones** y rellene los campos.

    ![Comando "Agregar grupo de acciones"](./media/monitoring-action-groups/add-action-group.png)
4. Escriba un nombre en el cuadro de texto **Nombre del grupo de acciones** y especifique un nombre en el cuadro de texto **Nombre corto**. El nombre corto se utiliza en lugar del nombre completo del grupo de acciones cuando se envían notificaciones mediante este grupo.

      ![Cuadro de diálogo "Agregar grupo de acciones"](./media/monitoring-action-groups/action-group-define.png)

5. El cuadro de texto **Suscripción** se rellena automáticamente con la suscripción actual. La suscripción es aquella en la que se guarda el grupo de acciones.

6. Seleccione el **Grupo de recursos** en el que se guarda el grupo de acciones.

7. Defina una lista de acciones proporcionando los siguientes valores para cada acción:

    a. **Nombre**: escriba un identificador único para esta acción.

    b. **Tipo de acción**: seleccionar correo electrónico, SMS, notificación push, voz, aplicación lógica, webhook, ITSM o runbook de Automation.

    c. **Detalles**: según el tipo de acción, proporcione un número de teléfono, una dirección de correo electrónico o un identificador URI de webhook, una aplicación de Azure, una conexión de ITSM o un runbook de Automation. Para la acción de ITSM, especifique además **Elemento de trabajo** y otros campos que requiera la herramienta ITSM.

8. Seleccione **Aceptar** para crear el grupo de acciones.

## <a name="action-specific-information"></a>Información específica de la acción
<dl>
<dt>Notificaciones push de la aplicación de Azure</dt>
<dd>En un grupo de acciones puede tener hasta 10 acciones de aplicación de Azure.</dd>
<dd>En este momento, la acción de aplicación de Azure solo admite alertas de ServiceHealth. Los demás momentos de alerta se omitirán. Consulte el artículo acerca de la [configuración de alertas siempre que se publique una notificación de estado de un servicio](monitoring-activity-log-alerts-on-service-notifications.md).</dd>

<dt>Correo electrónico</dt>
<dd>Se enviarán mensajes de correo electrónico desde las direcciones de correo electrónico siguientes. Asegúrese de que el filtrado de correo electrónico esté configurado correctamente.

    - azure-noreply@microsoft.com
    - azureemail-noreply@microsoft.com
    - alerts-noreply@mail.windowsazure.com
    
</dd>
<dd>En un grupo de acciones puede tener hasta 1000 acciones de correo electrónico.</dd>
<dd>Consulte el artículo de [información sobre las limitaciones](./monitoring-alerts-rate-limiting.md).</dd>

<dt>ITSM</dt>
<dd>En un grupo de acciones puede tener hasta 10 acciones de ITSM.</dd>
<dd>La acción de ITSM requiere una conexión de ITSM. Aprenda cómo crear una [conexión de ITSM](../log-analytics/log-analytics-itsmc-overview.md).</dd>

<dt>Aplicaciones lógicas</dt>
<dd>En un grupo de acciones puede tener hasta 10 acciones de aplicación lógica.</dd>

<dt>Runbook</dt>
<dd>En un grupo de acciones puede tener hasta 10 acciones de runbook.</dd>

<dt>SMS</dt>
<dd>En un grupo de acciones puede tener hasta 10 acciones de SMS.</dd>
<dd>Consulte el artículo de [información sobre las limitaciones](./monitoring-alerts-rate-limiting.md).</dd>
<dd>Consulte el artículo sobre el [comportamiento de las alertas por SMS](monitoring-sms-alert-behavior.md).</dd>

<dt>Voz</dt>
<dd>En un grupo de acciones puede tener hasta 10 acciones de voz.</dd>
<dd>Consulte el artículo de [información sobre las limitaciones](./monitoring-alerts-rate-limiting.md).</dd>

<dt>Webhook</dt>
<dd>En un grupo de acciones puede tener hasta 10 acciones de webhook.
<dd>Lógica de reintento: el tiempo de espera para una respuesta es de 10 segundos. Se volverá a intentar la llamada de webhook un máximo de 2 veces cuando se devuelvan los siguientes códigos de estado HTTP: 408, 429, 503, 504 o el punto de conexión HTTP no responda. El primer reintento se produce transcurridos 10 segundos. El segundo y último reintento se produce transcurridos 100 segundos.</dd>
</dl>

## <a name="manage-your-action-groups"></a>Administración de los grupos de acciones ##
Después de crear un grupo de acciones, está visible en la sección **Grupos de acciones** de la hoja **Supervisión**. Seleccione el grupo de acciones que desea administrar para:

* Agregar, editar o quitar acciones.
* Eliminar el grupo de acciones.

## <a name="next-steps"></a>Pasos siguientes ##
* Más información sobre el [comportamiento de las alertas por SMS](monitoring-sms-alert-behavior.md).  
* [Comprender el esquema de webhook de alertas del registro de actividad](monitoring-activity-log-alerts-webhook.md).  
* Obtenga más información sobre [Conector de ITSM](../log-analytics/log-analytics-itsmc-overview.md)
* Más información sobre la [limitación de velocidad](monitoring-alerts-rate-limiting.md) en las alertas.
* Consulte la [introducción a las alertas del registro de actividad](monitoring-overview-alerts.md) y aprenda cómo puede recibir alertas.  
* Aprenda a [configurar alertas siempre que se publique una notificación de mantenimiento de un servicio](monitoring-activity-log-alerts-on-service-notifications.md).
