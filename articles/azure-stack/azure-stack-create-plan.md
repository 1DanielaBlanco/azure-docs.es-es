---
title: Creación de un plan en Azure Stack | Microsoft Docs
description: Como administrador de la nube, cree un plan que permita a los suscriptores aprovisionar máquinas virtuales.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 3dc92e5c-c004-49db-9a94-783f1f798b98
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/07/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: 1fa01d23108ce92fbd7c854442c0474b19395d25
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/08/2018
ms.locfileid: "35248710"
---
# <a name="create-a-plan-in-azure-stack"></a>Creación de un plan en Azure Stack

*Se aplica a: sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

Los [planes](azure-stack-key-features.md) son agrupaciones de uno o varios servicios. Como proveedor puede crear planes y ofrecérselos a sus usuarios. A su vez, los usuarios se suscriben a las ofertas para usar los planes y los servicios que incluyen. Este ejemplo muestra cómo crear un plan que incluya a los proveedores de recursos de proceso, de red y de almacenamiento. Este plan ofrece a los suscriptores la capacidad de aprovisionar máquinas virtuales.

1. Inicie sesión en el portal de administración de Azure Stack (https://adminportal.local.azurestack.external).

2. Para crear un plan y una oferta a la que los usuarios puedan suscribirse, seleccione **Nuevo** > **Ofertas y planes** > **Plan**.
  
   ![Seleccionar un plan](media/azure-stack-create-plan/select-plan.png)

3. En **Nuevo plan**, escriba un **nombre para mostrar** y un **nombre del recurso**. El nombre para mostrar es el nombre descriptivo del plan que ven los usuarios. Solo el administrador puede ver el nombre del recurso, que usan los administradores para trabajar con el plan como un recurso de Azure Resource Manager.

   ![Especificar los detalles](media/azure-stack-create-plan/plan-name.png)

4. Cree un nuevo **Grupo de recursos** o seleccione uno existente, como contenedor para el plan.

   ![Especificar el grupo de recursos](media/azure-stack-create-plan/resource-group.png)

5. Seleccione **Servicios** y, a continuación, seleccione la casilla de verificación de **Microsoft.Compute**, **Microsoft.Network** y **Microsoft.Storage**. A continuación, elija **Seleccionar** para guardar la configuración. Las casillas de verificación aparecen cuando se desplaza el mouse sobre cada opción.
  
   ![Seleccionar servicios](media/azure-stack-create-plan/services.png)

6. Seleccione **Cuotas**, **Microsoft.Storage (local)** y, a continuación, elija la cuota predeterminada o seleccione **Crear nueva cuota** para crear una cuota personalizada.
  
   ![Cuotas](media/azure-stack-create-plan/quotas.png)

7. Si va a crear una nueva cuota, escriba un **Nombre** para la cuota > especifique los valores de cuota > seleccione **Aceptar**. El cuadro de diálogo **Crear cuota** se cierra.

   ![Nueva cuota](media/azure-stack-create-plan/new-quota.png)

   A continuación, seleccione la nueva cuota que ha creado. Al seleccionar la cuota, esta se asigna y se cierra el cuadro de diálogo de selección.
  
   ![Asignación de la cuota](media/azure-stack-create-plan/assign-quota.png)

8. Repita los pasos 6 y 7 para crear y asignar cuotas de **Microsoft.Network (local)** y **Microsoft.Compute (local)**. Cuando los tres servicios tienen cuotas asignadas, son similares a las del ejemplo siguiente.

   ![Asignaciones de cuota realizadas](media/azure-stack-create-plan/all-quotas-assigned.png)

9. En **Cuotas**, elija **Aceptar** y, a continuación, en **Nuevo plan** elija **Crear** para crear el plan.

    ![Crear el plan](media/azure-stack-create-plan/create.png)

10. Para ver el nuevo plan, seleccione **Todos los recursos** y, a continuación, busque el plan y seleccione su nombre. Si la lista de recursos es larga, utilice **Buscar** para encontrar el plan por su nombre.

   ![Revisar el plan](media/azure-stack-create-plan/plan-overview.png)

## <a name="next-steps"></a>Pasos siguientes

[Creación de una oferta](azure-stack-create-offer.md)
