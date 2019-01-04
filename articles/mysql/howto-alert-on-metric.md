---
title: Configuración de alertas de métricas para Azure Database for MySQL en Azure Portal
description: En este artículo se describe cómo configurar las alertas de métricas de Azure Database for MySQL, y obtener acceso a ellas, mediante Azure Portal.
author: rachel-msft
ms.author: raagyema
ms.service: mysql
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: 999b1d03ad8cb0b27de10ff6457c0e6cc9112ee7
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/17/2018
ms.locfileid: "53548738"
---
# <a name="use-the-azure-portal-to-set-up-alerts-on-metrics-for-azure-database-for-mysql"></a>Uso de Azure Portal para configurar alertas de métricas para Azure Database for MySQL 

En este artículo se explica cómo configurar alertas de Azure Database for MySQL mediante Azure Portal. Puede recibir una alerta basada en las métricas de supervisión para los servicios de Azure.

La alerta se desencadena cuando el valor de una métrica especificada cruza el umbral que se ha asignado. La alerta se desencadena tanto la primera vez que se cumple la condición como después, cuando dicha condición deja de cumplirse. 

Puede configurar una alerta para realizar las siguientes acciones cuando se desencadene:
* Enviar notificaciones de correo electrónico al administrador y los coadministradores del servicio.
* Enviar un correo electrónico a direcciones de correo electrónico adicionales que especifique.
* Llamar a un webhook

Puede obtener información sobre las reglas de alerta y configurarlas mediante:
* [Azure Portal](../monitoring-and-diagnostics/insights-alerts-portal.md)
* [PowerShell](../azure-monitor/platform/alerts-classic-portal.md)
* [Interfaz de la línea de comandos (CLI)](../azure-monitor/platform/alerts-classic-portal.md)
* [API de REST de Azure Monitor](https://msdn.microsoft.com/library/azure/dn931945.aspx)

## <a name="create-an-alert-rule-on-a-metric-from-the-azure-portal"></a>Creación de una regla de alerta sobre una métrica desde Azure Portal
1. En [Azure Portal](https://portal.azure.com/), seleccione el servidor de Azure Database for MySQL que quiera supervisar.

2. En la sección **Supervisión** de la barra lateral, seleccione **Reglas de alerta** como se muestra a continuación:

   ![Selección de Reglas de alerta](./media/howto-alert-on-metric/1-alert-rules.png)

3. Seleccione **Agregar alerta de métrica** (icono +). 

4. Se abre la página **Agregar regla**, como se muestra a continuación.  Rellene la información necesaria:

   ![Formulario de adición de alerta de métrica](./media/howto-alert-on-metric/2-add-rule-form.png)

   | Configuración | DESCRIPCIÓN  |
   |---------|---------|
   | NOMBRE | Especifique un nombre para la regla de alerta. Este valor se envía en el correo electrónico de notificación de alerta. |
   | DESCRIPCIÓN | Proporcione una descripción breve de la regla de alerta. Este valor se envía en el correo electrónico de notificación de alerta. |
   | Alerta activada | Elija **Métricas** para este tipo de alerta. |
   | Subscription | Este campo se rellena previamente con la suscripción que hospeda Azure Database for MySQL. |
   | Grupos de recursos | Este campo se rellena previamente con el grupo de recursos de Azure Database for MySQL. |
   | Recurso | Este campo se rellena previamente con el nombre de Azure Database for MySQL. |
   | Métrica | Seleccione la métrica para la que quiere emitir una alerta. Por ejemplo, **Porcentaje de almacenamiento**. |
   | Condición | Elija la condición para la métrica con la que va a realizar la comparación. Por ejemplo, **Mayor que**. |
   | Umbral | Valor de umbral de la métrica, por ejemplo, 85 (porcentaje). |
   | Período | Período de tiempo que debe cumplir la regla de métrica antes de que se desencadene la alerta. Por ejemplo, **En los últimos 30 minutos**. |

   Según el ejemplo, la alerta busca un porcentaje de almacenamiento por encima del 85 % durante un período de 30 minutos. Esa alerta se desencadena cuando el porcentaje de almacenamiento medio se mantiene por encima el 85 % durante 30 minutos. Una vez que se desencadena por primera vez, se vuelve a desencadenar cuando el porcentaje de almacenamiento medio está por debajo del 85 % durante más de 30 minutos.

5. Elija el método de notificación que quiera para la regla de alerta. 

   Active la opción **Enviar correo electrónico a propietarios, colaboradores y lectores** si quiere que se envíe un correo electrónico a los administradores y coadministradores de la suscripción cuando se active la alerta.

   Si quiere enviar una notificación a otras direcciones de correo electrónico cuando se active la alerta, agréguelas en el campo **Correos electrónicos adicionales del administrador**. Separe las direcciones de correo electrónico con punto y coma, de la siguiente manera: *email@contoso.com;email2@contoso.com*

   También puede indicar un identificador URI válido en el campo **Webhook** si quiere llamarlo cuando se active la alerta.

6. Seleccione **Aceptar** para crear la alerta.

   En cuestión de minutos, se activa la alerta y se desencadena tal como se describió anteriormente.

## <a name="manage-your-alerts"></a>Administración de alertas
Una vez que haya creado una alerta, puede seleccionarla y realizar las acciones siguientes:

* Ver un gráfico que muestre el umbral de las métricas y los valores reales del día anterior en relación con esta alerta.
* **Editar** o **eliminar** la regla de alerta.
* **Deshabilitar** o **habilitar** la alerta, si quiere detener temporalmente o reanudar la recepción de notificaciones.


## <a name="next-steps"></a>Pasos siguientes
* Obtenga más información sobre cómo [configurar webhooks en las alertas](../azure-monitor/platform/alerts-webhooks.md).
* Obtenga [información general sobre la colección de métricas](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) para garantizar que el servicio está disponible y que responder adecuadamente.
