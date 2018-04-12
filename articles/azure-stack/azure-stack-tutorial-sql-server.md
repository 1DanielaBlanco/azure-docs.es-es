---
title: Bases de datos SQL disponibles para los usuarios de Azure Stack | Microsoft Docs
description: Tutorial para instalar al proveedor de recursos de SQL Server y crear ofertas que permiten a los usuarios de Azure Stack crear bases de datos SQL.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/22/2017
ms.author: jeffgilb
ms.reviewer: ''
ms.custom: mvc
ms.openlocfilehash: f8d2dd65d9d427872fe78508ed0bcc61e644fdb0
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/05/2018
---
# <a name="make-sql-databases-available-to-your-azure-stack-users"></a>Bases de datos SQL disponibles para los usuarios de Azure Stack
Como administrador de la nube de Azure Stack, puede crear ofertas que permitan a los usuarios (inquilinos) crear bases de datos SQL que se puedan usar con sus aplicaciones en la nube, sitios web y cargas de trabajo. Si proporciona a los usuarios estas bases de datos personalizadas, en la nube y a petición, puede ahorrarles tiempo y recursos. Para configurar esta opción, tendrá que:

> [!div class="checklist"]
> * Implementar un proveedor de recursos de SQL Server
> * Creación de una oferta
> * Probar la oferta

## <a name="deploy-the-sql-server-resource-provider"></a>Implementar un proveedor de recursos de SQL Server

El proceso de implementación se describe en detalle en el artículo [Bases de datos de SQL en Azure Stack](azure-stack-sql-resource-provider-deploy.md) y consta de los siguientes pasos principales:

1. [Implemente el proveedor de recursos SQL]( azure-stack-sql-resource-provider-deploy.md#deploy-the-resource-provider).
2. [Compruebe la implementación]( azure-stack-sql-resource-provider-deploy.md#verify-the-deployment-using-the-azure-stack-portal).
3. Aprovisionamiento de capacidad conectando con un servidor SQL de hospedaje

## <a name="create-an-offer"></a>Creación de una oferta

1.  [Establezca una cuota](azure-stack-setting-quotas.md) y asígnele el nombre *CuotaDeSQLServer*. Seleccione **Microsoft.SQLAdapter** para el campo **Espacio de nombres**.
2.  [Cree un plan](azure-stack-create-plan.md). Asígnele el nombre *PlanDePruebaDeSQLServer*, seleccione el servicio **Microsoft.SQLAdapter** y la cuota **CuotaDeSQLServer**.

    > [!NOTE]
    > Para permitir que los usuarios creen otras aplicaciones, podrían ser necesarios otros servicios en el plan. Por ejemplo, Azure Functions requiere que el plan incluya el servicio **Microsoft.Storage**, mientras que Wordpress requiere **Microsoft.MySQLAdapter**.
    > 
    >

3.  [Cree una oferta](azure-stack-create-offer.md), asígnele el nombre **OfertaDePruebaDeSQLServer** y seleccione el plan **PlanDePruebaDeSQLServer**.

## <a name="test-the-offer"></a>Probar la oferta

Ahora que ha implementado el proveedor de recursos de SQL Server y ha creado una oferta, puede iniciar sesión como un usuario, suscribirse a la oferta y crear una base de datos.

### <a name="subscribe-to-the-offer"></a>Suscripción a la oferta
1. Inicie sesión en el portal de Azure Stack (https://portal.local.azurestack.external)) como inquilino.
2. Haga clic en **Obtener una suscripción** y, a continuación, escriba **SuscripciónDePruebaDeSQLServer** en **Nombre para mostrar**.
3. Haga clic en **Seleccionar una oferta** > **OfertaDePruebaDeSQLServer** > **Crear**.
4. Haga clic en **Más servicios** > **Suscripciones** > **SuscripciónDePruebaDeSQLServer** > **Proveedores de recursos**.
5. Haga clic en **Registrar** junto al proveedor **Microsoft.SQLAdapter**.

### <a name="create-a-sql-database"></a>Creación de una base de datos SQL

1. Haga clic en **+** > **Datos y almacenamiento** > **SQL Database**.
2. Deje los valores predeterminados para los campos o bien puede usar estos ejemplos:
    - **Nombre de la base de datos**: SQLdb
    - **Tamaño máximo en MB**: 100
    - **Suscripción**: OfertaDePruebaSQL
    - **Grupo de recursos**: SQL-RG
3. Haga clic en **Configuración de inicio de sesión**, escriba las credenciales de la base de datos y, a continuación, haga clic en **Aceptar**.
4. Haga clic en **SKU** > seleccione la SKU de SQL que ha creado para el servidor de hospedaje SQL > **Aceptar**.
5. Haga clic en **Create**(Crear).

## <a name="next-steps"></a>Pasos siguientes

En este tutorial aprendió lo siguiente:

> [!div class="checklist"]
> * Implementar un proveedor de recursos de SQL Server
> * Creación de una oferta
> * Probar la oferta

Prosiga con el siguiente tutorial para aprender a:

> [!div class="nextstepaction"]
> [Aplicaciones web, móviles y de API disponibles para los usuarios]( azure-stack-tutorial-app-service.md)

