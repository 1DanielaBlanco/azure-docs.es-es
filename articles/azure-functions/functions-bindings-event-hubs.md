---
title: Enlaces de Azure Event Hubs para Azure Functions
description: Descubra cómo usar los enlaces de Azure Event Hubs de Azure Functions.
services: functions
documentationcenter: na
author: tdykstra
manager: cfowler
editor: ''
tags: ''
keywords: azure functions, funciones, procesamiento de eventos, proceso dinámico, arquitectura sin servidor
ms.assetid: daf81798-7acc-419a-bc32-b5a41c6db56b
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/08/2017
ms.author: tdykstra
ms.openlocfilehash: 51f64f6f74875c6afac350dc9cc235573b89c524
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/12/2018
ms.locfileid: "38989595"
---
# <a name="azure-event-hubs-bindings-for-azure-functions"></a>Enlaces de Azure Event Hubs para Azure Functions

En este artículo se explica cómo usar enlaces de [Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md) para Azure Functions. Azure Functions admite enlaces de desencadenador y salida para Event Hubs.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Paquetes: Functions 1.x

En Azure Functions versión 1.x, los enlaces de Event Hubs se proporcionan en el paquete NuGet [Microsoft.Azure.WebJobs.ServiceBus](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus), versión 2.x.
El código fuente del paquete se encuentra en el repositorio [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs) de GitHub.


