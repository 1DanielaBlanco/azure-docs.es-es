---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 11/09/2018
ms.author: wesmc
ms.openlocfilehash: 01d6d933474d4a9c1e428b8df89fc766c9b40f20
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/12/2018
ms.locfileid: "51572691"
---
| Recurso | Límite |
| --- | --- |
| Tamaño de memoria caché |530 GB |
| Bases de datos |64 |
| Número máximo de clientes conectados |40.000 |
| Réplicas de caché en Redis (para una alta disponibilidad) |1 |
| Particiones en una caché premium con agrupación en clústeres |10 |

Los tamaños y límite de Azure Redis Cache son diferentes para cada nivel de precios. Para ver los planes de tarifa y sus tamaños asociados, vea [Precios de Azure Redis Cache](https://azure.microsoft.com/pricing/details/cache/).

Para más información sobre los límites de configuración de Azure Redis Cache, consulte [Configuración predeterminada del servidor Redis](../articles/redis-cache/cache-configure.md#default-redis-server-configuration).

Dado que Microsoft realiza la configuración y la administración de instancias de Azure Redis Cache, no se admiten todos los comandos de Redis en Azure Redis Cache. Para más información, consulte [Comandos de Redis que no se admiten en Azure Redis Cache](../articles/redis-cache/cache-configure.md#redis-commands-not-supported-in-azure-redis-cache).

