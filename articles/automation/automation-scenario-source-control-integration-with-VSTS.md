---
title: Integración de Azure Automation con control de código fuente de Visual Studio Team Services
description: Escenario que le lleva por la configuración de la integración con una cuenta de Azure Automation y el control de código fuente de Visual Studio Team Services.
services: automation
documentationcenter: ''
author: eamonoreilly
ms.author: eamono
keywords: azure powershell, VSTS, control de código fuente, automation
ms.service: automation
ms.topic: article
ms.date: 03/19/2017
ms.openlocfilehash: a60143db03e5f89685a25f26789003de30d91f4c
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/23/2018
---
# <a name="azure-automation-scenario---automation-source-control-integration-with-visual-studio-team-services"></a>Escenario de Azure Automation: integración del control del código fuente de Automation con Visual Studio Team Services

En este escenario, tiene un proyecto de Visual Studio Team Services que usa para administrar runbooks de Azure Automation o las configuraciones de DSC bajo control del código fuente.
En este artículo se describe cómo integrar VSTS con el entorno de Azure Automation para que se produzca la integración continua con cada inserción.

## <a name="getting-the-scenario"></a>Obtención del escenario

Este escenario consta de dos Runbooks de PowerShell que puede importar directamente desde la [Galería de Runbooks](automation-runbook-gallery.md) en Azure Portal o descargar de la [Galería de PowerShell](https://www.powershellgallery.com).

### <a name="runbooks"></a>Runbooks

Runbook | DESCRIPCIÓN| 
--------|------------|
Sync-VSTS | Importa runbooks o configuraciones de control de código fuente de VSTS cuando se realiza una inserción en el repositorio. Si se ejecuta manualmente, importa y publica todos los runbooks o configuraciones en la cuenta de Automation.| 
Sync-VSTSGit | Importa runbooks o configuraciones de VSTS bajo control de código fuente de GIT cuando se realiza una inserción en el repositorio. Si se ejecuta manualmente, importa y publica todos los runbooks o configuraciones en la cuenta de Automation.|

### <a name="variables"></a>variables

Variable | DESCRIPCIÓN|
-----------|------------|
VSToken | Proteja el recurso de variable que se crea y que contiene el token de acceso personal de VSTS. Puede aprender a crear un token de acceso personal de VSTS en la [página de autenticación de VSTS](/vsts/accounts/use-personal-access-tokens-to-authenticate).
## <a name="installing-and-configuring-this-scenario"></a>Instalación y configuración de este escenario

Cree un [token de acceso personal](/vsts/accounts/use-personal-access-tokens-to-authenticate) en VSTS que va a usar para sincronizar los Runbooks las configuraciones en su cuenta de automatización.

![](media/automation-scenario-source-control-integration-with-VSTS/VSTSPersonalToken.png) 

Cree una [variable segura](automation-variables.md) en su cuenta de automatización que contenga el token de acceso personal para que el runbook se pueda autenticar en VSTS y sincronice los runbooks o las configuraciones en la cuenta de Automation. Puede llamarla VSToken. 

![](media/automation-scenario-source-control-integration-with-VSTS/VSTSTokenVariable.png)

Importe el Runbook que sincroniza los runbooks o las configuraciones en la cuenta de automatización. Puede usar el [runbook de ejemplo de VSTS](https://www.powershellgallery.com/packages/Sync-VSTS/1.0/DisplayScript) o el [runbook de ejemplo de VSTS con GIT] (https://www.powershellgallery.com/packages/Sync-VSTSGit/1.0/DisplayScript)) de PowerShellGallery.com, según si usa el control de código fuente de VSTS o VSTS con GIT e implementarlo en su cuenta de automatización.

![](media/automation-scenario-source-control-integration-with-VSTS/VSTSPowerShellGallery.png)

Ahora puede [publicar](automation-creating-importing-runbook.md#publishing-a-runbook) este runbook y así crear un webhook. 
![](media/automation-scenario-source-control-integration-with-VSTS/VSTSPublishRunbook.png)

Cree un [webhook](automation-webhooks.md) para este runbook Sync-VSTS y rellene los parámetros como se muestra a continuación. Asegúrese de que copia la dirección URL del webhook, ya que la necesita para un enlace de servicio en VSTS. VSAccessTokenVariableName es el nombre (VSToken) de la variable segura que creó anteriormente para contener el token de acceso personal. 

La integración con VSTS (Sync-VSTS.ps1) toma los siguientes parámetros:
### <a name="sync-vsts-parameters"></a>Parámetros de Sync-VSTS

. | DESCRIPCIÓN| 
--------|------------|
WebhookData | Contiene la información de inserción enviada desde el enlace de servicio de VSTS. Este parámetro se debe dejar en blanco.| 
ResourceGroup | Este es el nombre del grupo de recursos en el que se encuentra la cuenta de automatización.|
AutomationAccountName | El nombre de la cuenta de automatización que se sincroniza con VSTS.|
VSFolder | El nombre de la carpeta en VSTS donde existen los runbooks y las configuraciones.|
VSAccount | El nombre de la cuenta de Visual Studio Team Services.| 
VSAccessTokenVariableName | El nombre de la variable segura (VSToken) que contiene el token de acceso personal de VSTS.| 


![](media/automation-scenario-source-control-integration-with-VSTS/VSTSWebhook.png)

Si va a usar VSTS con GIT (Sync-VSTSGit.ps1), se tomarán los siguientes parámetros.

. | DESCRIPCIÓN|
--------|------------|
WebhookData | Contendrá la información de inserción enviada desde el enlace de servicio de VSTS. Este parámetro se debe dejar en blanco.| ResourceGroup | Este es el nombre del grupo de recursos en el que se encuentra la cuenta de automatización.|
AutomationAccountName | El nombre de la cuenta de automatización que se sincroniza con VSTS.|
VSAccount | El nombre de la cuenta de Visual Studio Team Services.|
VSProject | El nombre del proyecto en VSTS donde existen los runbooks y las configuraciones.|
GitRepo | El nombre del repositorio GIT.|
GitBranch | El nombre de la bifurcación en el repositorio GIT de VSTS.|
Carpeta | El nombre de la carpeta en la bifurcación de GIT de VSTS.|
VSAccessTokenVariableName | El nombre de la variable segura (VSToken) que contiene el token de acceso personal de VSTS.|

![](media/automation-scenario-source-control-integration-with-VSTS/VSTSGitWebhook.png)

Cree un enlace de servicio en VSTS para inserciones a la carpeta que desencadena este webhook en la inserción de código. Seleccione **Enlaces web** como el servicio con el que realizar la integración al crear una nueva suscripción. Puede aprender más sobre los enlaces de servicio en la [documentación de enlaces de servicio de VSTS](https://www.visualstudio.com/en-us/docs/marketplace/integrate/service-hooks/get-started).
![](media/automation-scenario-source-control-integration-with-VSTS/VSTSServiceHook.png)

Ahora debería poder realizar todas las inserciones de sus runbooks y configuraciones en VSTS, así como sincronizarlos automáticamente en la cuenta de automatización.

![](media/automation-scenario-source-control-integration-with-VSTS/VSTSSyncRunbookOutput.png)

Si ejecuta este Runbook manualmente sin que lo desencadene VSTS, puede dejar vacío el parámetro webhookdata y se realizará una sincronización completa desde la carpeta de VSTS especificada.

Si quiere desinstalar el escenario, quite el enlace del servicio de VSTS y elimine el runbook y la variable VSToken.
