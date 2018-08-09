---
title: Referencia de Azure Functions para desarrolladores de Java | Microsoft Docs
description: Aprenda a desarrollar funciones con Java.
services: functions
documentationcenter: na
author: rloutlaw
manager: justhe
keywords: Azure Functions, funciones, procesamiento de eventos, webhooks, proceso dinámico, arquitectura sin servidor, java
ms.service: functions
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/07/2017
ms.author: routlaw
ms.openlocfilehash: 65964372cf2a0aa42be967f7c93749c58a9f56dd
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2018
ms.locfileid: "39621776"
---
# <a name="azure-functions-java-developer-guide"></a>Guía de Azure Functions para desarrolladores de Java

[!INCLUDE [functions-java-preview-note](../../includes/functions-java-preview-note.md)]

## <a name="programming-model"></a>Modelo de programación 

La instancia de Azure Functions debe ser un método de clase sin estado que procese entradas y produzca salidas. Aunque puede escribir métodos de instancia, la función no debe depender de los campos de instancia de la clase. Todos los métodos de la función deben tener un modificador de acceso `public`.

## <a name="triggers-and-annotations"></a>Desencadenadores y anotaciones

Normalmente, las funciones de Azure se invocan a causa de un desencadenador externo. La función debe procesar ese desencadenador y las entradas asociadas para generar una o más salidas.

Las anotaciones de Java se incluyen en el paquete `azure-functions-java-core` para enlazar las entradas y salidas a los métodos. Las anotaciones y los enlaces de los desencadenadores de entrada y las salidas se incluyen en la tabla siguiente:

Enlace | Anotación
---|---
CosmosDB | N/D
HTTP | <ul><li>`HttpTrigger`</li><li>`HttpOutput`</li></ul>
Mobile Apps | N/D
Notification Hubs | N/D
Storage Blob | <ul><li>`BlobTrigger`</li><li>`BlobInput`</li><li>`BlobOutput`</li></ul>
Cola de almacenamiento | <ul><li>`QueueTrigger`</li><li>`QueueOutput`</li></ul>
Tabla de almacenamiento | <ul><li>`TableInput`</li><li>`TableOutput`</li></ul>
Temporizador | <ul><li>`TimerTrigger`</li></ul>
Twilio | N/D

