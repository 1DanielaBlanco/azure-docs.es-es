---
title: Recompilación de un índice de Azure Search o actualización del contenido de búsqueda | Microsoft Docs
description: Agregue elementos nuevos, actualice elementos o documentos existentes o elimine documentos obsoletos en una indexación de recompilación completa o incremental parcial para actualizar un índice de Azure Search.
services: search
author: HeidiSteen
manager: cgronlun
ms.service: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: heidist
ms.openlocfilehash: cb99096c1217fca1527b17946dc12390ddf3f62c
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/01/2018
ms.locfileid: "34660204"
---
# <a name="how-to-scale-out-indexing-in-azure-seearch"></a>Cómo escalar la indización horizontalmente en Azure Search

A medida que los volúmenes de datos aumentan o las necesidades de procesamiento cambian, es posible que las [recompilaciones simples y los trabajos de reindización](search-howto-reindex.md) no basten. 

Como primer paso para cumplir con el aumento en las demandas, se recomienda aumentar la [escala y capacidad](search-capacity-planning.md) dentro de los límites del servicio existente. 

Un segundo paso, si puede usar [indizadores](search-indexer-overview.md), agrega mecanismos para la indización escalable. Los indexadores incluyen un programador integrado que permite distribuir la indexación en intervalos regulares o extender el procesamiento más allá de la ventana de 24 horas. Además, cuando se emparejan con las definiciones de los orígenes de datos, los indexadores ayudan a alcanzar una forma de paralelismo mediante la partición de los datos y el uso de programaciones para ejecutar en paralelo.

### <a name="scheduled-indexing-for-large-data-sets"></a>Indexación programada para conjuntos de datos de gran tamaño

La programación es un mecanismo importante para procesar conjuntos de datos de gran tamaño y análisis de ejecución lenta, como el análisis de imágenes en una canalización de Cognitive Search. El procesamiento de los indexadores funciona dentro de una ventana de 24 horas. Si el procesamiento no se completa en menos de 24 horas, los comportamientos de la programación de los indexadores pueden resultar beneficiosos. 

De manera predeterminada, la indexación programada se inicia en intervalos específicos, habitualmente con un trabajo que se completa antes de reanudar en el intervalo programado siguiente. Sin embargo, si el procesamiento no se completa dentro del intervalo, el indexador se detiene (porque se agotó el tiempo). En el intervalo siguiente, el procesamiento se reanuda donde quedó, porque el sistema lleva un registro de dónde sucedió eso. 

En la práctica, para cargas de índice que abarcan varios días, puede poner el indexador en una programación de 24 horas. Cuando la indexación se reanuda para el próximo período de 24 horas, se reinicia en el último documento correcto conocido. De este modo, un indexador puede abrirse camino a través del trabajo pendiente de un documento en una serie de días hasta procesar todos los documentos no procesados. Para más información sobre este enfoque, consulte la sección sobre la [indexación de conjuntos de datos de gran tamaño](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets).

<a name="parallel-indexing"></a>

## <a name="parallel-indexing"></a>Indexación en paralelo

Otra opción es configurar una estrategia de indexación en paralelo. En el caso de requisitos de indexación no rutinaria y que requieren muchos recursos informáticos, como OCR o documentos escaneados en una canalización de Cognitive Search, el enfoque adecuado para esa meta específica puede ser una estrategia de indexación en paralelo. En la canalización de enriquecimiento de Cognitive Search, el procesamiento de lenguaje natural y el análisis de imágenes implican una ejecución prolongada. La indexación en paralelo en un servicio que no administra soluciones de consulta de manera simultánea podría resultar una opción viable para trabajar a través de un gran volumen de contenido de procesamiento lento. 

Una estrategia de procesamiento paralelo tiene estos elementos:

+ Divida los datos de origen entre varios contenedores o varias carpetas virtuales dentro del mismo contenedor. 
+ Asigne cada pequeño conjunto de datos a un [origen de datos](https://docs.microsoft.com/rest/api/searchservice/create-data-source), emparejado con su propio [indexador](https://docs.microsoft.com/rest/api/searchservice/create-indexer).
+ En el caso de Cognitive Search, haga referencia al mismo [conjunto de datos](https://docs.microsoft.com/rest/api/searchservice/create-skillset) en cada definición de indexador.
+ Escriba en el mismo índice de búsqueda de destino. 
+ Programe todos los indexadores para que se ejecuten al mismo tiempo.

> [!Note]
> Azure Search no permite dedicar réplicas o particiones a cargas de trabajo específicas. El riesgo de una indexación simultánea intensa sobrecarga el sistema y perjudica el rendimiento de las consultas. Si tiene un entorno de prueba, implemente la indexación en paralelo ahí primero para comprender las ventajas y los inconvenientes.

## <a name="configure-parallel-indexing"></a>Configuración de la indexación en paralelo

En el caso de los indexadores, la capacidad de procesamiento se basa parcialmente en un subsistema de indexador para cada unidad de servicio que el servicio de búsqueda usa. Es posible tener varios indexadores simultáneos en el servicio de Azure Search aprovisionado en los niveles Básico o Estándar con dos réplicas al menos. 

1. En [Azure Portal](https://portal.azure.com), en la página **Información general** del panel del servicio de búsqueda, compruebe el **plan de tarifa** para comprobar que puede permitir la indexación en paralelo. Los niveles Básico y Estándar ofrecen varias réplicas.

2. En **Configuración** > **Escala**, [aumente las réplicas](search-capacity-planning.md) para el procesamiento paralelo: una réplica adicional para cada carga de trabajo de indexador. Deje un número suficiente para el volumen de consultas existente. No se recomienda sacrificar las cargas de trabajo por la indexación.

3. Distribuya los datos en varios contenedores en un nivel que puedan alcanzar los indexadores de Azure Search. Esto podría tratarse de varias tablas en Azure SQL Database, varios contenedores en Azure Blob Storage o varias colecciones. Defina un objeto de origen de datos para cada tabla o contenedor.

4. Cree y programe varios indexadores para que se ejecuten en paralelo:

   + Considere un servicio con seis réplicas. Configure seis indexadores, cada uno asignado a un origen de datos con un sexto del conjunto de datos para dividir en seis todo el conjunto de datos. 

   + Dirija cada indexador al mismo índice. En el caso de las cargas de trabajo de Cognitive Search, dirija cada indexador al mismo conjunto de aptitudes.

   + Dentro de cada definición de indexador, programe el mismo patrón de ejecución del tiempo de ejecución. Por ejemplo, `"schedule" : { "interval" : "PT8H", "startTime" : "2018-05-15T00:00:00Z" }` crea una programación para el 15-05-2018 en todos los indexadores, que se ejecutará a intervalos de ocho horas.

A la hora programada, todos los indexadores empezarán a ejecutarse, cargarán los datos, aplicarán los enriquecimientos (si configuró una canalización de Cognitive Search) y escribirán en el índice. Azure Search no bloquea las actualizaciones del índice. Las escrituras simultáneas se administran y se realiza un reintento si una escritura determinada no se realizó correctamente la primera vez.

> [!Note]
> Cuando aumente las réplicas, considere aumentar el número de particiones si se espera que el tamaño del índice aumente de manera considerable. Las particiones almacenan segmentos del contenido indexado: cuantas más particiones tenga, más pequeño será el segmento que cada una debe almacenar.

## <a name="see-also"></a>Otras referencias

+ [Información general del indexador](search-indexer-overview.md)
+ [Indexing in the portal](search-import-data-portal.md) (Indexación en el portal)
+ [Azure SQL Database indexer](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md) (Indexador de Azure SQL Database)
+ [Indexador de Azure Cosmos DB](search-howto-index-cosmosdb.md)
+ [Indexador de Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)
+ [Indexador de Azure Table Storage](search-howto-indexing-azure-tables.md)
+ [Security in Azure Search](search-security-overview.md) (Seguridad en Azure Search)