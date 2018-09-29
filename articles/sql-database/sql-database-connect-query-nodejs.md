---
title: Uso de Node.js para consultar Azure SQL Database | Microsoft Docs
description: En este tema se muestra cómo usar Node.js para crear un programa que se conecta a Azure SQL Database y realiza consultas mediante instrucciones Transact-SQL.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: nodejs
ms.topic: quickstart
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 04/01/2018
ms.openlocfilehash: c6cfbd330f77c5a2f448093e58684032a84a41d9
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/25/2018
ms.locfileid: "47063479"
---
# <a name="use-nodejs-to-query-an-azure-sql-database"></a>Uso de Node.js para consultar una base de datos SQL de Azure

En esta guía de inicio rápido se muestra cómo se usa [Node.js](https://nodejs.org/en/) para crear un programa que se conecte a una instancia de Azure SQL Database y que use instrucciones Transact-SQL para consultar los datos.

## <a name="prerequisites"></a>Requisitos previos

Para completar esta guía de inicio rápido, asegúrese de que dispone de lo siguiente:

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]

- Una [regla de firewall de nivel de servidor](sql-database-get-started-portal-firewall.md) para la dirección IP pública del equipo que usa para seguir esta guía de inicio rápido.

- Ha instalado Node.js y el software relacionado para el sistema operativo:
    - **MacOS**: instalación de Homebrew y Node.js y, a continuación, instalación del controlador ODBC y SQLCMD. Consulte [pasos 1.2 y 1.3](https://www.microsoft.com/sql-server/developer-get-started/node/mac/).
    - **Ubuntu**: instalación de Node.js y, a continuación, instalación del controlador ODBC y SQLCMD. Consulte [pasos 1.2 y 1.3](https://www.microsoft.com/sql-server/developer-get-started/node/ubuntu/).
    - **Windows**: Instalación de Chocolatey y Node.js y, a continuación, instalación del controlador ODBC y SQLCMD. Consulte [pasos 1.2 y 1.3](https://www.microsoft.com/sql-server/developer-get-started/node/windows/).

## <a name="sql-server-connection-information"></a>Información de conexión de SQL server

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]

> [!IMPORTANT]
> Debe tener una regla de firewall activa para la dirección IP pública del equipo en el que sigue este tutorial. Si se encuentra en un equipo diferente o tiene una dirección IP pública diferente, cree una [regla de firewall de nivel de servidor mediante Azure Portal](sql-database-get-started-portal-firewall.md). 

## <a name="create-a-nodejs-project"></a>Creación de un proyecto Node.js

Abra un símbolo del sistema y cree una carpeta denominada *sqltest*. Navegue hasta la carpeta que ha creado y ejecute el siguiente comando:

    
    npm init -y
    npm install tedious
    npm install async
    

## <a name="insert-code-to-query-sql-database"></a>Inserción de código para consultar la base de datos SQL

1. En el entorno de desarrollo o en el editor de texto, cree un nuevo archivo, **sqltest.js**.

2. Reemplace el contenido con el código siguiente y agregue los valores adecuados para el servidor, la base de datos, el usuario y la contraseña.

   ```js
   var Connection = require('tedious').Connection;
   var Request = require('tedious').Request;

   // Create connection to database
   var config = 
      {
        userName: 'someuser', // update me
        password: 'somepassword', // update me
        server: 'edmacasqlserver.database.windows.net', // update me
        options: 
           {
              database: 'somedb' //update me
              , encrypt: true
           }
      }
   var connection = new Connection(config);

   // Attempt to connect and execute queries if connection goes through
   connection.on('connect', function(err) 
      {
        if (err) 
          {
             console.log(err)
          }
       else
          {
              queryDatabase()
          }
      }
    );

   function queryDatabase()
      { console.log('Reading rows from the Table...');

          // Read all rows from table
        request = new Request(
             "SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName FROM [SalesLT].[ProductCategory] pc JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid",
                function(err, rowCount, rows) 
                   {
                       console.log(rowCount + ' row(s) returned');
                       process.exit();
                   }
               );
    
        request.on('row', function(columns) {
           columns.forEach(function(column) {
               console.log("%s\t%s", column.metadata.colName, column.value);
            });
                });
        connection.execSql(request);
      }
```

## <a name="run-the-code"></a>Ejecución del código

1. En el símbolo del sistema, ejecute los siguientes comandos:

   ```js
   node sqltest.js
   ```

2. Compruebe que se han devuelto las primeras 20 filas y, a continuación, cierre la ventana de la aplicación.

## <a name="next-steps"></a>Pasos siguientes

- Consulte información sobre el [Controlador de Microsoft Node.js para SQL Server](https://docs.microsoft.com/sql/connect/node-js/node-js-driver-for-sql-server/)
- Obtenga información acerca de cómo [conectarse y consultar una base de datos SQL de Azure mediante .NET Core](sql-database-connect-query-dotnet-core.md) en Windows, Linux y macOS.  
- Para más información, consulte [Introducción a .NET Core en Windows, Linux y macOS mediante la línea de comandos](/dotnet/core/tutorials/using-with-xplat-cli).
- Obtenga información acerca de cómo [diseñar la primera base de datos SQL de Azure con SSMS](sql-database-design-first-database.md) o [diseñar la primera base de datos SQL de Azure mediante .NET](sql-database-design-first-database-csharp.md).
- Obtenga información acerca de cómo [Conectarse y realizar consultas con SSMS](sql-database-connect-query-ssms.md)
- Obtenga información acerca de cómo [Conectarse y realizar consultas con Visual Studio Code](sql-database-connect-query-vscode.md).

