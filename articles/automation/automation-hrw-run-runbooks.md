---
title: Ejecución de runbooks en Azure Automation Hybrid Runbook Worker
description: Este artículo proporciona información acerca de cómo ejecutar runbooks en máquinas de un centro de datos local o en el proveedor de la nube con el rol Hybrid Runbook Worker.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 07/17/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: cd2578f2fd8217d513a693ef348a5c26a4b18623
ms.sourcegitcommit: b9786bd755c68d602525f75109bbe6521ee06587
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/18/2018
ms.locfileid: "39126514"
---
# <a name="running-runbooks-on-a-hybrid-runbook-worker"></a>Ejecución de runbooks en Hybrid Runbook Worker

No hay ninguna diferencia en la estructura de runbooks que se ejecutan en Azure Automation y los que se ejecutan en Hybrid Runbook Worker. Los runbooks que usa con cada uno de ellos probablemente sean muy distintos entre sí, debido a que los runbooks destinados a Hybrid Runbook Worker normalmente administran los recursos en el propio equipo local donde se implementan, mientras que los runbooks en Azure Automation suelen hacerlo en la nube de Azure.

Cuando cree runbooks para que se ejecuten en Hybrid Runbook Worker, debe editarlos y probarlos en la máquina que hospeda el rol Hybrid Worker. La máquina host tiene todos los módulos de PowerShell y el acceso a la red que necesita para administrar y acceder a los recursos locales. Una vez un runbook se haya editado y probado en la máquina de Hybrid Worker, puede cargarlo en el entorno de Azure Automation, que está disponible para ejecutarse en Hybrid Worker. Es importante saber que los trabajos se ejecutan en la cuenta de sistema local para Windows o en una cuenta de usuario especial, **nxautomation**, en Linux, que puede presentar sutiles diferencias. Cuando se crean runbooks para un Hybrid Runbook Worker, esto debe tenerse en cuenta.

## <a name="starting-a-runbook-on-hybrid-runbook-worker"></a>Inicio de un runbook en Hybrid Runbook Worker

[Inicio de un runbook en Azure Automation](automation-starting-a-runbook.md) describe los distintos métodos para iniciar un runbook. Trabajo híbrido de runbook agrega una opción **Ejecutar en** donde puede especificar el nombre de un grupo de Trabajos híbridos de runbook. Si se especifica un grupo, uno de los trabajos del grupo recupera y ejecuta el runbook. Si no se especifica esta opción, se ejecuta en Azure Automation de la manera normal.

Cuando inicie un runbook en Azure Portal, verá una opción **Ejecutar en** donde puede seleccionar **Azure** o **Hybrid Worker**. Si selecciona **Trabajo híbrido**, puede seleccionar el grupo en una lista desplegable.

Use el parámetro **RunOn**. Puede usar el comando siguiente para iniciar un runbook llamado Test-Runbook en un grupo de Hybrid Runbook Worker denominado MyHybridGroup con Windows PowerShell.

```azurepowershell-interactive
Start-AzureRmAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -RunOn "MyHybridGroup"
```

