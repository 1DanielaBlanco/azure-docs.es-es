---
title: Uso de una identidad MSI de máquina virtual Windows para acceder a Azure Storage
description: Tutorial que indica cómo usar Managed Service Identity (MSI) en una máquina virtual Windows para acceder a Azure Storage.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: daveba
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/20/2017
ms.author: daveba
ms.openlocfilehash: 9bfaf7f4fa4b7778650638b3b4670e29906838a8
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/01/2018
ms.locfileid: "34594788"
---
# <a name="tutorial-use-a-windows-vm-managed-service-identity-to-access-azure-storage-via-access-key"></a>Tutorial: Uso de Managed Service Identity en una máquina virtual Windows para tener acceso a Azure Storage con una clave de acceso

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Este tutorial muestra cómo habilitar Managed Service Identity (MSI) para una máquina virtual Windows y, a continuación, usar esa identidad para recuperar claves de acceso a la cuenta de almacenamiento. Puede usar claves de acceso de almacenamiento de la forma habitual al realizar operaciones de almacenamiento, por ejemplo, al usar el SDK de Storage. En este tutorial se cargan y descargan blobs mediante PowerShell de Azure Storage. Aprenderá a:


> [!div class="checklist"]
> * Habilitar MSI en una máquina virtual Windows 
> * Conceder acceso a la máquina virtual a las claves de acceso a la cuenta de almacenamiento en Resource Manager 
> * Obtener un token de acceso mediante la identidad de la máquina virtual y utilizarlo para recuperar las claves de acceso a la cuenta de almacenamiento desde Resource Manager 

## <a name="prerequisites"></a>requisitos previos

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Inicio de sesión en Azure

