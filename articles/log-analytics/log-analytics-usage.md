---
title: Análisis del uso de datos en Log Analytics | Microsoft Docs
description: Use el panel de uso y costo estimado en Log Analytics para evaluar cuántos datos se envían a Log Analytics e identificar los posibles motivos que podrían provocar aumentos imprevistos.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: 74d0adcb-4dc2-425e-8b62-c65537cef270
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/11/2018
ms.author: magoedte
ms.component: ''
ms.openlocfilehash: c14013121517267445e89f43e228b03ba184f013
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/31/2018
ms.locfileid: "50415269"
---
# <a name="analyze-data-usage-in-log-analytics"></a>Análisis del uso de datos en Log Analytics

> [!NOTE]
> En este artículo se describe cómo analizar el uso de datos de Log Analytics.  Consulte los artículos siguientes para obtener información relacionada.
> - En [Administración de los costos mediante el control del volumen de datos y la retención en Log Analytics](log-analytics-manage-cost-storage.md) se describe cómo controlar los costos al cambiar el período de retención de datos.
> - En [Supervisión del uso y costos estimados](../monitoring-and-diagnostics/monitoring-usage-and-estimated-costs.md) se describe cómo ver el uso y los costos estimados a través de varias características de supervisión de Azure para los distintos modelos de precios. También describe cómo cambiar el modelo de precios.

Log Analytics incluye información sobre la cantidad de datos recopilados, qué orígenes envían los datos y los diferentes tipos de datos enviados.  Use el panel **Uso de Log Analytics** para revisar y analizar el uso de datos. El panel muestra la cantidad de datos que recopila cada solución y cuántos datos envían los equipos.

## <a name="understand-the-usage-dashboard"></a>Comprender el panel Uso
El panel **Uso de Log Analytics** muestra la siguiente información:

- Volumen de datos
    - Data volume over time (Volumen de datos con el tiempo), en función del ámbito temporal actual
    - Data volume by solution (Volumen de datos por solución)
    - Data not associated with a computer (Datos no asociados a un equipo)
- Equipos
    - Computers sending data (Equipos que envían datos)
    - Computers with no data in last 24 hours (Equipos sin datos en las últimas 24 horas)
- Offerings (Ofertas)
    - Insight and Analytics nodes (Nodos Insight y Analytics)
    - Automation and Control nodes (Nodos Automation y Control)
    - Security nodes (Nodos de seguridad)  
- Rendimiento
    - Time taken to collect and index data (Tiempo dedicado a recopilar e indexar datos)  
- Lista de consultas

![Panel de uso y costo](media/log-analytics-usage/usage-estimated-cost-dashboard-01.png)<br>
)

