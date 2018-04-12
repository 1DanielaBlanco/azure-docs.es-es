---
title: 'CLI de Azure Service Fabric: sfctl | Microsoft Docs'
description: Se describen los comandos de sfctl de la CLI de Service Fabric.
services: service-fabric
documentationcenter: na
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 02/23/2018
ms.author: ryanwi
ms.openlocfilehash: 7c8563539ca8507f05fa99fdeffbf511b1540a6a
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="sfctl"></a>sfctl 
Comandos para administrar clústeres y entidades de Service Fabric. Esta versión es compatible con el entorno de ejecución de Service Fabric 6.1. Comandos siguen el patrón de nombre-verbo; consulte los siguientes subgrupos para obtener más información.

## <a name="subgroups"></a>Subgrupos
|Subgrupo|DESCRIPCIÓN|
| --- | --- |
| [application](service-fabric-sfctl-application.md)| Cree, elimine y administre aplicaciones y tipos de aplicaciones.|
| [chaos](service-fabric-sfctl-chaos.md)   | Inicie, detenga e informe sobre el servicio de prueba de Chaos.|
| [cluster](service-fabric-sfctl-cluster.md) | Seleccione, administre y opere clústeres de Service Fabric.|
| [compose](service-fabric-sfctl-compose.md) | Cree, elimine y administre aplicaciones de Docker Compose.|
| [is](service-fabric-sfctl-is.md)      | Consulte y envíe comandos al servicio de infraestructura.|
| [node](service-fabric-sfctl-node.md)    | Administre los nodos que forman un clúster.|
| [partition](service-fabric-sfctl-partition.md)  | Consulte y administre las particiones para cualquier servicio.|
| propiedad  | Almacene y consulte las propiedades con nombres de Service Fabric.|
| [rpm](service-fabric-sfctl-rpm.md)        | Consulte y envíe comandos al servicio del administrador de reparaciones.|
| [replica](service-fabric-sfctl-replica.md) | Administre las réplicas que pertenecen a las particiones del servicio.|
| [service](service-fabric-sfctl-service.md) | Cree, elimine y administre servicios, tipos de servicio y paquetes de servicio.|
| [store](service-fabric-sfctl-store.md)   | Realice operaciones básicas a nivel de archivo en el almacén de imágenes del clúster.|

## <a name="next-steps"></a>Pasos siguientes
- [Configuración](service-fabric-cli.md) de la CLI de Service Fabric.
- Obtenga información sobre cómo utilizar la CLI de Service Fabric con los [scripts de ejemplo](/azure/service-fabric/scripts/sfctl-upgrade-application).