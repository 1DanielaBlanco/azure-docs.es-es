---
title: Información general del acelerador de la solución de supervisión remota | Microsoft Docs
description: Introducción al acelerador de la solución de supervisión remota.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 11/10/2017
ms.author: dobett
ms.openlocfilehash: a8b5d9e3917c854cb255a35d3bbc901bcce52c24
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/28/2018
ms.locfileid: "37084538"
---
# <a name="remote-monitoring-solution-accelerator-overview"></a>Información general sobre el acelerador de la solución de supervisión remota

El [acelerador de la solución](../iot-accelerators/iot-accelerators-what-are-solution-accelerators.md) de supervisión remota implementa una solución de supervisión integral para varias máquinas en ubicaciones remotas. La solución combina servicios clave de Azure para proporcionar una implementación genérica del escenario de negocio. Puede usar la solución como punto de partida para su propia implementación, así como [personalizarla](../iot-accelerators/iot-accelerators-remote-monitoring-customize.md) para cumplir sus requisitos empresariales específicos.

Este artículo le guiará a través de algunos de los elementos clave de la solución de supervisión remota para que pueda entender cómo funciona. Esta información le ayuda a:

* Solucionar problemas de la solución.
* Planear cómo personalizar la solución para satisfacer sus propios requisitos específicos.
* Diseñar una solución de IoT propia que utilice servicios de Azure.

## <a name="logical-architecture"></a>Arquitectura lógica

El siguiente diagrama muestra los componentes lógicos del acelerador de la solución de supervisión remota superpuesta en la [arquitectura de IoT](../iot-accelerators/iot-accelerators-what-is-azure-iot.md):

![Arquitectura lógica](./media/iot-accelerators-remote-monitoring-sample-walkthrough/remote-monitoring-architecture.png)

## <a name="why-microservices"></a>¿Por qué microservicios?

La arquitectura en la nube ha evolucionado desde el lanzamiento de Microsoft de los primeros aceleradores de soluciones. Los [microservicios](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/) han surgido como un procedimiento probado para alcanzar el escalado y la flexibilidad, sin sacrificar la velocidad de desarrollo. Este modelo de arquitectura se usa internamente en varios servicios de Microsoft con grandes resultados con respecto a la escalabilidad y la confiabilidad. Los aceleradores de soluciones actualizados ponen estos conocimientos en práctica para que también pueda beneficiarse de ellos.