[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x"></a>Paquetes: Functions 2.x

En Functions 2.x, use el paquete [Microsoft.Azure.WebJobs.Extensions.EventHubs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs), versión 3.x.
El código fuente del paquete se encuentra en el repositorio [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs) de GitHub.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="trigger"></a>Desencadenador

Use el desencadenador de Event Hubs para responder a un evento enviado a una secuencia de eventos de centro de eventos. Debe tener acceso de lectura al centro de eventos para configurar el desencadenador.

Cuando se activa una función de desencadenador de Event Hubs, el mensaje que la activa se pasa a la función como una cadena.

## <a name="trigger---scaling"></a>Desencadenador: escalado

Cada instancia de una función de desencadenador de Event Hubs está respaldado por solo 1 instancia de EventProcessorHost (EPH). Event Hubs garantizan que solo 1 EPH puede obtener una concesión en una partición determinada.

Por ejemplo, supongamos que comienza con la instalación y las suposiciones siguientes para un centro de Event Hubs:

1. 10 particiones.
1. 1000 eventos distribuidos uniformemente en todas las particiones = > 100 mensajes en cada partición.

Cuando se habilita la función por primera vez, solo hay una instancia de la función. Vamos a llamar a esta instancia de función Function_0. Function_0 tendrá 1 EPH que administra para obtener una concesión en las 10 particiones. Comenzará a leer eventos de las particiones 0-9. Desde este punto en adelante, se producirá una de las siguientes acciones:

* **Solo se necesita 1 función**: Function_0 puede procesar todos los 1000 antes de que la lógica de escalado de Azure Functions se inicie. Por lo tanto, se procesan todos los mensajes de 1000 mediante Function_0.

* **Agregue 1 más instancia de función**: la lógica de escalado de Azure Functions determina que Function_0 tiene más mensajes de los que puede procesar, por lo que se crea una nueva instancia, Function_1. Event Hubs detecta que una nueva instancia de EPH está tratando leer los mensajes. Event Hubs comenzará a equilibrar la carga de las particiones entre las instancias de EPH, p. ej., 0-4 particiones se asignan a Function_0 y 5-9 particiones se asignan a Function_1. 

* **Agregue N instancias de función más**: la lógica de escalado de Azure Functions determina que Function_0 y Function_1 tiene más mensajes que los que puede procesar. Se volverá a escalar hasta Function_2... N, donde N es mayor que las particiones de Event Hubs. Event Hubs equilibrará la carga de las particiones entre las instancias de Function_0...9.

El hecho de que N sea mayor que el número de particiones es algo que solo pasa en la lógica de escalado actual de Azure Functions. Esto se hace para garantizar que siempre haya instancias de EPH disponibles listas para su uso para obtener rápidamente un bloqueo en las particiones cuando estén disponibles de otras instancias. Solo se cobra a los usuarios por los recursos usados cuando se ejecuta la instancia de la función, y no por este aprovisionamiento en exceso.

Si todas las ejecuciones de funciones se realizan sin errores, se agregan puntos de comprobación a la cuenta de almacenamiento asociada. Cuando se agreguen correctamente, los mensajes de 1000 no deberían poderse recuperar.

## <a name="trigger---example"></a>Desencadenador: ejemplo

Vea el ejemplo específico del lenguaje:

* [C#](#trigger---c-example)
* [Script de C# (.csx)](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [JavaScript](#trigger---javascript-example)

### <a name="trigger---c-example"></a>Desencadenador: ejemplo de C#

En el ejemplo siguiente se muestra una [función de C#](functions-dotnet-class-library.md) que registra el cuerpo del mensaje del desencadenador del centro de eventos.

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, TraceWriter log)
{
    log.Info($"C# Event Hub trigger function processed a message: {myEventHubMessage}");
}
```

Para acceder a los [metadatos del evento](#trigger---event-metadata) en el código de función, cree un enlace a un objeto [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) (requiere el uso de una instrucción para `Microsoft.ServiceBus.Messaging`). También puede acceder a las mismas propiedades mediante el uso de expresiones de enlace en la firma del método.  El ejemplo siguiente se muestran las dos maneras de obtener los mismos datos:

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run(
    [EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] EventData myEventHubMessage, 
    DateTime enqueuedTimeUtc, 
    Int64 sequenceNumber,
    string offset,
    TraceWriter log)
{
    log.Info($"Event: {Encoding.UTF8.GetString(myEventHubMessage.GetBytes())}");
    // Metadata accessed by binding to EventData
    log.Info($"EnqueuedTimeUtc={myEventHubMessage.EnqueuedTimeUtc}");
    log.Info($"SequenceNumber={myEventHubMessage.SequenceNumber}");
    log.Info($"Offset={myEventHubMessage.Offset}");
    // Metadata accessed by using binding expressions
    log.Info($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.Info($"SequenceNumber={sequenceNumber}");
    log.Info($"Offset={offset}");
}
```

Para recibir eventos en un lote, convierta `string` o `EventData` en una matriz:

```cs
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string[] eventHubMessages, TraceWriter log)
{
    foreach (var message in eventHubMessages)
    {
        log.Info($"C# Event Hub trigger function processed a message: {message}");
    }
}
```

### <a name="trigger---c-script-example"></a>Desencadenador: ejemplo de script de C#

En el ejemplo siguiente se muestra un enlace de desencadenador de centro de eventos en un archivo *function.json* y una [función de script de C#](functions-reference-csharp.md) que usa el enlace. La función registra el cuerpo del mensaje del desencadenador de centro de eventos.

Los ejemplos siguientes muestran datos de enlace de Event Hubs en el archivo *function.json*. El primer ejemplo es para Functions 1.x y el segundo para Functions 2.x. 

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```
```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Este es el código de script de C#:

```cs
using System;

public static void Run(string myEventHubMessage, TraceWriter log)
{
    log.Info($"C# Event Hub trigger function processed a message: {myEventHubMessage}");
}
```

Para acceder a los [metadatos del evento](#trigger---event-metadata) en el código de función, cree un enlace a un objeto [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) (requiere el uso de una instrucción para `Microsoft.ServiceBus.Messaging`). También puede acceder a las mismas propiedades mediante el uso de expresiones de enlace en la firma del método.  El ejemplo siguiente se muestran las dos maneras de obtener los mismos datos:

```cs
#r "Microsoft.ServiceBus"
using System.Text;
using System;
using Microsoft.ServiceBus.Messaging;

public static void Run(EventData myEventHubMessage,
    DateTime enqueuedTimeUtc, 
    Int64 sequenceNumber,
    string offset,
    TraceWriter log)
{
    log.Info($"Event: {Encoding.UTF8.GetString(myEventHubMessage.GetBytes())}");
    // Metadata accessed by binding to EventData
    log.Info($"EnqueuedTimeUtc={myEventHubMessage.EnqueuedTimeUtc}");
    log.Info($"SequenceNumber={myEventHubMessage.SequenceNumber}");
    log.Info($"Offset={myEventHubMessage.Offset}");
    // Metadata accessed by using binding expressions
    log.Info($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.Info($"SequenceNumber={sequenceNumber}");
    log.Info($"Offset={offset}");
}
```

Para recibir eventos en un lote, convierta `string` o `EventData` en una matriz:

```cs
public static void Run(string[] eventHubMessages, TraceWriter log)
{
    foreach (var message in eventHubMessages)
    {
        log.Info($"C# Event Hub trigger function processed a message: {message}");
    }
}
```

### <a name="trigger---f-example"></a>Desencadenador: ejemplo de F#

En el ejemplo siguiente se muestra un enlace de desencadenador de centro de eventos en un archivo *function.json* y una [función de F#](functions-reference-fsharp.md) que usa el enlace. La función registra el cuerpo del mensaje del desencadenador de centro de eventos.

Los ejemplos siguientes muestran datos de enlace de Event Hubs en el archivo *function.json*. El primer ejemplo es para Functions 1.x y el segundo para Functions 2.x. 

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```
```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Este es el código de F#:

```fsharp
let Run(myEventHubMessage: string, log: TraceWriter) =
    log.Info(sprintf "F# eventhub trigger function processed work item: %s" myEventHubMessage)
```

### <a name="trigger---javascript-example"></a>Desencadenador: ejemplo de JavaScript

En el ejemplo siguiente se muestra un enlace de desencadenador de centro de eventos en un archivo *function.json* y una [función de JavaScript](functions-reference-node.md) que usa el enlace. La función lee los [metadatos del evento](#trigger---event-metadata) y registra el mensaje.

Los ejemplos siguientes muestran datos de enlace de Event Hubs en el archivo *function.json*. El primer ejemplo es para Functions 1.x y el segundo para Functions 2.x. 

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```
```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Este es el código de JavaScript:

```javascript
module.exports = function (context, eventHubMessage) {
    context.log('Event Hubs trigger function processed message: ', myEventHubMessage);
    context.log('EnqueuedTimeUtc =', context.bindingData.enqueuedTimeUtc);
    context.log('SequenceNumber =', context.bindingData.sequenceNumber);
    context.log('Offset =', context.bindingData.offset);
     
    context.done();
};
```

Para recibir eventos en un lote, establezca `cardinality` en `many` en el archivo *function.json*, como se muestra en los ejemplos siguientes. El primer ejemplo es para Functions 1.x y el segundo para Functions 2.x. 

```json
{
  "type": "eventHubTrigger",
  "name": "eventHubMessages",
  "direction": "in",
  "path": "MyEventHub",
  "cardinality": "many",
  "connection": "myEventHubReadConnectionAppSetting"
}
```
```json
{
  "type": "eventHubTrigger",
  "name": "eventHubMessages",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "cardinality": "many",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Este es el código de JavaScript:

```javascript
module.exports = function (context, eventHubMessages) {
    context.log(`JavaScript eventhub trigger function called for message array ${eventHubMessages}`);
    
    eventHubMessages.forEach(message => {
        context.log(`Processed message ${message}`);
    });

    context.done();
};
```

## <a name="trigger---attributes"></a>Desencadenador: atributos

En las [bibliotecas de clases de C#](functions-dotnet-class-library.md), use el atributo [EventHubTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubTriggerAttribute.cs).

El constructor del atributo toma el nombre del centro de eventos, el nombre del grupo de consumidores y el nombre de una configuración de aplicación que contenga la cadena de conexión. Para obtener más información sobre estas configuraciones, vea la sección [Configuración de desencadenador](#trigger---configuration). Este es un ejemplo de atributo `EventHubTriggerAttribute`:

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, TraceWriter log)
{
    ...
}
```

Para un ejemplo completo, consulte [Desencadenador: ejemplo de C#](#trigger---c-example).

## <a name="trigger---configuration"></a>Desencadenador: configuración

En la siguiente tabla se explican las propiedades de configuración de enlace que se definen en el archivo *function.json* y el atributo `EventHubTrigger`.

|Propiedad de function.json | Propiedad de atributo |DESCRIPCIÓN|
|---------|---------|----------------------|
|**type** | N/D | Se debe establecer en `eventHubTrigger`. Esta propiedad se establece automáticamente cuando se crea el desencadenador en Azure Portal.|
|**dirección** | N/D | Se debe establecer en `in`. Esta propiedad se establece automáticamente cuando se crea el desencadenador en Azure Portal. |
|**name** | N/D | Nombre de la variable que representa el elemento de evento en el código de la función. | 
|**path** |**EventHubName** | Solo Functions 1.x. El nombre del centro de eventos.  | 
|**eventHubName** |**EventHubName** | Solo Functions 2.x. El nombre del centro de eventos.  |
|**consumerGroup** |**ConsumerGroup** | Una propiedad opcional que establece el [grupo de consumidores](../event-hubs/event-hubs-features.md#event-consumers) que se usará para suscribirse a los eventos del centro. Si se pasa por alto, se utilizará el grupo de consumidores `$Default`. | 
|**cardinalidad** | N/D | Para JavaScript. Defínalo como `many` para permitir el procesamiento por lotes.  Si se omite o se define como `one`, se pasa un único mensaje a la función. | 
|**conexión** |**Connection** | El nombre de una configuración de aplicación que contenga la cadena de conexión para el espacio de nombres del centro de eventos. Copie esta cadena de conexión haciendo clic en el botón **Información de conexión** del [espacio de nombres](../event-hubs/event-hubs-create.md#create-an-event-hubs-namespace), no del propio centro de eventos. Esta cadena de conexión debe tener al menos permisos de lectura para activar el desencadenador.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---event-metadata"></a>Desencadenador: metadatos de evento

El desencadenador de Event Hubs proporciona varias [propiedades de metadatos](functions-triggers-bindings.md#binding-expressions---trigger-metadata). Estas propiedades pueden usarse como parte de expresiones de enlace en otros enlaces o como parámetros del código. Estas son propiedades de la clase [EventData](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata).

|Propiedad|Escriba|DESCRIPCIÓN|
|--------|----|-----------|
|`PartitionContext`|[PartitionContext](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.partitioncontext)|Instancia de `PartitionContext`.|
|`EnqueuedTimeUtc`|`DateTime`|Hora de puesta en la cola en UTC.|
|`Offset`|`string`|El desplazamiento de los datos relacionados con el flujo de partición de Event Hubs. El desplazamiento es un marcador o identificador del flujo de Event Hubs. El identificador es único dentro de una partición del flujo de Event Hubs.|
|`PartitionKey`|`string`|La partición a la que se deben enviar los datos del evento.|
|`Properties`|`IDictionary<String,Object>`|Las propiedades de usuario de los datos del evento.|
|`SequenceNumber`|`Int64`|El número de secuencia de registro del evento.|
|`SystemProperties`|`IDictionary<String,Object>`|Las propiedades del sistema, incluidos los datos del evento.|

Consulte los [ejemplos de código](#trigger---example) que utilizan estas propiedades más arriba en este artículo.

## <a name="trigger---hostjson-properties"></a>Desencadenador: propiedades de host.json

El archivo [host.json](functions-host-json.md#eventhub) contiene opciones de configuración que controlan el comportamiento de Event Hubs.

[!INCLUDE [functions-host-json-event-hubs](../../includes/functions-host-json-event-hubs.md)]

## <a name="output"></a>Salida

Use el enlace de salida de Event Hubs para escribir eventos en una secuencia. Debe tener permiso de envío a un centro de eventos para escribir eventos en él.

Asegúrese de que las referencias de los paquetes necesarios están en su lugar: [Functions 1.x](#packages---functions-1.x) o [Functions 2.x](#packages---functions-2.x) 

## <a name="output---example"></a>Salida: ejemplo

Vea el ejemplo específico del lenguaje:

* [C#](#output---c-example)
* [Script de C# (.csx)](#output---c-script-example)
* [F#](#output---f-example)
* [JavaScript](#output---javascript-example)

### <a name="output---c-example"></a>Salida: ejemplo de C#

En el ejemplo siguiente se muestra una [función de C#](functions-dotnet-class-library.md) que escribe un mensaje en un centro de eventos usando el valor devuelto del método como resultado:

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, TraceWriter log)
{
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
    return $"{DateTime.Now}";
}
```

### <a name="output---c-script-example"></a>Salida: ejemplo de script de C#

En el ejemplo siguiente se muestra un enlace de desencadenador de centro de eventos en un archivo *function.json* y una [función de script de C#](functions-reference-csharp.md) que usa el enlace. La función escribe un mensaje a un centro de eventos.

Los ejemplos siguientes muestran datos de enlace de Event Hubs en el archivo *function.json*. El primer ejemplo es para Functions 1.x y el segundo para Functions 2.x. 

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```
```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

Este es el código de script de C# que crea un mensaje:

```cs
using System;

public static void Run(TimerInfo myTimer, out string outputEventHubMessage, TraceWriter log)
{
    String msg = $"TimerTriggerCSharp1 executed at: {DateTime.Now}";
    log.Verbose(msg);   
    outputEventHubMessage = msg;
}
```

Este es el código de script de C# que crea varios mensajes:

```cs
public static void Run(TimerInfo myTimer, ICollector<string> outputEventHubMessage, TraceWriter log)
{
    string message = $"Event Hub message created at: {DateTime.Now}";
    log.Info(message);
    outputEventHubMessage.Add("1 " + message);
    outputEventHubMessage.Add("2 " + message);
}
```

### <a name="output---f-example"></a>Salida: ejemplo de F#

En el ejemplo siguiente se muestra un enlace de desencadenador de centro de eventos en un archivo *function.json* y una [función de F#](functions-reference-fsharp.md) que usa el enlace. La función escribe un mensaje a un centro de eventos.

Los ejemplos siguientes muestran datos de enlace de Event Hubs en el archivo *function.json*. El primer ejemplo es para Functions 1.x y el segundo para Functions 2.x. 

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```
```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

Este es el código de F#:

```fsharp
let Run(myTimer: TimerInfo, outputEventHubMessage: byref<string>, log: TraceWriter) =
    let msg = sprintf "TimerTriggerFSharp1 executed at: %s" DateTime.Now.ToString()
    log.Verbose(msg);
    outputEventHubMessage <- msg;
```

### <a name="output---javascript-example"></a>Salida: ejemplo de JavaScript

En el ejemplo siguiente se muestra un enlace de desencadenador de centro de eventos en un archivo *function.json* y una [función de JavaScript](functions-reference-node.md) que usa el enlace. La función escribe un mensaje a un centro de eventos.

Los ejemplos siguientes muestran datos de enlace de Event Hubs en el archivo *function.json*. El primer ejemplo es para Functions 1.x y el segundo para Functions 2.x. 

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```
```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

Este es el código JavaScript que envía un mensaje:

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();
    context.log('Event Hub message created at: ', timeStamp);   
    context.bindings.outputEventHubMessage = "Event Hub message created at: " + timeStamp;
    context.done();
};
```

Este es el código JavaScript que envía varios mensajes:

```javascript
module.exports = function(context) {
    var timeStamp = new Date().toISOString();
    var message = 'Event Hub message created at: ' + timeStamp;

    context.bindings.outputEventHubMessage = [];

    context.bindings.outputEventHubMessage.push("1 " + message);
    context.bindings.outputEventHubMessage.push("2 " + message);
    context.done();
};
```

## <a name="output---attributes"></a>Salida: atributos

En las [bibliotecas de clases de C#](functions-dotnet-class-library.md), use el atributo [EventHubAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubAttribute.cs).

El constructor del atributo toma el nombre del centro de eventos y el nombre de una configuración de aplicación que contenga la cadena de conexión. Para obtener más información sobre estas configuraciones, vea [Salida: configuración](#output---configuration). Este es un ejemplo de atributo `EventHub`:

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, TraceWriter log)
{
    ...
}
```

Para obtener un ejemplo completo, consulte [Salida: ejemplo de C#](#output---c-example).

## <a name="output---configuration"></a>Salida: configuración

En la siguiente tabla se explican las propiedades de configuración de enlace que se definen en el archivo *function.json* y el atributo `EventHub`.

|Propiedad de function.json | Propiedad de atributo |DESCRIPCIÓN|
|---------|---------|----------------------|
|**type** | N/D | Debe establecerse en "eventHub". |
|**dirección** | N/D | Debe establecerse en "out". Este parámetro se establece automáticamente cuando se crea el enlace en Azure Portal. |
|**name** | N/D | Nombre de la variable que se usa en el código de la función que representa el evento. | 
|**path** |**EventHubName** | Solo Functions 1.x. El nombre del centro de eventos.  | 
|**eventHubName** |**EventHubName** | Solo Functions 2.x. El nombre del centro de eventos.  |
|**conexión** |**Connection** | El nombre de una configuración de aplicación que contenga la cadena de conexión para el espacio de nombres del centro de eventos. Copie esta cadena de conexión haciendo clic en el botón **Información de conexión** del *espacio de nombres*, no del propio centro de eventos. Esta cadena de conexión debe tener permisos de envío para enviar el mensaje a la secuencia de eventos.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Uso de salidas

En C# y scripts de C#, envíe mensajes mediante un parámetro de método, como `out string paramName`. En script de C#, `paramName` es el valor especificado en la propiedad `name` de *function.json*. Para escribir varios mensajes, puede usar `ICollector<string>` o `IAsyncCollector<string>` en lugar de `out string`.

En JavaScript, puede obtener acceso al evento de salida usando `context.bindings.<name>`. `<name>` es el valor especificado en la propiedad `name` de *function.json*.

## <a name="exceptions-and-return-codes"></a>Excepciones y códigos de retorno

| Enlace | Referencia |
|---|---|
| Centro de eventos | [Guía de operaciones](https://docs.microsoft.com/rest/api/eventhub/publisher-policy-operations) |

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Más información sobre desencadenadores y enlaces de Azure Functions](functions-triggers-bindings.md)
