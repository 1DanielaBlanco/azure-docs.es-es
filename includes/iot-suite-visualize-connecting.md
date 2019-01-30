---
title: archivo de inclusión
description: archivo de inclusión
services: iot-suite
author: dominicbetts
ms.service: iot-suite
ms.topic: include
ms.date: 04/24/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 73ba80878615f04e1755a4d12014691c5ae2a077
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/22/2019
ms.locfileid: "54453127"
---
## <a name="view-device-telemetry"></a>Ver la telemetría de dispositivo

Puede ver la telemetría enviada desde el dispositivo en la página **Dispositivos** de la solución.

1. Seleccione el dispositivo que aprovisionó en la lista de dispositivos de la página **Dispositivos**. Un panel muestra información sobre el dispositivo incluyendo un trazado de su telemetría:

    ![Ver detalles del dispositivo](media/iot-suite-visualize-connecting/devicesdetail.png)

1. Elija **Presión** para cambiar la presentación de telemetría:

    ![Ver la telemetría de presión](media/iot-suite-visualize-connecting/devicespressure.png)

1. Para ver información de diagnóstico sobre el dispositivo, desplácese hacia abajo hasta **Diagnósticos**:

    ![Ver el diagnóstico de los dispositivos](media/iot-suite-visualize-connecting/devicesdiagnostics.png)

## <a name="act-on-your-device"></a>Actúe en su dispositivo

Para invocar métodos en los dispositivos, use la página **Dispositivos** de la solución de supervisión remota. Por ejemplo, en la solución de implementación remota, los dispositivos **Refrigerador** implementan un método **FirmwareUpdate**.

1. Elija **Dispositivos** para ir hasta la página **Dispositivos** de la solución.

1. Seleccione el dispositivo que aprovisionó en la lista de dispositivos de la página **Dispositivos**:

    ![Selección del dispositivo real](media/iot-suite-visualize-connecting/devicesselect.png)

1. Para mostrar una lista de los métodos que se pueden llamar en su dispositivo, elija **Trabajos** y, luego, **Ejecutar método**. Para programar un trabajo para ejecutarse en varios dispositivos, puede seleccionarlos en la lista. El panel **Trabajos** muestra los tipos de métodos comunes a todos los dispositivos seleccionados.

1. Elija **FirmwareUpdate** y establezca el nombre del trabajo en **UpdatePhysicalChiller**. Configure la **versión de firmware** en **2.0.0**, configure el **URI de firmware** en  **http://contoso.com/updates/firmware.bin** y, a continuación, elija **Aplicar**:

    ![Programación de la actualización de firmware](media/iot-suite-visualize-connecting/deviceschedule.png)

1. Aparece una secuencia de mensajes en la consola donde se ejecuta el código del dispositivo cuando el dispositivo controla el método.

1. Una vez completada la actualización, la nueva versión de firmware se muestra en la página **Dispositivos**:

    ![Actualización completada](media/iot-suite-visualize-connecting/complete.png)

> [!NOTE]
> Para realizar un seguimiento del estado del trabajo en la solución, elija **Vista**.

## <a name="next-steps"></a>Pasos siguientes

En el artículo [Personalización de la solución preconfigurada de supervisión remota](../articles/iot-accelerators/iot-accelerators-remote-monitoring-customize.md) se describen algunas maneras de personalizar el acelerador de soluciones.