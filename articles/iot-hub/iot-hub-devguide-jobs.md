---
title: Conceptos de trabajos de IoT Hub de Azure | Microsoft Docs
description: 'Guía de desarrollador: programación de trabajos para su ejecución en varios dispositivos conectados al centro de IoT Hub. Trabajos pueden actualizar las etiquetas y propiedades que desee y llamar a métodos directas en varios dispositivos.'
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.author: dobett
ms.openlocfilehash: 460c7d24b2810de41e20ea803ded2ea988613f10
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/24/2018
ms.locfileid: "39223803"
---
# <a name="schedule-jobs-on-multiple-devices"></a>Programación de trabajos en varios dispositivos

Azure IoT Hub habilita cierto número de bloques de creación, como [etiquetas y propiedades de dispositivos gemelos][lnk-twin-devguide] y [métodos directos][lnk-dev-methods].  Normalmente, las aplicaciones de back-end permiten a los administradores y operadores de dispositivos actualizar e interactuar con dispositivos de IoT de forma masiva y a la hora programada.  Los trabajos ejecutan las actualizaciones de los dispositivos gemelos y los métodos directos con un conjunto de dispositivos a la hora programada.  Por ejemplo, un operador puede usar una aplicación de back-end que inicie y realice un seguimiento de un trabajo para reiniciar un conjunto de dispositivos en la compilación 43 y la planta 3 sin perturbar las operaciones de la creación.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Le recomendamos que tenga en cuenta la posibilidad de usar trabajos cuando necesite programar y realizar un seguimiento del progreso de cualquiera de las siguientes actividades en un conjunto de dispositivos:

* Actualizar las propiedades deseadas
* Actualizar etiquetas
* Invocar métodos directos

## <a name="job-lifecycle"></a>Ciclo de vida de trabajo
Los trabajos se inician mediante el back-end de solución y se mantienen mediante IoT Hub.  Puede iniciar un trabajo a través de un URI orientado a servicios (`{iot hub}/jobs/v2/{device id}/methods/<jobID>?api-version=2016-11-14`) y una consulta para el progreso de un trabajo en ejecución a través de un URI orientado a servicios (`{iot hub}/jobs/v2/<jobId>?api-version=2016-11-14`). Para actualizar el estado de los trabajos en ejecución una vez que se inicia un trabajo, ejecute una consulta de trabajo.

> [!NOTE]
> Cuando se inicia un trabajo, los valores y los nombres de propiedad solo pueden contener caracteres alfanuméricos US-ASCII imprimibles, excepto los del siguiente conjunto: `$ ( ) < > @ , ; : \ " / [ ] ? = { } SP HT`.

## <a name="jobs-to-execute-direct-methods"></a>Trabajos para ejecutar métodos directos
El siguiente fragmento de código muestra los detalles de la solicitud HTTP 1.1 para ejecutar un [método directo][lnk-dev-methods] en un conjunto de dispositivos mediante un trabajo:

    PUT /jobs/v2/<jobId>?api-version=2016-11-14

    Authorization: <config.sharedAccessSignature>
    Content-Type: application/json; charset=utf-8
    Request-Id: <guid>
    User-Agent: <sdk-name>/<sdk-version>

    {
        jobId: '<jobId>',
        type: 'scheduleDirectRequest', 
        cloudToDeviceMethod: {
            methodName: '<methodName>',
            payload: <payload>,                 
            responseTimeoutInSeconds: methodTimeoutInSeconds 
        },
        queryCondition: '<queryOrDevices>', // query condition
        startTime: <jobStartTime>,          // as an ISO-8601 date string
        maxExecutionTimeInSeconds: <maxExecutionTimeInSeconds>        
    }

La condición de consulta puede estar también en un solo identificador de dispositivo o en una lista de identificadores de dispositivo, como se muestra en los ejemplos siguientes:

```
queryCondition = "deviceId = 'MyDevice1'"
queryCondition = "deviceId IN ['MyDevice1','MyDevice2']"
queryCondition = "deviceId IN ['MyDevice1']
```
[Lenguaje de consulta de IoT Hub][lnk-query] aborda el lenguaje de consulta de IoT Hub con más detalle.

## <a name="jobs-to-update-device-twin-properties"></a>Trabajos para actualizar las propiedades del dispositivo gemelo
El siguiente fragmento de código muestra los detalles de la solicitud HTTP 1.1 para la actualización de las propiedades del dispositivo gemelo con un trabajo:

    PUT /jobs/v2/<jobId>?api-version=2016-11-14
    Authorization: <config.sharedAccessSignature>
    Content-Type: application/json; charset=utf-8
    Request-Id: <guid>
    User-Agent: <sdk-name>/<sdk-version>

    {
        jobId: '<jobId>',
        type: 'scheduleTwinUpdate', 
        updateTwin: <patch>                 // Valid JSON object
        queryCondition: '<queryOrDevices>', // query condition
        startTime: <jobStartTime>,          // as an ISO-8601 date string
        maxExecutionTimeInSeconds: <maxExecutionTimeInSeconds>        // format TBD
    }

