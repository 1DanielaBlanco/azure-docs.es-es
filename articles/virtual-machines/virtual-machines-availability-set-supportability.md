---
title: Compatibilidad a la hora de agregar máquinas virtuales de Azure a un conjunto de disponibilidad existente | Microsoft Docs
description: Compatibilidad a la hora de agregar máquinas virtuales de Azure a un conjunto de disponibilidad existente.
services: virtual-machines-linux
documentationcenter: ''
author: Deland-Han
manager: cshepard
editor: ''
ms.assetid: ''
ms.service: virtual-machines
ms.workload: virtual-machines
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: delhan
ms.openlocfilehash: 13448f4b335d84264d4141cb4fb8c3eadcf0303e
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/12/2018
ms.locfileid: "35775995"
---
# <a name="supportability-of-adding-azure-vms-to-an-existing-availability-set"></a>Compatibilidad a la hora de agregar máquinas virtuales de Azure a un conjunto de disponibilidad existente

En ocasiones, puede encontrar limitaciones al agregar nuevas máquinas virtuales (VM) a un conjunto de disponibilidad existente. El siguiente gráfico detalla las series de máquinas virtuales que puede mezclar en el mismo conjunto de disponibilidad.

Esta es la matriz de compatibilidad para mezclar distintos tipos de máquinas virtuales:

Serie y conjunto de disponibilidad|Segunda máquina virtual|Una |Av2|D|Dv2|Dv3|
|---|---|---|---|---|---|---|
|Primera máquina virtual|||||||
|Una ||OK|OK|OK|OK|OK|
|Av2||OK|OK|OK|OK|OK|
|D||OK|OK|OK|OK|OK|
|Dv2||OK|OK|OK|OK|OK|
|Dv3||OK|OK|OK|OK|OK|

Todas las otras series no pueden estar en el mismo conjunto de disponibilidad porque requieren un hardware específico.

El tamaño de una máquina virtual A8/A9 no se puede combinar debido al requisito en la red de backend de RDMA dedicada.
