---
title: Inicio rápido para controlar un dispositivo desde Azure IoT Hub (Node.js) | Microsoft Docs
description: En este inicio rápido, ejecuta dos aplicaciones Node.js de muestra. Una aplicación es una aplicación back-end que puede controlar dispositivos conectados al centro de manera remota. La otra aplicación simula un dispositivo conectado al centro que se puede controlar de manera remota.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: quickstart
ms.custom: mvc
ms.date: 04/30/2018
ms.author: dobett
ms.openlocfilehash: a173b77d969697f85eab0d5976e5bc0a1796fe75
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/06/2018
ms.locfileid: "34808577"
---
# <a name="quickstart-control-a-device-connected-to-an-iot-hub-nodejs"></a>Inicio rápido: controlar un dispositivo conectado a IoT Hub (Node.js)

[!INCLUDE [iot-hub-quickstarts-2-selector](../../includes/iot-hub-quickstarts-2-selector.md)]

IoT Hub es un servicio de Azure que le permite ingerir grandes volúmenes de datos de telemetría desde los dispositivos IoT en la nube y administrar dispositivos desde la nube. En este inicio rápido, usa un *método directo* para controlar un dispositivo simulado conectado a IoT Hub. Puede usar métodos directos para cambiar el comportamiento de un dispositivo conectado a IoT Hub de manera remota.

El inicio rápido usa dos aplicaciones Node.js escritas anteriormente:

* Una aplicación de dispositivo simulado que responde a métodos directos que se llaman desde una aplicación back-end. Para recibir las llamadas de método directo, esta aplicación se conecta a un punto de conexión específico del dispositivo en IoT Hub.
* Una aplicación back-end que llama a los métodos directos en el dispositivo simulado. Para llamar a un método directo en un dispositivo, esta aplicación se conecta a un punto de conexión de servicio en IoT Hub.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

## <a name="prerequisites"></a>requisitos previos

Las dos aplicaciones de ejemplo que se ejecutan en este inicio rápido se escriben con Node.js. Necesita Node.js 4.x.x o una versión posterior en el equipo de desarrollo.

