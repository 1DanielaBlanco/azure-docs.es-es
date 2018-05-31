---
title: 'Redes de Azure Stack: diferencias y consideraciones'
description: Aprenda sobre las diferencias y consideraciones al trabajar con redes en Azure Stack.
services: azure-stack
keywords: ''
author: mattbriggs
manager: femila
ms.author: mabrigg
ms.date: 05/14/2018
ms.topic: article
ms.service: azure-stack
ms.openlocfilehash: 2a4c5bce072970f158a89763ebdf4132eafe9cbe
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/16/2018
ms.locfileid: "34196260"
---
# <a name="considerations-for-azure-stack-networking"></a>Consideraciones para las redes de Azure Stack

*Se aplica a: sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

Las redes de Azure Stack tienen muchas de las características proporcionadas por las redes de Azure. Sin embargo, hay algunas diferencias clave que debe conocer antes de implementar una red de Azure Stack.

En este artículo se proporciona información general sobre las consideraciones únicas para las redes de Azure Stack y sus características. Para obtener información acerca de las diferencias de alto nivel entre Azure y Azure Stack, consulte el tema [Key considerations](azure-stack-considerations.md) (Consideraciones clave).

## <a name="cheat-sheet-networking-differences"></a>Hoja de referencia rápida: diferencias de servicios de red

|Servicio | Característica | Azure (global) | Azure Stack |
| --- | --- | --- | --- |
| DNS | DNS multiinquilino | Compatible| Todavía no se admite|
| |Registros AAAA de DNS|Compatible|No compatible|
| |Zonas DNS por suscripción|100 (valor predeterminado)<br>Puede aumentarse a petición.|100|
| |Conjuntos de registros DNS por zona|5000 (valor predeterminado)<br>Puede aumentarse a petición.|5000|
||Servidores de nombres para la delegación de zona|Azure proporciona cuatro servidores de nombres para cada zona de usuario (inquilino) que se crea.|Azure Stack proporciona dos servidores de nombres para cada zona de usuario (inquilino) que se crea.|
| Red virtual|Emparejamiento de redes virtuales de Azure|Conecte dos redes virtuales de la misma región mediante la red troncal de Azure.|Todavía no se admite|
| |Direcciones IPv6|Puede asignar una dirección IPv6 como parte de la [configuración de la interfaz de red](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface-addresses#ip-address-versions).|Se admite solo IPv4.|
|Puertas de enlace de VPN|Puerta de enlace de VPN de punto a sitio|Compatible|Todavía no se admite|
| |Puerta de enlace de VNET a VNET|Compatible|Todavía no se admite|
| |SKU de puerta de enlace de VPN|Compatibilidad con SKU básicas, GW1, GW2, GW3, alto rendimiento estándar, ultra alto rendimiento. |Compatibilidad con SKU básicas, estándar y de alto rendimiento.|
|Equilibrador de carga|Direcciones IP públicas IPv6|Compatibilidad para asignar una dirección IP pública IPv6 a un equilibrador de carga.|Se admite solo IPv4.|
|Network Watcher|Funciones de supervisión de red de inquilinos de Network Watcher|Compatible|Todavía no se admite|
|CDN|Perfiles de Content Delivery Network|Compatible|Todavía no se admite|
|puerta de enlace de aplicaciones|Equilibrio de carga de nivel 7|Compatible|Todavía no se admite|
|Traffic Manager|Dirija el tráfico entrante para obtener un rendimiento y una confiabilidad óptimos de las aplicaciones.|Compatible|Todavía no se admite|
|ExpressRoute|Configure una conexión privada rápida con los servicios en la nube de Microsoft desde su infraestructura local o una instalación de colocalización.|Compatible|Compatibilidad para conectar Azure Stack a un circuito de Express Route.|

## <a name="next-steps"></a>Pasos siguientes

[DNS en Azure Stack](azure-stack-dns.md)
