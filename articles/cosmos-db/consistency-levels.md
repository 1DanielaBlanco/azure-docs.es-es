---
title: Niveles de coherencia en Azure Cosmos DB | Documentos de Microsoft
description: Azure Cosmos DB tiene cinco niveles de coherencia para ayudar a equilibrar la coherencia, la disponibilidad y la latencia finales.
keywords: coherencia final, azure cosmos db, azure, Microsoft azure
services: cosmos-db
author: aliuy
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 03/27/2018
ms.author: andrl
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e9503759b776bb045c4dc0357b1ab88be1294013
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/10/2018
ms.locfileid: "40038561"
---
# <a name="tunable-data-consistency-levels-in-azure-cosmos-db"></a>Niveles de coherencia de datos optimizables en Azure Cosmos DB
El diseño de Azure Cosmos DB se llevó a cabo desde el principio pensando en la distribución global de cada modelo de datos. Se ha diseñado para que ofrezca garantías de una baja latencia predecible y varios modelos de coherencia moderada bien definidos. Actualmente, Azure Cosmos DB ofrece cinco niveles de coherencia: fuerte, de obsolescencia limitada, de sesión, de prefijo coherente y final. Obsolescencia limitada, sesión, prefijo coherente y posible se denominan "modelos de coherencia moderada", ya que proporcionan menos coherencia que la coherencia fuerte, que es el modelo más coherente disponible. 

Además de los modelos de **coherencia fuerte** y **final** que proporcionan habitualmente las bases de datos distribuidas, Azure Cosmos DB ofrece tres modelos de coherencia codificados y operacionalizados de forma cuidadosa: **obsolescencia limitada**, **sesión** y **prefijo coherente**. La utilidad de cada uno de estos niveles de coherencia se ha validado con casos de uso reales. De forma colectiva, estos cinco niveles de coherencia permiten lograr equilibrios bien razonados entre la coherencia, la disponibilidad y la latencia. 

En el siguiente vídeo, Andrew Liu, administrador de programas de Azure Cosmos DB, muestra las características de distribución global inmediata.

