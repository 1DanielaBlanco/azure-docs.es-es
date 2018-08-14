---
title: Validación de la configuración de cuentas de Azure Automation
description: En este artículo se describe cómo confirmar que la configuración de la cuenta de Automation es correcta.
services: automation
ms.service: automation
ms.component: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 08/08/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 32b73cf4570393ed24ee6d1121aef75aaf193134
ms.sourcegitcommit: d16b7d22dddef6da8b6cfdf412b1a668ab436c1f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2018
ms.locfileid: "39713761"
---
# <a name="test-azure-automation-run-as-account-authentication"></a>Comprobación de la autenticación con la cuenta de ejecución de Azure Automation

Una vez que se haya creado correctamente una cuenta de Automation, puede realizar una prueba sencilla para confirmar que puede autenticarse correctamente en Azure Resource Manager o en el modelo de implementación clásica de Azure mediante la cuenta de ejecución de Automation recién creada o actualizada.

## <a name="automation-run-as-authentication"></a>Autenticación con la cuenta de ejecución de Automation

Utilice el código de ejemplo que aparece a continuación para [crear un runbook de PowerShell](automation-creating-importing-runbook.md) para comprobar la autenticación mediante la cuenta de ejecución y también, en sus runbooks personalizados, para autenticar y administrar los recursos de Resource Manager con la cuenta de Automation.

```powershell
$connectionName = "AzureRunAsConnection"
try
{
    # Get the connection "AzureRunAsConnection "
    $servicePrincipalConnection = Get-AutomationConnection -Name $connectionName

    $logonAttempt = 0
    $logonResult = $False

    while(!($connectionResult) -And ($logonAttempt -le 10))
    {
        $LogonAttempt++
    # Logging in to Azure...
    $connectionResult = Connect-AzureRmAccount `
        -ServicePrincipal `
        -TenantId $servicePrincipalConnection.TenantId `
        -ApplicationId $servicePrincipalConnection.ApplicationId `
        -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint

     Start-Sleep -Seconds 30
    }
}
catch {
    if (!$servicePrincipalConnection)
    {
        $ErrorMessage = "Connection $connectionName not found."
        throw $ErrorMessage
    } else{
        Write-Error -Message $_.Exception
        throw $_.Exception
    }
}

#Get all ARM resources from all resource groups
$ResourceGroups = Get-AzureRmResourceGroup

foreach ($ResourceGroup in $ResourceGroups)
{
    Write-Output ("Showing resources in resource group " + $ResourceGroup.ResourceGroupName)
    $Resources = Find-AzureRmResource -ResourceGroupNameContains $ResourceGroup.ResourceGroupName | Select ResourceName, ResourceType
    ForEach ($Resource in $Resources)
    {
        Write-Output ($Resource.ResourceName + " of type " +  $Resource.ResourceType)
    }
    Write-Output ("")
}
```

Observe que el cmdlet usado para autenticarse en el Runbook - **Connect-AzureRmAccount** usa el conjunto de parámetros *ServicePrincipalCertificate*.  Se autentica mediante el certificado de la entidad de servicio, no las credenciales.  

> [!IMPORTANT]
> **Add-AzureRmAccount** es ahora un alias de **Connect-AzureRMAccount**. Al buscar elementos de biblioteca, si no ve **Connect-AzureRMAccount**, puede usar **Add-AzureRmAccount** o actualizar los módulos en su cuenta de Automation.

Cuando [ejecuta el runbook](automation-starting-a-runbook.md#starting-a-runbook-with-the-azure-portal) para validar la cuenta de ejecución, se crea un [trabajo de runbook](automation-runbook-execution.md), se muestra la página del trabajo y el estado de este aparece en el icono **Resumen del trabajo**. El estado del trabajo se iniciará como *En cola* , lo que indica que está esperando a que haya algún trabajo de Runbook disponible en la nube. Su estado cambiará a *Iniciando* cuando un trabajo de runbook lo solicite. Cuando el runbook comience a ejecutarse realmente, el estado será *En ejecución*.  Cuando el trabajo de runbook se complete, debería aparecer el estado **Completado**.

Para ver los resultados detallados del Runbook, haga clic en el icono **Salida** .  En la página **Salida**, debería ver que se ha autenticado correctamente y ha devuelto una lista de todos los recursos de todos los grupos de recursos de la suscripción.  

No olvide quitar el bloque de código que comienza con el comentario `#Get all ARM resources from all resource groups` cuando reutilice el código para sus runbooks.

## <a name="classic-run-as-authentication"></a>Autenticación con la cuenta de ejecución del modelo de implementación clásica de Azure

Utilice el código de ejemplo que aparece a continuación para [crear un runbook de PowerShell](automation-creating-importing-runbook.md) para comprobar la autenticación mediante la cuenta de ejecución clásica y también, en sus runbooks personalizados, para autenticar y administrar los recursos del modelo de implementación clásica.  

```powershell
$ConnectionAssetName = "AzureClassicRunAsConnection"
# Get the connection
$connection = Get-AutomationConnection -Name $connectionAssetName

# Authenticate to Azure with certificate
Write-Verbose "Get connection asset: $ConnectionAssetName" -Verbose
$Conn = Get-AutomationConnection -Name $ConnectionAssetName
if ($Conn -eq $null)
{
    throw "Could not retrieve connection asset: $ConnectionAssetName. Assure that this asset exists in the Automation account."
}

$CertificateAssetName = $Conn.CertificateAssetName
Write-Verbose "Getting the certificate: $CertificateAssetName" -Verbose
$AzureCert = Get-AutomationCertificate -Name $CertificateAssetName
if ($AzureCert -eq $null)
{
    throw "Could not retrieve certificate asset: $CertificateAssetName. Assure that this asset exists in the Automation account."
}

Write-Verbose "Authenticating to Azure with certificate." -Verbose
Set-AzureSubscription -SubscriptionName $Conn.SubscriptionName -SubscriptionId $Conn.SubscriptionID -Certificate $AzureCert
Select-AzureSubscription -SubscriptionId $Conn.SubscriptionID

#Get all VMs in the subscription and return list with name of each
Get-AzureVM | ft Name
```

Cuando [ejecuta el runbook](automation-starting-a-runbook.md#starting-a-runbook-with-the-azure-portal) para validar la cuenta de ejecución, se crea un [trabajo de runbook](automation-runbook-execution.md), se muestra la página Trabajo y el estado del trabajo aparece en el icono **Resumen del trabajo**. El estado del trabajo se iniciará como *En cola* , lo que indica que está esperando a que haya algún trabajo de Runbook disponible en la nube. Su estado cambiará a *Iniciando* cuando un trabajo de runbook lo solicite. Cuando el runbook comience a ejecutarse realmente, el estado será *En ejecución*.  Cuando el trabajo de runbook se complete, debería aparecer el estado **Completado**.

Para ver los resultados detallados del Runbook, haga clic en el icono **Salida** .  En la página **Salida**, debería ver que se ha autenticado correctamente y ha devuelto una lista de todas las máquinas virtuales de Azure ordenadas por VMName que están implementadas en la suscripción.  

No olvide quitar el cmdlet **Get-AzureVM** cuando reutilice el código para sus runbooks.

## <a name="next-steps"></a>Pasos siguientes

* Para empezar a trabajar con runbooks de PowerShell, consulte [Mi primer runbook de PowerShell](automation-first-runbook-textual-powershell.md).
* Para más información sobre la creación gráfica, consulte [Creación gráfica en Azure Automation](automation-graphical-authoring-intro.md).
