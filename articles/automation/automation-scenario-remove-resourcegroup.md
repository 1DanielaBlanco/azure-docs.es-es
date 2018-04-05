---
title: Automatización de la eliminación de grupos de recursos con Azure Automation
description: Versión del flujo de trabajo de PowerShell de un escenario de Azure Automation, que incluye Runbooks para quitar todos los grupos de recursos de su suscripción.
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/19/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 1d54e03c1b5518dece4e11d76593b12fe83dc8c2
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/23/2018
---
# <a name="azure-automation-scenario---automate-removal-of-resource-groups"></a>Escenario de Azure Automation: automatizar la eliminación de grupos de recursos
Muchos clientes crean más de un grupo de recursos. Algunos se podrían usar para administrar aplicaciones de producción y otros como entornos de desarrollo, pruebas y ensayo. Automatizar la implementación de estos recursos es una cosa, pero poder dar de baja un grupo de recursos con un clic del botón es otra. Esta tarea común de administración se puede optimizar mediante Azure Automation. Esto resulta útil si está trabajando con una suscripción de Azure que tiene un límite de gasto a través de una oferta de miembro como MSDN o el programa Microsoft Partner Network Cloud Essentials, por ejemplo.

Este escenario se basa en un Runbook de PowerShell y está diseñado para quitar uno o más grupos de recursos que especifique en su suscripción. La configuración predeterminada del Runbook es probar antes de continuar. De esta forma se tiene la seguridad de que no se elimina por accidente el grupo de recursos antes de que esté listo para completar este procedimiento.   

## <a name="getting-the-scenario"></a>Obtención del escenario
Este escenario consta de un Runbook de PowerShell que puede descargar de la [Galería de PowerShell](https://www.powershellgallery.com/packages/Remove-ResourceGroup/1.0/DisplayScript). También puede importarlo directamente desde la [galería de Runbooks](automation-runbook-gallery.md) en Azure Portal.<br><br>

| Runbook | DESCRIPCIÓN |
| --- | --- |
| Remove-ResourceGroup |Quita de la suscripción uno o más grupos de recursos de Azure y sus recursos asociados. |

<br>
Se definen los siguientes parámetros de entrada para este Runbook:

| . | DESCRIPCIÓN |
| --- | --- |
| NameFilter (obligatorio) |Especifica un filtro de nombre para limitar los grupos de recursos que pretende eliminar. Puede pasar varios valores mediante una lista separada por comas.<br>El filtro no distingue mayúsculas de minúsculas y coincide con cualquier grupo de recursos que contenga la cadena. |
| PreviewMode (opcional) |Ejecuta el Runbook para ver qué grupos de recursos se eliminarán, pero no realiza ninguna acción.<br>El valor predeterminado es **true** para ayudar a evitar la eliminación accidental de uno o más grupos de recursos pasados al Runbook. |

## <a name="install-and-configure-this-scenario"></a>Instalación y configuración de este escenario
### <a name="prerequisites"></a>requisitos previos
Este Runbook se autentica mediante la [Cuenta de ejecución de Azure](automation-sec-configure-azure-runas-account.md).    

### <a name="install-and-publish-the-runbooks"></a>Instalar y publicar los Runbooks
Después de descargar el Runbook, puede importarlo mediante el procedimiento que se describe en los [procedimientos de importación de Runbooks](automation-creating-importing-runbook.md#importing-a-runbook-from-a-file-into-azure-automation). Publique el Runbook después de que se hayan importado correctamente en la cuenta de Automation.

## <a name="using-the-runbook"></a>Uso del Runbook
Los pasos siguientes le guían a través de la ejecución de este Runbook y le ayudan a familiarizarse con su funcionamiento. Va a probar el Runbook de este ejemplo, sin eliminar realmente el grupo de recursos.  

1. Desde Azure Portal, abra su cuenta de Automation y haga clic en **Runbooks**.
2. Seleccione el Runbook **Remove-ResourceGroup** y haga clic en **Iniciar**.
3. Cuando se inicia el Runbook, se abre la página **Iniciar Runbook** y puede configurar los parámetros. Escriba los nombres de los grupos de recursos de su suscripción que puede usar para las pruebas y que no se ocasionan daños si se eliminan por accidente.

   > [!NOTE]
   > Asegúrese de que **Previewmode** esté establecido en **true** para evitar eliminar los grupos de recursos seleccionados. Este Runbook no quita el grupo de recursos que contiene la cuenta de Automation que lo ejecuta.  
   >
   >
1. Cuando haya configurado los valores de todos los parámetros, haga clic en **Aceptar** y el Runbook se pondrá en cola para su ejecución.  

Para ver los detalles del trabajo del Runbook **Remove-ResourceGroup** en Azure Portal, en **Recursos**, seleccione **Trabajos** en el Runbook. Seleccione el trabajo que desea ver. La opción de resumen del trabajo le mostrará los parámetros de entrada y el flujo de salida, además de información general sobre el trabajo y las excepciones que se hayan producido.<br> ![Estado del trabajo del Runbook Remove-ResourceGroup](media/automation-scenario-remove-resourcegroup/remove-resourcegroup-runbook-job-status.png).

El **resumen del trabajo** incluye los mensajes de los flujos de salida, advertencia y error. Seleccione **Salida** para ver resultados detallados de la ejecución del Runbook.<br> ![Resultados de la salida del Runbook Remove-ResourceGroup](media/automation-scenario-remove-resourcegroup/remove-resourcegroup-runbook-job-output.png)

## <a name="next-steps"></a>Pasos siguientes
* Para empezar a crear su propio Runbook, consulte [Creación o importación de un Runbook en Azure Automation](automation-creating-importing-runbook.md).
* Para empezar a trabajar con Runbooks de flujo de trabajo de PowerShell, consulte [Mi primer Runbook de flujo de trabajo de PowerShell](automation-first-runbook-textual.md).