>[!VIDEO https://www.youtube.com/embed/-4FsGysVD14]

## <a name="distributed-databases-and-consistency"></a>Coherencia y bases de datos distribuidas
Las bases de datos distribuidas comerciales se dividen en dos categorías: las bases de datos que no ofrecen opciones de coherencia bien definida en absoluto y las que ofrecen dos opciones de programación opuestas (coherencia fuerte y final). 

La primera supone una carga de detalles pormenorizados de los protocolos de replicación para los desarrolladores de aplicaciones y espera de ellos difíciles compensaciones entre coherencia, disponibilidad, latencia y rendimiento. La última obliga a elegir uno de los dos extremos. A pesar de la gran cantidad de investigación y propuestas de más de 50 modelos de coherencia, la comunidad de bases de datos distribuidas no ha sido capaz de comercializar otros niveles de coherencia distintos del alto y el final. Cosmos DB permite a los desarrolladores elegir entre cinco modelos de coherencia bien definidos dentro del espectro de coherencia: alta, de obsolescencia limitada, [de sesión](http://dl.acm.org/citation.cfm?id=383631), de prefijo coherente y final. 

![Azure Cosmos DB ofrece varios modelos de coherencia bien definida (progresivos) entre los que elegir](./media/consistency-levels/five-consistency-levels.png)

En la tabla siguiente se muestran las garantías específicas que ofrece cada nivel de coherencia.
 
**Niveles de coherencia y garantías**

| Nivel de coherencia | Garantías |
| --- | --- |
| Alta | Linealidad. Se garantiza que las lecturas devolverán la versión más reciente de un elemento.|
| De obsolescencia entrelazada | Prefijo coherente. Los mayoría de los prefijos k y los intervalos t retrasan las lecturas tras las escrituras |
| Sesión   | Prefijo coherente. Lecturas monótonas, escrituras monótonas, lectura de la escritura, escritura tras las lecturas |
| De prefijo coherente | Las actualizaciones devueltas son prefijos de todas las actualizaciones, sin espacios |
| Ocasional  | Lecturas sin orden |

Puede establecer la coherencia predeterminada en la cuenta de Cosmos DB (y después reemplazar la para una solicitud de lectura concreta). Internamente, el nivel de coherencia predeterminado se aplica a los datos de los conjuntos de particiones, que pueden abarcar regiones. Aproximadamente un 73 % de los inquilinos de Azure Cosmos DB usan la coherencia de sesión y un 20 % prefiere la obsolescencia limitada. Aproximadamente el 3 % de los clientes de Azure Cosmos DB experimentan con distintos niveles de coherencia inicialmente antes de fijar una opción de coherencia específica para su aplicación. Solo un 2 % de los inquilinos de Azure Cosmos DB reemplazan los niveles de coherencia por solicitud. 

En Cosmos DB, las lecturas de coherencia de sesión, prefijo coherente y final son el doble de económicas que las de coherencia fuerte o de obsolescencia limitada. Cosmos DB presenta unos Acuerdos de Nivel de Servicio líderes del sector completos, que incluyen garantías de coherencia, además de disponibilidad, rendimiento y latencia. Azure Cosmos DB emplea un [comprobador de linealidad](http://dl.acm.org/citation.cfm?id=1806634), que funciona continuamente sobre la telemetría de servicio y le notifica abiertamente las infracciones de coherencia. Para la obsolescencia limitada, Azure Cosmos DB supervisa y notifica las infracciones de los límites de k y t. Para los cinco niveles de coherencia moderada, Azure Cosmos DB también le notifica directamente la [métrica de obsolescencia limitada probabilística](http://dl.acm.org/citation.cfm?id=2212359).  

## <a name="service-level-agreements"></a>Contratos de nivel de servicio

Azure Cosmos DB ofrece [Acuerdos de Nivel de Servicio](https://azure.microsoft.com/support/legal/sla/cosmos-db/) completos del 99,99 % que garantizan el rendimiento, la coherencia, la disponibilidad y la latencia de las cuentas de base de datos de Azure Cosmos DB en el ámbito de una sola región de Azure configurada con alguno de los cinco niveles de coherencia, o cuentas de base de datos que abarcan varias regiones de Azure, configuradas con alguno de los cuatro niveles de coherencia moderada. Además, independientemente de la elección del nivel de coherencia, Azure Cosmos DB ofrece un Acuerdo del Nivel de Servicio del 99,999 % para la disponibilidad de lectura para cuentas de base de datos que abarcan dos o más regiones de Azure.

## <a name="scope-of-consistency"></a>Ámbito de coherencia
El ámbito de la granularidad de coherencia se limita a una única solicitud de usuario. Una solicitud de escritura puede corresponder a una transacción de inserción, reemplazo, upsert o eliminación. Al igual que con las escrituras, el ámbito de una transacción de lectura o consulta también se limita a una única solicitud de usuario. Es posible que el usuario deba paginar un conjunto grande de resultados, que abarque varias particiones, pero el ámbito de cada transacción de lectura se restringe a una sola página y se atiende desde dentro de una sola partición.

## <a name="consistency-levels"></a>Niveles de coherencia
Se puede configurar el nivel de coherencia predeterminado en la cuenta de la base de datos que se aplica a todos los contenedores (y bases de datos) de la cuenta de Cosmos DB. De manera predeterminada, todas las lecturas y consultas enviadas a los recursos definidos por el usuario usan el nivel de coherencia predeterminado especificado en la cuenta de la base de datos. Puede relajar el nivel de coherencia de una solicitud de lectura o consulta específica que se usa en cada una de las API admitidas. Existen cinco tipos de niveles de coherencia admitidos por el protocolo de replicación de Azure Cosmos DB que proporcionan un equilibrio claro entre las garantías de coherencia específicas y el rendimiento, como se describe en esta sección.

<a id="strong"></a>
**Fuerte**: 

* La coherencia fuerte ofrece una garantía de [linealidad](https://aphyr.com/posts/313-strong-consistency-models) que asegura que las lecturas devuelvan la versión más reciente de un elemento. 
* la coherencia alta garantiza que una escritura solo es visible después de confirmarse de forma duradera por la mayoría del cuórum de réplicas. Una escritura se confirma sincrónicamente de forma duradera tanto por la réplica principal como por el cuórum de las secundarias, o se anula. Una lectura siempre se confirma por la mayoría del cuórum de lectura; un cliente no puede ver nunca una escritura no confirmada o parcial y cuenta con la garantía de que leerá la última escritura confirmada. 
* Las cuentas de Azure Cosmos DB configuradas para usar la coherencia fuerte no pueden asociar más de una región de Azure a su cuenta de Azure Cosmos DB.  
* El costo de una operación de lectura (en términos de [unidades de solicitud](request-units.md) consumidas) con coherencia fuerte es mayor que con la de sesión o la eventual, pero igual que con la de obsolescencia entrelazada.

<a id="bounded-staleness"></a>
**Obsolescencia limitada**: 

* La coherencia de obsolescencia limitada garantiza que las lecturas puedan ir con retraso respecto a las escrituras en un máximo de versiones *K* o prefijos de un elemento o el intervalo de tiempo *t*. 
* Por lo tanto, si elige la obsolescencia limitada, la "obsolescencia" puede configurarse de dos maneras: por el número de versiones *K* del elemento por el que las lecturas van detrás de las escrituras y por el intervalo de tiempo *t*. 
* La obsolescencia limitada ofrece un orden global total, excepto dentro de la "ventana de obsolescencia". La garantía de lectura monotónica existe dentro de una región, tanto dentro como fuera de la "ventana de obsolescencia". 
* La obsolescencia limitada proporciona una garantía de coherencia más fuerte que la coherencia de sesión, la de prefijo coherente o la final. Para las aplicaciones de distribución global, se recomienda utilizar la obsolescencia entrelazada para aquellos escenarios donde se desea disponer de coherencia fuerte pero también de una disponibilidad del 99,99 % y una latencia baja.   
* Las cuentas de Azure Cosmos DB que están configuradas con la coherencia de obsolescencia limitada pueden asociar cualquier número de regiones de Azure a su cuenta de Azure Cosmos DB. 
* El costo de una operación de lectura (en términos de unidades de solicitud consumidas) con uso vinculado es mayor que con la de sesión o la eventual, pero igual que con la de coherencia fuerte.

<a id="session"></a>
**Sesión**: 

* A diferencia de los modelos de coherencia global ofrecidos por los niveles de coherencia fuerte y de obsolescencia entrelazada, el ámbito de la coherencia de sesión corresponde a una sesión de cliente. 
* La coherencia de sesión es ideal para todos los escenarios en los que exista una sesión de usuario o dispositivo, ya que garantiza lecturas monotónicas, escrituras monotónicas y lectura de sus propias escrituras (RYW). 
* La coherencia de sesión proporciona una coherencia predecible para una sesión, así como de un rendimiento de lectura máximo, al mismo tiempo que ofrece las escrituras y lecturas con menor latencia. 
* Las cuentas de Azure Cosmos DB que están configuradas con la coherencia de sesión pueden asociar cualquier número de regiones de Azure a su cuenta de Azure Cosmos DB. 
* El costo de una operación de lectura (en términos de unidades de solicitud consumidas) con el nivel de coherencia de sesión es menor que con la fuerte o la de obsolescencia limitada, pero mayor que con la final.

<a id="consistent-prefix"></a>
**De prefijo coherente**: 

* La coherencia de prefijo coherente garantiza que, en ausencia de escrituras adicionales, las réplicas dentro del grupo terminarán por converger. 
* El prefijo coherente garantiza que las lecturas nunca vean escrituras desordenadas. Si se realizan escrituras en el orden `A, B, C`, un cliente ve `A`, `A,B` o `A,B,C`, pero nunca un desorden como `A,C` o `B,A,C`.
* Las cuentas de Azure Cosmos DB que están configuradas con la coherencia de prefijo coherente pueden asociar cualquier número de regiones de Azure a su cuenta de Azure Cosmos DB. 

<a id="eventual"></a>
**Final**: 

* La coherencia final garantiza que, en ausencia de escrituras adicionales, las réplicas dentro del grupo terminarán por converger. 
* La coherencia eventual es la forma más débil de coherencia, ya que un cliente puede obtener los valores que son más antiguos que los que ha visto antes.
* La coherencia ocasional proporciona la coherencia de lectura más débil, pero ofrece la latencia más baja tanto para lecturas como para escrituras.
* Las cuentas de Azure Cosmos DB que están configuradas con la coherencia final pueden asociar cualquier número de regiones de Azure a su cuenta de Azure Cosmos DB. 
* El costo de una operación de lectura (en términos de unidades de solicitud consumidas) con el nivel de coherencia final es el más bajo de entre todos los niveles de coherencia de Azure Cosmos DB.

## <a name="configuring-the-default-consistency-level"></a>Configuración del nivel de coherencia predeterminado
1. En la barra de accesos de [Azure Portal](https://portal.azure.com/), haga clic en **Azure Cosmos DB**.
2. En la página **Azure Cosmos DB**, seleccione la cuenta de base de datos que quiere modificar.
3. En la página Cuenta, haga clic en **Coherencia predeterminada**.
4. En la página **Coherencia predeterminada**, seleccione el nuevo nivel de coherencia y haga clic en **Guardar**.
   
    ![Captura de pantalla que muestra el icono Configuración y la entrada Coherencia predeterminada](./media/consistency-levels/database-consistency-level-1.png)

## <a name="consistency-levels-for-queries"></a>Niveles de coherencia para consultas
De manera predeterminada, para los recursos definidos por el usuario, el nivel de coherencia de las consultas es igual que el de las lecturas. De forma predeterminada, el índice se actualiza de forma sincrónica con cada inserción, reemplazo o eliminación de un elemento en el contenedor de Cosmos DB. Esto permite a las consultas respetar el mismo nivel de coherencia que el de las lecturas puntuales. Aunque Azure Cosmos DB está optimizado para operaciones de escritura y admite volúmenes sostenidos de estas operaciones, un mantenimiento sincrónico de índices y un servicio de consultas coherentes, se pueden configurar determinados contenedores para actualizar el índice de manera diferida. La indización diferida incrementa aún más el rendimiento de las escrituras y es ideal para aquellos escenarios de ingesta en bloque en donde la carga de trabajo sea sobre todo de lecturas.  

| Modo de indexación | Lecturas | Consultas |
| --- | --- | --- |
| Coherente (predeterminado) |Seleccionar entre fuerte, de obsolescencia limitada, de sesión, de prefijo coherente o final |Seleccionar entre fuerte, de obsolescencia entrelazada, de sesión y eventual |
| Diferida |Seleccionar entre fuerte, de obsolescencia limitada, de sesión, de prefijo coherente o final |Ocasional |
| None |Seleccionar entre fuerte, de obsolescencia limitada, de sesión, de prefijo coherente o final |No aplicable |

Al igual que con las solicitudes de lectura, puede disminuir el nivel de coherencia de una solicitud de consulta concreta en cada API.

## <a name="consistency-levels-for-the-mongodb-api"></a>Niveles de coherencia de la API de MongoDB

Azure Cosmos DB implementa actualmente la versión 3.4 de MongoDB, que tiene dos opciones de configuración de coherencia: fuerte y eventual. Como Azure Cosmos DB tiene varias API, la configuración de coherencia se puede aplicar en el nivel de cuenta y cada una de las API controla el cumplimiento de la coherencia.  Hasta MongoDB 3.6, no había ningún concepto de una coherencia de sesión, por lo que si establece una cuenta de API de MongoDB para usar coherencia de sesión, la coherencia cambia a eventual al usar las API de MongoDB. Si necesita una garantía de lectura de lo que ha escrito para una cuenta de API de MongoDB, el nivel de coherencia predeterminado para la cuenta se debe establecer en fuerte u obsolescencia limitada.

## <a name="next-steps"></a>Pasos siguientes
Si desea leer más sobre los niveles de coherencia y los compromisos, recomendamos los siguientes recursos:

* [Replicated Data Consistency explained through baseball (Coherencia de datos replicados explicada mediante el béisbol) (vídeo), por Doug Terry](https://www.youtube.com/watch?v=gluIh8zd26I)
* [Replicated Data Consistency explained through baseball (Coherencia de datos replicados explicada mediante el béisbol) (notas del producto), por Doug Terry](http://research.microsoft.com/pubs/157411/ConsistencyAndBaseballReport.pdf)
* [Session Guarantees for Weakly Consistent Replicated Data (Garantías de sesión para datos replicados con coherencia débil)](http://dl.acm.org/citation.cfm?id=383631)
* [Consistency Tradeoffs in Modern Distributed Database Systems Design: CAP is only part of the story (Compromisos de coherencia en el diseño de sistemas modernos de bases de datos distribuidas: CAP es solo una parte de la historia)](http://computer.org/csdl/mags/co/2012/02/mco2012020037-abs.html)
* [Probabilistic Bounded Staleness (PBS) for Practical Partial Quorums](http://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf) [Obsolescencia limitada probabilística (PBS) para cuórums parciales prácticos].
* [Eventual Consistent - Revisited (Coherencia final: revisión)](http://allthingsdistributed.com/2008/12/eventually_consistent.html)
* [The Load, Capacity, and Availability of Quorum Systems (La carga, capacidad y disponibilidad de los sistemas de cuórum), SIAM Journal on Computing](http://epubs.siam.org/doi/abs/10.1137/S0097539795281232)
* [Line-up: a complete and automatic linearizability checker (Line-up: un comprobador de linearización completo y automático), registros de la conferencia 2010 ACM SIGPLAN sobre diseño e implementación de lenguajes de programación](http://dl.acm.org/citation.cfm?id=1806634)
* [Probabilistic Bounded Staleness (PBS) for Practical Partial Quorums](http://dl.acm.org/citation.cfm?id=2212359) (Obsolescencia limitada probabilística [PBS] para cuórums parciales prácticos).