Las entradas y salidas de los desencadenadores para la aplicación también se definen en [function.json](/azure/azure-functions/functions-reference#function-code).

> [!IMPORTANT] 
> Debe configurar una cuenta de Azure Storage en [local.settings.json](/azure/azure-functions/functions-run-local#local-settings-file) para ejecutar los desencadenadores de Azure Blob/Queue/Table Storage de manera local.

Ejemplo con anotaciones:

```java
import com.microsoft.azure.serverless.functions.annotation.HttpTrigger;
import com.microsoft.azure.serverless.functions.ExecutionContext;

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {"post"},  authLevel = AuthorizationLevel.ANONYMOUS) 
        String req, ExecutionContext context) {
        return String.format(req);
    }
}
```

La misma función escrita sin anotaciones:

```java
package com.example;

public class MyClass {
    public static String echo(String in) {
        return in;
    }
}
```

con el archivo `function.json` correspondiente:

```json
{
  "scriptFile": "azure-functions-example.jar",
  "entryPoint": "com.example.MyClass.echo",
  "bindings": [
    {
      "type": "httpTrigger",
      "name": "req",
      "direction": "in",
      "authLevel": "anonymous",
      "methods": [ "post" ]
    },
    {
      "type": "http",
      "name": "$return",
      "direction": "out"
    }
  ]
}

```

## <a name="data-types"></a>Tipo de datos

Puede utilizar todos los tipos de datos de Java para la entrada y salida, incluidos los tipos nativos; los personalizados de Java y los tipos especializados de Azure que se definen en el paquete `azure-functions-java-core`. Los intentos en tiempo de ejecución de Azure Functions convierten la entrada recibida en el tipo que solicita el código.

### <a name="strings"></a>Cadenas

Los valores pasados a métodos de función se convierten a cadenas si el tipo de parámetro de entrada correspondiente para la función es `String`. 

### <a name="plain-old-java-objects-pojos"></a>Objetos POJO

Las cadenas con formato JSON se convierten a tipos de Java si la entrada del método función espera ese tipo de Java. Esta conversión permite pasar entradas JSON a las funciones y trabajar con tipos de Java en el código sin tener que aplicar la conversión en el propio código.

Los tipos POJO que se utilizan como entradas para las funciones deben tener el mismo modificador de acceso `public` que los métodos de la función donde se utilizan. No tiene que declarar los campos de la clase POJO `public`. Por ejemplo, una cadena JSON `{ "x": 3 }` puede convertirse al tipo POJO siguiente:

```Java
public class MyData {
    private int x;
}
```

### <a name="binary-data"></a>Datos binarios

Los datos binarios se representan como `byte[]` en el código de las funciones de Azure. Enlace entradas o salidas binarias a las funciones mediante la configuración del campo `dataType` de function.json en `binary`:

```json
 {
  "scriptFile": "azure-functions-example.jar",
  "entryPoint": "com.example.MyClass.echo",
  "bindings": [
    {
      "type": "blob",
      "name": "content",
      "direction": "in",
      "dataType": "binary",
      "path": "container/myfile.bin",
      "connection": "ExampleStorageAccount"
    },
  ]
}
```

A continuación, utilícelo en el código de la función:

```java
// Class definition and imports are omitted here
public static String echoLength(byte[] content) {
}
```

Use el tipo `OutputBinding<byte[]>` para realizar un enlace de salida binaria.


## <a name="function-method-overloading"></a>Sobrecarga de los métodos de función

Se pueden sobrecargar los métodos de función del mismo nombre, pero de distintos tipos. Por ejemplo, puede tener `String echo(String s)` y `String echo(MyType s)` en una clase y el sistema en tiempo de ejecución de Azure Functions decide cuál invocar al examinar el tipo de entrada real (para una entrada HTTP, el tipo MIME `text/plain` deriva en `String`, mientras que `application/json` representa `MyType`).

## <a name="inputs"></a>Entradas

Las entradas se dividen en dos categorías Azure Functions: una es la entrada del desencadenador y la otra es una entrada adicional. Aunque son diferentes en `function.json`, se usan igual en el código de Java. Tomemos el siguiente fragmento de código a modo de ejemplo:

```java
package com.example;

import com.microsoft.azure.serverless.functions.annotation.BindingName;
import java.util.Optional;

public class MyClass {
    public static String echo(Optional<String> in, @BindingName("item") MyObject obj) {
        return "Hello, " + in.orElse("Azure") + " and " + obj.getKey() + ".";
    }

    private static class MyObject {
        public String getKey() { return this.RowKey; }
        private String RowKey;
    }
}
```

La anotación `@BindingName` acepta una propiedad `String` que representa el nombre del enlace/desencadenador definido en `function.json`:

```json
{
  "scriptFile": "azure-functions-example.jar",
  "entryPoint": "com.example.MyClass.echo",
  "bindings": [
    {
      "type": "httpTrigger",
      "name": "req",
      "direction": "in",
      "authLevel": "anonymous",
      "methods": [ "put" ],
      "route": "items/{id}"
    },
    {
      "type": "table",
      "name": "item",
      "direction": "in",
      "tableName": "items",
      "partitionKey": "Example",
      "rowKey": "{id}",
      "connection": "ExampleStorageAccount"
    },
    {
      "type": "http",
      "name": "$return",
      "direction": "out"
    }
  ]
}
```

Por lo tanto, cuando se invoca esta función, la carga de la solicitud HTTP pasa un `String` opcional para el argumento `in` y un tipo `MyObject` de Azure Table Storage para el argumento `obj`. Use el tipo `Optional<T>` para controlar entradas en las funciones que pueden ser nulas.

## <a name="outputs"></a>Salidas

Las salidas se expresan como valores devueltos o como parámetros de salida. Si hay una sola salida, se recomienda utilizar el valor devuelto. Para varias salidas, deberá utilizar parámetros de salida.

El valor devuelto es la forma más sencilla de salida, solo devuelve el valor de cualquier tipo y el sistema en tiempo de ejecución de Azure Functions intentará serializar el tipo real (por ejemplo, una respuesta HTTP). En `functions.json`, se usa `$return` como nombre del enlace de salida.

Para generar varios valores de salida, use el tipo `OutputBinding<T>` que se define en el paquete `azure-functions-java-core`. Si necesita realizar una respuesta HTTP e insertar un mensaje en una cola, puede escribir algo similar a lo siguiente:

```java
package com.example;

import com.microsoft.azure.serverless.functions.OutputBinding;
import com.microsoft.azure.serverless.functions.annotation.BindingName;

public class MyClass {
    public static String echo(String body, 
    @QueueOutput(queueName = "messages", connection = "AzureWebJobsStorage", name = "queue") OutputBinding<String> queue) {
        String result = "Hello, " + body + ".";
        queue.setValue(result);
        return result;
    }
}
```

que debe definir el enlace de salida en `function.json`:

```json
{
  "scriptFile": "azure-functions-example.jar",
  "entryPoint": "com.example.MyClass.echo",
  "bindings": [
    {
      "type": "httpTrigger",
      "name": "req",
      "direction": "in",
      "authLevel": "anonymous",
      "methods": [ "post" ]
    },
    {
      "type": "queue",
      "name": "queue",
      "direction": "out",
      "queueName": "messages",
      "connection": "AzureWebJobsStorage"
    },
    {
      "type": "http",
      "name": "$return",
      "direction": "out"
    }
  ]
}
```
## <a name="specialized-types"></a>Tipos especializados

En ocasiones, es posible que una función deba controlar minuciosamente las entradas y salidas. Los tipos especializados del paquete `azure-functions-java-core` sirven para manipular la información de la solicitud y adaptar el estado de retorno de un desencadenador HTTP:

| Tipo especializado      |       Destino        | Uso típico                  |
| --------------------- | :-----------------: | ------------------------------ |
| `HttpRequestMessage<T>`  |    Desencadenador HTTP     | Método get, encabezados o consultas |
| `HttpResponseMessage<T>` | Enlace de salida HTTP | Estado de retorno distinto de 200   |

> [!NOTE] 
> También puede usar la anotación `@BindingName` para obtener encabezados y consultas HTTP. Por ejemplo, `@BindingName("name") String query` recorre en iteración los encabezados de solicitud y las consultas HTTP, y pasa ese valor al método. Por ejemplo, `query` será `"test"` si la dirección URL de solicitud es `http://example.org/api/echo?name=test`.

### <a name="metadata"></a>Metadatos

Los metadatos proceden de orígenes diferentes, como los encabezados HTTP, las consultas HTTP, y los [metadatos de desencadenador](/azure/azure-functions/functions-triggers-bindings#trigger-metadata-properties). Use la anotación `@BindingName` junto con el nombre de metadatos para obtener el valor.

Por ejemplo, `queryValue` en el siguiente fragmento de código será `"test"` si la dirección URL solicitada es `http://{example.host}/api/metadata?name=test`.

```Java
package com.example;

import java.util.Optional;
import com.microsoft.azure.serverless.functions.annotation.*;

public class MyClass {
    @FunctionName("metadata")
    public static String metadata(
        @HttpTrigger(name = "req", methods = { "get", "post" }, authLevel = AuthorizationLevel.ANONYMOUS) Optional<String> body,
        @BindingName("name") String queryValue
    ) {
        return body.orElse(queryValue);
    }
}
```

## <a name="functions-execution-context"></a>Contexto de ejecución de las funciones

Se interactúa con el entorno de ejecución de Azure Functions a través del objeto `ExecutionContext` que se define en el paquete `azure-functions-java-core`. Utilice el objeto `ExecutionContext` para utilizar la información de invocación y de tiempo de ejecución de las funciones en el código.

### <a name="logging"></a>Registro

Al registro del tiempo de ejecución de las funciones se accede a través del objeto `ExecutionContext`. Este registrador está asociado al monitor de Azure y permite marcar las advertencias y los errores detectados durante la ejecución de la función.

El código de ejemplo siguiente registra un mensaje de advertencia al recibir un cuerpo de solicitud en blanco.

```java
import com.microsoft.azure.serverless.functions.annotation.HttpTrigger;
import com.microsoft.azure.serverless.functions.ExecutionContext;

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {"post"}, authLevel = AuthorizationLevel.ANONYMOUS) String req, ExecutionContext context) {
        if (req.isEmpty()) {
            context.getLogger().warning("Empty request body received by function " + context.getFunctionName() + " with invocation " + context.getInvocationId());
        }
        return String.format(req);
    }
}
```

## <a name="environment-variables"></a>Variables de entorno

A menudo resulta deseable extraer información de secreto del código fuente por motivos de seguridad. Esto permite publicar el código en repositorios de código fuente sin proporcionar por accidente las credenciales a otros desarrolladores. Esto puede lograrse simplemente mediante variables de entorno, tanto al ejecutar Azure Functions localmente como al implementar las funciones en Azure.

Para establecer las variables de entorno con facilidad al ejecutar Azure Functions localmente, puede optar por agregar estas variables en el archivo local.settings.json. Si no tiene uno en el directorio raíz del proyecto de la función, puede crear uno. Este es el aspecto que debería tener el archivo:

```xml
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "",
    "AzureWebJobsDashboard": ""
  }
}
```

Cada asignación de clave y valor en el mapa `values` se pondrá a disposición en tiempo de ejecución como variable de entorno, a la que podrá tener acceso mediante una llamada a `System.getenv("<keyname>")`, por ejemplo, `System.getenv("AzureWebJobsStorage")`. Agregar pares adicionales de clave/valor es una práctica aceptada y recomendada.

> [!NOTE]
> Si se sigue este enfoque, asegúrese de que tener en cuenta si agregar el archivo local.settings.json al repositorio omitir archivos, para que no se confirme.

Ahora que el código depende de estas variables de entorno, puede iniciar sesión en Azure Portal para establecer el mismo par de clave/valor en la configuración de aplicación de la función, para que el código funcione de forma equivalente al probar localmente y al implementar en Azure.

## <a name="next-steps"></a>Pasos siguientes
Para obtener más información, consulte los siguientes recursos:

* [Procedimientos recomendados para Azure Functions](functions-best-practices.md)
* [Referencia para desarrolladores de Azure Functions](functions-reference.md)
* [Enlaces y desencadenadores de Azure Functions](functions-triggers-bindings.md)
* [Depuración remota de Azure Functions en Java con Visual Studio Code](https://code.visualstudio.com/docs/java/java-serverless#_remote-debug-functions-running-in-the-cloud)
