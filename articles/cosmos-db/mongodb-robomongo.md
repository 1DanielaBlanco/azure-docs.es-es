---
title: Uso de Robomongo para Azure Cosmos DB | Microsoft Docs
description: 'Obtenga información sobre cómo usar Robomongo con una cuenta de Azure Cosmos DB: API para MongoDB.'
keywords: Robomongo
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.devlang: na
ms.topic: conceptual
ms.date: 05/23/2017
ms.author: sngun
ms.openlocfilehash: b6d64d7d7b30d4175fb8c8bf6c98127465427d4e
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2018
ms.locfileid: "34795040"
---
# <a name="use-robomongo-with-an-azure-cosmos-db-api-for-mongodb-account"></a>Uso de Robomongo con una cuenta de Azure Cosmos DB: API para MongoDB
Para conectarse a una cuenta de Azure Cosmos DB: API para MongoDB con Robomongo, debe hacer lo siguiente:

* Descargar e instalar [Robomongo](https://robomongo.org/)
* Disponer de la información de la [cadena de conexión](connect-mongodb-account.md) de la cuenta de Cosmos DB: API para MongoDB

## <a name="connect-using-robomongo"></a>Conectarse mediante Robomongo
Para agregar la cuenta de Azure Cosmos DB: API para MongoDB a las conexiones de MongoDB de Robomongo, siga estos pasos.

1. Recupere la información de conexión de la cuenta de Azure Cosmos DB: API para MongoDB siguiendo [estas](connect-mongodb-account.md) instrucciones.

    ![Captura de pantalla de la hoja Cadena de conexión](./media/mongodb-robomongo/connectionstringblade.png)
2. Ejecute *Robomongo.exe*.

3. Haga clic en el botón de conexión, que está bajo **File** (Archivo), para administrar las conexiones. Después, haga clic en la opción **Create** (Create) de la ventana **MongoDB Connections**; se abrirá **Connection Settings** (Configuración de conexiones).

4. En la ventana **Connection Settings** (Configuración de conexiones), elija un nombre. Después, busque el **host** y **puerto** en la información de conexión del paso 1 y escríbalos en **Address** (Dirección) y **Port** (Puerto) respectivamente.

    ![Captura de pantalla de la funcionalidad de administración de conexiones de Robomongo](./media/mongodb-robomongo/manageconnections.png)
5. En la pestaña **Authentication** (Autenticación), haga clic en **Perform authentication** (Realizar autenticación). Después, escriba la base de datos (el valor predeterminado es *Admin*), el **nombre de usuario** y la **contraseña**.
Tanto el **nombre de usuario** como la **contraseña** están en la información de conexión del paso 1.

    ![Captura de pantalla de la pestaña de autenticación de Robomongo](./media/mongodb-robomongo/authentication.png)
6. En la pestaña **SSL**, marque **Use SSL protocol** (Usar protocolo SSL) y, luego, cambie el **método de autenticación** a **Self-signed Certificate** (Certificado autofirmado).

    ![Captura de pantalla de la pestaña SSL de Robomongo](./media/mongodb-robomongo/SSL.png)
7. Por último, haga clic en **Test** (Probar) para comprobar que puede conectarse. Después, seleccione **Save** (Guardar).

## <a name="next-steps"></a>Pasos siguientes
* Explore [ejemplos](mongodb-samples.md) de Azure Cosmos DB: API para MongoDB.
