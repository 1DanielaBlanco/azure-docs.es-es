---
title: Nivel de compatibilidad de los trabajos de Azure Stream Analytics
description: Obtenga información acerca de cómo establecer un nivel de compatibilidad para un trabajo de análisis de Azure Stream Analytics y de los principales cambios en el nivel de compatibilidad más reciente.
services: stream-analytics
author: jasonwhowell
ms.author: jasonh
manager: kfile
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/03/2018
ms.openlocfilehash: 32e73918b2dd98822d42d74002b705ff730145d9
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/06/2018
---
# <a name="compatibility-level-for-azure-stream-analytics-jobs"></a>Nivel de compatibilidad de los trabajos de Azure Stream Analytics
 
El nivel de compatibilidad se refiere a los comportamientos específicos de la versión de un servicio de Azure Stream Analytics. Azure Stream Analytics es un servicio administrado, con actualizaciones regulares de características y mejoras de rendimiento. Normalmente, las actualizaciones estarán disponibles automáticamente para los usuarios finales. Sin embargo, algunas características nuevas pueden introducir cambios importantes, como cambios en el comportamiento de un trabajo existente, cambios en los procesos que consumen datos de estos trabajos, etc. Un nivel de compatibilidad se utiliza para representar un cambio introducido en Stream Analytics. Los cambios importantes siempre se introducen con un nuevo nivel de compatibilidad. 

Un nivel de compatibilidad asegura que los trabajos existentes se ejecuten sin errores. Cuando se crea un nuevo trabajo de Stream Analytics, se recomienda crearlo con el nivel de compatibilidad más reciente que tenga disponible. 
 
## <a name="set-a-compatibility-level"></a>Establecer un nivel de compatibilidad 

Los niveles de compatibilidad controlan el comportamiento en tiempo de ejecución de un trabajo de Stream Analytics. Puede establecer el nivel de compatibilidad de un trabajo de Stream Analytics desde el portal o con la [llamada de API de REST para crear un trabajo](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job). Actualmente, Azure Stream Analytics admite dos niveles de compatibilidad "1.0" y "1.1". De forma predeterminada, el nivel de compatibilidad se establece en "1.0", que se introdujo durante la disponibilidad general de Azure Stream Analytics. Para actualizar el valor predeterminado, vaya a su trabajo de Stream Analytics > seleccione la opción **Nivel de compatibilidad** en la sección **Configurar** y cambie el valor. 

Asegúrese de detener el trabajo antes de actualizar el nivel de compatibilidad. No se puede actualizar el nivel de compatibilidad si el trabajo está en un estado de ejecución. 

![Nivel de compatibilidad del portal](media\stream-analytics-compatibility-level/image1.png)

 
Cuando se actualiza el nivel de compatibilidad, el compilador de T-SQL valida el trabajo con la sintaxis que se corresponde con el nivel de compatibilidad seleccionado. 

## <a name="major-changes-in-the-latest-compatibility-level-11"></a>Principales cambios del nivel de compatibilidad más reciente (1.1)

En el nivel de compatibilidad 1.1 se introdujeron los siguientes cambios principales:

* **Formato XML de Service Bus**  

  * **versiones anteriores:** Azure Stream Analytics usaba DataContractSerializer, por lo que el contenido del mensaje incluía etiquetas XML. Por ejemplo: 
    
   @\u0006string\b3http://schemas.microsoft.com/2003/10/Serialization/\u0001{ “SensorId”:”1”, “Temperature”:64\}\u0001 

  * **versión actual:** el contenido del mensaje contiene directamente la secuencia sin ninguna etiqueta adicional. Por ejemplo: 
  
   { “SensorId”:”1”, “Temperature”:64} 
 
* **Distinción entre mayúsculas y minúsculas persistente para nombres de campo**  

  * **versiones anteriores:** los nombres de campo se cambiaban a minúsculas cuando los procesaba el motor de Azure Stream Analytics. 

  * **versión actual:** la distinción entre mayúsculas y minúsculas se mantiene para nombres de campo que procesa el motor de Azure Stream Analytics. 

  > [!NOTE] 
  > La distinción entre mayúsculas y minúsculas persistente aún no está disponible para trabajos de Stream Analytics hospedados mediante un entorno de Edge. En consecuencia, todos los nombres de campo se convierten a minúsculas si el trabajo se hospeda en Edge. 

* **FloatNaNDeserializationDisabled**  

  * **versiones anteriores:** el comando CREATE TABLE no filtraba eventos con NaN (por ejemplo, Infinity, -Infinity) en una columna de tipo FLOAT, porque están fuera del intervalo documentado para esos números.

  * **versión actual:** el comando CREATE TABLE le permite especificar un esquema seguro. El motor de Stream Analytics valida que los datos se ajustan a este esquema. Con este modelo, el comando puede filtrar eventos con valores NaN. 

* **Deshabilitación de la conversión automática a tipo básico para las cadenas de fecha y hora de JSON**  

  * **versiones anteriores:** el analizador JSON convertía automáticamente los valores de cadena con información de fecha, hora y zona al tipo DateTime y, después, los convertía a UTC. Esto hacía que se perdiese la información de la zona horaria.

  * **versión actual:** ya no existe la conversión automática de los valores de cadenas con información de fecha, hora y zona al tipo DateTime. Como resultado, se mantiene la información de zona horaria. 

## <a name="next-steps"></a>Pasos siguientes
* [Guía de solución de problemas de Azure Stream Analytics](stream-analytics-troubleshooting-guide.md)
* [Hoja Estado de recursos de Stream Analytics](stream-analytics-resource-health.md)