Puede descargar Node.js para varias plataformas desde [nodejs.org](https://nodejs.org).

Puede verificar la versión actual de Node.js en el equipo de desarrollo con el comando siguiente:

```cmd/sh
node --version
```

Si aún no lo ha hecho, descargue el proyecto de Node.js de muestra desde https://github.com/Azure-Samples/azure-iot-samples-node/archive/master.zip y extraiga el archivo ZIP.

## <a name="create-an-iot-hub"></a>Crear un centro de IoT

Si ha completado el anterior [Quickstart: Send telemetry from a device to an IoT hub](quickstart-send-telemetry-node.md) (Inicio rápido: enviar datos de telemetría desde un dispositivo a IoT Hub), puede omitir este paso.

[!INCLUDE [iot-hub-quickstarts-create-hub](../../includes/iot-hub-quickstarts-create-hub.md)]

## <a name="register-a-device"></a>Registrar un dispositivo

Si ha completado el anterior [Quickstart: Send telemetry from a device to an IoT hub](quickstart-send-telemetry-node.md) (Inicio rápido: enviar datos de telemetría desde un dispositivo a IoT Hub), puede omitir este paso.

Debe registrar un dispositivo con IoT Hub antes de poder conectarlo. En esta guía de inicio rápido, va a usar la CLI de Azure para registrar un dispositivo simulado.

1. Agregue la extensión de la CLI de IoT Hub y cree la identidad del dispositivo. Reemplace `{YourIoTHubName}` por el nombre de IoT Hub:

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyNodeDevice
    ```

    Si elige otro nombre para el dispositivo, actualícelo en las aplicaciones de ejemplo antes de ejecutarlas.

1. Ejecute el siguiente comando para obtener la _cadena de conexión del dispositivo_ que acaba de registrar:

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyNodeDevice --output table
    ```

    Anote la cadena de conexión del dispositivo, que será parecida a `Hostname=...=`. Usará este valor más adelante en este inicio rápido.

1. También necesita una _cadena de conexión de servicio_ para permitir que la aplicación back-end se conecte a IoT Hub y recupere los mensajes. El comando siguiente recupera la cadena de conexión del servicio de su instancia de IoT Hub:

    ```azurecli-interactive
    az iot hub show-connection-string --hub-name {YourIoTHubName} --output table
    ```

    Anote la cadena de conexión del servicio, que será parecida a `Hostname=...=`. Usará este valor más adelante en este inicio rápido. La cadena de conexión de servicio es diferente de la cadena de conexión de dispositivo.

## <a name="listen-for-direct-method-calls"></a>Escuchas para llamadas de método directo

La aplicación del dispositivo simulado se conecta a un punto de conexión específico del dispositivo en IoT Hub, envía los datos de telemetría simulados y escucha llamadas de método directo desde el centro. En este inicio rápido, la llamada de método directo desde el centro indica al dispositivo que debe cambiar el intervalo en el que envía los datos de telemetría. El dispositivo simulado envía una confirmación al centro después de que ejecute el método directo.

1. En una ventana de terminal, vaya a la carpeta raíz del proyecto de Node.js de muestra. A continuación, vaya a la carpeta **iot-hub\Quickstarts\simulated-device-2**.

1. Abra el archivo **SimulatedDevice.js** en el editor de texto de su elección.

    Reemplace el valor de la variable `connectionString` por la cadena de conexión de dispositivo que anotó anteriormente. A continuación, guarde los cambios realizados en el archivo **SimulatedDevice.js**.

1. En la ventana de terminal, ejecute los comandos siguientes para instalar las bibliotecas necesarias y ejecute la aplicación de dispositivo simulado:

    ```cmd/sh
    npm install
    node SimulatedDevice.js
    ```

    La siguiente captura de pantalla muestra la salida en la que la aplicación de dispositivo simulado envía datos de telemetría a IoT Hub:

    ![Ejecutar el dispositivo simulado](media/quickstart-control-device-node/SimulatedDevice-1.png)

## <a name="call-the-direct-method"></a>Llamar al método directo

La aplicación back-end se conecta a un punto de conexión de servicio en IoT Hub. La aplicación realiza llamadas de método directo a un dispositivo con IoT Hub y realiza escuchas para confirmaciones. Normalmente, una aplicación back-end de IoT Hub se ejecuta en la nube.

1. En otra ventana de terminal, vaya a la carpeta raíz del proyecto de Node.js de muestra. A continuación, vaya a la carpeta **iot-hub\Quickstarts\back-end-application**.

1. Abra el archivo **BackEndApplication.js** en el editor de texto de su elección.

    Reemplace el valor de la variable `connectionString` por la cadena de conexión de servicio que anotó anteriormente. A continuación, guarde los cambios realizados en el archivo **BackEndApplication.js**.

1. En la ventana de terminal, ejecute los comandos siguientes para instalar las bibliotecas necesarias y ejecute la aplicación back-end:

    ```cmd/sh
    npm install
    node BackEndApplication.js
    ```

    La siguiente captura de pantalla muestra la salida en la que la aplicación realiza una llamada de método directo en el dispositivo y recibe una confirmación:

    ![Ejecutar la aplicación back-end](media/quickstart-control-device-node/BackEndApplication.png)

    Después de ejecutar la aplicación back-end, verá un mensaje en la ventana de consola que ejecuta el dispositivo simulado y cambiará la velocidad a la que envía mensajes:

    ![Cambio en el cliente simulado](media/quickstart-control-device-node/SimulatedDevice-2.png)

## <a name="clean-up-resources"></a>Limpieza de recursos

Si tiene pensado pasar a los tutoriales, deje IoT Hub y el grupo de recursos y vuelva a usarlos más adelante.

Si ya no los va a necesitar más, elimínelos en el portal. Para ello, seleccione el grupo de recursos que contiene la instancia de IoT Hub y haga clic en **Eliminar**.

## <a name="next-steps"></a>Pasos siguientes

En este inicio rápido, ha llamado a un método directo en un dispositivo desde una aplicación back-end y ha respondido a la llamada de método directo en una aplicación de dispositivo simulado.

Para obtener información sobre cómo redirigir mensajes del dispositivo a la nube a diferentes destinos en la nube, continúe con el siguiente tutorial.

> [!div class="nextstepaction"]
> [Tutorial: Enrutar datos de telemetría a distintos puntos de conexión para procesamiento](tutorial-routing.md)