> [!NOTE]
> El parámetro **RunOn** se agregó al cmdlet **Start-AzureAutomationRunbook** en la versión 0.9.1 de Microsoft Azure PowerShell. Si tiene instalada una versión anterior, deberá [descargar la versión más reciente](https://azure.microsoft.com/downloads/) . Solo necesita instalar esta versión en una estación de trabajo donde inicia el runbook desde PowerShell. No es necesario que la instale en el equipo de trabajo, a menos que tenga la intención de iniciar runbooks desde ese equipo"

## <a name="runbook-permissions"></a>Permisos de runbooks

Los runbooks que se ejecutan en una instancia de Hybrid Runbook Worker no pueden usar el mismo método que se usa normalmente para los runbooks que se autentican en los recursos de Azure, debido a que tendrán acceso a recursos fuera de Azure. El runbook puede proporcionar su propia autenticación a los recursos locales, o puede especificar una cuenta RunAs para proporcionar un contexto de usuario para todos los runbooks.

### <a name="runbook-authentication"></a>Autenticación de runbook

De forma predeterminada, los Runbooks se ejecutan en el contexto de la cuenta de sistema local para Windows y de una cuenta de usuario especial, **nxautomation**, para Linux en el equipo local, por lo que deben proporcionar su propia autenticación a los recursos a los que obtienen acceso.

Puede usar los recursos [redencial](automation-credentials.md) y [ertificado](automation-certificates.md) en el Runbook con cmdlets que le permitan especificar las credenciales, para que así pueda autenticarse en distintos recursos. El ejemplo siguiente muestra una porción de un runbook que reinicia un equipo. Recupera las credenciales desde un recurso de Credencial y el nombre del equipo desde un recurso de Variable y, a continuación, usa estos valores con el cmdlet Restart-Computer.

```azurepowershell-interactive
$Cred = Get-AzureRmAutomationCredential -ResourceGroupName "ResourceGroup01" -Name "MyCredential"
$Computer = Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" -Name  "ComputerName"

Restart-Computer -ComputerName $Computer -Credential $Cred
```

También puede aprovechar [InlineScript](automation-powershell-workflow.md#inlinescript), que le permite ejecutar bloques de código en otro equipo con credenciales especificadas por el [parámetro común PSCredential](/powershell/module/psworkflow/about/about_workflowcommonparameters).

### <a name="runas-account"></a>Cuenta RunAs

De forma predeterminada, Hybrid Runbook Worker usa el sistema local para Windows y una cuenta de usuario especial, **nxautomation**, para Linux a fin de ejecutar runbooks. En lugar de hacer que los runbooks proporcionen su propia autenticación a los recursos locales, puede especificar una cuenta **RunAs** para un grupo de Hybrid Worker. Especifique un [recurso de credencial](automation-credentials.md) que tenga acceso a los recursos locales. Todos los runbooks se ejecutan con estas credenciales si se ejecutan en una instancia de Hybrid Runbook Worker del grupo.

El nombre de usuario de la credencial debe tener uno de los siguientes formatos:

* dominio\nombre de usuario
* username@domain
* nombre de usuario (para cuentas locales en el equipo local)

Utilice el procedimiento siguiente para especificar una cuenta RunAs de un grupo de Hybrid Worker:

1. Cree un [recurso de credencial](automation-credentials.md) con acceso a los recursos locales.
2. Abra la cuenta de Automation en Azure Portal.
3. Seleccione el icono **Grupos de Hybrid Worker** y luego seleccione el grupo.
4. Seleccione **Toda la configuración** y luego **Configuración del grupo de Hybrid Worker**.
5. Cambie **Ejecutar como** de **Predeterminado** a **Personalizado**.
6. Seleccione la credencial y haga clic en **Guardar**.

### <a name="automation-run-as-account"></a>Cuenta de ejecución de Automation

Como parte del proceso de compilación automatizado que implementa recursos en Azure, es posible que sea necesario acceder a los sistemas locales para admitir una tarea o conjunto de pasos en la secuencia de implementación. A fin de admitir la autenticación en Azure mediante la cuenta de ejecución, debe instalar el certificado de la cuenta de ejecución.

El siguiente runbook de PowerShell, *Export-RunAsCertificateToHybridWorker*, exporta el certificado de ejecución desde la cuenta de Azure Automation y lo descarga e importa en el almacén de certificados de la máquina local de un Hybrid Worker conectado a la misma cuenta. Una vez completado dicho paso, comprueba que el trabajo puede autenticarse correctamente en Azure con la cuenta de ejecución.

```azurepowershell-interactive
<#PSScriptInfo
.VERSION 1.0
.GUID 3a796b9a-623d-499d-86c8-c249f10a6986
.AUTHOR Azure Automation Team
.COMPANYNAME Microsoft
.COPYRIGHT
.TAGS Azure Automation
.LICENSEURI
.PROJECTURI
.ICONURI
.EXTERNALMODULEDEPENDENCIES
.REQUIREDSCRIPTS
.EXTERNALSCRIPTDEPENDENCIES
.RELEASENOTES
#>

<#
.SYNOPSIS
Exports the Run As certificate from an Azure Automation account to a hybrid worker in that account.

.DESCRIPTION
This runbook exports the Run As certificate from an Azure Automation account to a hybrid worker in that account.
Run this runbook in the hybrid worker where you want the certificate installed.
This allows the use of the AzureRunAsConnection to authenticate to Azure and manage Azure resources from runbooks running in the hybrid worker.

.EXAMPLE
.\Export-RunAsCertificateToHybridWorker

.NOTES
AUTHOR: Azure Automation Team
LASTEDIT: 2016.10.13
#>

# Generate the password used for this certificate
Add-Type -AssemblyName System.Web -ErrorAction SilentlyContinue | Out-Null
$Password = [System.Web.Security.Membership]::GeneratePassword(25, 10)

# Stop on errors
$ErrorActionPreference = 'stop'

# Get the management certificate that will be used to make calls into Azure Service Management resources
$RunAsCert = Get-AutomationCertificate -Name "AzureRunAsCertificate"

# location to store temporary certificate in the Automation service host
$CertPath = Join-Path $env:temp  "AzureRunAsCertificate.pfx"

# Save the certificate
$Cert = $RunAsCert.Export("pfx",$Password)
Set-Content -Value $Cert -Path $CertPath -Force -Encoding Byte | Write-Verbose

Write-Output ("Importing certificate into $env:computername local machine root store from " + $CertPath)
$SecurePassword = ConvertTo-SecureString $Password -AsPlainText -Force
Import-PfxCertificate -FilePath $CertPath -CertStoreLocation Cert:\LocalMachine\My -Password $SecurePassword -Exportable | Write-Verbose

# Test that authentication to Azure Resource Manager is working
$RunAsConnection = Get-AutomationConnection -Name "AzureRunAsConnection"

Connect-AzureRmAccount `
    -ServicePrincipal `
    -TenantId $RunAsConnection.TenantId `
    -ApplicationId $RunAsConnection.ApplicationId `
    -CertificateThumbprint $RunAsConnection.CertificateThumbprint | Write-Verbose

Set-AzureRmContext -SubscriptionId $RunAsConnection.SubscriptionID | Write-Verbose

# List automation accounts to confirm Azure Resource Manager calls are working
Get-AzureRmAutomationAccount | Select-Object AutomationAccountName
```

> [!IMPORTANT]
> **Add-AzureRmAccount** es ahora un alias de **Connect-AzureRMAccount**. Al buscar elementos de biblioteca, si no ve **Connect-AzureRMAccount**, puede usar **Add-AzureRmAccount** o actualizar los módulos en su cuenta de Automation.

Guarde el runbook *Export-RunAsCertificateToHybridWorker* en el equipo con una extensión `.ps1`. Impórtelo en la cuenta de Automation y edite el runbook, cambiando el valor de la variable `$Password` con su propia contraseña. Publique y, a continuación, ejecute el runbook dirigido al grupo de Hybrid Worker que ejecuta y autentica runbooks con la cuenta de ejecución. La transmisión del trabajo informa sobre el intento de importar el certificado en el almacén de la máquina local y sigue con varias líneas dependiendo del número de cuentas de Automation definidas en la suscripción y de si la autenticación se realiza correctamente.

## <a name="job-behavior"></a>Comportamiento del trabajo

Los trabajos se controlan de forma ligeramente diferente en Hybrid Runbook Workers que cuando se ejecutan en espacios aislados de Azure. Una diferencia clave es que no hay ningún límite en la duración del trabajo en Hybrid Runbook Workers. Los runbooks ejecutados en espacios aislados de Azure están limitados a 3 horas debido al [reparto equitativo](automation-runbook-execution.md#fair-share). Si tiene un runbook de larga ejecución, querrá asegurarse de que es resistente a posibles reinicios, por ejemplo, si se reinicia la máquina que hospeda Hybrid Worker. Si la máquina host de Hybrid Worker se reinicia, cualquier trabajo de runbook en ejecución se reinicia desde el principio o desde el último punto de comprobación para runbooks de flujo de trabajo de PowerShell. Si un trabajo de runbook se reinicia más de 3 veces, se suspende.

## <a name="run-only-signed-runbooks"></a>Ejecución solo de Runbooks firmados

Es posible configurar instancias de Hybrid Runbook Worker para que ejecuten solo runbooks firmados con cierta configuración. En la sección siguiente se describe cómo configurar las instancias de Hybrid Runbook Worker para ejecutar runbooks firmados y cómo firmar los runbooks.

> [!NOTE]
> Una vez que haya configurado una instancia de Hybrid Runbook Worker para que solo ejecute runbooks firmados, los runbooks que **no** estén firmados no se ejecutarán en el trabajo.

### <a name="create-signing-certificate"></a>Creación de certificado de firma

En el ejemplo siguiente se crea un certificado autofirmado que se puede usar para firmar runbooks. El ejemplo crea el certificado y lo exporta. El certificado se importa a las instancias de Hybrid Runbook Worker más adelante. La huella digital también se devuelve y más adelante se usa para hacer referencia al certificado.

```powershell
# Create a self signed runbook that can be used for code signing
$SigningCert = New-SelfSignedCertificate -CertStoreLocation cert:\LocalMachine\my `
                                        -Subject "CN=contoso.com" `
                                        -KeyAlgorithm RSA `
                                        -KeyLength 2048 `
                                        -Provider "Microsoft Enhanced RSA and AES Cryptographic Provider" `
                                        -KeyExportPolicy Exportable `
                                        -KeyUsage DigitalSignature `
                                        -Type CodeSigningCert


# Export the certificate so that it can be imported to the hybrid workers
Export-Certificate -Cert $SigningCert -FilePath .\hybridworkersigningcertificate.cer

# Import the certificate into the trusted root store so the certificate chain can be validated
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\Root

# Retrieve the thumbprint for later use
$SigningCert.Thumbprint
```

### <a name="configure-the-hybrid-runbook-workers"></a>Configuración de instancias de Hybrid Runbook Worker

Copie el certificado que se creó en cada instancia de Hybrid Runbook Worker en un grupo. Ejecute el script siguiente para importar el certificado y configurar la instancia de Hybrid Worker para usar la validación de firma en los runbooks.

```powershell
# Install the certificate into a location that will be used for validation.
New-Item -Path Cert:\LocalMachine\AutomationHybridStore
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\AutomationHybridStore

# Import the certificate into the trusted root store so the certificate chain can be validated
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\Root

# Configure the hybrid worker to use signature validation on runbooks.
Set-HybridRunbookWorkerSignatureValidation -Enable $true -TrustedCertStoreLocation "Cert:\LocalMachine\AutomationHybridStore"
```

### <a name="sign-your-runbooks-using-the-certificate"></a>Firma de los runbooks con el certificado

Con las instancias de Hybrid Runbook Worker configuradas para usar solo runbooks firmados. Debe iniciar sesión con los runbooks que se usarán en la instancia de Hybrid Runbook Worker. Use la instancia de PowerShell de ejemplo siguiente para firmar los runbooks.

```powershell
$SigningCert = ( Get-ChildItem -Path cert:\LocalMachine\My\<CertificateThumbprint>)
Set-AuthenticodeSignature .\TestRunbook.ps1 -Certificate $SigningCert
```

Una vez firmado el runbook, se debe importar a la cuenta de Automation y publicar con el bloque de firma. Para saber cómo importar runbooks, consulte [Importar un runbook de un archivo en Azure Automation](automation-creating-importing-runbook.md#importing-a-runbook-from-a-file-into-azure-automation).

## <a name="troubleshoot"></a>Solución de problemas

Si los runbooks no se completan correctamente, revise la guía de solución de problemas sobre los [errores de ejecución de un runbook](troubleshoot/hybrid-runbook-worker.md#runbook-execution-fails).

## <a name="next-steps"></a>Pasos siguientes

* Para más información sobre los distintos métodos que se pueden utilizar para iniciar un runbook, consulte [Inicio de un runbook en Azure Automation](automation-starting-a-runbook.md).
* Para entender los diferentes procedimientos para trabajar con runbooks de PowerShell y de flujo de trabajo de PowerShell en Azure Automation mediante el editor de texto, consulte [Edición de runbooks de texto en Azure Automation](automation-edit-textual-runbook.md)
