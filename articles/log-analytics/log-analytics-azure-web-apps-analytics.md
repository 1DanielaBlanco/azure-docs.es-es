---
title: Visualización de datos de Azure Web Apps Analytics | Documentos de Microsoft
description: Puede usar la solución Azure Web Apps Analytics para obtener información sobre Azure Web Apps mediante la recopilación de distintas métricas en todos los recursos de aplicaciones web de Azure.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: 20ff337f-b1a3-4696-9b5a-d39727a94220
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/26/2018
ms.author: magoedte
ms.component: na
ms.openlocfilehash: 7915a255c24fc33cfa489354b49596ca0feec473
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/29/2018
ms.locfileid: "37128952"
---
# <a name="view-analytic-data-for-metrics-across-all-your-azure-web-app-resources"></a>Visualización de datos de análisis para métricas en todos los recursos de aplicaciones web de Azure

![Símbolo de Web Apps](./media/log-analytics-azure-web-apps-analytics/azure-web-apps-analytics-symbol.png)  

> [!NOTE]
> La solución Azure Web Apps Analytics está en desuso.  Los clientes que ya tengan instalada la solución pueden seguir utilizándola, pero no se puede agregar Azure Web Apps Analytics a las nuevas áreas de trabajo.  Para supervisar la aplicación web, le recomendamos que use [Application Insights](../application-insights/app-insights-overview.md). 

La solución Azure Web Apps Analytics (versión preliminar) proporciona información sobre [Azure Web Apps](../app-service/app-service-web-overview.md) mediante la recopilación de distintas métricas en todos los recursos de aplicaciones web de Azure. Con la solución, puede analizar y buscar datos de métricas de recursos de aplicaciones web.

Con la solución, puede ver:

- Las instancias de Web Apps principales con el mayor tiempo de respuesta
- El número de solicitudes en Web Apps, incluidas las solicitudes correctas y con errores
- Las principales instancias de Web Apps con mayor tráfico entrante y saliente
- Los principales planes de servicio con un uso intensivo de CPU y memoria
- Operaciones de registro de actividad de Azure Web Apps

## <a name="connected-sources"></a>Orígenes conectados

A diferencia de la mayoría de las demás soluciones Log Analytics, los agentes no recopilan datos para Azure Web Apps. Todos los datos usados por la solución proceden directamente de Azure.

| Origen conectado | Compatible | DESCRIPCIÓN |
| --- | --- | --- |
| [Agentes de Windows](log-analytics-windows-agent.md) | Sin  | La solución no recopila información de los agentes de Windows. |
| [Agentes de Linux](log-analytics-linux-agents.md) | Sin  | La solución no recopila información de los agentes de Linux. |
| [Grupo de administración de SCOM](log-analytics-om-agents.md) | Sin  | La solución no recopila información de los agentes de un grupo de administración SCOM conectado. |
| [Cuenta de Almacenamiento de Azure](log-analytics-azure-storage.md) | Sin  | La solución no recopila información de Azure Storage. |

## <a name="prerequisites"></a>requisitos previos

- Para acceder a la información de métricas de recursos de Azure Web Apps, debe tener una suscripción de Azure.

## <a name="configuration"></a>Configuración

Realice los pasos siguientes para configurar la solución Azure Web Apps Analytics para las áreas de trabajo.

