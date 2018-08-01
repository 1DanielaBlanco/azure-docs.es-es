---
title: archivo de inclusión
description: archivo de inclusión
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: fad9b990b6ff1021efdaf8aadeb1e19d8a55871d
ms.sourcegitcommit: dc646da9fbefcc06c0e11c6a358724b42abb1438
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/18/2018
ms.locfileid: "39138079"
---
|**SKU**   | **Túneles<br>S2S/entre redes virtuales** | **Conexiones<br>P2S** | **Pruebas comparativas de rendimiento<br>agregado** |
|---       | ---                             | ---                    | ---                         |
|**VpnGw1**| Máx. 30*                         | Máx. 128\*\*             | 650 MBps                    |
|**VpnGw2**| Máx. 30*                         | Máx. 128\*\*             | 1 Gbps                      |
|**VpnGw3**| Máx. 30*                         | Máx. 128\*\*             | 1,25 Gbps                   |
|**Básico** | Máx. 10                         | Máx. 128               | 100 Mbps                    | 

* (*) Use una [red WAN virtual](../articles/virtual-wan/virtual-wan-about.md) si necesita más de 30 túneles VPN S2S.

* (\*\*) Póngase en contacto con soporte técnico si se necesitan conexiones adicionales.

* Las pruebas comparativas de rendimiento agregado se basan en las mediciones de varios túneles agregados a través de una sola puerta de enlace. No es un rendimiento garantizado debido a las condiciones del tráfico de Internet y a los comportamientos de las aplicaciones.

* Puede encontrar más información sobre los precios en la página de [precios](https://azure.microsoft.com/pricing/details/vpn-gateway).

* La información del SLA (contrato de nivel de servicio) puede encontrarse en la página [SLA](https://azure.microsoft.com/support/legal/sla/vpn-gateway/).

* Se admiten VpnGw1, VpnGw2 y VpnGw3 para las puertas de enlace VPN únicamente con modelo de implementación de Resource Manager.