Inicie sesión en Azure Portal en [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-windows-virtual-machine-in-a-new-resource-group"></a>Creación de una máquina virtual Windows en un nuevo grupo de recursos

En este tutorial, se crea una nueva máquina virtual Windows. También puede habilitar MSI en una máquina virtual existente.

1.  Haga clic en el botón **+/Crear nuevo servicio** de la esquina superior izquierda de Azure Portal.
2.  Seleccione **Compute** y, después, seleccione **Windows Server 2016 Datacenter**. 
3.  Escriba la información de la máquina virtual. El **nombre de usuario** y la **contraseña** creados aquí son las credenciales que se usan para iniciar sesión en la máquina virtual.
4.  Elija la **suscripción** adecuada de la máquina virtual en la lista desplegable.
5.  Para seleccionar un nuevo **grupo de recursos** en el que quiere crear la máquina virtual, elija **Crear nuevo**. Cuando haya terminado, haga clic en **Aceptar**.
6.  Seleccione el tamaño de la máquina virtual. Para ver más tamaños, seleccione **Ver todo** o cambie el filtro **Supported disk type** (Tipo de disco admitido). En la hoja de configuración, conserve los valores predeterminados y haga clic en **Aceptar**.

    ![Texto alternativo de imagen](../media/msi-tutorial-windows-vm-access-arm/msi-windows-vm.png)

## <a name="enable-msi-on-your-vm"></a>Habilitación de MSI en la máquina virtual

Una identidad MSI de máquina virtual le permite obtener tokens de acceso de Azure AD sin tener que incluir las credenciales en el código. En un segundo plano, la habilitación de MSI permite hacer dos cosas: registrar la máquina virtual con Azure Active Directory para crear su identidad administrada y configurar la identidad en la máquina virtual.

1. Desplácese hasta el grupo de recursos de la nueva máquina virtual y seleccione la máquina virtual que creó en el paso anterior.
2. En la configuración de máquina virtual, a la izquierda, haga clic en **Configuración**.
3. Para registrar y habilitar MSI, seleccione **Sí**; si desea deshabilitarla, elija No.
4. No olvide hacer clic en **Guardar** para guardar la configuración.

    ![Texto alternativo de imagen](../media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

## <a name="create-a-storage-account"></a>Crear una cuenta de almacenamiento 

Si aún no tiene una, creará ahora una cuenta de almacenamiento. También puede omitir este paso y conceder acceso para MSI en la máquina virtual a las claves de una cuenta de almacenamiento existente. 

1. Haga clic en el botón **+/Crear nuevo servicio** de la esquina superior izquierda de Azure Portal.
2. Haga clic en **Storage**, a continuación, en **Cuenta de almacenamiento** y se mostrará un nuevo panel "Crear cuenta de almacenamiento".
3. Escriba un nombre para la cuenta de almacenamiento, que utilizará más adelante.  
4. **Modelo de implementación** y **Clase de cuenta** debe establecerse en "Resource Manager" y "Uso General", respectivamente. 
5. Asegúrese de que **Suscripción** y **Grupo de recursos** coinciden con los que especificó cuando creó la máquina virtual en el paso anterior.
6. Haga clic en **Create**(Crear).

    ![Creación de una nueva cuenta de almacenamiento](../media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-in-the-storage-account"></a>Creación de un contenedor de blobs en la cuenta de almacenamiento

Más adelante se cargará y descargará un archivo a la nueva cuenta de almacenamiento. Dado que los archivos requieren almacenamiento de blobs, es necesario crear un contenedor de blobs en el que almacenar el archivo.

1. Vuelva a la cuenta de almacenamiento recién creada.
2. Haga clic en el vínculo **Contenedores** a la izquierda, en "Blob service".
3. Haga clic en **+ Contenedor** en la parte superior de la página y se abrirá un panel "Nuevo contenedor".
4. Asigne un nombre al contenedor, seleccione un nivel de acceso y, a continuación, haga clic en **Aceptar**. El nombre especificado se utilizará más adelante en el tutorial. 

    ![Creación de contenedores de almacenamiento](../media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

## <a name="grant-your-vms-msi-access-to-use-storage-account-access-keys"></a>Concesión de acceso MSI a la máquina virtual para usar las claves de acceso de la cuenta de almacenamiento 

Azure Storage no admite la autenticación de Azure AD de forma nativa.  No obstante, puede usar una instancia de MSI para recuperar las claves de acceso a la cuenta de almacenamiento desde Resource Manager y usar una clave para acceder al almacenamiento.  En este paso, se concede a la instancia de MSI en la máquina virtual acceso a las claves de la cuenta de almacenamiento.   

1. Vuelva a la cuenta de almacenamiento recién creada.  
2. Haga clic en el vínculo **Control de acceso (IAM)** en el panel izquierdo.  
3. Haga clic en **+ Agregar** en la parte superior de la página para agregar una nueva asignación de roles para la máquina virtual
4. Establezca **Rol** en "Rol de servicio del operador de claves de cuentas de almacenamiento", en el lado derecho de la página. 
5. En el menú desplegable siguiente, establezca **Asignar acceso a** en el recurso "Máquina virtual".  
6. A continuación, asegúrese de que la suscripción adecuada aparece en el menú desplegable **Suscripción** y después, establezca **Grupo de recursos** en "Todos los grupos de recursos".  
7. Por último, en **Seleccionar**, elija la máquina virtual Windows en el menú desplegable y haga clic en **Guardar**. 

    ![Texto alternativo de imagen](../media/msi-tutorial-linux-vm-access-storage/msi-storage-role.png)

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-azure-resource-manager"></a>Obtención de un token de acceso utilizando la identidad de la máquina virtual y su uso para llamar a Azure Resource Manager 

En el resto del tutorial, vamos a trabajar desde la máquina virtual que se creó anteriormente. 

En esta parte tendrá que usar los cmdlets de PowerShell de Azure Resource Manager.  Si no lo tiene instalado, [descargue la versión más reciente](https://docs.microsoft.com/powershell/azure/overview) antes de continuar.

1. En Azure Portal, vaya a **Máquinas virtuales**, vaya a la máquina virtual Windows y, a continuación, desde la página **Información general**, haga clic en **Conectar** en la parte superior. 
2. Escriba su **nombre de usuario** y **contraseña** que agregó cuando creó la máquina virtual Windows. 
3. Ahora que ha creado una **conexión a Escritorio remoto** con la máquina virtual, abra PowerShell en la sesión remota.
4. Mediante Invoke-WebRequest de PowerShell, realice una solicitud al punto de conexión de MSI local para obtener un token de acceso para Azure Resource Manager.

    ```powershell
       $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -Method GET -Headers @{Metadata="true"}
    ```
    
    > [!NOTE]
    > El valor del parámetro "resource" debe coincidir exactamente con el que Azure AD espera. Al usar el id. de recurso de Azure Resource Manager, debe incluir la barra diagonal final en el URI.
    
    A continuación, extraiga el elemento "Content", que se almacena como una cadena con formato de notación de objetos JavaScript (JSON) en el objeto $response. 
    
    ```powershell
    $content = $response.Content | ConvertFrom-Json
    ```
    Luego, extraiga el token de acceso de la respuesta.
    
    ```powershell
    $ArmToken = $content.access_token
    ```
 
## <a name="get-storage-account-access-keys-from-azure-resource-manager-to-make-storage-calls"></a>Obtención de las claves de almacenamiento desde Azure Resource Manager para realizar llamadas al almacenamiento  

Ahora utilice PowerShell para llamar a Resource Manager mediante el token de acceso que se recuperó en la sección anterior, para recuperar la clave de acceso de almacenamiento. Una vez que tenemos la clave de acceso de almacenamiento, podemos hacer llamadas a operaciones de carga y descarga del almacenamiento.

```powershell
$keysResponse = Invoke-WebRequest -Uri https://management.azure.com/subscriptions/<SUBSCRIPTION-ID>/resourceGroups/<RESOURCE-GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE-ACCOUNT>/listKeys/?api-version=2016-12-01 -Method POST -Headers @{Authorization="Bearer $ARMToken"}
```
> [!NOTE] 
> La dirección URL distingue mayúsculas de minúsculas, por lo tanto, asegúrese de que usa las mismas mayúsculas y minúsculas que al asignar el nombre al grupo de recursos, así como la "G" mayúscula de "resourceGroups". 

```powershell
$keysContent = $keysResponse.Content | ConvertFrom-Json
$key = $keysContent.keys[0].value
```

A continuación, creamos un archivo llamado "test.txt". Seguidamente, use la clave de acceso al almacenamiento para autenticarse con el cmdlet `New-AzureStorageContent`, cargue el archivo en el contenedor de blobs y, después, descargue el archivo.

```bash
echo "This is a test text file." > test.txt
```

Asegúrese de instalar en primer lugar los cmdlets de Azure Storage, mediante `Install-Module Azure.Storage`. Después, cargue el blob recién creado utilizando el cmdlet `Set-AzureStorageBlobContent` de PowerShell:

```powershell
$ctx = New-AzureStorageContext -StorageAccountName <STORAGE-ACCOUNT> -StorageAccountKey $key
Set-AzureStorageBlobContent -File test.txt -Container <CONTAINER-NAME> -Blob testblob -Context $ctx
```

Respuesta:

```powershell
ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob
BlobType          : BlockBlob
Length            : 56
ContentType       : application/octet-stream
LastModified      : 9/13/2017 6:14:25 PM +00:00
SnapshotTime      :
ContinuationToken :
Context           : Microsoft.WindowsAzure.Commands.Storage.AzureStorageContext
Name              : testblob
```

También puede descargar el blob recién creado utilizando el cmdlet `Get-AzureStorageBlobContent` de PowerShell:

```powershell
Get-AzureStorageBlobContent -Blob testblob -Container <CONTAINER-NAME> -Destination test2.txt -Context $ctx
```

Respuesta:

```powershell
ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob
BlobType          : BlockBlob
Length            : 56
ContentType       : application/octet-stream
LastModified      : 9/13/2017 6:14:25 PM +00:00
SnapshotTime      :
ContinuationToken :
Context           : Microsoft.WindowsAzure.Commands.Storage.AzureStorageContext
Name              : testblob
```

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha obtenido información sobre cómo crear una instancia de Managed Service Identity para acceder a Azure Storage mediante una clave de acceso.  Para obtener más información sobre las claves de acceso de Azure Storage, vea:

> [!div class="nextstepaction"]
>[Administración de las claves de acceso de almacenamiento](/azure/storage/common/storage-create-storage-account#manage-your-storage-access-keys)

