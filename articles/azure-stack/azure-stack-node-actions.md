---
title: Acciones de los nodos de unidad de escalado en Azure Stack | Microsoft Docs
description: Aprenda a ver el estado de los nodos y usar las acciones de nodo para encender, apagar, purgar y reanudar en un sistema integrado de Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/14/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: 1f59f2ce6e3bf8d34ce225aa93da76ad523775e0
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/15/2018
ms.locfileid: "41946464"
---
# <a name="scale-unit-node-actions-in-azure-stack"></a>Acciones de los nodos de unidad de escalado en Azure Stack

*Se aplica a: sistemas integrados de Azure Stack*

Este artículo describe cómo ver el estado de una unidad de escalado y sus nodos asociados, y cómo usar las acciones de nodo disponibles. Las acciones de nodo son encender, apagar, purgar, reanudar y reparar. Normalmente, estas acciones de nodo se utilizan durante el reemplazo de componentes, o en escenarios de recuperación de nodos.

> [!Important]  
> Todas las acciones de nodo que se describen en este artículo solo deben señalar a un nodo cada vez.


## <a name="view-the-status-of-a-scale-unit-and-its-nodes"></a>Visualización del estado de una unidad de escalado y sus nodos

En el portal de administrador, puede ver fácilmente el estado de una unidad de escalado y sus nodos asociados.

Para ver el estado de una unidad de escalado:

1. En el icono **Administración de regiones**, seleccione la región.
2. A la izquierda, bajo **Infrastructure resources** (Recursos de infraestructura), seleccione **Scale units** (Unidades de escalado).
3. En los resultados, seleccione la unidad de escalado.
 
En este caso, puede ver la información siguiente:

- Nombre de la región. Se hace referencia al nombre de la región con **-Location** en el módulo de PowerShell.
- tipo de sistema
- núcleos lógicos en total
- memoria total
- la lista de nodos individuales y su estado: **en ejecución** o **detenido**.

![Icono de la unidad de escalado que muestra el estado En ejecución para cada nodo](media/azure-stack-node-actions/ScaleUnitStatus.PNG)

## <a name="view-information-about-a-scale-unit-node"></a>Visualización de información sobre un nodo de unidad de escalado

Si selecciona un nodo individual, puede ver la información siguiente:

- nombre de la región
- modelo de servidor
- dirección IP del controlador de administración de placa base (BMC)
- estado operativo
- número total de núcleos
- cantidad total de memoria
 
![Icono de la unidad de escalado que muestra el estado En ejecución para cada nodo](media/azure-stack-node-actions/NodeActions.PNG)

También puede realizar acciones de nodo de unidad de escalado desde aquí.

## <a name="scale-unit-node-actions"></a>Acciones de nodo de unidad de escalado

Al ver la información sobre un nodo de la unidad de escalado, también puede realizar acciones de nodo como:

- Purgado y reanudación
- Reparación

El estado operativo del nodo determina qué opciones están disponibles.

### <a name="power-off"></a>Apagado

La acción de **apagado** desactiva el nodo. Es lo mismo que si se presiona el botón de encendido. **No** envía una señal de cierre al sistema operativo. Para las operaciones de apagado planeadas, asegúrese de que purga un nodo de la unidad de escalado en primer lugar.

Esta acción se utiliza normalmente cuando un nodo está en un estado bloqueado y ya no responde a las solicitudes.

> [!Important] 
> Esta funcionalidad solo está disponible a través de PowerShell. Estará disponible en el portal del administrador de Azure Stack de nuevo más adelante.


Para ejecutar la acción de apagado a través de PowerShell:

````PowerShell
  Stop-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
```` 

En el caso poco probable de que la acción de apagado no funcione, utilice en su lugar la interfaz web BMC.

### <a name="power-on"></a>Encendido

La acción de **encendido** activa el nodo. Es lo mismo que si se presiona el botón de encendido. 

> [!Important] 
> Esta funcionalidad solo está disponible a través de PowerShell. Estará disponible en el portal del administrador de Azure Stack de nuevo más adelante.

Para ejecutar la acción de encendido a través de PowerShell:

````PowerShell
  Start-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
````

En el caso poco probable de que la acción de encendido no funcione, utilice en su lugar la interfaz web BMC.

### <a name="drain"></a>Purga

La acción de **purga** evacua todas las cargas de trabajo activas distribuyéndolas entre los nodos restantes de esa unidad de escalado concreta.

Esta acción se suele utilizar durante el reemplazo de diversos componentes, por ejemplo de un nodo completo.

> [!IMPORTANT]  
> Asegúrese de purgar un nodo solo durante un período de mantenimiento planeado, del cual se haya notificado a los usuarios. En determinadas condiciones, las cargas de trabajo activas pueden experimentar interrupciones.

Para ejecutar la acción de purga a través de PowerShell:

  ````PowerShell
  Disable-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
  ````

### <a name="resume"></a>Reanudación

La acción de **reanudación** reanuda un nodo purgado y lo marca como activo para la colocación de cargas de trabajo. Las cargas de trabajo anteriores que se estuvieran ejecutando en el nodo no conmutan por recuperación. (Si purga un nodo y, a continuación, lo apaga, al encenderlo de nuevo, no se marca como activo para la colocación de cargas de trabajo. Cuando esté listo, debe usar la acción de reanudación para marcar el nodo como activo).

Para ejecutar la acción de reanudación a través de PowerShell:

  ````PowerShell
  Enable-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
  ````

### <a name="repair"></a>Reparación

La acción de **reparación** repara un nodo. Úsela solamente para alguno de los escenarios siguientes:

- Al reemplazar un nodo completo (con o sin discos de datos nuevos)
- Después del error de un componente de hardware y de su reemplazo (si se recomienda en la documentación de la unidad reemplazable de campo [FRU]).

> [!IMPORTANT]  
> Consulte la documentación de la FRU de su proveedor de hardware OEM para conocer los pasos exactos cuando necesite reemplazar un nodo o componentes de hardware individuales. La documentación de la FRU especificará si tiene que ejecutar la acción de reparación después de reemplazar un componente de hardware.  

Al ejecutar la acción de reparación, debe especificar la dirección IP de BMC. 

Para ejecutar la acción de reparación a través de PowerShell:

  ````PowerShell
  Repair-AzsScaleUnitNode -Location <RegionName> -Name <NodeName> -BMCIPAddress <BMCIPAddress>
  ````

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre el módulo de administrador Fabric de Azure Stack, consulte [Azs.Fabric.Admin](https://docs.microsoft.com/powershell/module/azs.fabric.admin/?view=azurestackps-1.4.0).