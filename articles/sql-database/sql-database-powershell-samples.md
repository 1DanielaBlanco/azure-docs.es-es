---
title: Ejemplos de script de Azure PowerShell para SQL Database | Microsoft Docs
description: Ejemplos de scripts de Azure PowerShell para ayudar a crear y administrar servidores de Azure SQL Database, grupos elásticos, bases de datos y firewalls.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 09/14/2018
ms.openlocfilehash: 87b020e74e9eaee9a5e20acd3bb84d48e40273c6
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/25/2018
ms.locfileid: "47163705"
---
# <a name="azure-powershell-samples-for-azure-sql-database"></a>Ejemplos de Azure PowerShell para Azure SQL Database

En la tabla siguiente se incluyen vínculos a scripts de Azure PowerShell para Azure SQL Database.

| |  |
|---|---|
|**Creación de una base de datos única y un grupo elástico**||
| [Creación de una base de datos única y configuración de una regla de firewall](scripts/sql-database-create-and-configure-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Este script de PowerShell crea una instancia de Azure SQL Database y configura una regla de firewall en el nivel de servidor. |
| [Creación de grupos elásticos y traslado de bases de datos agrupadas](scripts/sql-database-move-database-between-pools-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Este script de PowerShell crea grupos elásticos de Azure SQL Database, traslada las bases de datos agrupadas y cambia los tamaños de proceso.|
| [Creación y administración de una Instancia administrada](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/27/quick-start-script-create-azure-sql-managed-instance-using-powershell/) | Estos scripts de la CLI muestran que debe crear y administrar una Instancia administrada mediante Azure PowerShell |
|**Configuración de la replicación geográfica y de la conmutación por error**||
| [Configuración y conmutación por error de una base de datos única mediante la replicación geográfica activa](scripts/sql-database-setup-geodr-and-failover-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Este script de PowerShell configura la replicación geográfica activa para una instancia de Azure SQL Database y la conmuta por error a la réplica secundaria. |
| [Configuración y conmutación por error de una base de datos agrupada mediante la replicación geográfica activa](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Este script de PowerShell configura la replicación geográfica activa para una instancia de Azure SQL Database en un grupo elástico de SQL y la conmuta por error a la réplica secundaria. |
| [Configuración y conmutación por error de un grupo de conmutación por error para una sola base de datos](scripts/sql-database-setup-geodr-failover-database-failover-group-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Este script de PowerShell configura un grupo de conmutación por error para una instancia de servidor de Azure SQL Database, agrega una base de datos al grupo de conmutación por error y lo conmuta por error al servidor secundario |
|**Escalado de una base de datos única y un grupo elástico**||
| [Escalado de una base de datos única](scripts/sql-database-monitor-and-scale-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Este script de PowerShell supervisa las métricas de rendimiento de una base de datos de Azure SQL Database, la escala a un tamaño de proceso superior y crea una regla de alerta en una de las métricas de rendimiento. |
| [Escalado de un grupo elástico](scripts/sql-database-monitor-and-scale-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Este script de PowerShell supervisa las métricas de rendimiento de grupo elástico de Azure SQL Database, lo escala a un tamaño de proceso superior y crea una regla de alerta en una de las métricas de rendimiento.  |
| **Detección de amenazas y auditoría** |
| [Configuración de detección de amenazas y auditoría](scripts/sql-database-auditing-and-threat-detection-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Este script de PowerShell configura las directivas de auditoría y detección de amenazas para una instancia de Azure SQL Database. |
| **Restauración, copia e importación de una base de datos**||
| [Restauración de una base de datos](scripts/sql-database-restore-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Este script de PowerShell restaura una instancia de Azure SQL Database desde una copia de seguridad con redundancia geográfica y restaura una instancia de Azure SQL Database eliminada a la copia de seguridad más reciente. |
| [Copia de una base de datos en un nuevo servidor](scripts/sql-database-copy-database-to-new-server-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Este script de PowerShell crea una copia de una instancia de Azure SQL Database existente en un nuevo servidor SQL de Azure. |
| [Importación de una base de datos desde un archivo bacpac](scripts/sql-database-import-from-bacpac-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Este script de PowerShell importa una base de datos a un servidor SQL de Azure desde un archivo bacpac. |
| **Sincronización de datos entre bases de datos**||
| [Sincronización de datos entre bases de datos SQL](scripts/sql-database-sync-data-between-sql-databases.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Este script de PowerShell configura la sincronización de datos para realizar la sincronización entre varias bases de datos Azure SQL. |
| [Sincronización de datos entre la base de datos SQL Database y SQL Server local](scripts/sql-database-sync-data-between-azure-onprem.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Este script de PowerShell configura la sincronización de datos entre una base de datos SQL de Azure y una base de datos SQL Server local. |
| [Actualización del esquema de sincronización de SQL Data Sync](scripts/sql-database-sync-update-schema.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Este script de PowerShell agrega o quita elementos del esquema de sincronización de Data Sync. |
|||