## <a name="querying-for-progress-on-jobs"></a>Consulta del progreso de los trabajos
El siguiente fragmento de código muestra los detalles de la solicitud HTTPS 1.1 para consultar trabajos:

    GET /jobs/v2/query?api-version=2016-11-14[&jobType=<jobType>][&jobStatus=<jobStatus>][&pageSize=<pageSize>][&continuationToken=<continuationToken>]

    Authorization: <config.sharedAccessSignature>
    Content-Type: application/json; charset=utf-8
    Request-Id: <guid>
    User-Agent: <sdk-name>/<sdk-version>

Se proporciona el continuationToken de la respuesta.  

Puede consultar el estado de ejecución del trabajo en cada dispositivo mediante el [lenguaje de consulta de IoT Hub para dispositivos gemelos, trabajos y enrutamiento de mensajes][lnk-query].

## <a name="jobs-properties"></a>Propiedades de trabajos
La siguiente lista muestra las propiedades y las descripciones correspondientes, que se pueden utilizar al consultar trabajos o resultados de trabajos.

| Propiedad | DESCRIPCIÓN |
| --- | --- |
| **jobId** |Id. proporcionado de la aplicación para el trabajo. |
| **startTime** |Hora de inicio proporcionada de la aplicación (ISO 8601) para el trabajo. |
| **endTime** |Fecha proporcionada de IoT Hub (ISO 8601) cuando hay un trabajo completado. Válido solo después de que el trabajo alcance el estado 'completado'. |
| **type** |Tipos de trabajos: |
| | **scheduledUpdateTwin**: un trabajo que se usa para actualizar un conjunto de propiedades deseadas o etiquetas. |
| | **scheduledDeviceMethod**: un trabajo que se utiliza para invocar un método de dispositivo en un conjunto de dispositivos gemelos. |
| **estado** |Estado actual del trabajo. Posibles valores para el estado: |
| | **pending**: programado y en espera a que lo recopile el servicio de trabajo. |
| | **scheduled**: programado para una hora futura. |
| | **running**: trabajo activo actualmente. |
| | **cancelled**: el trabajo se ha cancelado. |
| | **failed**: trabajo erróneo. |
| | **completed**: el trabajo se ha completado. |
| **deviceJobStatistics** |Estadísticas sobre la ejecución del trabajo. |
| | Propiedades **deviceJobStatistics**: |
| | **deviceJobStatistics.deviceCount**: número de dispositivos en el trabajo. |
| | **deviceJobStatistics.failedCount**: número de dispositivos en los que no se produjo un error del trabajo. |
| | **deviceJobStatistics.succeededCount**: número de dispositivos en los que el trabajo se realizó correctamente. |
| | **deviceJobStatistics.runningCount**: número de dispositivos que están ejecutando el trabajo en estos momentos. |
| | **deviceJobStatistics.pendingCount**: número de dispositivos que tienen pendiente ejecutar el trabajo. |

### <a name="additional-reference-material"></a>Material de referencia adicional
Otros temas de referencia en la guía del desarrollador de IoT Hub son los siguientes:

* En [Puntos de conexión de IoT Hub][lnk-endpoints], se describen los diferentes puntos de conexión que expone cada centro de IoT Hub para las operaciones en tiempo de ejecución y de administración.
* En [Cuotas y limitación][lnk-quotas], se describen las cuotas que se aplican al servicio IoT Hub y el comportamiento de limitación que se espera al usar el servicio.
* En [SDK de dispositivo y servicio IoT de Azure][lnk-sdks] se muestran los diversos SDK de lenguaje que puede usar para desarrollar aplicaciones para dispositivo y de servicio que interactúen con IoT Hub.
* En [Lenguaje de consulta de IoT Hub para dispositivos gemelos, trabajos y enrutamiento de mensajes][lnk-query] se describe el lenguaje de consulta de IoT Hub. Use este lenguaje de consulta para recuperar información de IoT Hub sobre los trabajos y dispositivos gemelos.
* En [Compatibilidad con MQTT de IoT Hub][lnk-devguide-mqtt], se proporciona más información sobre la compatibilidad de IoT Hub con el protocolo MQTT.

## <a name="next-steps"></a>Pasos siguientes
Para probar algunos de los conceptos descritos en este artículo, vea el siguiente tutorial de IoT Hub:

* [Schedule and broadcast jobs][lnk-jobs-tutorial] (Programación y difusión de trabajos)

<!-- links and images -->

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-jobs-tutorial]: iot-hub-node-node-schedule-jobs.md
[lnk-c2d-methods]: quickstart-control-device-node.md
[lnk-dev-methods]: iot-hub-devguide-direct-methods.md
[lnk-get-started-twin]: iot-hub-node-node-twin-getstarted.md
[lnk-twin-devguide]: iot-hub-devguide-device-twins.md