1. [Habilite el registro de las métricas de recursos de Azure en Log Analytics mediante PowerShell](https://blogs.technet.microsoft.com/msoms/2017/01/17/enable-azure-resource-metrics-logging-using-powershell).

La solución Azure Web Apps Analytics recopila dos conjuntos de métricas de Azure:

- Métricas de Azure Web Apps
  - Espacio de trabajo de memoria promedio
  - Tiempo de respuesta promedio
  - Bytes enviados/recibidos
  - Tiempo de CPU
  - Requests
  - Espacio de trabajo de memoria
  - Httpxxx
- Métricas del plan de App Service
  - Bytes enviados/recibidos
  - Porcentaje de CPU
  - Longitud de la cola de disco
  - Longitud de la cola HTTP
  - Porcentaje de memoria

Solo se recopilan las métricas del plan de App Service si utiliza un plan de servicio dedicado. Esto no se aplica a los planes de App Service gratis o compartidos.

Después de configurar la solución, los datos deben comenzar a fluir hacia el área de trabajo en unos 15 minutos.

## <a name="using-the-solution"></a>Uso de la solución

Al agregar la solución Azure Web Apps Analytics al área de trabajo, el icono **Azure Web Apps Analytics** se agrega al panel Información general. Este icono muestra un recuento del número de Azure Web Apps a las que tiene acceso la solución en su suscripción de Azure.

![Icono de Azure Web Apps Analytics](./media/log-analytics-azure-web-apps-analytics/azure-web-apps-analytics-tile.png)

### <a name="view-azure-web-apps-analytics-information"></a>Visualización de la información de Azure Web Apps Analytics

Haga clic en el icono **Azure Web Apps Analytics** para abrir el panel de **Azure Web Apps Analytics**. El panel incluye las hojas de la tabla siguiente. Cada hoja muestra hasta diez elementos que coinciden con los criterios de esa hoja para el ámbito e intervalo de tiempo especificados. Puede ejecutar una búsqueda de registros que devuelva todos los registros si hace clic en **Ver todo** en la parte inferior de la hoja o si hace clic en el encabezado de esta.


| Columna | DESCRIPCIÓN |
| --- | --- |
| Azure Web Apps |   |
| Tendencias de solicitud de Web Apps | Muestra un gráfico de líneas de la tendencia de solicitud de Web Apps para el intervalo de fechas seleccionado y muestra una lista de las diez principales solicitudes web. Haga clic en el gráfico de líneas para ejecutar una búsqueda de registros de <code>AzureMetrics &#124; where ResourceId == "/MICROSOFT.WEB/SITES/" and (MetricName == "Requests" or MetricName startswith_cs "Http") &#124; summarize AggregatedValue = avg(Average) by MetricName, bin(TimeGenerated, 1h)</code> <br>Haga clic en un elemento de la solicitud web para ejecutar una búsqueda de registros de la tendencia de métrica de solicitud web que lo soliciten. |
| Tiempo de respuesta de Web Apps | Muestra un gráfico de líneas del tiempo de respuesta de Web Apps para el intervalo de fechas que haya seleccionado. También muestra una lista de los diez principales tiempos de respuesta de Web Apps. Haga clic en el gráfico para ejecutar una búsqueda de registros de <code>AzureMetrics &#124; where ResourceId == "/MICROSOFT.WEB/SITES/" and MetricName == "AverageResponseTime" &#124; summarize AggregatedValue = avg(Average) by Resource, bin(TimeGenerated, 1h)</code><br> Haga clic en una aplicación web para ejecutar una búsqueda de registros que devuelva los tiempos de respuesta de la aplicación web. |
| Tráfico de Web Apps | Muestra un gráfico de líneas para el tráfico de Web Apps, en MB y muestra el tráfico principal de Web Apps. Haga clic en el gráfico para ejecutar una búsqueda de registros de <code>AzureMetrics &#124; where ResourceId == "/MICROSOFT.WEB/SITES/" and (MetricName == "BytesSent" or MetricName == "BytesReceived") &#124; summarize AggregatedValue = sum(Average) by Resource, bin(TimeGenerated, 1h)</code><br> Muestra todas las instancias de Web Apps con tráfico durante el último minuto. Haga clic en una aplicación web para ejecutar una búsqueda de registros que muestren los bytes recibidos y enviados a la aplicación web. |
| Planes de Azure App Service |   |
| Planes de App Service con un uso de CPU &gt; 80 % | Muestra el número total de planes de App Service que tienen un uso de CPU mayor que el 80 % y enumera los 10 planes de App Service principales por uso de CPU. Haga clic en el área total para ejecutar una búsqueda de registros de <code>AzureMetrics &#124; where ResourceId == "/MICROSOFT.WEB/SERVERFARMS/" and MetricName == "CpuPercentage" &#124; summarize AggregatedValue = avg(Average) by Resource</code><br> Muestra una lista de los planes de App Service y su uso de CPU promedio. Haga clic en un plan de App Service para ejecutar una búsqueda de registros que muestre su uso de CPU promedio. |
| Planes de App Service con un uso de memoria &gt; 80 % | Muestra el número total de planes de App Service que tienen un uso de memoria mayor que el 80 % y enumera los 10 planes de App Service principales por uso de memoria. Haga clic en el área total para ejecutar una búsqueda de registros de <code>AzureMetrics &#124; where ResourceId == "/MICROSOFT.WEB/SERVERFARMS/" and MetricName == "MemoryPercentage" &#124; summarize AggregatedValue = avg(Average) by Resource</code><br> Muestra una lista de los planes de App Service y su uso de memoria promedio. Haga clic en un plan de App Service para ejecutar una búsqueda de registros que muestre la utilización de memoria promedio. |
| Registros de actividad de Azure Web Apps |   |
| Auditoría de actividad de Azure Web Apps | Muestra el número total de Web Apps con [registros de actividad](log-analytics-activity.md) y enumera las 10 primeras operaciones de registro de actividad. Haga clic en el área total para ejecutar una búsqueda de registros de <code>AzureActivity #124; where ResourceProvider == "Azure Web Sites" #124; summarize AggregatedValue = count() by OperationName</code><br> Muestra una lista de las operaciones de registro de actividad. Haga clic en una operación de registro de actividad para ejecutar una búsqueda de registros que enumere los registros de la operación. |



### <a name="azure-web-apps"></a>Azure Web Apps

En el panel, puede explorar en profundidad para obtener más información sobre las métricas de Web Apps. Este primer conjunto de hojas muestra la tendencia de las solicitudes de Web Apps, el número de errores (por ejemplo, HTTP404), el tráfico y el tiempo medio de respuesta con el tiempo. También muestra un desglose de esas métricas para las diferentes Web Apps.

![Hojas de Azure Web Apps](./media/log-analytics-azure-web-apps-analytics/web-apps-dash01.png)

La razón principal para mostrar esos datos es para identificar una aplicación web con un elevado tiempo de respuesta e investigar para averiguar la causa raíz. También se aplica un límite de umbral para poder identificar más fácilmente las aplicaciones con problemas.

- Las instancias de Web Apps que se muestran en rojo tienen un tiempo de respuesta superior a 1 segundo.
- Las instancias de Web Apps que se muestran en color naranja tienen un tiempo de respuesta superior a 0,7 segundos e inferior a 1 segundo.
- Las instancias de Web Apps que se muestran en verde tienen un tiempo de respuesta inferior a 0,7 segundos.

En la imagen de ejemplo de búsqueda de registros siguiente puede ver que la aplicación web *anugup3* tenía un tiempo de respuesta mucho mayor que las otras aplicaciones web.

![ejemplo de búsqueda de registros](./media/log-analytics-azure-web-apps-analytics/web-app-search-example.png)

### <a name="app-service-plans"></a>Planes de App Service

Si está usando planes de servicio dedicados, también puede recopilar métricas para los planes de App Service. En esta vista, verá los planes de App Service con un uso intensivo de CPU o memoria (&gt; 80 %). También muestra los principales servicios de aplicaciones con un uso intensivo de memoria o CPU. De igual forma, se aplica un límite de umbral para poder identificar más fácilmente las aplicaciones con problemas.

- Los planes de App Service que se muestran en rojo tienen un uso de CPU/memoria superior al 80 %.
- Los planes de App Service que se muestran en naranja tienen un uso de CPU/memoria superior al 60 % e inferior al 80 %.
- Los planes de App Service que se muestran en verde tienen un uso de CPU/memoria inferior al 60 %.

![Hojas de planes de Azure App Service](./media/log-analytics-azure-web-apps-analytics/web-apps-dash02.png)

## <a name="azure-web-apps-log-searches"></a>Búsquedas de registros de Azure Web Apps

La **lista de consultas populares de búsqueda de Azure Web Apps** muestra todos los registros de actividad relacionados para Web Apps, que proporcionan información sobre las operaciones que se realizaron en los recursos de Web Apps. También se muestran todas las operaciones relacionadas y el número de veces que se han ocurrido.

Con cualquiera de las consultas de búsqueda de registros como punto de partida, puede crear fácilmente una alerta. Por ejemplo, puede crear una alerta cuando el tiempo medio de respuesta de una métrica es mayor de 1 segundo.

## <a name="next-steps"></a>Pasos siguientes

- Cree un [alerta](log-analytics-alerts-creating.md) para una métrica específica.
- Use [Búsqueda de registros](log-analytics-log-searches.md) para ver información detallada de los registros de actividad.
