---
title: Tiempo excesivo de carga de una aplicación con el proxy de aplicación | Microsoft Docs
description: Solución de problemas de rendimiento de carga de páginas con el Proxy de aplicación de Azure AD
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: c78abf05fb28b0370e17107deccd46259df47c47
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/31/2018
ms.locfileid: "39367069"
---
# <a name="an-application-proxy-application-takes-too-long-to-load"></a>Tiempo excesivo de carga de una aplicación con el proxy de aplicación

Este artículo le ayuda a comprender por qué una aplicación de Azure AD Application Proxy puede tardar mucho tiempo en cargarse. También se explica lo que puede hacer para resolver este problema.

## <a name="overview"></a>Información general
Aunque las aplicaciones funcionan, pueden experimentar una latencia elevada. Puede haber ajustes que puede realizar en la topología de red para mejorar la velocidad. Para una evaluación de diferentes topologías, consulte el [documento de consideraciones de red](manage-apps/application-proxy-network-topology.md).

Además de la topología de red, no hay actualmente recomendaciones adicionales sobre el ajuste del rendimiento. A medida que el servicio Application Proxy se expande, podría llegar a un centro de datos que esté físicamente cercano. La mayor proximidad puede ayudar con la latencia. Para obtener una lista de centros de datos de Azure, consulte la [página de prueba de latencia](http://www.azurespeed.com/Azure/Latency). 

Con la [herramienta de prueba de los puertos del conector](https://aadap-portcheck.connectorporttest.msappproxy.net/) encontrará los centros de datos con el servicio de Proxy de aplicación. 

## <a name="feedback-on-application-proxy-data-center-locations"></a>Comentarios sobre las ubicaciones de los centros de datos de Proxy de aplicación 
Puede haber centros de datos de Azure que todavía no incluyan Application Proxy, pero que le supongan una mejora importante de la latencia. Envíe la ubicación del centro de datos a aadapfeedback@microsoft.com. Microsoft usa sus comentarios con fines de expansión.

Microsoft está trabajando en funcionalidades adicionales para mejorar la latencia. Tan pronto como estas mejoras estén disponibles, se actualizará la documentación.

## <a name="next-steps"></a>Pasos siguientes
[Trabajo con servidores proxy locales existentes](manage-apps/application-proxy-configure-connectors-with-proxy-servers.md)
