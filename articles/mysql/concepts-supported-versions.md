---
title: Versiones admitidas de Azure Database for MySQL
description: Se describen las versiones admitidas de Azure Database for MySQL.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 10/10/2018
ms.openlocfilehash: f2a9348e267ad6a5929fda64cc08dcd837243e71
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2018
ms.locfileid: "49093347"
---
# <a name="supported-azure-database-for-mysql-server-versions"></a>Versiones admitidas de servidores de Azure Database for MySQL
Azure Database for MySQL se ha desarrollado a partir de [MySQL Community Edition](https://www.mysql.com/products/community/), con el motor InnoDB. Azure Database for MySQL actualmente admite las siguientes versiones:

## <a name="mysql-version-5639"></a>MySQL versión 5.6.39
Consulte la [documentación](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-39.html) de MySQL para más información sobre las mejoras y correcciones de MySQL 5.6.39.

## <a name="mysql-version-5721"></a>MySQL versión 5.7.21
Consulte la [documentación](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-21.html) de MySQL para más información sobre las mejoras y correcciones de MySQL 5.7.21.

> [!NOTE]
> En el servicio, se usa una puerta de enlace para redirigir las conexiones a las instancias de servidor. Una vez establecida la conexión, el cliente de MySQL muestra la versión de MySQL establecida en la puerta de enlace, no la versión real que se ejecuta en la instancia del servidor MySQL. Para determinar la versión de la instancia del servidor MySQL, use el comando `SELECT VERSION();` en el símbolo del sistema de MySQL.

## <a name="managing-updates-and-upgrades"></a>Administración de actualizaciones
El servicio administra automáticamente la aplicación de partes para las actualizaciones de las versiones de corrección de errores. Actualmente, no se admite la actualización de versión secundaria. Por ejemplo, no se admite la actualización de MySQL 5.6 a MySQL 5.7. Si quiere actualizar a la siguiente versión secundaria, realice un [volcado y restáurelo](./concepts-migrate-dump-restore.md) a un servidor que se haya creado con la nueva versión del motor.

## <a name="next-steps"></a>Pasos siguientes

Para obtener información sobre las cuotas y limitaciones aplicables a recursos específicos en función de su **nivel de servicio**, consulte [Niveles de servicio](./concepts-pricing-tiers.md).
