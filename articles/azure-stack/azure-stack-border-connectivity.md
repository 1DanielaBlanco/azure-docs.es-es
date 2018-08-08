---
title: Consideraciones de integración de red de conectividad de borde para sistemas integrados de Azure Stack | Microsoft Docs
description: Obtenga información acerca de lo que puede hacer para planear la conectividad de red de borde de centro de datos con Azure Stack de varios nodos.
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
ms.date: 07/26/2018
ms.author: jeffgilb
ms.reviewer: wamota
ms.openlocfilehash: 260c58ad9099a4532c8a6558cfcf5c13f0fc8d52
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/27/2018
ms.locfileid: "39282015"
---
# <a name="border-connectivity"></a>Conectividad de borde 
El planeamiento de la integración de red es un requisito previo importante para la correcta implementación, operación y administración de sistemas integrados de Azure Stack. El planeamiento de conectividad de borde comienza con la elección de si se debe usar el enrutamiento dinámico con el protocolo de puerta de enlace de borde (BGP). Esto requiere la asignación de un número de sistema autónomo de BGP de 16 bits (público o privado) o el uso del enrutamiento estático, en el cual se asigna una ruta estática predeterminada a los dispositivos de borde.

> [!IMPORTANT]
> Los conmutadores de la parte superior del rack (Tor) requieren vínculos superiores de capa 3 con direcciones IP de punto a punto (/30 redes) configuradas en las interfaces físicas. No se admite el uso de vínculos superiores de capa 2 con conmutadores de Tor que admiten operaciones de Azure Stack. 

## <a name="bgp-routing"></a>Enrutamiento BGP
El uso de un protocolo de enrutamiento dinámico, como BGP, garantiza que el sistema siempre sea consciente de los cambios de red y facilita su administración. 

Como se muestra en el siguiente diagrama, la publicidad del espacio IP privado en el conmutador de Tor se restringe mediante una lista de prefijos. Las listas de prefijos deniegan las subredes IP privadas y su aplicación como un mapa de ruta en la conexión entre el Tor y el borde.

El equilibrador de carga de software (SLB) que se ejecuta dentro de los niveles de la solución de Azure Stack se empareja con los dispositivos Tor para que pueda anunciar dinámicamente las direcciones VIP.

Para asegurarse de que el tráfico de usuario se recupera del error de forma transparente e inmediata, el VPC o MLAG configurados entre los dispositivos de Tor permiten el uso de la agregación de vínculos de chasis múltiples a los hosts y HSRP o VRRP que proporciona redundancia de red para las redes IP.

![Enrutamiento BGP](media/azure-stack-border-connectivity/bgp-routing.png)

## <a name="static-routing"></a>Enrutamiento estático
El enrutamiento estático requiere una configuración adicional para los dispositivos de borde. Precisa mayor intervención manual y administración, así como un análisis exhaustivo antes de realizar cambios, y los problemas causados por un error de configuración pueden tardar más tiempo en revertirse según los cambios realizados. No es el método de enrutamiento recomendado, pero se admite.

Para integrar Azure Stack en su entorno de red con enrutamiento estático, los cuatro vínculos físicos entre el borde y el dispositivo de Tor deben estar conectados, y no se puede garantizar la alta disponibilidad debido al modo en que el enrutamiento estático funciona.

El dispositivo de borde debe configurarse con rutas estáticas que apunten a los dispositivos de Tor P2P para el tráfico destinado a direcciones VIP públicas y de red externa, y la red de la infraestructura. Requiere rutas estáticas a la red BMC de la implementación. Los clientes pueden decidir dejar rutas estáticas en el borde para acceder a algunos recursos que residan en la red BMC.  Es opcional agregar rutas estáticas a la red de *infraestructura de conmutadores* y de *administración de conmutadores*.

Los dispositivos de Tor vienen configurados con una ruta estática predeterminada que envía todo el tráfico a los dispositivos de borde. La única excepción de tráfico a la regla predeterminada se da en el espacio privado, que se bloquea con una lista de control de acceso aplicada en la conexión de Tor a borde.

El enrutamiento estático solo se aplica a los vínculos superiores entre los conmutadores de Tor y de borde. El enrutamiento dinámico BGP se usa en el bastidor porque es una herramienta esencial para SLB y otros componentes, y no se puede deshabilitar ni quitar.

![Enrutamiento estático](media/azure-stack-border-connectivity/static-routing.png)

## <a name="transparent-proxy"></a>Proxy transparente
Si el centro de datos requiere que todo el tráfico utilice un proxy, debe configurar un *proxy transparente* para procesar todo el tráfico del bastidor y tratarlo de acuerdo con la directiva, separando el tráfico entre las zonas de la red.

> [!IMPORTANT]
> La solución Azure Stack no es compatible con servidores proxy web normales.  

Un proxy transparente (también conocido como proxy interceptor, alineado o forzado) intercepta la comunicación normal en la capa de red sin requerir ninguna configuración especial del cliente. Los clientes no necesitan estar enterados de la existencia del proxy.

![Proxy transparente](media/azure-stack-border-connectivity/transparent-proxy.png)

## <a name="next-steps"></a>Pasos siguientes
[Integración de DNS](azure-stack-integrate-dns.md)