---
title: Registros de Azure Diagnostics | Microsoft Docs
description: Los clientes pueden habilitar el análisis de registros para la red CDN de Azure.
services: cdn
documentationcenter: ''
author: dksimpson
manager: akucer
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2017
ms.author: rli
ms.openlocfilehash: 73c19383b791438c2ae899b45e1b4635e9cd5802
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
---
# <a name="azure-diagnostic-logs"></a>Registros de diagnósticos de Azure

Con los registros de Azure Diagnostics, puede ver análisis básicos y guardarlos en uno o varios destino, por ejemplo:

 - Cuenta de Azure Storage
 - Azure Event Hubs
 - [Área de trabajo de Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-get-started)
 
Esta característica está disponible en puntos de conexión de CDN de todos los planes de tarifa. 

Los registros de Azure Diagnostics le permiten exportar métricas básicas de uso desde su punto de conexión de la red CDN a diversos orígenes, de modo que pueda consumirlas de forma personalizada. Por ejemplo, puede realizar los siguientes tipos de exportación de datos:

- Exportar datos a Blob Storage, exportar a CSV y generar gráficos en Excel.
- Exportar datos a Event Hubs y correlacionarlos con los datos de otros servicios de Azure.
- Exportar datos a Log Analytics y verlos en su propia área de trabajo de Log Analytics

La siguiente ilustración muestra una vista habitual de análisis básico de red CDN de los datos.

![Portal: Registros de diagnóstico](./media/cdn-diagnostics-log/01_OMS-workspace.png)

*Figura 1: Vista de análisis básico de la red CDN*

Para más información sobre los registros de diagnóstico, consulte [Registros de diagnóstico](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs).

## <a name="enable-logging-with-azure-portal"></a>Habilitación del registro con Azure Portal

Siga los pasos siguientes para habilitar el registro con análisis básico de la red CDN:

Inicie sesión en el [Azure Portal](http://portal.azure.com). Si no se ha habilitado ya la red CDN para el flujo de trabajo, [habilítela](cdn-create-new-endpoint.md) antes de continuar.

1. En el portal, vaya a **Perfil de CDN**.
2. Seleccione un perfil de CDN y luego seleccione el punto de conexión de CDN para el que desea habilitar **Registros de diagnóstico**.

    ![Portal: Registros de diagnóstico](./media/cdn-diagnostics-log/02_Browse-to-Diagnostics-logs.png)

3. Seleccione **Registros de diagnóstico** en la sección **Supervisión**.

    ![Portal: Registros de diagnóstico](./media/cdn-diagnostics-log/03_Diagnostics-logs-options.png)

### <a name="enable-logging-with-azure-storage"></a>Habilitación del registro con Azure Storage
    
1. Para usar Azure Storage para almacenar los registros, seleccione **Archivar en una cuenta de almacenamiento**, seleccione **Análisis básico** y luego elija los días de retención en **Retención (días)**. Con una retención de cero días, los registros se almacenan indefinidamente. 
2. Escriba un nombre para la configuración y luego haga clic en **Cuenta de Storage**. Después de haber seleccionado una cuenta de almacenamiento, haga clic en **Guardar**.

![Portal: Registros de diagnóstico](./media/cdn-diagnostics-log/04_Diagnostics-logs-storage.png)

*Figura 2: registro con Azure Storage*

### <a name="logging-with-log-analytics"></a>Registro con Log Analytics

Para usar Log Analytics para almacenar los registros, siga estos pasos:

1. En la hoja **Registros de diagnóstico**, seleccione **Enviar a Log Analytics**. 

    ![Portal: Registros de diagnóstico](./media/cdn-diagnostics-log/05_Ready-to-Configure.png)    

2. Haga clic en **Configurar** para configurar el registro de Log Analytics. En el cuadro de diálogo Áreas de trabajo de OMS, puede seleccionar un área de trabajo anterior o crear una nueva.

    ![Portal: Registros de diagnóstico](./media/cdn-diagnostics-log/06_Choose-workspace.png)

3. Haga clic en **Crear nueva área de trabajo**.

    ![Portal: Registros de diagnóstico](./media/cdn-diagnostics-log/07_Create-new.png)

4. Escriba un nuevo nombre del área de trabajo de Log Analytics. El nombre del área de trabajo de Log Analytics debe ser único y contener solo letras, números y guiones; no se permiten espacios ni caracteres de subrayado. 
5. A continuación, seleccione una suscripción existente, un grupo de recursos (nuevo o existente), una ubicación y un plan de tarifa. Tiene la opción de anclar esta configuración al panel. Haga clic en **Aceptar** para completar la configuración.

    ![Portal: Registros de diagnóstico](./media/cdn-diagnostics-log/08_Workspace-resource.png)

5.  Una vez que se crea el área de trabajo, vuelve a la ventana de registros de diagnóstico. Confirme el nombre de la nueva área de trabajo de Log Analytics.

    ![Portal: Registros de diagnóstico](./media/cdn-diagnostics-log/09_Return-to-logging.png)

    Cuando haya realizado la configuración de Log Analytics, compruebe que ha seleccionado **Análisis básico**.

6. Haga clic en **Save**(Guardar).

7. Para ver la nueva área de trabajo de Log Analytics, vaya al panel de Azure Portal y haga clic en el nombre del área de trabajo de Log Analytics. Haga clic en el icono de Portal de OMS para ver el área de trabajo de Log Analytics. 

    ![Portal: Registros de diagnóstico](./media/cdn-diagnostics-log/11_OMS-dashboard.png) 

    El área de trabajo de Log Analytics ahora está lista para registrar datos. Para consumir estos datos, debe utilizar una [solución de Log Analytics](#consuming-diagnostics-logs-from-a-log-analytics-workspace), que se describe más adelante en este artículo.

Para más información sobre los retrasos de los datos de registro, consulte [Retrasos en el registro de datos](#log-data-delays).

## <a name="enable-logging-with-powershell"></a>Habilitación del registro con PowerShell

En los ejemplos siguientes se muestra cómo habilitar registros de diagnóstico mediante los cmdlets de Azure PowerShell.

### <a name="enabling-diagnostic-logs-in-a-storage-account"></a>Habilitación de registros de diagnóstico en una cuenta de Storage

Inicio de sesión y selección de una suscripción:

    Connect-AzureRmAccount 

    Select-AzureSubscription -SubscriptionId 


Para habilitar los registros de diagnóstico en una cuenta de Storage, use este comando:

```powershell
    Set-AzureRmDiagnosticSetting -ResourceId "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Cdn/profiles/{profileName}/endpoints/{endpointName}" -StorageAccountId "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ClassicStorage/storageAccounts/{storageAccountName}" -Enabled $true -Categories CoreAnalytics
```
Para habilitar los registros de diagnóstico en un área de trabajo de Log Analytics, use este comando:

```powershell
    Set-AzureRmDiagnosticSetting -ResourceId "/subscriptions/`{subscriptionId}<subscriptionId>
    .<subscriptionName>" -WorkspaceId "/subscriptions/<workspaceId>.<workspaceName>" -Enabled $true -Categories CoreAnalytics 
```



## <a name="consuming-diagnostics-logs-from-azure-storage"></a>Consumo de registros de diagnósticos desde Azure Storage
En esta sección se describe el esquema del análisis básico de la red CDN, cómo se organiza dentro de una cuenta de Azure Storage, y se proporciona código de ejemplo para descargar los registros en un archivo CSV.

### <a name="using-microsoft-azure-storage-explorer"></a>Uso del Explorador de Microsoft Azure Storage
Antes de poder acceder a los datos de análisis básico desde la cuenta de Azure Storage, primero necesita una herramienta para acceder a los contenidos de una cuenta de almacenamiento. Aunque hay varias herramientas disponibles en el mercado, la única que recomendamos es el Explorador de Microsoft Azure Storage. Para descargar la herramienta, consulte [Explorador de Azure Storage](http://storageexplorer.com/). Después de descargar e instalar el software, configúrelo para usar la misma cuenta de Azure Storage que configuró como destino para los registros de diagnóstico de la red CDN.

1.  Abra el **Explorador de Microsoft Azure Storage**
2.  Busque la cuenta de almacenamiento.
3.  Vaya al nodo **"Contenedores de blob”** en esta cuenta de almacenamiento y expanda el nodo.
4.  Seleccione el contenedor llamado **"insights-logs-coreanalytics"** y haga doble clic en él
5.  Los resultados se muestran en el panel derecho, comenzando por el primer nivel, que se parece a **"resourceId="**. Siga haciendo clic hasta que vea el archivo **PT1H.json**. Consulte la nota siguiente para ver una explicación de la ruta de acceso.
6.  Cada blob **PT1H.json** representa los registros de análisis durante una hora de un punto de conexión de red CDN concreto o de su dominio personalizado.
7.  El esquema del contenido de este archivo JSON se describe en la sección Esquema de los registros de análisis básico.


> [!NOTE]
> **Formato de ruta de acceso de blob**
> 
> Los registros de análisis básico se generan cada hora y los datos se recopilan y almacenan en un único blob de Azure como una carga JSON. Dado que la herramienta Explorador de Storage interpreta "/" como un separador de directorios y muestra la jerarquía, la ruta de acceso al blob de Azure aparece como si hubiera una estructura jerárquica y representa el nombre del blob. Este nombre del blob sigue la convención de nomenclatura siguiente: 
    
    resourceId=/SUBSCRIPTIONS/{Subscription Id}/RESOURCEGROUPS/{Resource Group Name}/PROVIDERS/MICROSOFT.CDN/PROFILES/{Profile Name}/ENDPOINTS/{Endpoint Name}/ y={Year}/m={Month}/d={Day}/h={Hour}/m={Minutes}/PT1H.json

**Descripción de los campos:**

|Valor|DESCRIPCIÓN|
|-------|---------|
|Id. de suscripción    |Identificador de la suscripción de Azure en formato GUID.|
|Nombre del grupo de recursos |Nombre del grupo de recursos al que pertenecen los recursos de CDN.|
|Nombre del perfil |Nombre del perfil de CDN|
|Nombre del punto de conexión |Nombre del punto de conexión de CDN|
|Year|  Representación del año en formato de cuatro dígitos, por ejemplo, 2017.|
|Mes| Representación del mes en formato de dos dígitos. 01=enero ... 12=diciembre|
|Día|   Representación del día del mes en formato de dos dígitos.|
|PT1H.json| Archivo JSON real donde se almacenan los datos de análisis|

### <a name="exporting-the-core-analytics-data-to-a-csv-file"></a>Exportación de los datos de análisis básico a un archivo CSV

Para facilitar el acceso a análisis básico, se proporciona código de ejemplo para una herramienta. Esta herramienta permite descargar los archivos JSON en un formato plano de archivo separado por comas que se puede usar para crear fácilmente gráficos y otras agregaciones.

A continuación se muestra cómo puede usar la herramienta:

1.  Visite el vínculo de GitHub: [https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv ](https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv )
2.  Descargue el código.
3.  Siga las instrucciones para compilarlo y configurarlo.
4.  Ejecute la herramienta.
5.  El archivo CSV resultante muestra los datos de análisis en una jerarquía sencilla plana.

## <a name="consuming-diagnostics-logs-from-a-log-analytics-workspace"></a>Consumo de registros de diagnóstico desde un área de trabajo de Log Analytics
Log Analytics es un servicio de Azure que supervisa los entornos local y en la nube para mantener su disponibilidad y rendimiento. Recopila los datos generados por los recursos en los entornos local y de nube y mediante otras herramientas de supervisión, para proporcionar análisis entre varios orígenes. 

Para usar Log Analytics, debe [habilitar el registro](#enable-logging-with-azure-storage) en el área de trabajo de Log Analytics de Azure, que se describe anteriormente en este artículo.

### <a name="using-the-log-analytics-workspace"></a>Uso del área de trabajo de Log Analytics

 El siguiente diagrama muestra la arquitectura de las entradas y salidas del repositorio:

![Área de trabajo de Log Analytics](./media/cdn-diagnostics-log/12_Repo-overview.png)

*Figura 3: repositorio de Log Analytics*

Puede mostrar los datos en una variedad de formas mediante el uso de Soluciones de administración. Puede obtener Soluciones de administración en [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/monitoring-management?page=1&subcategories=management-solutions).

Puede instalar las Soluciones de administración de Azure Marketplace, haciendo clic en el vínculo **Obtenerla ahora** en la parte inferior de cada solución.

### <a name="adding-a-log-analytics-cdn-management-solution"></a>Incorporación de una solución de administración de CDN de Log Analytics

Siga estos pasos para agregar una solución de administración:

1.   Si aún no lo ha hecho, inicie sesión en Azure Portal mediante su suscripción de Azure y vaya al panel.
    ![Panel de Azure](./media/cdn-diagnostics-log/13_Azure-dashboard.png)

2. En la hoja **Nuevo**, en **Marketplace**, seleccione **Supervisión y administración**.

    ![Marketplace](./media/cdn-diagnostics-log/14_Marketplace.png)

3. En la hoja **Supervisión y administración**, haga clic en **Ver todo**.

    ![Ver todos](./media/cdn-diagnostics-log/15_See-all.png)

4.  Busque CDN en el cuadro de búsqueda.

    ![Ver todos](./media/cdn-diagnostics-log/16_Search-for.png)

5.  Seleccione **Análisis Básico de la red CDN de Azure**. 

    ![Ver todos](./media/cdn-diagnostics-log/17_Core-analytics.png)

6.  Después de hacer clic en **Crear**, se le pedirá que cree una nueva área de trabajo de Log Analytics o que utilice una ya existente. 

    ![Ver todos](./media/cdn-diagnostics-log/18_Adding-solution.png)

7.  Seleccione el área de trabajo que creó antes. A continuación, debe agregar una cuenta de Automation.

    ![Ver todos](./media/cdn-diagnostics-log/19_Add-automation.png)

8. La siguiente pantalla muestra el formulario de la cuenta de Automation que debe rellenar. 

    ![Ver todos](./media/cdn-diagnostics-log/20_Automation.png)

9. Una vez haya creado la cuenta de Automation, está listo para agregar la solución. Haga clic en el botón **Crear** .

    ![Ver todos](./media/cdn-diagnostics-log/21_Ready.png)

10. La solución se ha agregado al área de trabajo. Vuelva al panel de Azure Portal.

    ![Ver todos](./media/cdn-diagnostics-log/22_Dashboard.png)

    Haga clic en el área de trabajo de Log Analytics que ha creado para ir al área de trabajo. 

11. Haga clic en el icono del **Portal de OMS** para ver la nueva solución.

    ![Ver todos](./media/cdn-diagnostics-log/23_workspace.png)

12. El portal debería tener un aspecto similar al de la siguiente captura de pantalla:

    ![Ver todos](./media/cdn-diagnostics-log/24_OMS-solution.png)

    Haga clic en uno de los iconos para ver las distintas vistas de los datos.

    ![Ver todos](./media/cdn-diagnostics-log/25_Interior-view.png)

    Puede desplazarse a izquierda o derecha para ver más iconos que representan vistas individuales de los datos. 

    Al hacer clic en uno de los iconos, se dan más detalles sobre los datos.

     ![Ver todos](./media/cdn-diagnostics-log/26_Further-detail.png)

### <a name="offers-and-pricing-tiers"></a>Ofertas y planes de tarifa

Puede ver ofertas y planes de tarifa de las soluciones de administración [aquí](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions#offers-and-pricing-tiers).

### <a name="customizing-views"></a>Personalización de las vistas

Puede personalizar la vista de los datos mediante el **Diseñador de vistas**. Para comenzar a diseñar, vaya al área de trabajo de Log Analytics y haga clic en el icono **Diseñador de vistas**.

![Ver diseñador](./media/cdn-diagnostics-log/27_Designer.png)

Puede arrastrar y colocar los tipos de gráficos y rellenar los detalles de los datos que quiere analizar.

![Ver diseñador](./media/cdn-diagnostics-log/28_Designer.png)

    
## <a name="log-data-delays"></a>Retrasos en el registro de datos

En la tabla siguiente se muestran los retrasos de los datos de registro de **Azure CDN Estándar de Microsoft**, **Azure CDN Estándar de Akamai** y **Azure CDN Estándar/Premium de Verizon**.

Retrasos de datos de registro de Microsoft | Retrasos en el registro de datos de Verizon | Retrasos en el registro de datos de Akamai
--- | --- | ---
Retraso de 1 hora. | Se retrasan 1 hora y pueden tardar hasta 2 horas en comenzar a aparecer tras la finalización de la propagación del punto de conexión. | Se retrasan 24 horas, y tardan 2 horas en comenzar a aparecer si se crearon hace más de 24 horas. Si se acaban de crear, los registros tardan en comenzar a aparecer hasta 25 horas.

## <a name="diagnostic-log-types-for-cdn-core-analytics"></a>Tipos de registro de diagnóstico para análisis básico de la red CDN

Actualmente solo se ofrecen los registros de análisis básico que contienen métricas que muestran estadísticas de respuesta HTTP y estadísticas de salida, como se ven desde los servidores POP y perimetrales de la red CDN.

### <a name="core-analytics-metrics-details"></a>Detalles de las métricas de análisis básico
En la tabla siguiente se muestra una lista de métricas disponibles en los registros de análisis principales de **Azure CDN Estándar de Microsoft**, **Azure CDN Estándar de Akamai** y **Azure CDN Estándar/Premium de Verizon**. No todas las métricas están disponibles en todos los proveedores, si bien tales diferencias son mínimas. En la tabla también se muestra si un proveedor dispone de una métrica determinada. Tenga en cuenta que las métricas solo están disponibles para esos puntos de conexión de CDN que contienen tráfico.


|Métrica                     | DESCRIPCIÓN | Microsoft | Verizon | Akamai |
|---------------------------|-------------|----------|---------|--------|
| RequestCountTotal         | Número total de aciertos de solicitud durante este periodo. | Sí | Sí |Sí |
| RequestCountHttpStatus2xx | Recuento de todas las solicitudes que dieron lugar a un código HTTP 2xx (por ejemplo, 200, 202). | Sí | Sí |Sí |
| RequestCountHttpStatus3xx | Recuento de todas las solicitudes que dieron lugar a un código HTTP 3xx (por ejemplo, 300, 302). | Sí | Sí |Sí |
| RequestCountHttpStatus4xx | Recuento de todas las solicitudes que dieron lugar a un código HTTP 4xx (por ejemplo, 400, 404). | Sí | Sí |Sí |
| RequestCountHttpStatus5xx | Recuento de todas las solicitudes que dieron lugar a un código HTTP 5xx (por ejemplo, 500, 504). | Sí | Sí |Sí |
| RequestCountHttpStatusOthers | Recuento de todos los demás códigos HTTP (fuera del intervalo 2xx-5xx). | Sí | Sí |Sí |
| RequestCountHttpStatus200 | Recuento de todas las solicitudes que dieron lugar a una respuesta de código HTTP 200. | Sí | Sin   |Sí |
| RequestCountHttpStatus206 | Recuento de todas las solicitudes que dieron lugar a una respuesta de código HTTP 206. | Sí | Sin   |Sí |
| RequestCountHttpStatus302 | Recuento de todas las solicitudes que dieron lugar a una respuesta de código HTTP 302. | Sí | Sin   |Sí |
| RequestCountHttpStatus304 | Recuento de todas las solicitudes que dieron lugar a una respuesta de código HTTP 304. | Sí | Sin   |Sí |
| RequestCountHttpStatus404 | Recuento de todas las solicitudes que dieron lugar a una respuesta de código HTTP 404. | Sí | Sin   |Sí |
| RequestCountCacheHit | Recuento de todas las solicitudes que dieron lugar a un acierto de caché. El recurso se atendió directamente desde el servidor POP al cliente. | Sí | Sí | Sin   |
| RequestCountCacheMiss | Recuento de todas las solicitudes que dieron lugar a un error de caché. Esto significa que el recurso no se encontró en el servidor POP más cercano al cliente y, por tanto, se recupera del origen. | Sí | Sí | Sin  |
| RequestCountCacheNoCache | Recuento de todas las solicitudes a un recurso a las que se les impide almacenarse en caché debido a una configuración de usuario en el servidor perimetral. | Sí | Sí | Sin  |
| RequestCountCacheUncacheable | Recuento de todas las solicitudes a recursos cuyos encabezados Cache-Control y Expires impiden que se almacenen en caché. Estos encabezados indican que no se deben almacenar en caché en un servidor POP o por el cliente HTTP. | Sí | Sí | Sin  |
| RequestCountCacheOthers | Recuento de todas las solicitudes con un estado de caché no cubierto por lo anterior. | Sin  | Sí | Sin   |
| EgressTotal | Transferencia de datos salientes en GB | Sí |Sí |Sí |
| EgressHttpStatus2xx | Transferencia de datos salientes* para respuestas con códigos de estado HTTP 2xx en GB. | Sí | Sí | Sin   |
| EgressHttpStatus3xx | Transferencia de datos salientes para respuestas con códigos de estado HTTP 3xx en GB. | Sí | Sí | Sin   |
| EgressHttpStatus4xx | Transferencia de datos de salida para respuestas con códigos de estado HTTP 4xx en GB. | Sí | Sí | Sin   |
| EgressHttpStatus5xx | Transferencia de datos de salida para respuestas con códigos de estado HTTP 5xx en GB. | Sí | Sí | Sin  |
| EgressHttpStatusOthers | Transferencia de datos de salida para respuestas con otros códigos de estado HTTP en GB. | Sí | Sí | Sin   |
| EgressCacheHit | Transferencia de datos de salida para respuestas que se entregaron directamente desde la caché de CDN en los servidores POP/perimetrales de CDN. | Sí | Sí | Sin  |
| EgressCacheMiss. | Transferencia de datos de salida para respuestas que no se encontraron en el servidor POP más cercano y que se recuperaron del servidor de origen. | Sí | Sí | Sin  |
| EgressCacheNoCache | Transferencia de datos de salida para recursos a los que se les impide almacenarse en caché debido a una configuración de usuario en el servidor perimetral. | Sí | Sí | Sin  |
| EgressCacheUncacheable | Transferencia de datos de salida para recursos cuyos encabezados Cache-Control o Expires impiden que se almacenen en caché. Indica que no se debería almacenar en caché en un servidor POP o por el cliente HTTP. | Sí | Sí | Sin  |
| EgressCacheOthers | Transferencias de datos de salida para otros escenarios de caché. | Sin  | Sí | Sin  |

\*Con transferencia de datos de salida nos referimos al tráfico entregado al cliente desde los servidores POP de la red CDN.


### <a name="schema-of-the-core-analytics-logs"></a>Esquema de los registros de análisis básico 

Todos los registros se almacenan en formato JSON y cada entrada tiene campos de cadena que siguen el siguiente esquema:

```json
    "records": [
        {
            "time": "2017-04-27T01:00:00",
            "resourceId": "<ARM Resource Id of the CDN Endpoint>",
            "operationName": "Microsoft.Cdn/profiles/endpoints/contentDelivery",
            "category": "CoreAnalytics",
            "properties": {
                "DomainName": "<Name of the domain for which the statistics is reported>",
                "RequestCountTotal": integer value,
                "RequestCountHttpStatus2xx": integer value,
                "RequestCountHttpStatus3xx": integer value,
                "RequestCountHttpStatus4xx": integer value,
                "RequestCountHttpStatus5xx": integer value,
                "RequestCountHttpStatusOthers": integer value,
                "RequestCountHttpStatus200": integer value,
                "RequestCountHttpStatus206": integer value,
                "RequestCountHttpStatus302": integer value,
                "RequestCountHttpStatus304": integer value,
                "RequestCountHttpStatus404": integer value,
                "RequestCountCacheHit": integer value,
                "RequestCountCacheMiss": integer value,
                "RequestCountCacheNoCache": integer value,
                "RequestCountCacheUncacheable": integer value,
                "RequestCountCacheOthers": integer value,
                "EgressTotal": double value,
                "EgressHttpStatus2xx": double value,
                "EgressHttpStatus3xx": double value,
                "EgressHttpStatus4xx": double value,
                "EgressHttpStatus5xx": double value,
                "EgressHttpStatusOthers": double value,
                "EgressCacheHit": double value,
                "EgressCacheMiss": double value,
                "EgressCacheNoCache": double value,
                "EgressCacheUncacheable": double value,
                "EgressCacheOthers": double value,
            }
        }

    ]
}
```

Donde *time* representa la hora de inicio del límite horario cuyas estadísticas se notifican. Cuando un proveedor de CDN no admite una métrica, en lugar de un valor doble o entero, hay un valor nulo. Este valor nulo indica la ausencia de una métrica, y esto es diferente de un valor de 0. Hay un conjunto de estas métricas por dominio configurado en el punto de conexión.

Propiedades de ejemplo:

```json
{
     "DomainName": "manlingakamaitest2.azureedge.net",
     "RequestCountTotal": 480,
     "RequestCountHttpStatus2xx": 480,
     "RequestCountHttpStatus3xx": 0,
     "RequestCountHttpStatus4xx": 0,
     "RequestCountHttpStatus5xx": 0,
     "RequestCountHttpStatusOthers": 0,
     "RequestCountHttpStatus200": 480,
     "RequestCountHttpStatus206": 0,
     "RequestCountHttpStatus302": 0,
     "RequestCountHttpStatus304": 0,
     "RequestCountHttpStatus404": 0,
     "RequestCountCacheHit": null,
     "RequestCountCacheMiss": null,
     "RequestCountCacheNoCache": null,
     "RequestCountCacheUncacheable": null,
     "RequestCountCacheOthers": null,
     "EgressTotal": 0.09,
     "EgressHttpStatus2xx": null,
     "EgressHttpStatus3xx": null,
     "EgressHttpStatus4xx": null,
     "EgressHttpStatus5xx": null,
     "EgressHttpStatusOthers": null,
     "EgressCacheHit": null,
     "EgressCacheMiss": null,
     "EgressCacheNoCache": null,
     "EgressCacheUncacheable": null,
     "EgressCacheOthers": null
}

```

## <a name="additional-resources"></a>Recursos adicionales

* [Registros de diagnóstico de Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)
* [Análisis básico mediante el portal complementario de la red CDN](https://docs.microsoft.com/azure/cdn/cdn-analyze-usage-patterns)
* [Azure Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)
* [API de REST de Azure Log Analytics](https://docs.microsoft.com/rest/api/loganalytics)







