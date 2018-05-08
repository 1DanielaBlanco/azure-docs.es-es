---
title: Módulo C# de Azure IoT Edge | Microsoft Docs
description: Crear un módulo IoT Edge con código de C# e implementarlo en un dispositivo perimetral
services: iot-edge
keywords: ''
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 03/14/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 09e20d9a80b881075d9bb6be7d4daafc739340a1
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/03/2018
---
# <a name="develop-and-deploy-a-c-iot-edge-module-to-your-simulated-device---preview"></a>Desarrollar e implementar un módulo IoT Edge C# en su dispositivo simulado: versión preliminar

Los módulos de IoT Edge se pueden usar para implementar código que, a su vez, implementa una lógica de negocios directamente en los dispositivos de IoT Edge. Este tutorial le guía a través de la creación e implementación de un módulo de IoT Edge que filtra los datos de sensor. Usará el dispositivo de IoT Edge simulado que ha creado en los tutoriales sobre cómo implementar Azure IoT Edge en un dispositivo simulado de [Windows] [ lnk-tutorial1-win] o [Linux] [ lnk-tutorial1-lin]. En este tutorial, aprenderá a:    

> [!div class="checklist"]
> * Usar Visual Studio Code para crear un módulo IoT Edge basado en .NET Core 2.0
> * Usar Visual Studio Code y Docker para crear una imagen de Docker y publicarla en el Registro 
> * Implementar el módulo en el dispositivo IoT Edge
> * Visualización de datos generados


El módulo IoT Edge que se crea en este tutorial filtra los datos de temperatura generados por el dispositivo. Solo envía mensajes al siguiente nivel si la temperatura es superior al umbral especificado. Este tipo de análisis perimetral resulta útil para reducir la cantidad de datos que se comunican a la nube y se almacenan en ella. 

## <a name="prerequisites"></a>requisitos previos

