---
title: Visualización de datos de supervisión remota con Azure Time Series Insights | Microsoft Docs
description: Aprenda a configurar el entorno de Time Series Insights para explorar y analizar los datos de series temporales de la solución de supervisión remota.
author: philmea
manager: timlt
ms.author: philmea
ms.date: 04/29/2018
ms.topic: conceptual
ms.service: iot-accelerators
services: iot-accelerators
ms.openlocfilehash: 10617c129212d8196897af750c02647f0086c8e5
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/20/2018
ms.locfileid: "39185897"
---
# <a name="visualize-remote-monitoring-data-with-time-series-insights"></a>Visualización de datos de supervisión remota con Time Series Insights

Puede que un operador desee ampliar la visualización de datos lista para usar proporcionada por la solución preconfigurada de supervisión remota. El Solution Accelerator ofrece integración lista para usar con TSI. En este tema de procedimientos obtendrá información sobre cómo configurar Time Series Insights para analizar la telemetría de dispositivo y detectar anomalías.

## <a name="prerequisites"></a>Requisitos previos

Para completar este tema de procedimientos, necesitará lo siguiente:

* [Implementación de la solución preconfigurada de supervisión remota](quickstart-remote-monitoring-deploy.md)

## <a name="create-a-consumer-group"></a>Creación de un grupo de consumidores

Debe crear un grupo de consumidores dedicado en el centro de IoT para usarlo en el streaming de datos a Time Series Insights.

> [!NOTE]
> Las aplicaciones usan grupos de consumidores para extraer datos de Azure IoT Hub. Cada grupo de consumidores permite hasta cinco consumidores de salida. Debe crear un grupo de consumidores para cada cinco receptores de salida y puede crear hasta treinta y dos grupos de consumidores.

1. En Azure Portal, haga clic en el botón Cloud Shell.

1. Ejecute el siguiente comando para crear un grupo de consumidores:

```azurecli-interactive
az iot hub consumer-group create --hub-name contosorm30526 --name timeseriesinsights --resource-group ContosoRM
```

## <a name="create-a-new-time-series-insights-environment"></a>Creación de un entorno de Time Series Insights

Azure Time Series Insights es un servicio de análisis, almacenamiento y visualización totalmente administrado que permite administrar los datos de serie temporal a escala de IoT en la nube. Ofrece almacenamiento de datos de serie temporal de escalabilidad masiva y permite explorar y analizar miles de millones de eventos transmitidos desde todo el mundo en cuestión de segundos. Use Time Series Insights para almacenar y administrar terabytes de datos de serie temporal, explorar y visualizar miles de millones de eventos al mismo tiempo, realizar análisis de la causa principal y comparar varios sitios y recursos.

