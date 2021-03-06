---
title: 'Conceptos y definición del índice: Azure Search'
description: Introducción a los términos de índice y los conceptos de Azure Search, incluida la estructura física de un índice invertido.
author: brjohnstmsft
manager: jlembicz
ms.author: brjohnst
services: search
ms.service: search
ms.topic: conceptual
ms.date: 11/08/2017
ms.custom: seodec2018
ms.openlocfilehash: 5a39021367c2f51125876081e9174eb372d7b9c9
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/16/2019
ms.locfileid: "54353165"
---
# <a name="indexes-and-indexing-overview-in-azure-search"></a>Introducción a los índices y la indización en Azure Search

En Azure Search, un *índice* es un almacén persistente de *documentos* y otras construcciones que se usa para las búsquedas de texto completo y filtrado en los servicios de Azure Search. Un documento es una sola unidad de datos habilitada para búsquedas. Por ejemplo, un minorista de comercio electrónico podría tener un documento para cada objeto que vende, una organización de noticias podría tener un documento para cada artículo, etc. Estos conceptos pueden equipararse a equivalentes de base de datos más conocidos: un *índice* es conceptualmente similar a una *tabla* y los *documentos* son más o menos equivalentes a las *filas* de una tabla.

Cuando agrega o carga documentos o cuando envía consultas de búsqueda a Azure Search, está enviando las solicitudes a un índice específico del servicio de búsqueda. El proceso de agregar documentos a un índice se denomina *indización*.

## <a name="field-types-and-attributes-in-an-azure-search-index"></a>Tipos de campo y atributos en un índice de Azure Search
Al definir el esquema, debe especificar el nombre, el tipo y los atributos de cada campo del índice. El tipo de campo permite clasificar los datos que se almacenan en ese campo. Los atributos se establecen en campos individuales para especificar cómo se usa el campo. En la tabla siguiente se enumeran los tipos y los atributos que puede especificar.

### <a name="field-types"></a>Tipos de campo
| Escriba | DESCRIPCIÓN |
| --- | --- |
| *Edm.String* |Texto que opcionalmente se puede acortar para búsquedas de texto completo (separación de palabras, lematización, etc.). |
| *Collection(Edm.String)* |Una lista de cadenas que opcionalmente se pueden acortar para búsquedas de texto completo. En teoría, no hay ningún límite superior para el número de elementos de una colección, pero el límite de 16 MB en el tamaño de la carga se aplica a las colecciones. |
| *Edm.Boolean* |Contiene valores true/false. |
| *Edm.Int32* |Valores enteros de 32 bits. |
| *Edm.Int64* |Valores enteros de 64 bits. |
| *Edm.Double* |Datos numéricos de precisión doble. |
| *Edm.DateTimeOffset* |Los valores de hora y fecha se representan con el formato OData V4 (por ejemplo, `yyyy-MM-ddTHH:mm:ss.fffZ` o `yyyy-MM-ddTHH:mm:ss.fff[+/-]HH:mm`). |
| *Edm.GeographyPoint* |Un punto que representa una ubicación geográfica en todo el mundo. |

Puede encontrar información más detallada sobre los [tipos de datos de Azure Search admitidos aquí](https://docs.microsoft.com/rest/api/searchservice/Supported-data-types).

### <a name="field-attributes"></a>Atributos de campo
| Atributo | DESCRIPCIÓN |
| --- | --- |
| *Clave* |Una cadena que proporciona el identificador único de cada documento, que se usa para buscar los documentos. Todos los índices deben tener una clave. Solo un campo puede ser la clave y se debe establecer su tipo en Edm.String. |
| *Retrievable* |Establece si el campo se puede devolver en un resultado de búsqueda. |
| *Filterable* |Permite que el campo se use en consultas de filtro. |
| *Sortable* |Permite que una consulta ordene los resultados de búsqueda mediante este campo. |
| *Facetable* |Permite que un campo se use en una estructura de [navegación con facetas](search-faceted-navigation.md) para el filtrado autodirigido. Normalmente los campos que contienen valores repetitivos que se pueden usar para agrupar varios documentos (por ejemplo, varios documentos que forman parte de una única categoría de servicio o un único producto) funcionan mejor como facetas. |
| *Searchable* |Marca el campo como campo de búsqueda de texto completo. |

Puede encontrar información más detallada sobre los [atributos de índice de Azure Search aquí](https://docs.microsoft.com/rest/api/searchservice/Create-Index).

## <a name="guidance-for-defining-an-index-schema"></a>Guía para definir un esquema de índice
Al diseñar el índice, tómese su tiempo en la fase de planeación y reflexione cada decisión. Es importante que tenga en cuenta sus necesidades de negocio y experiencia de búsqueda como usuario al diseñar el índice ya que debe asignar a cada campo los [atributos adecuados](https://docs.microsoft.com/rest/api/searchservice/Create-Index). El cambio de un índice después de la implementación implica volver a generar y volver a cargar los datos.

Si los requisitos de almacenamiento de datos cambian con el tiempo, puede aumentar o disminuir la capacidad agregando o quitando particiones. Para más información, consulte [Administración del servicio de búsqueda en Azure](search-manage.md) o [Límites de servicio](search-limits-quotas-capacity.md).

