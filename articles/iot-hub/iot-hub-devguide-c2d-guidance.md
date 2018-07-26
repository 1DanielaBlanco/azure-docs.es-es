---
title: Opciones de nube a dispositivo de IoT Hub de Azure | Microsoft Docs
description: 'Guía del desarrollador: una guía sobre cuándo usar métodos directos, propiedades notificadas del dispositivo gemelo o mensajes de nube a dispositivo para comunicaciones de este mismo tipo.'
author: fsautomata
manager: ''
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.author: elioda
ms.openlocfilehash: 2cc9bd39371741caaa3ae025df494e225dc754b0
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/20/2018
ms.locfileid: "39187043"
---
# <a name="cloud-to-device-communications-guidance"></a>Guía de comunicación de nube a dispositivo
IoT Hub proporciona tres opciones para aplicaciones de dispositivo que exponen funcionalidades a una aplicación de back-end:

* [Métodos directos][lnk-methods], para las comunicaciones que requieren confirmación inmediata del resultado. Los métodos directos se utilizan frecuentemente para el control interactivo de dispositivos, como la activación de un ventilador.
* [Propiedades deseadas del dispositivo gemelo][lnk-twins], para comandos de ejecución prolongada destinados a poner el dispositivo en un determinado estado deseado. Por ejemplo, establecer el intervalo de envío de telemetría en 30 minutos.
* [Mensajes de nube a dispositivo][lnk-c2d], para notificaciones unidireccionales a la aplicación de dispositivo.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Esta es una comparación detallada de las distintas opciones de comunicación de nube a dispositivo.

|  | Métodos directos | Propiedades deseadas del dispositivo gemelo | Mensajes de nube a dispositivo |
| ---- | ------- | ---------- | ---- |
| Escenario | Comandos que necesitan confirmación inmediata, por ejemplo, encender un ventilador. | Comandos de ejecución prolongada destinados a poner el dispositivo en un determinado estado deseado. Por ejemplo, establecer el intervalo de envío de telemetría en 30 minutos. | Notificaciones unidireccionales a la aplicación de dispositivo. |
| flujo de datos | Bidireccional. La aplicación de dispositivo puede responder al método inmediatamente. El back-end de solución recibe el resultado contextualmente a la solicitud. | Unidireccional. La aplicación de dispositivo recibe una notificación con el cambio de propiedad. | Unidireccional. La aplicación de dispositivo recibe el mensaje.
| Durabilidad. | No se establece contacto con los dispositivos desconectados. Se notifica al back-end de la solución que el dispositivo no está conectado. | Se conservan los valores de propiedad en el dispositivo gemelo. El dispositivo los leerá en la siguiente reconexión. Los valores de propiedad son recuperables con el [lenguaje de consulta de IoT Hub][lnk-query]. | IoT Hub puede conservar los mensajes durante 48 horas como máximo. |
| Destinos | Un único dispositivo que usa **deviceId**, o varios dispositivos que usan [trabajos][lnk-jobs]. | Un único dispositivo que usa **deviceId**, o varios dispositivos que usan [trabajos][lnk-jobs]. | Dispositivo único por **deviceId**. |
| Tamaño | El tamaño de carga máximo del método directo es de 128 KB. | El tamaño máximo de las propiedades deseadas es 8 KB. | Mensajes de hasta 64 KB. |
| Frecuencia | Alta. Para más información, consulte los [Límites de IoT Hub][lnk-quotas]. | Mediana. Para más información, consulte los [Límites de IoT Hub][lnk-quotas]. | Baja. Para más información, consulte los [Límites de IoT Hub][lnk-quotas]. |
| Protocolo | Disponible con MQTT o AMQP. | Disponible con MQTT o AMQP. | Disponible en todos los protocolos. El dispositivo debe sondear al usar HTTPS. |

Aprenda a usar métodos directos, propiedades deseadas y mensajes de nube a dispositivo en los siguientes tutoriales:

* [Uso de métodos directos][lnk-methods-tutorial], para métodos directos.
* [Uso de propiedades deseadas para configurar dispositivos][lnk-twin-properties], para propiedades deseadas del dispositivo gemelo. 
* [Envío de mensajes de nube a dispositivo][lnk-c2d-tutorial], para mensajes de nube a dispositivo.

[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-jobs]: iot-hub-devguide-jobs.md
[lnk-c2d]: iot-hub-devguide-messages-c2d.md
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-methods-tutorial]: quickstart-control-device-node.md
[lnk-twin-properties]: tutorial-device-twins.md
[lnk-c2d-tutorial]: iot-hub-node-node-c2d.md
