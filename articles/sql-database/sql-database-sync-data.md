---
title: Azure SQL Data Sync | Microsoft Docs
description: Esta introducción presenta Azure SQL Data Sync
services: sql-database
author: allenwux
manager: craigg
ms.service: sql-database
ms.custom: data-sync
ms.topic: conceptual
ms.date: 04/10/2018
ms.author: xiwu
ms.reviewer: douglasl
ms.openlocfilehash: bb5a383828e98c773c079dcea8e3cf37f9a068f0
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/27/2018
ms.locfileid: "37017442"
---
# <a name="sync-data-across-multiple-cloud-and-on-premises-databases-with-sql-data-sync"></a>Sincronización de datos entre varias bases de datos locales y de la nube con SQL Data Sync

SQL Data Sync es un servicio basado en Azure SQL Database que permite sincronizar los datos seleccionados de manera bidireccional entre varias bases de datos SQL e instancias de SQL Server.

Data Sync se basa en el concepto de un grupo de sincronización. Un grupo de sincronización es un grupo de bases de datos que desea sincronizar.

Un grupo de sincronización tiene las siguientes propiedades:

-   En el **esquema de sincronización** se describen qué datos se están sincronizando.

-   La **dirección de sincronización** puede ser bidireccional o puede fluir solo en una dirección. Es decir, la dirección de sincronización puede ser de la *base de datos central al cliente* o del *cliente a la base de datos central*, o ambas opciones.

-   El **intervalo de sincronización** se refiere a la frecuencia con que se produce la sincronización.

-   El **directiva de resolución de conflictos** es una directiva de nivel de grupo, que puede ser *Prevalece la base de datos central* o *Prevalece el cliente*.

Data Sync usa una topología de concentrador y radio para sincronizar los datos. Defina una de las bases de datos del grupo como base de datos central. El resto de las bases de datos son bases de datos miembro. La sincronización solo se produce entre la base de datos central y los clientes individuales.
-   La **base de datos central** debe ser una instancia de Azure SQL Database.
-   Las **bases de datos miembro** pueden ser instancias de SQL Database, bases de datos de SQL Server locales o instancias de SQL Server en Azure Virtual Machines.
-   La **base de datos de sincronización** contiene los metadatos y el registro para Data Sync. La base de datos de sincronización tiene que ser una base de datos Azure SQL Database ubicada en la misma región que la base de datos central. La base de datos la crea el propio cliente y es de su propiedad.

