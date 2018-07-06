---
title: Supervisión del almacenamiento en memoria de XTP | Microsoft Docs
description: Estimar y supervisar el uso y la capacidad del almacenamiento en memoria de XTP; resolver el error de capacidad 41823
services: sql-database
author: jodebrui
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: jodebrui
ms.openlocfilehash: f74c9bf06cad8b84d08baf7a0a0504b9cb729bf4
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/21/2018
ms.locfileid: "36308686"
---
# <a name="monitor-in-memory-oltp-storage"></a>Supervisión del almacenamiento OLTP en memoria
Cuando se usa [OLTP en memoria](sql-database-in-memory.md), los datos de tablas optimizadas para memoria y las variables de tabla residen en el almacenamiento OLTP en memoria. Cada nivel de servicio Premium y Crítico para la empresa tiene un tamaño máximo de almacenamiento OLTP en memoria. Consulte [Límites de los recursos basados en DTU para bases de datos únicas](sql-database-dtu-resource-limits-single-databases.md), [Límites de los recursos basados en DTU para grupos elásticos](sql-database-dtu-resource-limits-elastic-pools.md), [Límites de los recursos basados en núcleos virtuales para bases de datos únicas](sql-database-vcore-resource-limits-single-databases.md) y [Límites de los recursos basados en núcleos virtuales para grupos elásticos](sql-database-vcore-resource-limits-elastic-pools.md).

Una vez que se supera ese límite, las operaciones de inserción y actualización pueden empezar a producir el error 41823 para las bases de datos independientes y el 41840 para los grupos elásticos. En ese momento, es necesario eliminar los datos para reclamar memoria o actualizar el nivel de rendimiento de la base de datos.

## <a name="determine-whether-data-fits-within-the-in-memory-oltp-storage-cap"></a>Determinación de si los datos se ajustan al límite de almacenamiento de OLTP en memoria
Determine los extremos de almacenamiento de los diferentes niveles de servicio. Consulte [Límites de los recursos basados en DTU para bases de datos únicas](sql-database-dtu-resource-limits-single-databases.md), [Límites de los recursos basados en DTU para grupos elásticos](sql-database-dtu-resource-limits-elastic-pools.md), [Límites de los recursos basados en núcleos virtuales para bases de datos únicas](sql-database-vcore-resource-limits-single-databases.md) y [Límites de los recursos basados en núcleos virtuales para grupos elásticos](sql-database-vcore-resource-limits-elastic-pools.md).

La estimación de los requisitos de memoria para una tabla optimizada para memoria funciona de la misma manera para SQL Server tal y como se hace en la base de datos SQL de Azure. Dedique algunos minutos a revisar ese artículo en [MSDN](https://msdn.microsoft.com/library/dn282389.aspx).

La tabla y las filas de variables de tabla, así como los índices, se tienen en cuenta para el tamaño máximo de los datos de usuario. Además, ALTER TABLE necesita suficiente espacio para crear una nueva versión de toda la tabla y sus índices.

## <a name="monitoring-and-alerting"></a>Supervisión y alertas
Puede supervisar el uso del almacenamiento en memoria como un porcentaje del extremo de almacenamiento para su nivel de rendimiento en [Azure Portal](https://portal.azure.com/): 

1. En la hoja Base de datos, busque el cuadro de uso de recursos y haga clic en Editar.
2. Seleccione la métrica `In-Memory OLTP Storage percentage`.
3. Para agregar una alerta, haga clic en el cuadro Uso de recursos para abrir la hoja de métricas y después haga clic en Agregar alerta.

O bien, use la siguiente consulta para mostrar el uso del almacenamiento en memoria:

    SELECT xtp_storage_percent FROM sys.dm_db_resource_stats


## <a name="correct-out-of-in-memory-oltp-storage-situations---errors-41823-and-41840"></a>Corrección de las situaciones de almacenamiento de OLTP en memoria agotado: errores 41823 y 41840
Al alcanzar el límite de almacenamiento de OLTP en memoria de la base de datos se producen errores en las operaciones de inserción, actualización, modificación y creación, cuyos mensajes de error son 41823 (para bases de datos independientes) y 41840 (para grupos elásticos). Ambos errores anulan la transacción activa.

Los mensajes de error 41823 y 41840 indican que las tablas optimizadas en memoria y las variables de tabla de la base de datos o el grupo han alcanzado el tamaño máximo de almacenamiento de OLTP en memoria.

Para resolver este error, haga uno de los siguientes:

* Elimine datos de las tablas optimizadas para memoria, lo que potencialmente descarga los datos a tablas tradicionales, basadas en disco; o bien,
* Actualice el nivel de servicio a uno con suficiente espacio de almacenamiento en memoria para los datos que necesita mantener en tablas optimizadas para memoria.

> [!NOTE] 
> En raras ocasiones, los errores 41823 y 41840 pueden ser transitorios, lo que significa que hay suficiente espacio de OLTP disponible en memoria y la operación se realiza de nuevo correctamente. Por lo tanto, se recomienda supervisar el almacenamiento de OLTP en memoria global disponible y volver a intentar cuando primero cuando se produce el error 41823 o 41840. Para más información acerca de la lógica de reintento, consulte [Detección de conflictos y lógica de reintento de OLTP en memoria](https://docs.microsoft.com/sql/relational-databases/In-memory-oltp/transactions-with-memory-optimized-tables#conflict-detection-and-retry-logic).

## <a name="next-steps"></a>Pasos siguientes
Para obtener instrucciones sobre la supervisión, consulte [Supervisión de Azure SQL Database con vistas de administración dinámica](sql-database-monitoring-with-dmvs.md).
