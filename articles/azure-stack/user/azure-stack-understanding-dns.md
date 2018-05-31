---
title: Información acerca de iDNS en Azure Stack | Microsoft Docs
description: Información acerca de las características y funcionalidades de iDNS en Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 60f5ac85-be19-49ac-a7c1-f290d682b5de
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/15/2018
ms.author: mabrigg
ms.openlocfilehash: df9c22877eeac381d936f2fb86f5720c9cc9c930
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/16/2018
ms.locfileid: "34196702"
---
# <a name="introducing-idns-for-azure-stack"></a>Presentación de iDNS para Azure Stack

*Se aplica a: sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

iDNS es una característica de redes de Azure Stack que le permite resolver nombres DNS externos (por ejemplo, http://www.bing.com.)). También permite registrar los nombres de red virtual internos. Al hacerlo, puede resolver las máquinas virtuales en la misma red virtual por nombre en lugar de por dirección IP. Este enfoque elimina la necesidad de proporcionar las entradas de servidor DNS personalizadas. Para más información acerca de DNS, consulte la [Introducción a Azure DNS](https://docs.microsoft.com/en-us/azure/dns/dns-overview).

## <a name="what-does-idns-do"></a>¿Para qué sirve iDNS?

Con iDNS en Azure Stack, dispone de las siguientes funcionalidades sin tener que especificar entradas del servidor DNS personalizadas:

* Servicios de resolución de nombres Compartir los servicios de resolución de nombres DNS compartidos para las cargas de trabajo del inquilino.
* Servicio DNS autoritativo para la resolución de nombres y el registro DNS en la red virtual del inquilino.
* Servicio DNS recursivo para la resolución de los nombres de Internet de las máquinas virtuales de los inquilinos. Los inquilinos ya no se necesitan especificar entradas de DNS personalizadas para resolver nombres de Internet (por ejemplo, www.bing.com).

Si lo desea, puede traer su propio sistema DNS y utilizar servidores DNS personalizados. Sin embargo, mediante el uso de iDNS, puede resolver nombres DNS de Internet y conectarse a otras máquinas virtuales en la misma red virtual sin necesidad de crear entradas DNS personalizadas.

## <a name="what-doesnt-idns-do"></a>¿Qué es lo que no hace iDNS?

Lo que iDNS no permite hacer es crear un registro DNS para un nombre que se pueda resolver desde fuera de la red virtual.

En Azure, tiene la opción de especificar una etiqueta de nombre DNS que se puede asociar con una dirección IP pública. Puede elegir la etiqueta (prefijo), pero Azure elige el sufijo, que depende de la región en la que crea la dirección IP pública.

![Ejemplo de una etiqueta de nombre DNS](media/azure-stack-understanding-dns-in-tp2/image3.png)

Como muestra la imagen anterior, Azure creará un registro "A" en DNS para la etiqueta de nombre DNS especificada en la zona **westus.cloudapp.azure.com**. El prefijo y el sufijo se combinan para componer un [nombre de dominio completo](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) (FQDN) que se puede resolver desde cualquier lugar de Internet pública.

Azure Stack solo admite iDNS para el registro de nombres interno, por lo que no puede hacer lo siguiente:

* Crear un registro DNS en una zona DNS hospedada existente (por ejemplo, local.azurestack.external).
* Crear una zona DNS (como Contoso.com).
* Crear un registro en su propia zona DNS personalizada.
* Admitir la compra de nombres de dominio.

## <a name="next-steps"></a>Pasos siguientes

[Uso de DNS en Azure Stack](azure-stack-dns.md)