> [!NOTE]
> Si usa una base de datos local, debe [configurar un agente local](sql-database-get-started-sql-data-sync.md#add-on-prem).

![Sincronización de datos entre bases de datos](media/sql-database-sync-data/sync-data-overview.png)

## <a name="when-to-use-data-sync"></a>Cuándo usar Data Sync

Data Sync es útil en los casos en que es necesario mantener los datos actualizados entre varias bases de datos de Azure SQL Database o de SQL Server. Estos son los casos de uso principales de Data Sync:

-   **Sincronización de datos híbridos:** con la sincronización de datos, puede mantener los datos sincronizados entre bases de datos locales e instancias de Azure SQL Databases para habilitar aplicaciones híbridas. Esta funcionalidad puede interesar a los clientes que se plantean realizar la migración a la nube y les gustaría colocar algunas de sus aplicaciones en Azure.

-   **Aplicaciones distribuidas:** en muchos casos, es conveniente separar diferentes cargas de trabajo entre diferentes bases de datos. Por ejemplo, si tiene una base de datos de producción de grande, pero también debe ejecutar una carga de trabajo de informes o análisis en estos datos, resulta útil tener una segunda base de datos para esta carga de trabajo adicional. Este enfoque minimiza el impacto de rendimiento en la carga de trabajo de producción. Puede usar Data Sync para mantener estas dos bases de datos sincronizadas.

-   **Aplicaciones globalmente distribuidas:** muchas empresas abarcan varias regiones e incluso varios países. Para minimizar la latencia de red, es preferible disponer de los datos en una región más cercana. Con Data Sync, puede mantener sincronizadas con facilidad las bases de datos de regiones de todo el mundo.

Data Sync no es la mejor solución para los siguientes escenarios:

| Escenario | Algunas soluciones recomendadas |
|----------|----------------------------|
| Recuperación ante desastres | [Copias de seguridad con redundancia geográfica de Azure](sql-database-automated-backups.md) |
| Escalado de lectura | [Uso de réplicas de solo lectura para equilibrar la carga de las cargas de trabajo de consultas de solo lectura](sql-database-read-scale-out.md) |
| ETL (OLTP a OLAP) | [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) o [SQL Server Integration Services](https://docs.microsoft.com/sql/integration-services/sql-server-integration-services?view=sql-server-2017) |
| Migración de SQL Server local a Azure SQL Database | [Azure Database Migration Service](https://azure.microsoft.com/services/database-migration/) |
|||

## <a name="how-does-data-sync-work"></a>¿Cómo funciona Data Sync? 

-   **Seguimiento de cambios de datos:** Data Sync realiza un seguimiento de cambios mediante los desencadenadores de inserción, actualización y eliminación. Los cambios se registran en una tabla en la base de datos de usuario.

-   **Sincronización de datos:** Data Sync está diseñado en un modelo de concentrador y radio. La base de datos central se sincronizada con cada cliente individualmente. Los cambios de la base de datos central se descargan en el cliente y, después, los cambios del cliente se cargan en la base de datos central.

-   **Resolución de conflictos:** Data Sync proporciona dos opciones para la resolución de conflictos, *Prevalece la base de datos central* o *Prevalece el cliente*.
    -   Si selecciona *Prevalece la base de datos central*, los cambios de la base de datos central siempre sobrescriben los cambios del cliente.
    -   Si selecciona *Prevalece el cliente*, los cambios del cliente sobrescriben los cambios de la base de datos central. Si hay más de un cliente, el valor final depende del cliente que primero se sincronice.

## <a name="sync-req-lim"></a> Requisitos y limitaciones

### <a name="general-considerations"></a>Consideraciones generales

#### <a name="eventual-consistency"></a>Coherencia final
Como Data Sync se basa en desencadenadores, la coherencia transaccional no está garantizada. Microsoft garantiza que todos los cambios se realicen finalmente y que Data Sync no ocasione pérdida de datos.

#### <a name="performance-impact"></a>Impacto en el rendimiento
Data Sync usa desencadenadores de inserción, actualización y eliminación para realizar un seguimiento de los cambios. Crea tablas laterales en la base de datos de usuario para hacer un seguimiento de los cambios. Estas actividades de seguimiento de cambios afectan a la carga de trabajo de la base de datos. Evalúe el nivel de servicio y realice la actualización si fuera necesario.

El aprovisionamiento y desaprovisionamiento durante la creación, actualización y eliminación de grupos de sincronización pueden afectar también al rendimiento de la base de datos. 

### <a name="general-requirements"></a>Requisitos generales

-   Cada tabla debe tener una clave principal. No cambie el valor de la clave principal de ninguna fila. Si tiene que cambiar un valor de clave principal, elimine la fila y vuelva a crearla con el nuevo valor de clave principal. 

-   Se debe habilitar el aislamiento de instantánea. Para más información, consulte [Aislamiento de instantáneas en SQL Server](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/snapshot-isolation-in-sql-server).

### <a name="general-limitations"></a>Limitaciones generales

-   Una tabla no puede tener una columna de identidad que no sea la clave principal.

-   Una clave principal no puede tener el tipo de datos de datetime.

-   Los nombres de objetos (bases de datos, tablas y columnas) no pueden contener los caracteres imprimibles punto (.), corchete de apertura ([) o corchete de cierre (]).

-   No se admite la autenticación de Azure Active Directory.

#### <a name="unsupported-data-types"></a>Tipos de datos no admitidos

-   Secuencia de archivos

-   UDT SQL/CLR

-   XMLSchemaCollection (XML admitido)

-   Cursor, Timestamp, Hierarchyid

#### <a name="limitations-on-service-and-database-dimensions"></a>Limitaciones de las dimensiones de la base de datos y del servicio

| **Dimensiones**                                                      | **Límite**              | **Solución alternativa**              |
|-----------------------------------------------------------------|------------------------|-----------------------------|
| Número máximo de grupos de sincronización a los que una base de datos puede pertenecer       | 5                      |                             |
| Número máximo de puntos de conexión en un único grupo de sincronización              | 30                     | Crear varios grupos de sincronización |
| Número máximo de puntos de conexión locales en un único grupo de sincronización | 5                      | Crear varios grupos de sincronización |
| Nombres de base de datos, tabla, esquema y columna                       | 50 caracteres por nombre |                             |
| Tablas de un grupo de sincronización                                          | 500                    | Crear varios grupos de sincronización |
| Columnas de una tabla de un grupo de sincronización                              | 1000                   |                             |
| Tamaño de la fila de datos en una tabla                                        | 24 Mb                  |                             |
| Intervalo de sincronización mínimo                                           | 5 minutos              |                             |
|||

## <a name="faq-about-sql-data-sync"></a>Preguntas frecuentes sobre SQL Data Sync

### <a name="how-much-does-the-sql-data-sync-service-cost"></a>¿Cuánto cuesta el servicio SQL Data Sync?

El servicio SQL Data Sync en sí no se cobra.  Sin embargo, sí se acumularán los cargos de transferencia de datos por la entrada y salida de datos de su instancia de SQL Database. Para más información, consulte [Precios de SQL Database](https://azure.microsoft.com/pricing/details/sql-database/).

### <a name="what-regions-support-data-sync"></a>¿En qué regiones se admite Data Sync?

SQL Data Sync está disponible en todas las regiones de la nube pública.

### <a name="is-a-sql-database-account-required"></a>¿Es necesaria una cuenta de SQL Database? 

Sí. Debe tener una cuenta de SQL Database para hospedar la base de datos central.

### <a name="can-i-use-data-sync-to-sync-between-sql-server-on-premises-databases-only"></a>¿Puedo usar Data Sync para realizar la sincronización solo bases de datos locales de SQL Server? 
No directamente. Sin embargo, es posible realizar una sincronización indirecta entre bases de datos locales de SQL Server, mediante la creación de una base de datos central en Azure y la posterior incorporación de bases de datos locales al grupo de sincronización.

### <a name="can-i-use-data-sync-to-sync-between-sql-databases-that-belong-to-different-subscriptions"></a>¿Puedo usar Data Sync para sincronizar instancias de SQL Database que pertenecen a suscripciones diferentes?
Sí. Puede sincronizar instancias de SQL Database que pertenecen a grupos de recursos de distintas suscripciones.
-   Si las suscripciones pertenecen al mismo inquilino y tiene permiso en todas las suscripciones, puede configurar el grupo de sincronización en Azure Portal.
-   De lo contrario, tendrá que usar PowerShell para agregar los miembros de sincronización que pertenecen a suscripciones diferentes.
   
### <a name="can-i-use-data-sync-to-seed-data-from-my-production-database-to-an-empty-database-and-then-keep-them-synchronized"></a>¿Puedo usar Data Sync para propagar datos de mi base de datos de producción a una base de datos vacía y, después, mantenerlos sincronizados? 
Sí. Cree el esquema manualmente en la base de datos nueva mediante la generación de scripts del original. Después de crear el esquema, agregue las tablas a un grupo de sincronización para copiar los datos y mantenerlos sincronizados.

### <a name="should-i-use-sql-data-sync-to-back-up-and-restore-my-databases"></a>¿Se debe usar SQL Data Sync para realizar una copia de seguridad de las bases de datos y restaurarlas?

No se recomienda usar SQL Data Sync para crear una copia de seguridad de los datos. No se puede realizar una copia de seguridad y restaurarla a un momento específico, ya que las sincronizaciones de SQL Data Sync no tienen asignada una versión. Además, SQL Data Sync no realiza una copia de seguridad de otros objetos SQL, como procedimientos almacenados, ni hace el equivalente a una operación de restauración rápidamente.

Consulte [Copiar una base de datos Azure SQL Database](sql-database-copy.md) para ver una técnica de copia de seguridad recomendada.

### <a name="can-data-sync-sync-encrypted-tables-and-columns"></a>¿Puede Data Sync sincronizar tablas y columnas cifradas?

-   Si una base de datos utiliza Always Encrypted, puede sincronizar solo las tablas y columnas que *no* estén cifrados. No se pueden sincronizar las columnas cifradas, porque la Data Sync no puede descifrar los datos.

-   Si una columna utiliza el cifrado de nivel de columna (CLE), puede sincronizar la columna, siempre que el tamaño de fila sea menor que 24 Mb (tamaño máximo). Data Sync trata a la columna que se ha cifrado mediante clave (CLE) como datos binarios normales. Para descifrar los datos de otros miembros de la sincronización, necesita el mismo certificado.

### <a name="is-collation-supported-in-sql-data-sync"></a>¿Se admite la intercalación en SQL Data Sync?

Sí. SQL Data Sync admite intercalación en los escenarios siguientes:

-   Si las tablas del esquema de sincronización seleccionadas no están ya en sus bases de datos centrales o bases de datos miembro, al implementar el grupo de sincronización, el servicio crea automáticamente las tablas y columnas correspondientes con la configuración de intercalación seleccionada en las bases de datos de destino vacías.

-   Si las tablas que se van a sincronizar ya existen tanto en las bases de datos centrales como en las bases de datos miembro, SQL Data Sync requiere que las columnas de clave principal tengan la misma intercalación entre las bases de datos centrales y las bases de datos miembro para implementar correctamente el grupo de sincronización. No hay restricciones de intercalación para columnas distintas a las columnas de clave principal.

### <a name="is-federation-supported-in-sql-data-sync"></a>¿Se admite la federación en SQL Data Sync?

La base de datos raíz de federación puede utilizarse en el servicio SQL Data Sync sin limitaciones. No se puede añadir el punto de conexión de la base de datos federada a la versión actual de SQL Data Sync.

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre SQL Data Sync, consulte:

-   [Configuración de Azure SQL Data Sync](sql-database-get-started-sql-data-sync.md)
-   [Procedimientos recomendados para SQL Data Sync de Azure](sql-database-best-practices-data-sync.md)
-   [Supervisión de Azure SQL Data Sync con Log Analytics](sql-database-sync-monitor-oms.md)
-   [Solución de problemas de SQL Data Sync de Azure](sql-database-troubleshoot-data-sync.md)

-   Para obtener ejemplos completos de PowerShell que muestren cómo configurar SQL Data Sync:
    -   [Uso de PowerShell para sincronizar entre varias bases de datos SQL de Azure.](scripts/sql-database-sync-data-between-sql-databases.md)
    -   [Uso de PowerShell para realizar la sincronización entre Azure SQL Database y una base de datos de SQL Server local](scripts/sql-database-sync-data-between-azure-onprem.md)

-   [Descarga de la documentación de la API de REST de SQL Data Sync](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_REST_API.pdf?raw=true)

Para más información sobre SQL Database, consulte:

-   [Información general de SQL Database](sql-database-technical-overview.md)
-   [Administración del ciclo de vida de las aplicaciones](https://msdn.microsoft.com/library/jj907294.aspx)