### <a name="to-work-with-usage-data"></a>Para trabajar con datos de uso, siga estos pasos:
1. Inicie sesión en el [Azure Portal](https://portal.azure.com).
2. En Azure Portal, haga clic en **Todos los servicios**. En la lista de recursos, escriba **Log Analytics**. Cuando comience a escribir, la lista se filtrará en función de la entrada. Seleccione **Log Analytics**.<br><br> ![Azure Portal](media/log-analytics-usage/azure-portal-01.png)<br><br>  
3. En la lista de áreas de trabajo de Log Analytics, seleccione un área de trabajo.
4. Seleccione **Uso y costos estimados** en la lista del panel izquierdo.
5. En el panel **Uso y costos estimados**, puede modificar el intervalo de tiempo seleccionando la opción **Tiempo: Últimas 24 horas** y cambiar el intervalo de tiempo.<br><br> ![Intervalo de tiempo](./media/log-analytics-usage/usage-time-filter-01.png)<br><br>
6. Vea las hojas de categoría de uso que muestren áreas que le interesen. Elija una hoja y haga clic en un elemento en ella para ver más detalles en [Búsqueda de registros](log-analytics-log-searches.md).<br><br> ![KPI de uso de datos de ejemplo](media/log-analytics-usage/data-volume-kpi-01.png)<br><br>
7. En el panel Búsqueda de registros, revise los resultados devueltos por la búsqueda.<br><br> ![Búsqueda de registros de uso de ejemplo](./media/log-analytics-usage/usage-log-search-01.png)

## <a name="create-an-alert-when-data-collection-is-higher-than-expected"></a>Creación de una alerta cuando la colección de datos es mayor de lo esperado
En esta sección se describe cómo crear una alerta si:
- El volumen de datos supera una cantidad especificada.
- Se prevé que el volumen de datos supere un importe especificado.

Alertas de Azure admite [alertas de registro](../monitoring-and-diagnostics/monitor-alerts-unified-log.md) que usen consultas de búsqueda. 

La consulta siguiente produce un resultado cuando hay más de 100 GB de datos recopilados en las últimas 24 horas:

`union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize DataGB = sum((Quantity / 1024)) by Type | where DataGB > 100`

La consulta siguiente utiliza una fórmula simple para predecir cuándo se enviarán más de 100 GB de datos en un día: 

`union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize EstimatedGB = sum(((Quantity * 8) / 1024)) by Type | where EstimatedGB > 100`

Para generar una alerta en un volumen de datos diferente, cambie el 100 de las consultas por el número de GB sobre el que desea alertar.

Siga los pasos explicados en [crear una nueva regla de alerta](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md) para recibir una notificación cuando la colección de datos sea mayor de lo previsto.

Al crear la alerta en la primera consulta; cuando hay más de 100 GB de datos en 24 horas, establezca los siguientes valores:  

- **Definición de la condición de alerta**: especifique el área de trabajo de Log Analytics como el destino del recurso.
- **Criterios de alerta** especifique lo siguiente:
   - **Nombre de señal**: seleccione **Custom log search** (Búsqueda de registros personalizada)
   - **Consulta de búsqueda** en `union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize DataGB = sum((Quantity / 1024)) by Type | where DataGB > 100`
   - **Lógica de alerta** está **basada en** *número de resultados* y **Condición** es *Mayor que* un **umbral**  de *0*
   - **Período de tiempo** de *1440* minutos y **Frecuencia de la alerta** cada *60* minutos ya que los datos de uso solo se actualizan una vez por hora.
- **Definición de los detalles de la alerta**: especifique lo siguiente:
   - **Nombre** en *Volumen de datos mayor que 100 GB en 24 horas*
   - **Gravedad** en *Advertencia*

Especifique un [grupo de acciones](../monitoring-and-diagnostics/monitoring-action-groups.md) existente o cree uno nuevo para que cuando la alerta de registro coincida con criterios, se le notifique.

Al crear la alerta para la segunda consulta; cuando se prevé que va a haber más de 100 GB de datos en 24 horas, establezca los siguientes valores:

- **Definición de la condición de alerta**: especifique el área de trabajo de Log Analytics como el destino del recurso.
- **Criterios de alerta** especifique lo siguiente:
   - **Nombre de señal**: seleccione **Custom log search** (Búsqueda de registros personalizada)
   - **Consulta de búsqueda** en `union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize EstimatedGB = sum(((Quantity * 8) / 1024)) by Type | where EstimatedGB > 100`
   - **Lógica de alerta** está **basada en** *número de resultados* y **Condición** es *Mayor que* un **umbral**  de *0*
   - **Período de tiempo** de *180* minutos y **Frecuencia de la alerta** cada *60* minutos ya que los datos de uso solo se actualizan una vez por hora.
- **Definición de los detalles de la alerta**: especifique lo siguiente:
   - **Nombre** en *Volumen de datos que se espera que sea mayor que 100 GB en 24 horas*
   - **Gravedad** en *Advertencia*

Especifique un [grupo de acciones](../monitoring-and-diagnostics/monitoring-action-groups.md) existente o cree uno nuevo para que cuando la alerta de registro coincida con criterios, se le notifique.

Cuando se recibe una alerta, siga los pasos de la sección siguiente para solucionar el problema del uso mayor de lo esperado.

## <a name="troubleshooting-why-usage-is-higher-than-expected"></a>Solución del problema de un uso mayor de lo esperado
El panel de uso le ayuda a identificar por qué el uso (y, por tanto, el costo) es mayor del previsto.

Un mayor uso está provocado por una de estas causas o por ambas:
- Más datos de lo esperado enviados a Log Analytics
- Más nodos de lo previsto que envían datos a Log Analytics

### <a name="check-if-there-is-more-data-than-expected"></a>Compruebe si hay más datos de lo esperado 
Hay dos secciones fundamentales de la página de uso que ayudan a identificar qué es lo que está causando la recopilación de la mayoría de los datos.

El gráfico *Volumen de datos con el tiempo* muestra el volumen total de los datos enviados y los equipos que envían la mayor cantidad de datos. El gráfico de la parte superior permite ver si aumenta el uso de datos general, si permanece constante o disminuye. La lista de equipos muestra los 10 equipos que envían la mayoría de los datos.

El gráfico *Volumen de datos por solución* muestra el volumen de datos que se envía con cada solución y las soluciones que envían la mayoría de los datos. El gráfico de la parte superior muestra el volumen total de datos enviados por cada solución a lo largo del tiempo. Esta información le permite identificar si una solución envía más datos, más o menos la misma cantidad de datos, o menos datos a lo largo del tiempo. La lista de soluciones muestra las 10 soluciones que envían la mayoría de los datos. 

Estos dos gráficos muestran todos los datos. Algunos datos son facturables, mientras que otros son gratis. Para centrarse solo en los datos facturables, modifique la consulta en la página de búsqueda para incluir `IsBillable=true`.  

![gráficos de volumen de datos](./media/log-analytics-usage/log-analytics-usage-data-volume.png)

Eche un vistazo al gráfico *Volumen de datos con el tiempo*. Para ver las soluciones y los tipos de datos que envían la mayoría de los datos a un equipo específico, haga clic en el nombre del equipo. Haga clic en el nombre del primer equipo de la lista.

En la captura de pantalla siguiente, el tipo de datos *Log Management / Perf* envía la mayoría de los datos al equipo.<br><br> ![volumen de datos para un equipo](./media/log-analytics-usage/log-analytics-usage-data-volume-computer.png)<br><br>

Ahora, vuelva al panel *Uso* y mire el gráfico *Volumen de datos por solución*. Para ver los equipos que envían la mayoría de los datos a una solución, haga clic en el nombre de la solución en la lista. Haga clic en el nombre de la primera solución de la lista. 

En la captura de pantalla siguiente, se confirma que el equipo *mycon* envía la mayoría de los datos a la solución Administración de registro.<br><br> ![volumen de datos para una solución](./media/log-analytics-usage/log-analytics-usage-data-volume-solution.png)<br><br>

Si es necesario, realice análisis adicionales para identificar grandes volúmenes dentro de una solución o tipo de datos. Entre las consultas de ejemplo se incluyen:

+ Solución **Security**
  - `SecurityEvent | summarize AggregatedValue = count() by EventID`
+ Solución **Log Management**
  - `Usage | where Solution == "LogManagement" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | summarize AggregatedValue = count() by DataType`
+ Tipo de datos **Perf**
  - `Perf | summarize AggregatedValue = count() by CounterPath`
  - `Perf | summarize AggregatedValue = count() by CounterName`
+ Tipo de datos **Event**
  - `Event | summarize AggregatedValue = count() by EventID`
  - `Event | summarize AggregatedValue = count() by EventLog, EventLevelName`
+ Tipo de datos **Syslog**
  - `Syslog | summarize AggregatedValue = count() by Facility, SeverityLevel`
  - `Syslog | summarize AggregatedValue = count() by ProcessName`
+ Tipo de datos de **AzureDiagnostics**
  - `AzureDiagnostics | summarize AggregatedValue = count() by ResourceProvider, ResourceId`

Use los pasos siguientes para reducir el volumen de registros recopilados:

| Origen del mayor volumen de datos | Cómo reducir el volumen de datos |
| -------------------------- | ------------------------- |
| Eventos de seguridad            | Seleccione los [eventos de seguridad común o mínima](https://blogs.technet.microsoft.com/msoms/2016/11/08/filter-the-security-events-the-oms-security-collects/). <br> Cambie la directiva de auditoría de seguridad para recopilar únicamente los eventos necesarios. En particular, revise la necesidad de recopilar eventos para <br> - [auditar plataforma de filtrado](https://technet.microsoft.com/library/dd772749(WS.10).aspx) <br> - [auditar registro](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941614(v%3dws.10))<br> - [auditar sistema de archivos](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772661(v%3dws.10))<br> - [auditar objeto de kernel](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941615(v%3dws.10))<br> - [auditar manipulación de identificadores](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772626(v%3dws.10))<br> - auditar almacenamiento extraíble |
| contadores de rendimiento       | Cambie la [configuración de los contadores de rendimiento](log-analytics-data-sources-performance-counters.md) para: <br> - Reducir la frecuencia de la colección <br> - Reducir el número de contadores de rendimiento |
| Registros de eventos                 | Cambie la [configuración del registro de eventos](log-analytics-data-sources-windows-events.md) para: <br> - Reducir el número de registros de eventos recopilados <br> - Recopilar solo los niveles de eventos necesarios Por ejemplo, no recopile eventos de nivel de *información*. |
| syslog                     | Cambie la [configuración de syslog](log-analytics-data-sources-syslog.md) para: <br> - Reducir el número de instalaciones recopiladas <br> - Recopilar solo los niveles de eventos necesarios Por ejemplo, no recopile eventos de nivel de *información* y *depuración*. |
| AzureDiagnostics           | Cambie la colección de registros de recursos para: <br> - Reducir el número de registros de recursos enviados a Log Analytics <br> - Recopilar solo los registros necesarios |
| Datos de la solución procedentes de equipos que no necesitan la solución | Use la [selección de destino de solución](../operations-management-suite/operations-management-suite-solution-targeting.md) para recopilar datos solo de los grupos de equipos necesarios. |

### <a name="check-if-there-are-more-nodes-than-expected"></a>Comprobar si hay más nodos de lo esperado
Si está en el plan de tarifa *por nodo (Log Analytics)*, se le cobrará según el número de nodos y soluciones que use. Puede ver cuántos nodos de cada oferta se usan en la sección de *ofertas* del panel de uso.<br><br> ![panel de uso](./media/log-analytics-usage/log-analytics-usage-offerings.png)<br><br>

Haga clic en **Ver todos...**  para ver la lista completa de los equipos que envían datos a la oferta seleccionada.

Use la [selección de destino de solución](../operations-management-suite/operations-management-suite-solution-targeting.md) para recopilar datos solo de los grupos de equipos necesarios.

## <a name="next-steps"></a>Pasos siguientes
* Consulte [Búsquedas de registro en Log Analytics](log-analytics-log-searches.md) para obtener información sobre cómo usar el lenguaje de búsqueda. Puede utilizar las consultas de búsqueda para realizar análisis adicionales sobre los datos de uso.
* Siga los pasos explicados en [Crear una nueva alerta de registro](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md) para recibir una notificación cuando se cumplan los criterios de búsqueda.
* Use la [selección de destino de solución](../operations-management-suite/operations-management-suite-solution-targeting.md) para recopilar datos solo de los grupos de equipos necesarios.
* Para configurar una directiva eficaz de recopilación de eventos de seguridad, revise la [Directiva de filtrado de Azure Security Center](../security-center/security-center-enable-data-collection.md).
* Cambie la [configuración de los contadores de rendimiento](log-analytics-data-sources-performance-counters.md).
* Para modificar la configuración de recopilación de eventos, revise la [configuración de registros de eventos](log-analytics-data-sources-windows-events.md).
* Para modificar la configuración de la recopilación de syslog, revise la [configuración de syslog](log-analytics-data-sources-syslog.md).
