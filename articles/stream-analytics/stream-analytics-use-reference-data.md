---
title: Uso de datos de referencia para las búsquedas en Azure Stream Analytics
description: En este artículo se describe cómo usar los datos de referencia para buscar o poner en correlación datos en el diseño de consultas de un trabajo de Azure Stream Analytics.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/25/2018
ms.openlocfilehash: f87337d51b86f6b1eb053c1b618a2fc0696a9eb2
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/18/2018
ms.locfileid: "39114514"
---
# <a name="using-reference-data-for-lookups-in-stream-analytics"></a>Uso de datos de referencia para las búsquedas en Stream Analytics
Los datos de referencia (también denominados tabla de consulta) son un conjunto finito de datos estáticos o de cambio lento de naturaleza, que se usan para realizar una búsqueda o para relacionarlos con el flujo de datos. Azure Stream Analytics carga los datos de referencia en la memoria para lograr un procesamiento del flujo de baja latencia. Para usar los datos de referencia en un trabajo de Azure Stream Analytics, por lo general usará una [instrucción JOIN de los datos de referencia](https://msdn.microsoft.com/library/azure/dn949258.aspx) en la consulta. Stream Analytics usa Azure Blob Storage como capa de almacenamiento para los datos de referencia y con Azure Data Factory los datos de referencia se pueden transformar o copiar en Azure Blob Storage para su uso como datos de referencia, desde [cualquier número de almacenes de datos locales y en la nube](../data-factory/copy-activity-overview.md). Los datos de referencia se modelan como una secuencia de blobs (que se define en la configuración de entrada) en orden ascendente por la fecha y hora que se especifique en el nombre del blob. **Solo** se admite que se agreguen al final de la secuencia con una fecha y hora **posteriores** a las especificadas en el último blob de la secuencia.

Stream Analytics admite datos de referencia con **tamaño máximo de 300 MB**. El tamaño máximo de 300 MB de datos de referencia es factible solo con consultas sencillas. A medida que aumenta la complejidad de la consulta para incluir el procesamiento con estado, tal como los agregados por intervalos de tiempo, combinaciones temporales y funciones de análisis temporal, se espera que se reduzca el tamaño máximo admitido de datos de referencia. Si Azure Stream Analytics no puede cargar los datos de referencia y realizar operaciones complejas, el trabajo se quedará sin memoria y producirá un error. En estos casos, la métrica de porcentaje de uso de SU llegará a 100 %.    

|**Número de unidades de streaming**  |**Tamaño máximo aprox. admitido (en MB)**  |
|---------|---------|
|1   |50   |
|3   |150   |
|6 y más   |300   |

Aumentar el número de unidades de streaming de un trabajo a más de 6 no aumenta el tamaño máximo admitido de datos de referencia.

La compatibilidad con la compresión no está disponible para los datos de referencia. 

## <a name="configuring-reference-data"></a>Configuración de datos de referencia
Para configurar los datos de referencia, tiene que crear primero una entrada que sea del tipo **Datos de referencia**. En la siguiente tabla se explica cada propiedad que necesitará proporcionar al crear la entrada de los datos de referencia con su descripción:

|**Nombre de la propiedad**  |**Descripción**  |
|---------|---------|
|Alias de entrada   | Nombre descriptivo que se usará en la consulta de trabajo para hacer referencia a esta entrada.   |
|Cuenta de almacenamiento   | El nombre de la cuenta de almacenamiento donde se encuentran los blobs. Si está en la misma suscripción que su trabajo de Stream Analytics, puede seleccionarla en el menú desplegable.   |
|Clave de cuenta de almacenamiento   | La clave secreta asociada con la cuenta de almacenamiento. Se rellena automáticamente si la cuenta de almacenamiento está en la misma suscripción que el trabajo de Stream Analytics .   |
|Contenedor de almacenamiento   | Los contenedores proporcionan una agrupación lógica de los blobs almacenados en Microsoft Azure Blob service. Cuando carga un blob a Blob service, debe especificar un contenedor para ese blob.   |
|Patrón de la ruta de acceso   | La ruta de acceso para ubicar los blobs dentro del contenedor especificado. Dentro de la ruta, puede elegir especificar una o más instancias de las siguientes dos variables:<BR>{date}, {time}<BR>Ejemplo 1: products/{date}/{time}/product-list.csv<BR>Ejemplo 2: products/{date}/product-list.csv<BR><br> Si el blob no existe en la ruta de acceso especificada, el trabajo de Stream Analytics esperará indefinidamente a que el blob esté disponible.   |
|Formato de fecha [opcional]   | Si ha usado {date} dentro del patrón de ruta de acceso especificado, puede seleccionar el formato de fecha en el que se organizan los blobs en la lista desplegable de formatos admitidos.<BR>Ejemplo: AAAA/MM/DD, MM/DD/AAAA, etc.   |
|Formato de hora [opcional]   | Si ha usado {time} dentro del patrón de ruta de acceso especificado, puede seleccionar el formato de hora en el que se organizan los blobs en la lista desplegable de formatos admitidos.<BR>Ejemplo: HH, HH/mm o HH: mm  |
|Formato de serialización de eventos   | Para asegurarse de que las consultas funcionen como se espera, Stream Analytics debe saber cuál es el formato de serialización que usa para los flujos de datos entrantes. Para los datos de referencia, los formatos admitidos son CSV y JSON.  |
|Encoding   | Por el momento, UTF-8 es el único formato de codificación compatible.  |

## <a name="generating-reference-data-on-a-schedule"></a>Generación de datos de referencia en una programación
Si los datos de referencia son un conjunto de datos que cambia con poca frecuencia, se pueden actualizar los datos de referencia especificando un patrón de ruta de acceso en la configuración de entrada con los tokens de sustitución de {date} y {time}. Stream Analytics selecciona las definiciones actualizadas de los datos de referencia según este patrón de ruta de acceso. Por ejemplo, un patrón de `sample/{date}/{time}/products.csv` con un formato de fecha de **"AAAA-MM-DD"** y un formato de hora de **"HH:mm"** indica a Stream Analytics que seleccione el blob actualizado `sample/2015-04-16/17-30/products.csv` a las 17:30 del 16 de abril de 2015 (zona horaria UTC).

> [!NOTE]
> En estos momentos, los trabajos de Stream Analytics buscan la actualización de blobs solo cuando la hora de la máquina coincide con la hora codificada en el nombre del blob. Por ejemplo, el trabajo buscará `sample/2015-04-16/17-30/products.csv` en cuanto sea posible, pero no antes de las 17:30 del 16 de abril de 2015 (zona horaria UTC). No buscará *nunca* un blob con una hora codificada anterior a la última detectada.
> 
> Por ejemplo, una vez que el trabajo encuentre el blob `sample/2015-04-16/17-30/products.csv`, ignorará los archivos cuya fecha codificada sea anterior a las 17:30 del 16 de abril de 2015. Por tanto, si un blob `sample/2015-04-16/17-25/products.csv` se crea en el mismo contenedor posteriormente, el trabajo no lo usará.
> 
> Del mismo modo, si `sample/2015-04-16/17-30/products.csv` solo se crea a las 22:03 del 16 de abril de 2015, pero en el contenedor no hay ningún blob con una fecha anterior, el trabajo usará este archivo a partir de las 22:03 del 16 de abril de 2015, además de los datos de referencia anteriores hasta ese momento.
> 
> Una excepción se produce cuando el trabajo debe volver a procesar datos anteriores en el tiempo o cuando se inicia por primera vez. En momento de iniciarse, el trabajo busca el blob más reciente generado antes de la hora de inicio del trabajo especificada. Esto se hace para garantizar que haya un conjunto de datos de referencia **no vacío** al iniciarse el trabajo. Si no se encuentra ninguno, el trabajo muestra el diagnóstico siguiente: `Initializing input without a valid reference data blob for UTC time <start time>`.
> 
> 

[Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) puede usarse para organizar la tarea de crear los blobs actualizados requeridos por Stream Analytics para actualizar las definiciones de datos de referencia. Factoría de datos es un servicio de integración de datos basado en la nube que organiza y automatiza el movimiento y la transformación de datos. Factoría de datos admite la [conexión a un gran número de almacenes de datos locales y en la nube](../data-factory/copy-activity-overview.md) y el desplazamiento sencillo de los datos con la regularidad que se especifique. Para obtener más información e instrucciones paso a paso sobre cómo configurar una canalización de Factoría de datos para generar datos de referencia para Stream Analytics que se actualiza según una programación predefinida, consulte este [ejemplo de GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ReferenceDataRefreshForASAJobs).

## <a name="tips-on-refreshing-your-reference-data"></a>Sugerencias sobre cómo actualizar los datos de referencia
1. Si sobrescribe blobs de datos de referencia, el Stream Analytics no volverá a cargar el blob y, en algunos casos, puede impedir que el trabajo se ejecute. La mejor forma de cambiar datos de referencia consiste en agregar un nuevo blob con el mismo patrón de ruta de acceso y contenedor definidos en la entrada del trabajo, y usar una fecha y hora **posterior** a la especificada en el último blob de la secuencia.
2. Los blobs de datos de referencia **no** se ordenan por la hora de "Última modificación" del blob sino únicamente por la fecha y la hora que se especifiquen en el nombre del blob mediante las sustituciones de {date} y {time}.
3. Para evitar tener que enumerar un gran número de blobs, considere la posibilidad de eliminar los blobs muy antiguos para los que ya no se va a realizar el procesamiento. Tenga en cuenta que ASA puede tener que reprocesar una pequeña cantidad en algunos escenarios como un reinicio.

## <a name="next-steps"></a>Pasos siguientes
> [!div class="nextstepaction"]
> [Guía de inicio rápido: Creación de un trabajo de Stream Analytics mediante Azure Portal](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
