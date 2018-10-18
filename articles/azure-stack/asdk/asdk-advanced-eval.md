---
title: Tareas de evaluación avanzadas de Azure Stack | Documentos de Microsoft
description: En este artículo se describen las tareas de evaluación avanzadas de Azure Stack.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 0b65f37310b911184c05fd8fb8554eeb824814b8
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/17/2018
ms.locfileid: "49364819"
---
# <a name="advanced-azure-stack-development-kit-evaluation-tasks"></a>Tareas de evaluación avanzadas del Kit de desarrollo de Azure Stack
Una vez que se haya familiarizado con las características y funcionalidades básicas del Kit de desarrollo de Azure Stack (ASDK), puede ampliar sus conocimientos acerca de Azure Stack mediante el examen de escenarios más avanzados. Estas tareas de evaluación más avanzadas se documentan de forma muy completa en la documentación del operador de Azure Stack.

> [!NOTE]
> Aunque muchas de las tareas del operador se admiten tanto en las implementaciones en ASDK y producción como en Azure Stack multinodo, no todos los escenarios de uso son compatibles con las implementaciones de ASDK. Para más información, consulte [ASDK y sus diferencias con Azure Stack multinodo](asdk-what-is.md#asdk-and-multi-node-azure-stack-differences).

## <a name="delegate-offers-in-azure-stack"></a>Delegación de ofertas en Azure Stack
Como operador de Azure Stack, es posible que a menudo desee delegar en otras personas las tareas de creación de ofertas y registro de usuarios. Por ejemplo, si es un proveedor de servicios puede desear que los distribuidores registren a los clientes y los administren en su nombre. Si forma parte de un grupo central de TI en una empresa, puede que quiera que las subsidiarias registren usuarios sin su intervención.

[La delegación de ofertas en Azure Stack](.\.\azure-stack-delegated-provider.md) le ayuda con estas tareas, ya que le permite acceder y administrar mas usuarios de los que podría hacer de forma directa. 

## <a name="make-sql-databases-available-to-your-azure-stack-users"></a>Bases de datos SQL disponibles para los usuarios de Azure Stack
Los operadores de Azure Stack pueden crear ofertas que permitan a los usuarios (inquilinos) crear bases de datos SQL que puedan usar con sus aplicaciones nativas de la nube, sitios webs y cargas de trabajo. Si proporciona a los usuarios estas bases de datos personalizadas, en la nube y a petición, puede ahorrarles tiempo y recursos. 

Use el adaptador del proveedor de recursos de SQL Server para que [los usuarios de Azure Stack puedan disponer de las bases de datos SQL](.\.\azure-stack-tutorial-sql-server.md) como un servicio de Azure Stack. Después de instalar el proveedor de recursos conéctelo a una o varias instancias de SQL Server.

## <a name="make-web-and-api-apps-available-to-your-azure-stack-users"></a>Aplicaciones web y aplicaciones de API disponibles para los usuarios de Azure Stack
Los administradores de Azure Stack pueden crear ofertas que permitan a los usuarios (inquilinos) crear aplicaciones web y de API, así como de Azure Functions. Si proporciona a los usuarios acceso a estas aplicaciones en la nube y a petición, puede ahorrarles tiempo y recursos.

Implemente el proveedor de recursos de App Service para que tanto las [aplicaciones web como las aplicaciones de API estén disponibles para los usuarios de Azure Stack](.\.\azure-stack-tutorial-app-service.md)

## <a name="next-steps"></a>Pasos siguientes
[Más información acerca de la oferta de servicios con los sistemas integrados de Azure Stack](.\.\azure-stack-offer-services-overview.md)