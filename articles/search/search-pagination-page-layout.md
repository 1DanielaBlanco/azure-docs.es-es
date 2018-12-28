---
title: 'Cómo paginar los elementos en una página de resultados de la búsqueda: Azure Search'
description: Paginación de Azure Search, un servicio de búsqueda hospedado en la nube en Microsoft Azure.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 08/29/2016
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 5f36dbb72e2518f7e3a27ef3aadec85312d751c2
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/12/2018
ms.locfileid: "53309351"
---
# <a name="how-to-page-search-results-in-azure-search"></a>Cómo paginar los resultados de la búsqueda en Azure Search
Este artículo proporciona orientación sobre la forma de usar la API de REST del servicio Azure Search para implementar los elementos habituales de una página de resultados de búsqueda, como recuentos totales, recuperación de documentos, criterios de ordenación y navegación.

En todos los casos que se mencionan a continuación, las opciones relacionadas con la página que aportan datos o información a la página de resultados de búsqueda se especifican a través de solicitudes [Buscar documento](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) que se envían a su servicio Azure Search. Las solicitudes incluyen un comando GET, ruta de acceso y parámetros de consulta que informan el servicio de lo que se solicita y de cómo formular la respuesta.

> [!NOTE]
> Una solicitud válida incluye una serie de elementos, como una dirección URL del servicio y la ruta de acceso, el verbo HTTP, `api-version`, etc. Para mayor brevedad, hemos acortado los ejemplos para resaltar solo la sintaxis que resulta relevante para la paginación. Consulte la documentación de [API de REST del servicio Azure Search](https://docs.microsoft.com/rest/api/searchservice) para obtener más información acerca de la sintaxis de solicitud.
> 
> 

## <a name="total-hits-and-page-counts"></a>Total de resultados y recuentos de página
Muestra el número total de resultados devueltos por una consulta y, a continuación, devolver los resultados en bloques más pequeños, es fundamental para casi todas las páginas de búsqueda.

![][1]

En Azure Search se utilizan los parámetros `$count`, `$top` y `$skip` para devolver esos valores. En el ejemplo siguiente se muestra un ejemplo de solicitud del total de resultados, que se devuelve como `@OData.count`:

        GET /indexes/onlineCatalog/docs?$count=true

Recuperar documentos en grupos de 15 y mostrar también el total de resultados, comenzando por la primera página:

        GET /indexes/onlineCatalog/docs?search=*$top=15&$skip=0&$count=true

Paginar resultados requiere `$top` y `$skip`, donde `$top` especifica cuántos elementos se devuelven en un lote y `$skip` especifica cuántos elementos se omiten. En el ejemplo siguiente, cada página muestra los siguientes 15 elementos, indicado por los saltos incrementales en el parámetro `$skip` .

        GET /indexes/onlineCatalog/docs?search=*$top=15&$skip=0&$count=true

        GET /indexes/onlineCatalog/docs?search=*$top=15&$skip=15&$count=true

        GET /indexes/onlineCatalog/docs?search=*$top=15&$skip=30&$count=true

## <a name="layout"></a>Diseño
En una página de resultados de búsqueda, puede ser deseable mostrar una imagen en miniatura, un subconjunto de campos y un vínculo a una página de producto completa.

 ![][2]

En Azure Search se utiliza `$select` y un comando de búsqueda para implementar esta experiencia.

Para devolver un subconjunto de campos con un diseño en mosaico:

        GET /indexes/ onlineCatalog/docs?search=*&$select=productName,imageFile,description,price,rating 

Las imágenes y los archivos multimedia no se pueden buscar directamente y se deben almacenar en otra plataforma de almacenamiento, como Almacenamiento de blobs de Azure, para reducir los costes. En el índice y los documentos, defina un campo que almacene la dirección URL del contenido externo. Después puede utilizar el campo como referencia de imagen. La dirección URL de la imagen debe estar en el documento.

Para recuperar una página de descripción de producto para un evento **onClick** , use [Buscar documento](https://docs.microsoft.com/rest/api/searchservice/Lookup-Document) para pasar la clave del documento que se va a recuperar. El tipo de datos de la clave es `Edm.String`. En este ejemplo, es *246810*. 

        GET /indexes/onlineCatalog/docs/246810

## <a name="sort-by-relevance-rating-or-price"></a>Ordenar por relevancia, clasificación o precio
A menudo, el orden predeterminado se basa en la relevancia, pero es habitual poner a disposición de los clientes otros criterios de ordenación para que puedan reorganizar rápidamente los resultados existentes en un orden diferente.

 ![][3]

En Azure Search, la ordenación se basa en la expresión `$orderby` para todos los campos que se indizan como `"Sortable": true.`

La relevancia está estrechamente asociada con perfiles de puntuación. Puede utilizar la puntuación predeterminada, que se basa en el análisis de texto y las estadísticas para ordenar todos los resultados, con las puntuaciones más altas destinadas a documentos con más coincidencias de un término de búsqueda o con coincidencias más importantes.

Los criterios de ordenación alternativos se suelen asociar a eventos **onClick** que llaman a un método que compila el criterio de ordenación. Por ejemplo, con este elemento de página:

 ![][4]

Deberá crear un método que acepte la opción de ordenación seleccionada como entrada y devuelva una lista ordenada de los criterios asociados a esa opción.

 ![][5]

> [!NOTE]
> Aunque la puntuación predeterminada es suficiente para muchos escenarios, se recomienda basar la relevancia en un perfil de puntuación personalizado. Un perfil personalizado de puntuación le ofrece una forma de aumentar los elementos que son más útiles para su negocio. Consulte [Incorporación de un perfil de puntuación](https://docs.microsoft.com/rest/api/searchservice/Add-scoring-profiles-to-a-search-index) para obtener más información. 
> 
> 

## <a name="faceted-navigation"></a>Navegación por facetas
La navegación de búsqueda es habitual en una página de resultados; a menudo se encuentra en un lado o en la parte superior de una página. En Azure Search, la navegación por facetas proporciona una búsqueda autodirigida basándose en filtros predefinidos. Consulte [Navegación por facetas en Azure Search](search-faceted-navigation.md) para obtener más detalles

## <a name="filters-at-the-page-level"></a>Filtros en el nivel de página
Si el diseño de la solución incluye páginas de búsqueda dedicadas para determinados tipos de contenido (por ejemplo, una aplicación comercial en línea que enumera los departamentos en la parte superior de la página), puede insertar una expresión de filtro junto con un evento **onClick** para abrir una página en un estado prefiltrado. 

Puede enviar un filtro con o sin expresión de búsqueda. Por ejemplo, la siguiente solicitud filtrará por el nombre de la marca y devolverá solamente los documentos que coincidan con él.

        GET /indexes/onlineCatalog/docs?$filter=brandname eq ‘Microsoft’ and category eq ‘Games’

Consulte [Search Documents (Azure Search API)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) (Búsqueda de documentos [API de Azure Search]) para más información sobre las expresiones `$filter`.

## <a name="see-also"></a>Otras referencias
* [API de REST del Servicio Azure Search](https://docs.microsoft.com/rest/api/searchservice)
* [Operaciones de índice](https://docs.microsoft.com/rest/api/searchservice/Index-operations)
* [Operaciones del documento](https://docs.microsoft.com/rest/api/searchservice/Document-operations)
* [Navegación por facetas en Azure Search](search-faceted-navigation.md)

<!--Image references-->
[1]: ./media/search-pagination-page-layout/Pages-1-Viewing1ofNResults.PNG
[2]: ./media/search-pagination-page-layout/Pages-2-Tiled.PNG
[3]: ./media/search-pagination-page-layout/Pages-3-SortBy.png
[4]: ./media/search-pagination-page-layout/Pages-4-SortbyRelevance.png
[5]: ./media/search-pagination-page-layout/Pages-5-BuildSort.png 
