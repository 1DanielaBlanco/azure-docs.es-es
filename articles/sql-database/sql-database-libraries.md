---
title: Bibliotecas de conexiones para SQL Database | Microsoft Docs
description: Proporciona vínculos para obtener descargas de los módulos que permiten la conexión a SQL Server y a SQL Database desde una amplia variedad de lenguajes de programación de cliente.
services: sql-database
author: MightyPen
manager: craigg
ms.service: sql-database
ms.custom: develop apps
ms.topic: article
ms.date: 04/01/2018
ms.author: genemi
ms.openlocfilehash: a1e74e9fd2f1ed5eec27d6ede9ba51a7ba93263d
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/06/2018
---
# <a name="connectivity-libraries-and-frameworks-for-sql-server"></a>Bibliotecas y entornos de conectividad para SQL Server

Vea nuestros [tutoriales de introducción](http://aka.ms/sqldev) para iniciarse rápidamente en los lenguajes de programación como C#, Java, Node.js, PHP y Python. A continuación, compile una aplicación mediante el uso de SQL Server en Linux o Windows o Docker en macOS.

En la siguiente tabla se enumeran las bibliotecas de conectividad o *controladores* que las aplicaciones cliente pueden utilizar desde una variedad de lenguajes para conectarse y que usan SQL Server de forma local o en la nube. Puede utilizarlas en Docker, Windows o Linux y usarlas para conectarse a Azure SQL Database y Azure SQL Data Warehouse. 

| Idioma | Plataforma | Recursos adicionales | Descargar | Introducción |
| :-- | :-- | :-- | :-- | :-- |
| C# | Windows, Linux, macOS | [Microsoft ADO.NET para SQL Server](https://docs.microsoft.com/sql/connect/ado-net/microsoft-ado-net-for-sql-server) | [Descargar](https://www.microsoft.com/net/download/) | [Introducción](https://www.microsoft.com/sql-server/developer-get-started/csharp/ubuntu)
| Java | Windows, Linux, macOS | [Controlador JDBC de Microsoft para SQL Server](http://msdn.microsoft.com/library/mt484311.aspx) | [Descargar](https://go.microsoft.com/fwlink/?linkid=852460) |  [Introducción](https://www.microsoft.com/sql-server/developer-get-started/java/ubuntu)
| PHP | Windows, Linux, macOS| [Controlador SQL de PHP para SQL Server](http://msdn.microsoft.com/library/dn865013.aspx) | Sistema operativo: <br/> \*[Windows](https://www.microsoft.com/download/details.aspx?id=55642) <br/> \*[Linux](https://github.com/Microsoft/msphpsql/tree/dev#install-unix) <br/> \*[macOS](https://github.com/Microsoft/msphpsql/tree/dev#install-unix) |  [Introducción](https://www.microsoft.com/sql-server/developer-get-started/php/ubuntu)
| Node.js | Windows, Linux, macOS | [Controlador de Node.js para SQL Server](http://msdn.microsoft.com/library/mt652093.aspx) | [Instalación](https://msdn.microsoft.com/library/mt652094.aspx) |  [Introducción](https://www.microsoft.com/sql-server/developer-get-started/node/ubuntu)
| Python | Windows, Linux, macOS | [Controlador de Python para SQL Server](http://msdn.microsoft.com/library/mt652092.aspx) | Opciones de instalación: <br/> \*[pymssql](https://msdn.microsoft.com/library/mt694094.aspx) <br/> \*[pyodbc](http://msdn.microsoft.com/library/mt763257.aspx) |  [Introducción](https://www.microsoft.com/sql-server/developer-get-started/python/ubuntu)
| Ruby | Windows, Linux, macOS | [Controlador de Ruby para SQL Server](http://msdn.microsoft.com/library/mt691981.aspx) | [Instalación](https://msdn.microsoft.com/library/mt711041.aspx) | [Introducción](https://www.microsoft.com/sql-server/developer-get-started/ruby/ubuntu)
| C++ | Windows, Linux, macOS | [Microsoft ODBC Driver for SQL Server](https://msdn.microsoft.com/library/mt654048(v=sql.1).aspx) | [Descargar](https://msdn.microsoft.com/library/mt654048(v=sql.1).aspx) |  

En la tabla siguiente se muestran ejemplos de marcos de asignación relacional de objetos (ORM) y marcos web que las aplicaciones cliente pueden utilizar con SQL Server de forma local o en la nube. Puede utilizar los marcos en Docker, Windows o Linux y usarlos para conectarse a SQL Database y SQL Data Warehouse. 

| Idioma | Plataforma | ORM |
| :-- | :-- | :-- |
| C# | Windows, Linux, macOS | [Entity Framework](https://docs.microsoft.com/ef)<br>[Entity Framework Core](https://docs.microsoft.com/ef/core/index) |
| Java | Windows, Linux, macOS |[Hibernate ORM](http://hibernate.org/orm)|
| PHP | Windows, Linux | [Laravel (Eloquent)](https://laravel.com/docs/5.0/eloquent) |
| Node.js | Windows, Linux, macOS | [Sequelize ORM](http://docs.sequelizejs.com) |
| Python | Windows, Linux, macOS |[Django](https://www.djangoproject.com/) |
| Ruby | Windows, Linux, macOS | [Ruby on Rails](http://rubyonrails.org/) |
||||

## <a name="related-links"></a>Vínculos relacionados
- [Controladores de SQL Server](http://msdn.microsoft.com/library/mt654049.aspx) que se usan para conectarse desde aplicaciones cliente
- Conexión a SQL Database:
    - [Conexión a SQL Database mediante .NET (C#)](sql-database-connect-query-dotnet.md)
    - [Conexión a SQL Database mediante PHP en Windows](sql-database-connect-query-php.md)
    - [Conexión a SQL Database mediante Node.js](sql-database-connect-query-nodejs.md)
    - [Conexión a SQL Database mediante Java con JDBC en Windows](sql-database-connect-query-java.md)
    - [Conexión a SQL Database mediante Python](sql-database-connect-query-python.md)
    - [Conexión a SQL Database mediante Ruby](sql-database-connect-query-ruby.md)
- Ejemplos de código de la lógica de reintento:
    - [Conexión resistente a SQL con ADO.NET][step-4-connect-resiliently-to-sql-with-ado-net-a78n]
    - [Conexión resistente a SQL con PHP][step-4-connect-resiliently-to-sql-with-php-p42h]


<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-to-sql-with-ado-net

[step-4-connect-resiliently-to-sql-with-php-p42h]: https://docs.microsoft.com/sql/connect/php/step-4-connect-resiliently-to-sql-with-php

