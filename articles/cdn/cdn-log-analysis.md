---
title: Análisis de patrones de uso de Azure CDN | Microsoft Docs
description: En este artículo se describen los diferentes tipos de informes de análisis disponibles para los productos de Azure CDN.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: 95e18b3c-b987-46c2-baa8-a27a029e3076
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2017
ms.author: magattus
ms.openlocfilehash: 45b3698dd77bda815218b43405d64819c3e4789f
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2018
ms.locfileid: "49091273"
---
# <a name="analyze-azure-cdn-usage-patterns"></a>Análisis de patrones de uso de Azure CDN

Después de habilitar CDN para la aplicación, puede supervisar el uso de la red CDN, comprobar el mantenimiento de su entrega y solucionar posibles problemas. Azure CDN proporciona estas funcionalidades de las siguientes maneras: 

## <a name="core-analytics-via-azure-diagnostic-logs"></a>Análisis básicos a través de los registros de diagnóstico de Azure

El análisis esencial está disponible para los puntos de conexión de CDN de todos los planes de tarifa. Los registros de diagnóstico de Azure permiten exportar el análisis esencial a Azure Storage, Event Hubs o Log Analytics. Azure Log Analytics ofrece una solución con gráficos que el usuario puede configurar y personalizar. Para más información sobre los registros de diagnóstico de Azure, consulte [Registros de diagnóstico de Azure](cdn-azure-diagnostic-logs.md).

## <a name="verizon-core-reports"></a>Informes principales de Verizon

Como usuario de Azure CDN con un **perfil estándar de Azure CDN de Verizon** o un **perfil premium de Azure CDN de Verizon**, puede ver los informes principales de Verizon en el portal complementario de Verizon. Se puede acceder a los informes principales de Verizon con la opción **Administrar** de Azure Portal y ofrecen una variedad de gráficos y vistas. Para más información, vea [Informes principales de Verizon](cdn-analyze-usage-patterns.md).

## <a name="verizon-custom-reports"></a>Informes personalizados de Verizon

Como usuario de Azure CDN con un **perfil estándar de Azure CDN de Verizon** o un **perfil premium de Azure CDN de Verizon**, puede ver los informes personalizados de Verizon en el portal complementario de Verizon. Se puede acceder a los informes personalizados de Verizon con la opción **Administrar** de Azure Portal. La página de informes personalizados de Verizon muestra el número de aciertos o datos transferidos para cada CName perimetral que pertenece a un perfil de Azure CDN. Los datos se pueden agrupar por código de respuesta HTTP o estado de caché durante cualquier período de tiempo. Para más información, consulte [Informes personalizados de Verizon](cdn-verizon-custom-reports.md).

## <a name="azure-cdn-premium-from-verizon-reports"></a>Informes de Azure CDN Premium de Verizon

Con **Azure CDN premium de Verizon**, también puede tener acceso a los informes siguientes:
   * [Informes de HTTP avanzados](cdn-advanced-http-reports.md)
   * [Estadísticas en tiempo real](cdn-real-time-stats.md)
   * [Rendimiento del nodo perimetral](cdn-edge-performance.md)