1. Inicie sesión en el [Azure Portal](http://portal.azure.com/).

1. Seleccione **Crear un recurso** > **Internet de las cosas** > **Time Series Insights**.

    ![Nuevo entorno de Time Series Insights](./media/iot-accelerators-time-series-insights/new-time-series-insights.png)

1. Para crear el entorno de Time Series Insights, use los valores de la tabla siguiente:

    | Configuración | Valor |
    | ------- | ----- |
    | Nombre del entorno | En la captura de pantalla siguiente se usa el nombre **contorosrmtsi**. Elija un nombre exclusivo de su elección al completar este paso. |
    | Subscription | Seleccione la suscripción de Azure en el menú desplegable. |
    | Grupos de recursos | **Cree uno nuevo**. Se usa el nombre **ContosoRM**. |
    | Ubicación | Se usa **Este de EE. UU**. Cree el entorno en la misma región que la solución de supervisión remota. |
    | SKU |**S1** |
    | Capacity | **1** |
    | Anclar al panel | **Sí** |

    ![Creación del entorno de Time Series Insights](./media/iot-accelerators-time-series-insights/new-time-series-insights-create.png)

1. Haga clic en **Create**(Crear). El entorno puede tardar unos instantes en crearse.

## <a name="create-event-source"></a>Creación de un origen del evento

Cree un origen del evento para conectarlo al centro de IoT. Asegúrese de utilizar el grupo de consumidores creado en los pasos anteriores. Time Series Insights requiere que cada servicio tenga un grupo de consumidores dedicado que no esté en uso por otro servicio.

1. Vaya al nuevo entorno de Time Series.

1. En el lado izquierdo, seleccione **Orígenes de eventos**.

    ![Visualización de orígenes de eventos](./media/iot-accelerators-time-series-insights/time-series-insights-event-sources.png)

1. Haga clic en **Agregar**.

    ![Adición de orígenes de eventos](./media/iot-accelerators-time-series-insights/time-series-insights-event-sources-add.png)

1. Para configurar el centro de IoT como un nuevo origen del evento, use los valores de la tabla siguiente:

    | Configuración | Valor |
    | ------- | ----- |
    | Nombre de origen de eventos | En la captura de pantalla siguiente se usa el nombre **contosorm-iot-hub**. Use un nombre exclusivo de su elección al completar este paso. |
    | Origen | **IoT Hub** |
    | Opción de importación | **Usar IoT Hub desde suscripciones disponibles** |
    | Id. de suscripción | Seleccione la suscripción de Azure en el menú desplegable. |
    | Nombre de IoT Hub | **contosorma57a6**. Use el nombre del centro de IoT de la solución de supervisión remota. |
    | Nombre de directiva de IoT Hub | **iothubowner** Asegúrese de que la directiva utilizada es una directiva de propietario. |
    | Clave de la directiva de IoT Hub | Este campo se rellena automáticamente. |
    | Grupo de consumidores de IoT Hub | **timeseriesinsights** |
    | Formato de serialización de eventos | **JSON**     | Nombre de la propiedad de marca de tiempo | Déjelo en blanco |

    ![Creación de un origen del evento](./media/iot-accelerators-time-series-insights/time-series-insights-event-source-create.png)

1. Haga clic en **Create**(Crear).

> [!NOTE]
> Si necesita conceder a otros usuarios acceso al explorador de Time Series Insights, puede utilizar estos pasos para [conceder acceso a datos](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-data-access#grant-data-access).

## <a name="time-series-insights-explorer"></a>Explorador de Time Series Insights

El explorador de Time Series Insights es una aplicación web que ayuda a crear visualizaciones de los datos.

1. Seleccione la pestaña **Información general**.

1. Haga clic en **Go To Environment** (Ir al entorno), que abrirá la aplicación web del explorador de Time Series Insights.

    ![Explorador de Time Series Insights](./media/iot-accelerators-time-series-insights/time-series-insights-environment.png)

1. En el panel de selección de tiempo, seleccione **Últimas 12 horas** en el menú rápido de tiempo y haga clic en **Buscar**.

    ![Búsqueda en el explorador de Time Series Insights](./media/iot-accelerators-time-series-insights/time-series-insights-search-time.png)

1. En el panel de términos de la izquierda, seleccione un valor de medida de **temperatura** y una división por valor de **iothub-connection-device-id**.

    ![Consulta en el explorador de Time Series Insights](./media/iot-accelerators-time-series-insights/time-series-insights-query1.png)

1. Haga clic con el botón derecho en el gráfico y seleccione **Explorar eventos**.

    ![Eventos del explorador de Time Series Insights](./media/iot-accelerators-time-series-insights/time-series-insights-explore-events.png)

1. Los eventos se representan en la cuadrícula en formato tabular.

    ![Tabla del explorador de Time Series Insights](./media/iot-accelerators-time-series-insights/time-series-insights-table.png)

1. Haga clic en el botón de la vista de perspectiva.

    ![Perspectiva del explorador de Time Series Insights](./media/iot-accelerators-time-series-insights/time-series-insights-explorer-perspective.png)

1. Haga clic en **Agregar** para crear una consulta en la perspectiva.

    ![Consulta para agregar en el explorador de Time Series Insights](./media/iot-accelerators-time-series-insights/time-series-insights-new-query.png)

1. Seleccione un tiempo rápido de **Últimas 12 horas**, una medida de **humedad** y un valor de división por de **iothub-connection-device-id**.

    ![Consulta en el explorador de Time Series Insights](./media/iot-accelerators-time-series-insights/time-series-insights-query2.png)

1. Haga clic en el botón de vista de perspectiva para ver el panel de las métricas del dispositivo.

    ![Panel del explorador de Time Series Insights](./media/iot-accelerators-time-series-insights/time-series-insights-dashboard.png)

## <a name="next-steps"></a>Pasos siguientes

Para obtener información sobre cómo explorar y consultar datos en el explorador de Time Series Insights, vea [Explorador de Azure Time Series Insights](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-explorer).
