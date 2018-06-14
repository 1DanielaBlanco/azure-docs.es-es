---
title: Solución de problemas de conmutación por error en Azure | Microsoft Docs
description: En este artículo se describe cómo solucionar problemas y errores comunes de la conmutación por error en Azure
services: site-recovery
documentationcenter: ''
author: ponatara
manager: abhemraj
editor: ''
ms.assetid: ''
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 03/09/2018
ms.author: ponatara
ms.openlocfilehash: 5c94e26c4639284f7e4c53d924f16040118d996c
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/12/2018
ms.locfileid: "29874366"
---
# <a name="troubleshoot-errors-when-failing-over-a-virtual-machine-to-azure"></a>Solución de problemas y errores cuando una máquina virtual se conmuta por error en Azure
Es posible que aparezca uno de los errores siguientes mientras se realiza la conmutación por error de una máquina virtual en Azure. Para solucionar los problemas, use los pasos que se describen para cada condición de error.


## <a name="failover-failed-with-error-id-28031"></a>No se pudo realizar la conmutación por error con el identificador de error 28031

Site Recovery no pudo crear una máquina virtual conmutada por error en Azure. Esto podría deberse a uno de los siguientes motivos:

* No hay una cuota suficiente disponible para crear la máquina virtual: para comprobar la cuota disponible, vaya a Suscripción -> Uso y cuotas. Puede abrir una [solicitud de soporte técnico nueva](http://aka.ms/getazuresupport) para aumentar la cuota.

* Se intenta conmutar por error máquinas virtuales de familias de distinto tamaño en el mismo conjunto de disponibilidad. Asegúrese de elegir una familia del mismo tamaño para todas las máquinas virtuales del mismo conjunto de disponibilidad. Cambie el tamaño en la configuración de Proceso y red de la máquina virtual y reintente la conmutación por error.

* Hay una directiva de la suscripción que impide crear una máquina virtual. Cambie la directiva para permitir la creación de una máquina virtual y reintente la conmutación por error.

## <a name="failover-failed-with-error-id-28092"></a>No se pudo realizar la conmutación por error con el identificador de error 28092

Site Recovery no pudo crear una interfaz de red para la máquina virtual conmutada por error. Asegúrese de tener una cuota suficiente disponible para crear interfaces de red en la suscripción. Para comprobar la cuota disponible, vaya a Suscripción ->Uso y cuotas. Puede abrir una [solicitud de soporte técnico nueva](http://aka.ms/getazuresupport) para aumentar la cuota. Si tiene una cuota suficiente, puede tratarse de un error intermitente. Vuelva a intentar la operación. Si el problema sigue después de varios reintentos, deje un comentario al final de este documento.  

## <a name="failover-failed-with-error-id-70038"></a>No se pudo realizar la conmutación por error con el identificador de error 70038

Site Recovery no pudo crear una máquina virtual clásica conmutada por error en Azure. Esto podría suceder porque:

* No existe uno de los recursos, como una red virtual, que se requieren para crear la máquina virtual. Cree la red virtual según lo indicado en la configuración de Proceso y red de la máquina virtual, o bien modifique la configuración a una red virtual que ya existe y reintente la conmutación por error.


## <a name="next-steps"></a>Pasos siguientes

Si necesita más ayuda, publique la consulta en el [foro de Site Recovery](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr) o deje un comentario al final de este documento. Tenemos una comunidad activa que debería poder ayudarle.
