---
title: Información sobre el punto de conexión integrado de Azure IoT Hub | Microsoft Docs
description: En esta guía del desarrollador se describe cómo usar el punto de conexión integrado compatible con Event Hubs para leer los mensajes del dispositivo a la nube.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/18/2018
ms.author: dobett
ms.openlocfilehash: 767c91e4926e553b63b8331ac99edcd7823d2c13
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/07/2018
ms.locfileid: "44055022"
---
# <a name="read-device-to-cloud-messages-from-the-built-in-endpoint"></a>Leer mensajes del dispositivo a la nube desde el punto de conexión integrado

De forma predeterminada, los mensajes se enrutan al punto de conexión orientado al servicio integrado (**/messages/events**), que es compatible con [Event Hubs][lnk-event-hubs]. Actualmente este punto de conexión solo se expone mediante el protocolo [AMQP][lnk-amqp] en el puerto 5671. IoT Hub muestra las propiedades siguientes para permitirle controlar el punto de conexión de mensajería integrado compatible con Event Hubs **messages/events**.

| Propiedad            | DESCRIPCIÓN |
| ------------------- | ----------- |
| **Número de particiones** | Establezca esta propiedad durante la creación para definir el número de [particiones][lnk-event-hub-partitions] para ingesta de eventos de dispositivo a nube. |
| **Tiempo de retención**  | Esta propiedad especifica cuánto tiempo, en días, IoT Hub conserva los mensajes. El valor predeterminado es un día, pero se puede aumentar a siete días. |

IoT Hub también le permite administrar los grupos de consumidores en el punto de conexión de recepción de dispositivo a nube integrado.

De forma predeterminada, todos los mensajes que no cumplen de forma explícita una regla de enrutamiento de mensajes se escriben en el punto de conexión integrado. Si deshabilita esta ruta de reserva, los mensajes que no cumplen explícitamente ninguna reglas de enrutamiento de mensajes se quitan.

Puede modificar el tiempo de retención mediante programación con las[API de REST del proveedor de recursos de IoT Hub][lnk-resource-provider-apis] o con [Azure Portal][lnk-management-portal].

IoT Hub expone el punto de conexión integrado **messages/events** para los servicios de back-end con el fin de leer los mensajes de dispositivo a nube recibidos por el centro. Este punto de conexión es compatible con Event Hubs, lo que permite usar cualquiera de los mecanismos del servicio Event Hubs para leer mensajes.

## <a name="read-from-the-built-in-endpoint"></a>Leer desde el punto de conexión integrado

Al usar el [SDK de Azure Service Bus para .NET][lnk-servicebus-sdk] o [Event Hubs - host del procesador de eventos][lnk-eventprocessorhost], puede usar cualquier cadena de conexión de IoT Hub con los permisos correctos. A continuación, utilice **messages/events** como el nombre del Centro de eventos.

Cuando use SDK (o integraciones de productos) que no detecten IoT Hub, tiene que recuperar un punto de conexión compatible con el centro de eventos y un nombre compatible con el centro de eventos:

1. Inicie sesión en [Azure Portal][lnk-management-portal] y vaya a IoT Hub.
1. Haga clic en **Extremos**.
1. En la sección **Built-in endpoints** (Puntos de conexión integrados), haga clic en **Eventos**. 
1. Se abrirá una página de propiedades, que contiene los siguientes valores: **Event Hub-compatible endpoint** (Punto de conexión compatible con el Centro de eventos), **Event Hub-compatible name** (Nombre compatible con el Centro de eventos), **Particiones**, **Tiempo de retención** y **Grupos de consumidores**.

    ![Configuración de dispositivo a nube][img-eventhubcompatible]

El SDK de IoT Hub requiere el nombre del punto de conexión de IoT Hub, que es **messages/events** tal y como se muestra en **Puntos de conexión**.

Si el SDK que utiliza requiere un valor de **Nombre de host** o **Espacio de nombres**, quite el esquema de **Event Hub-compatible endpoint** (Punto de conexión compatible con el Centro de eventos). Por ejemplo, si el punto de conexión compatible con el Centro de eventos es **sb://iothub-ns-myiothub-1234.servicebus.windows.net/**, el **nombre de host** sería **iothub-ns-myiothub-1234.servicebus.windows.net**. El **Namespace** sería **iothub-ns-myiothub-1234**.

A continuación, puede usar cualquier directiva de acceso compartido que tenga permisos **ServiceConnect** para conectarse a la instancia de Event Hub especificada.

En caso de que tenga que crear una cadena de conexión del Centro de eventos con la información anterior, use el modelo siguiente:

`Endpoint={Event Hub-compatible endpoint};SharedAccessKeyName={iot hub policy name};SharedAccessKey={iot hub policy key}`

Los SDK y las integraciones que puede usar con los puntos de conexión compatibles con Event Hubs que expone IoT Hub incluyen los elementos de la lista siguiente:

* [Cliente de Event Hubs de Java](https://github.com/Azure/azure-event-hubs-java).
* [Spout de Apache Storm](../hdinsight/storm/apache-storm-develop-csharp-event-hub-topology.md). Puede ver el [origen de spout](https://github.com/apache/storm/tree/master/external/storm-eventhubs) en GitHub.
* [Integración de Apache Spark](../hdinsight/spark/apache-spark-eventhub-streaming.md).

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre los puntos de conexión de IoT Hub, vea [Puntos de conexión de IoT Hub][lnk-endpoints].

En los [inicios rápidos][lnk-get-started] se muestra cómo enviar mensajes del dispositivo a la nube desde dispositivos simulados y cómo leer los mensajes desde el punto de conexión integrado. Para obtener más información, vea el tutorial [Procesamiento de mensajes de dispositivo a nube de IoT Hub mediante rutas][lnk-d2c-tutorial].

Si quiere enrutar los mensajes del dispositivo a la nube a puntos de conexión personalizados, vea [Use message routes and custom endpoints for device-to-cloud messages][lnk-custom] (Usar rutas de mensajes y puntos de conexión personalizados para los mensajes del dispositivo a la nube).

[img-eventhubcompatible]: ./media/iot-hub-devguide-messages-read-builtin/eventhubcompatible.png

[lnk-custom]: iot-hub-devguide-messages-read-custom.md
[lnk-get-started]: quickstart-send-telemetry-node.md
[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-resource-provider-apis]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-event-hubs]: http://azure.microsoft.com/documentation/services/event-hubs/
[lnk-management-portal]: https://portal.azure.com
[lnk-d2c-tutorial]: tutorial-routing.md
[lnk-event-hub-partitions]: ../event-hubs/event-hubs-features.md#partitions
[lnk-servicebus-sdk]: https://www.nuget.org/packages/WindowsAzure.ServiceBus
[lnk-eventprocessorhost]: https://docs.microsoft.com/azure/event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph
[lnk-amqp]: https://www.amqp.org/