> [!TIP]
> Para más información sobre las arquitecturas de microservicios, consulte [.NET Application Architecture](https://www.microsoft.com/net/learn/architecture) (Arquitectura de la aplicación .NET) y [Microservices: An application revolution powered by the cloud](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/) (Microservicios: una revolución de aplicaciones con la tecnología de la nube).

## <a name="device-connectivity"></a>Conectividad de dispositivos

La solución incluye los componentes siguientes en la parte de la conectividad de dispositivos de la arquitectura lógica:

### <a name="simulated-devices"></a>Dispositivos simulados

La solución incluye un microservicio que le permite administrar un grupo de dispositivos simulados para probar el flujo de un extremo a otro en la solución. Los dispositivos simulados:

* Generan datos de telemetría del dispositivo a la nube.
* Responder a llamadas a métodos de la nube al dispositivo desde IoT Hub.

El microservicio proporciona un punto de conexión REST para permitirle crear, iniciar y detener simulaciones. Cada simulación consta de un conjunto de dispositivos virtuales de diferentes tipos que envían datos de telemetría y responden a llamadas a métodos.

Puede aprovisionar dispositivos simulados desde el panel en el portal de la solución.

### <a name="physical-devices"></a>Dispositivos físicos

Puede conectar dispositivos físicos a la solución. Puede implementar el comportamiento de los dispositivos simulados con los SDK de dispositivo IoT de Azure.

Puede aprovisionar dispositivos físicos desde el panel en el portal de la solución.

### <a name="iot-hub-and-the-iot-manager-microservice"></a>IoT Hub y el microservicio de administrador de IoT

[IoT Hub](../iot-hub/index.yml) ingiere los datos enviados desde los dispositivos en la nube y los pone a disposición del microservicio `telemetry-agent`.

La instancia de IoT Hub en la solución también:

* Mantiene un registro de identidades que almacena los identificadores y las claves de autenticación de todos los dispositivos que se pueden conectar al portal. Puede habilitar y deshabilitar dispositivos a través del registro de identidades.
* Invoca los métodos de los dispositivos en nombre del portal de la solución.
* Mantiene los dispositivos gemelos para todos los dispositivos registrados. Un dispositivo gemelo almacena los valores de la propiedad notificados por un dispositivo. Un dispositivo gemelo también almacena las propiedades deseadas, establecidas en el portal de la solución, para que el dispositivo las recupere la siguiente vez que se conecte.
* Programa trabajos para establecer las propiedades de varios dispositivos o invocar métodos en varios dispositivos.

La solución incluye el microservicio `iot-manager` para controlar las interacciones con IoT Hub, como:

* Crear y administrar dispositivos de IoT.
* Administrar dispositivos gemelos.
* Invocar métodos en los dispositivos.
* Administrar credenciales de IoT.

Este servicio también ejecuta consultas de IoT Hub para recuperar los dispositivos que pertenecen a grupos definidos por el usuario.

El microservicio proporciona un punto de conexión REST para administrar dispositivos y dispositivos gemelos, invocar métodos y ejecutar consultas de IoT Hub.

## <a name="data-processing-and-analytics"></a>Procesamiento de datos y análisis

La solución incluye los componentes siguientes en la parte de la proceso de datos y análisis de la arquitectura lógica:

### <a name="device-telemetry"></a>Telemetría de dispositivo

La solución incluye dos microservicios para controlar la telemetría del dispositivo.

El microservicio [telemetry-agent](https://github.com/Azure/telemetry-agent-dotnet):

* Almacena datos de telemetría en Azure Cosmos DB.
* Analiza el flujo de telemetría de los dispositivos.
* Genera alarmas según las reglas definidas.

Las alarmas se almacenan en Azure Cosmos DB.

El microservicio [telemetry-agent](https://github.com/Azure/telemetry-agent-dotnet) permite que el portal de la solución lea los datos de telemetría enviados desde los dispositivos. El portal de la solución también utiliza este servicio para:

* Definir reglas de supervisión tales como los umbrales que desencadenan las alarmas
* Recuperar la lista de últimas alarmas.

Usar el punto de conexión REST proporcionado por este microservicio para administrar telemetría, reglas y alarmas.

### <a name="storage"></a>Storage

El microservicio [storage-adapter](https://github.com/Azure/pcs-storage-adapter-dotnet) es un adaptador delante del servicio de almacenamiento principal usado para el acelerador de la solución. Proporciona almacenamiento de colección simple y clave-valor.

La implementación estándar del acelerador de la solución utiliza Azure Cosmos DB como servicio de almacenamiento principal.

La base de datos de Azure Cosmos DB almacena los datos en el acelerador de la solución. El microservicio **storage-adapter** actúa como un adaptador para que los otros microservicios de la solución tengan acceso a los servicios de almacenamiento.

## <a name="presentation"></a>Presentación

La solución incluye los componentes siguientes en la parte de presentación de la arquitectura lógica:

La [interfaz de usuario web es una aplicación React de Javascript](https://github.com/Azure/pcs-remote-monitoring-webui). La aplicación:

* Solo utiliza Javascript React y se ejecuta completamente en el explorador.
* El estilo se realiza con CSS.
* Interactúa con los microservicios de acceso público a través de llamadas AJAX.

La interfaz de usuario presenta toda la funcionalidad del acelerador de la solución e interactúa con otros servicios, como:

* El microservicio [authentication](https://github.com/Azure/pcs-auth-dotnet) para proteger los datos del usuario.
* El microservicio [iothub-manager](https://github.com/Azure/iothub-manager-dotnet) para enumerar y administrar los dispositivos de IoT.

El microservicio [ui-config](https://github.com/Azure/pcs-config-dotnet) permite que la interfaz de usuario almacene y recupere valores de configuración.

## <a name="next-steps"></a>Pasos siguientes

Si desea examinar el código fuente y la documentación para desarrolladores, comience con uno los dos repositorios de GitHub principales:

* [Acelerador de la solución de supervisión remota con Azure IoT (.NET)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/).
* [Acelerador de la solución de supervisión remota con Azure IoT (Java)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java).

Diagramas detallados de la arquitectura de la solución:
* [Acelerador de la solución de supervisión remota (arquitectura)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Architecture).

Para obtener información conceptual sobre el acelerador de la solución de supervisión remota, consulte [Personalizar el acelerador de la solución](../iot-accelerators/iot-accelerators-remote-monitoring-customize.md).