* El dispositivo de Azure IoT Edge que creó en la guía de inicio rápido o en el primer tutorial.
* La cadena de conexión de clave principal para el dispositivo de IoT Edge.  
* [Visual Studio Code](https://code.visualstudio.com/). 
* [Extensión de Azure IoT Edge para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge). 
* [Extensión de C# para Visual Studio Code (con tecnología de OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) 
* [Docker](https://docs.docker.com/engine/installation/) en el mismo equipo que tiene Visual Studio Code. La Community Edition (CE) es suficiente para este tutorial. 
* [SDK de .NET Core 2.0](https://www.microsoft.com/net/core#windowscmd). 

## <a name="create-a-container-registry"></a>Creación de un registro de contenedor
En este tutorial, puede usar la extensión de Azure IoT Edge para VS Code a fin de generar un módulo y crear una **imagen de contenedor** a partir de los archivos. Inserte luego esta imagen en un **Registro** que almacena y administra las imágenes. Por último, implemente la imagen en el Registro para que se ejecute en el dispositivo de IoT Edge.  

Puede usar cualquier registro compatible con Docker para este tutorial. Dos servicios populares del Registro de Docker disponibles en la nube son [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) y [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). En este tutorial se usa Azure Container Registry. 

1. En [Azure Portal](https://portal.azure.com), seleccione **Crear un recurso** > **Contenedores** > **Azure Container Registry**.
2. Asigne un nombre al Registro, elija una suscripción, elija un grupo de recursos y establezca la SKU en el nivel **Básico**. 
3. Seleccione **Crear**.
4. Una vez creado el registro de contenedor, desplácese hasta él y seleccione **Claves de acceso**. 
5. Cambie **Usuario administrador** a **Habilitar**.
6. Copie los valores de **Servidor de inicio de sesión**, **Nombre de usuario** y **Contraseña**. Usará estos valores más adelante en este tutorial al publicar la imagen de Docker en el registro y al agregar las credenciales del registro al tiempo de ejecución de Edge. 

## <a name="create-an-iot-edge-module-project"></a>Crear un proyecto de módulo IoT Edge
En los siguientes pasos puede ver cómo crear un módulo IoT Edge basado en .NET Core 2.0 mediante Visual Studio Code y la extensión de Azure IoT Edge.
1. En Visual Studio Code, seleccione **Ver** > **Terminal integrado** para abrir el terminal integrado de VS Code.
2. En el terminal integrado, escriba el siguiente comando para instalar (o actualizar) la plantilla **AzureIoTEdgeModule** en dotnet:

    ```cmd/sh
    dotnet new -i Microsoft.Azure.IoT.Edge.Module
    ```

3. Cree un proyecto para el nuevo módulo. El comando siguiente crea la carpeta del proyecto, **FilterModule**, con el repositorio de contenedores. Si usa Azure Container Registry, el segundo parámetro debe tener el formato de `<your container registry name>.azurecr.io`. Escriba el siguiente comando en la carpeta de trabajo actual:

    ```cmd/sh
    dotnet new aziotedgemodule -n FilterModule -r <your container registry address>/filtermodule
    ```
 
4. Seleccione **Archivo** > **Abrir carpeta**.
5. Vaya a la carpeta **FilterModule** y haga clic en **Seleccionar carpeta** para abrir el proyecto en VS Code.
6. En el explorador de VS Code, haga clic en **Program.cs** para abrirlo.

   ![Abrir Program.cs][1]

7. En la parte superior del espacio de nombres **FilterModule**, agregue tres instrucciones `using` para los tipos que se usarán más adelante en:

    ```csharp
    using System.Collections.Generic;     // for KeyValuePair<>
    using Microsoft.Azure.Devices.Shared; // for TwinCollection
    using Newtonsoft.Json;                // for JsonConvert
    ```

8. Agregue la variable `temperatureThreshold` a la clase **Program**. Esta variable establece el valor que debe superar la temperatura medida para que los datos se envíen a IoT Hub. 

    ```csharp
    static int temperatureThreshold { get; set; } = 25;
    ```

9. Agregue las clases `MessageBody`, `Machine` y `Ambient` a la clase **Program**. Estas clases definen el esquema esperado para el cuerpo de los mensajes entrantes.

    ```csharp
    class MessageBody
    {
        public Machine machine {get;set;}
        public Ambient ambient {get; set;}
        public string timeCreated {get; set;}
    }
    class Machine
    {
       public double temperature {get; set;}
       public double pressure {get; set;}         
    }
    class Ambient
    {
       public double temperature {get; set;}
       public int humidity {get; set;}         
    }
    ```

10. En el método **Init**, el código crea y configura un objeto **DeviceClient**. Este objeto permite al módulo conectarse al runtime de Azure IoT Edge local para enviar y recibir mensajes. La cadena de conexión que se usa en el método **Init** la suministra el runtime de IoT Edge al módulo. Tras la creación de **DeviceClient**, el código lee el umbral de temperatura de las propiedades deseadas del módulo gemelo y registra una devolución de llamada para recibir mensajes del centro de IoT Edge a través del punto de conexión **input1**. Reemplace el método `SetInputMessageHandlerAsync` por otro y agregue un método `SetDesiredPropertyUpdateCallbackAsync` para las actualizaciones de propiedades que quiera. Para realizar este cambio, reemplace la última línea del método **Init** con el siguiente código:

    ```csharp
    // Register callback to be called when a message is received by the module
    // await ioTHubModuleClient.SetImputMessageHandlerAsync("input1", PipeMessage, iotHubModuleClient);

    // Read TemperatureThreshold from Module Twin Desired Properties
    var moduleTwin = await ioTHubModuleClient.GetTwinAsync();
    var moduleTwinCollection = moduleTwin.Properties.Desired;
    try {
        temperatureThreshold = moduleTwinCollection["TemperatureThreshold"];
    } catch(ArgumentOutOfRangeException e) {
        Console.WriteLine("Proerty TemperatureThreshold not exist");
    }

    // Attach callback for Twin desired properties updates
    await ioTHubModuleClient.SetDesiredPropertyUpdateCallbackAsync(onDesiredPropertiesUpdate, null);

    // Register callback to be called when a message is received by the module
    await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FilterMessages, ioTHubModuleClient);
    ```

11. Agregue el método `onDesiredPropertiesUpdate` a la clase **Program**. Este método recibe las actualizaciones sobre las propiedades que se quieren del módulo gemelo y actualiza la variable **temperatureThreshold** para que coincida. Todos los módulos tienen su propio módulo gemelo, que le permite configurar el código que se ejecuta dentro de un módulo directamente desde la nube.

    ```csharp
    static Task onDesiredPropertiesUpdate(TwinCollection desiredProperties, object userContext)
    {
        try
        {
            Console.WriteLine("Desired property change:");
            Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));

            if (desiredProperties["TemperatureThreshold"]!=null)
                temperatureThreshold = desiredProperties["TemperatureThreshold"];

        }
        catch (AggregateException ex)
        {
            foreach (Exception exception in ex.InnerExceptions)
            {
                Console.WriteLine();
                Console.WriteLine("Error when receiving desired property: {0}", exception);
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error when receiving desired property: {0}", ex.Message);
        }
        return Task.CompletedTask;
    }
    ```

12. Reemplace el método `PipeMessage` por el método `FilterMessages`. Se llama a este método cada vez que el módulo recibe un mensaje del centro de IoT Edge. Filtra los mensajes que informan de temperaturas por debajo del umbral de temperatura que se establecen mediante el módulo gemelo. También agrega la propiedad **MessageType** al mensaje con el valor establecido en **Alerta**. 

    ```csharp
    static async Task<MessageResponse> FilterMessages(Message message, object userContext)
    {
        var counterValue = Interlocked.Increment(ref counter);

        try {
            DeviceClient deviceClient = (DeviceClient)userContext;

            var messageBytes = message.GetBytes();
            var messageString = Encoding.UTF8.GetString(messageBytes);
            Console.WriteLine($"Received message {counterValue}: [{messageString}]");

            // Get message body
            var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

            if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
            {
                Console.WriteLine($"Machine temperature {messageBody.machine.temperature} " +
                    $"exceeds threshold {temperatureThreshold}");
                var filteredMessage = new Message(messageBytes);
                foreach (KeyValuePair<string, string> prop in message.Properties)
                {
                    filteredMessage.Properties.Add(prop.Key, prop.Value);
                }

                filteredMessage.Properties.Add("MessageType", "Alert");
                await deviceClient.SendEventAsync("output1", filteredMessage);
            }

            // Indicate that the message treatment is completed
            return MessageResponse.Completed;
        }
        catch (AggregateException ex)
        {
            foreach (Exception exception in ex.InnerExceptions)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", exception);
            }
            // Indicate that the message treatment is not completed
            var deviceClient = (DeviceClient)userContext;
            return MessageResponse.Abandoned;
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
            // Indicate that the message treatment is not completed
            DeviceClient deviceClient = (DeviceClient)userContext;
            return MessageResponse.Abandoned;
        }
    }
    ```

13. Guarde este archivo.

## <a name="create-a-docker-image-and-publish-it-to-your-registry"></a>Crear una imagen de Docker y publicarla en el Registro

1. Inicie sesión en Docker escribiendo el comando siguiente en el terminal integrado de VS Code: 
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```
   Para buscar el nombre de usuario, la contraseña y el servidor de inicio de sesión que se van a usar en este comando, vaya a [Azure Portal] (https://portal.azure.com). En **Todos los recursos**, haga clic en el icono para que Azure Container Registry abra sus propiedades y, después, haga clic en **Claves de acceso**. Copie los valores en los campos **Nombre de usuario**, **Contraseña** y **Servidor de inicio de sesión**. 

2. En el explorador de VS Code, haga clic con el botón derecho en el archivo **module.json** y haga clic en **Build and Push IoT Edge module Docker image** (Compilar e insertar la imagen de Docker del módulo de IoT Edge). En el cuadro de lista desplegable emergente situado en la parte superior de la ventana de VS Code, seleccione la plataforma de contenedor, **amd64** para el contenedor de Linux o **windows-amd64** para el contenedor de Windows. A continuación, VS Code compila el código, pone `FilterModule.dll` en contenedores y lo inserta en el registro de contenedor especificado.


3. Puede obtener la dirección de la imagen de contenedor completa con etiqueta en la terminal integrada de VS Code. Para obtener más información acerca de la definición de compilación y de inserción, puede consultar el archivo `module.json`.

## <a name="add-registry-credentials-to-edge-runtime"></a>Agregar las credenciales del Registro al runtime de Edge
Agregue las credenciales del Registro al runtime de Edge en el equipo en que ejecuta el dispositivo de Edge. Estas credenciales proporcionan acceso al runtime para extraer el contenedor. 

- En Windows, ejecute el siguiente comando:
    
    ```cmd/sh
    iotedgectl login --address <your container registry address> --username <username> --password <password> 
    ```

- En Linux, ejecute el siguiente comando:
    
    ```cmd/sh
    sudo iotedgectl login --address <your container registry address> --username <username> --password <password> 
    ```

## <a name="run-the-solution"></a>Ejecución de la solución

1. En [Azure Portal](https://portal.azure.com), navegue hasta su centro de IoT.
2. Vaya a **IoT Edge (versión preliminar)** y seleccione el dispositivo de IoT Edge.
3. Seleccione **Set modules** (Establecer módulos). 
4. Compruebe que el módulo **tempSensor** se rellena automáticamente. Si no es así, realice los pasos siguientes para agregarlo:
    1. Seleccione **Add IoT Edge module** (Agregar módulo de IoT Edge).
    2. En el campo **Nombre**, escriba `tempSensor`.
    3. En el campo **URI de la imagen**, escriba `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`.
    4. Deje los restantes valores intactos y haga clic en **Guardar**.
5. Agregue el módulo **filterModule** que creó en secciones anteriores. 
    1. Seleccione **Add IoT Edge module** (Agregar módulo de IoT Edge).
    2. En el campo **Nombre**, escriba `filterModule`.
    3. En el campo **URI de la imagen**, escriba la dirección de la imagen; por ejemplo, `<your container registry address>/filtermodule:0.0.1-amd64`. La dirección de la imagen completa puede encontrarse en la sección anterior.
    4. Active la casilla **Habilitar** para que pueda modificar el módulo gemelo. 
    5. Reemplace el archivo JSON del cuadro de texto del módulo gemelo por el siguiente: 

        ```json
        {
           "properties.desired":{
              "TemperatureThreshold":25
           }
        }
        ```
 
    6. Haga clic en **Save**(Guardar).
6. Haga clic en **Next**.
7. En el paso **Specify Routes** (Especificar rutas), copie el archivo JSON siguiente en el cuadro de texto. Los módulos publican todos los mensajes en el runtime de Edge. Las reglas declarativas del runtime definen por dónde fluyen esos mensajes. En este tutorial, necesita dos rutas. La primera transporta mensajes desde el sensor de temperatura hasta el módulo de filtro a través del punto de conexión "input1", que es el que configuró con el controlador **FilterMessages**. La segunda transporta mensajes desde el módulo de filtro hasta IoT Hub. En esta ruta, `upstream` es un destino especial que indica a Edge Hub que envíe mensajes a IoT Hub. 

    ```json
    {
       "routes":{
          "sensorToFilter":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filterModule/inputs/input1\")",
          "filterToIoTHub":"FROM /messages/modules/filterModule/outputs/output1 INTO $upstream"
       }
    }
    ```

8. Haga clic en **Next**.
9. En el paso **Review Template** (Revisar plantilla), haga clic en **Enviar**. 
10. Vuelva a la página de detalles del dispositivo IoT Edge y haga clic en **Actualizar**. Debería ver el nuevo **filtermodule** en ejecución junto con el módulo **tempSensor** y el **runtime de IoT Edge**. 

## <a name="view-generated-data"></a>Ver datos generados

Para supervisar los mensajes del dispositivo a la nube enviados al centro de IoT desde el dispositivo de IoT Edge:
1. Configure la extensión de Azure IoT Toolkit con la cadena de conexión del centro de IoT: 
    1. Abra el explorador de VS Code seleccionando **Ver** > **Explorador**. 
    2. En el explorador, haga clic en **IOT HUB DEVICES** y, luego, en **...**. Haga clic en **Set IoT Hub Connection String** (Establecer cadena de conexión de IoT Hub) y escriba la cadena de conexión del centro de IoT al que se conecta el dispositivo de IoT Edge en la ventana emergente. 

        Para buscar la cadena de conexión, haga clic en el icono del centro de IoT en Azure Portal y, después, en **Directivas de acceso compartido**. En **Directivas de acceso compartido**, haga clic en la directiva **iothubowner** y copie la cadena de conexión de IoT Hub en la ventana **iothubowner**.   

2. Para supervisar los datos que llegan a IoT Hub, seleccione **Ver** > **Paleta de comandos** y busque el comando de menú **IoT: Start monitoring D2C message** (IoT: iniciar la supervisión del mensaje de D2C). 
3. Para dejar de supervisar datos, use el comando de menú **IoT: Start monitoring D2C message** (IoT: detener la supervisión del mensaje de D2C). 

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, creó un módulo IoT Edge que contiene código para filtrar datos sin procesar generados por el dispositivo IoT Edge. Puede continuar con cualquiera de los siguientes tutoriales para obtener información sobre otras formas en que Azure IoT Edge puede ayudarle a transformar datos en perspectivas empresariales en el límite.

> [!div class="nextstepaction"]
> [Deploy Azure Function as a module (Implementar Azure Functions como módulo)](tutorial-deploy-function.md)
> [Deploy Azure Stream Analytics as a module (Implementar Azure Stream Analytics como módulo)](tutorial-deploy-stream-analytics.md)


<!-- Links -->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md

<!-- Images -->
[1]: ./media/tutorial-csharp-module/programcs.png
[2]: ./media/tutorial-csharp-module/build-module.png
[3]: ./media/tutorial-csharp-module/docker-os.png
