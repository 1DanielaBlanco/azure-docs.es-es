---
title: Administración de servidores registrados con Azure File Sync (versión preliminar) | Microsoft Docs
description: Aprenda a registrar y anular el registro de una instancia de Windows Server con un servicio de sincronización de almacenamiento de Azure File Sync.
services: storage
documentationcenter: ''
author: wmgries
manager: klaasl
editor: jgerend
ms.assetid: 297f3a14-6b3a-48b0-9da4-db5907827fb5
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2017
ms.author: wgries
ms.openlocfilehash: 9367b2bdb1bb77725356d2be41d5e44d900cb927
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/19/2018
---
# <a name="manage-registered-servers-with-azure-file-sync-preview"></a>Administración de servidores registrados con Azure File Sync (versión preliminar)
Azure File Sync (versión preliminar) permite centralizar los recursos compartidos de archivos de su organización en Azure Files sin renunciar a la flexibilidad, el rendimiento y la compatibilidad de un servidor de archivos local. Para ello la transformación de los servidores Windows Server en una caché rápida de los recursos compartidos de Azure Files. Puede usar cualquier protocolo disponible en Windows Server para tener acceso a los datos localmente (incluidos SMB, NFS y FTPS) y puede tener tantas cachés según sea necesario en todo el mundo.

En el artículo siguiente se ilustra cómo registrar y administrar un servidor con un servicio de sincronización de almacenamiento. Consulte [How to deploy Azure File Sync (preview)](storage-sync-files-deployment-guide.md) (Implementación de Azure File Sync [versión preliminar]).

## <a name="registerunregister-a-server-with-storage-sync-service"></a>Registro y anulación del registro de un servidor con el servicio de sincronización de almacenamiento
Al registrar un servidor con Azure File Sync se establece una relación de confianza entre Windows Server y Azure. A continuación, se puede usar esta relación para crear *puntos de conexión de servidor* en el servidor, que representan carpetas concretas que deben sincronizarse con un recurso compartido de archivos de Azure (también conocido como *punto de conexión de nube*). 

### <a name="prerequisites"></a>requisitos previos
Para registrar un servidor con un servicio de sincronización de almacenamiento, debe preparar el servidor con los requisitos previos necesarios:

* El servidor debe ejecutar una versión compatible de Windows Server. Para más información, consulte [Versiones compatibles de Windows Server](storage-sync-files-planning.md#supported-versions-of-windows-server).
* Asegúrese de que se ha implementado un servicio de sincronización de almacenamiento. Para más información sobre cómo implementar un servicio de sincronización de almacenamiento, consulte [How to deploy Azure File Sync (preview)](storage-sync-files-deployment-guide.md) (Implementación de Azure File Sync [versión preliminar]).
* Asegúrese de que el servidor está conectado a Internet y que se puede acceder a Azure.
* Deshabilite la configuración de seguridad mejorada de IE para administradores con la interfaz de usuario del Administrador del servidor.
    
    ![Interfaz de usuario del Administrador del servidor con la configuración de seguridad mejorada de IE resaltada](media/storage-sync-files-server-registration/server-manager-ie-config.png)

* Asegúrese de que el módulo AzureRM de PowerShell esté instalado en el servidor. Si el servidor es miembro de un clúster de conmutación por error, todos los nodos del clúster necesitarán el módulo de AzureRM. Pueden encontrar más detalles sobre cómo instalar el módulo AzureRM en [Install and configure Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) (Instalación y configuración de Azure PowerShell).

    > [!Note]  
    > Se recomienda usar la versión más reciente del módulo AzureRM de PowerShell para registrar o anular el registro de un servidor. Si el paquete de AzureRM se ha instalado anteriormente en este servidor (y la versión de PowerShell en este servidor es 5.* o superior), puede usar el cmdlet `Update-Module` para actualizar este paquete. 
* Si utiliza un servidor proxy de red en su entorno, configure el proxy en el servidor para que lo utilice el agente de sincronización.
    1. Determine la dirección IP y el número de puerto del proxy.
    2. Edite estos dos archivos:
        * C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config
        * C:\Windows\Microsoft.NET\Framework\v4.0.30319\Config\machine.config
    3. Agregue las líneas de la figura 1 (debajo de esta sección) a /System.ServiceModel en los dos archivos anteriores cambiando 127.0.0.1:8888 por la dirección IP correcta (reemplace 127.0.0.1) y el número de puerto correcto (reemplace 8888):
    4. Establezca la configuración del proxy WinHTTP mediante la línea de comandos:
        * Mostrar el proxy: netsh winhttp show proxy
        * Establecer el proxy: netsh winhttp set proxy 127.0.0.1:8888
        * Restablecer el proxy: netsh winhttp reset proxy
        * Si se configura esto después de instalar el agente, reinicie el agente de sincronización: net stop filesyncsvc
    
```XML
    Figure 1:
    <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
            <proxy autoDetect="false" bypassonlocal="false" proxyaddress="http://127.0.0.1:8888" usesystemdefault="false" />
        </defaultProxy>
    </system.net>
```    

### <a name="register-a-server-with-storage-sync-service"></a>Registro de un servidor con el servicio de sincronización de almacenamiento
Antes de usar un servidor como *punto de conexión del servidor* en un *grupo de sincronización* de Azure File Sync, se debe registrar con un *servicio de sincronización de almacenamiento*. Un servidor solo se puede registrar con un único servicio de sincronización de almacenamiento al mismo tiempo.

#### <a name="install-the-azure-file-sync-agent"></a>Instalación del agente de Azure File Sync
1. [Descargue el agente de Azure File Sync](https://go.microsoft.com/fwlink/?linkid=858257).
2. Inicie el instalador del agente de Azure File Sync.
    
    ![El primer panel del instalador del agente de Azure File Sync](media/storage-sync-files-server-registration/install-afs-agent-1.png)

3. Asegúrese de habilitar las actualizaciones del agente de Azure File Sync mediante Microsoft Update. Esto es importante porque las correcciones críticas de seguridad y las mejoras de características del paquete de servidor se envían a través de Microsoft Update.

    ![Asegúrese de que Microsoft Update está habilitado en el panel de Microsoft Update del instalador del agente de Azure File Sync.](media/storage-sync-files-server-registration/install-afs-agent-2.png)

4. Si el servidor no se ha registrado anteriormente, se abre inmediatamente la interfaz de usuario de registro del servidor tras finalizar la instalación.

> [!Important]  
> Si el servidor es miembro de un clúster de conmutación por error, el agente de Azure File Sync se deberá instalar en cada nodo del clúster.

#### <a name="register-the-server-using-the-server-registration-ui"></a>Registro del servidor mediante la interfaz de usuario de registro del servidor
> [!Important]  
> Las suscripciones del proveedor de soluciones en la nube (CSP) no pueden utilizar la interfaz de usuario de registro del servidor. En su lugar, use PowerShell (debajo de esta sección).

1. Si la interfaz de usuario de registro del servidor no se inició inmediatamente después de finalizar la instalación del agente de Azure File Sync, se puede iniciar manualmente ejecutando `C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe`.
2. Haga clic en *Iniciar sesión* para acceder a la suscripción de Azure. 

    ![Apertura del cuadro de diálogo de la interfaz de usuario de registro de servidor](media/storage-sync-files-server-registration/server-registration-ui-1.png)

3. En el cuadro de diálogo, seleccione la suscripción correcta, el grupo de recursos y el servicio de sincronización de almacenamiento.

    ![Información del servicio de sincronización de almacenamiento](media/storage-sync-files-server-registration/server-registration-ui-2.png)

4. En la versión preliminar, se necesita otro inicio de sesión más para completar el proceso. 

    ![Cuadro de diálogo de inicio de sesión](media/storage-sync-files-server-registration/server-registration-ui-3.png)

> [!Important]  
> Si el servidor es miembro de un clúster de conmutación por error, cada servidor debe ejecutar el registro de servidor. Cuando ve los servidores registrados en Azure Portal, Azure File Sync reconoce automáticamente cada nodo como miembro del mismo clúster de conmutación por error y los agrupa de la manera adecuada.

#### <a name="register-the-server-with-powershell"></a>Registro del servidor con PowerShell
También puede realizar el registro del servidor a través de PowerShell. Esta es la única forma admitida de registrar el servidor para las suscripciones del proveedor de soluciones en la nube (CSP):

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll"
Login-AzureRmStorageSync -SubscriptionID "<your-subscription-id>" -TenantID "<your-tenant-id>"
Register-AzureRmStorageSyncServer -SubscriptionId "<your-subscription-id>" - ResourceGroupName "<your-resource-group-name>" - StorageSyncService "<your-storage-sync-service-name>"
```

### <a name="unregister-the-server-with-storage-sync-service"></a>Cancelación del registro del servidor del servicio de sincronización de almacenamiento
Hay varios pasos que son necesarios para anular el registro de un servidor del servicio de sincronización de almacenamiento. A continuación se indica cómo anular el registro correctamente de un servidor.

#### <a name="optional-recall-all-tiered-data"></a>(Opcional) Recuperación de todos los datos con niveles
Cuando la característica de niveles de nube está habilitada para un punto de conexión de servidor, los archivos se *apilan* a los recursos compartidos de Azure Files. De esta forma, los recursos compartidos de archivos locales funcionan como una caché, en lugar de como una copia completa del conjunto de datos, para realizar un uso eficiente del espacio del servidor de archivos. Sin embargo, si se quita un punto de conexión de servidor con archivos con niveles que aún se encuentran localmente en el servidor, esos archivos se volverán inaccesibles. Por tanto, si sigue queriendo acceder a los archivos, debe recuperar todos los archivos con niveles de Azure Files antes de continuar con la cancelación del registro. 

Para ello, se puede usar el cmdlet de PowerShell, tal y como se muestra a continuación:

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint>
```

> [!Warning]  
> Si el volumen local que hospeda el punto de conexión de servidor no tiene suficiente espacio disponible para recuperar todos los datos con niveles, el cmdlet `Invoke-StorageSyncFileRecall` dará error.  

#### <a name="remove-the-server-from-all-sync-groups"></a>Eliminación del servidor de todos los grupos de sincronización
Antes de anular el registro del servidor en el servicio de sincronización de almacenamiento, se deben quitar todos los puntos de conexión de servidor de ese servidor. Esta acción también se puede hacer mediante Azure Portal:

1. Vaya al servicio de sincronización de almacenamiento donde está registrado el servidor.
2. Quite todos los puntos de conexión de servidor de este servidor en cada grupo de sincronización del servicio de sincronización de almacenamiento. Para ello, haga clic con el botón derecho en el punto de conexión de servidor que le interese en el panel de grupos de sincronización.

    ![Eliminación de un punto de conexión de servidor de un grupo de sincronización](media/storage-sync-files-server-registration/sync-group-server-endpoint-remove-1.png)

Esta acción también se puede realizar con un script simple de PowerShell:

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll"

$accountInfo = Connect-AzureRmAccount
Login-AzureRmStorageSync -SubscriptionId $accountInfo.Context.Subscription.Id -TenantId $accountInfo.Context.Tenant.Id -ResourceGroupName "<your-resource-group>"

$StorageSyncService = "<your-storage-sync-service>"

Get-AzureRmStorageSyncGroup -StorageSyncServiceName $StorageSyncService | ForEach-Object { 
    $SyncGroup = $_; 
    Get-AzureRmStorageSyncServerEndpoint -StorageSyncServiceName $StorageSyncService -SyncGroupName $SyncGroup.Name | Where-Object { $_.DisplayName -eq $env:ComputerName } | ForEach-Object { 
        Remove-AzureRmStorageSyncServerEndpoint -StorageSyncServiceName $StorageSyncService -SyncGroupName $SyncGroup.Name -ServerEndpointName $_.Name 
    } 
}
```

#### <a name="unregister-the-server"></a>Anulación del registro del servidor
Ahora que todos los datos se han recuperado y que el servidor se ha quitado de todos los grupos de sincronización, se puede anular el registro del servidor. 

1. En Azure Portal, vaya a la sección *Servidores registrados* del servicio de sincronización de almacenamiento.
2. Haga clic con el botón derecho en el servidor cuyo registro desea anular y haga clic en "Cancelar registro de servidor".

    ![Anulación del registro del servidor](media/storage-sync-files-server-registration/unregister-server-1.png)

## <a name="ensuring-azure-file-sync-is-a-good-neighbor-in-your-datacenter"></a>Configuración de Azure File Sync para que sea un buen vecino en el centro de datos 
Puesto que Azure File Sync rara vez será el único servicio en ejecución en el centro de datos, es posible que le interese limitar el uso de red y de almacenamiento de Azure File Sync.

> [!Important]  
> Si establece límites demasiado bajos, el rendimiento de la sincronización y la recuperación de Azure File Sync se verá afectado.

### <a name="set-azure-file-sync-network-limits"></a>Establecimiento de límites de red de Azure File Sync
Puede limitar el uso que hace Azure File Sync de la red mediante los cmdlets `StorageSyncNetworkLimit`. 

Por ejemplo, puede crear un nuevo límite del regulador de carga de trabajo para asegurarse de que Azure File Sync no utiliza más de 10 Mbps entre las 9 a. m. y las 5 p. m. (17:00 h) durante la semana laboral: 

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
New-StorageSyncNetworkLimit -Day Monday, Tuesday, Wednesday, Thursday, Friday -StartHour 9 -EndHour 17 -LimitKbps 10000
```

Puede ver el límite mediante el siguiente cmdlet:

```PowerShell
Get-StorageSyncNetworkLimit # assumes StorageSync.Management.ServerCmdlets.dll is imported
```

Para quitar límites de red, use `Remove-StorageSyncNetworkLimit`. Por ejemplo, el comando siguiente quita todos los límites de red:

```PowerShell
Get-StorageSyncNetworkLimit | ForEach-Object { Remove-StorageSyncNetworkLimit -Id $_.Id } # assumes StorageSync.Management.ServerCmdlets.dll is imported
```

### <a name="use-windows-server-storage-qos"></a>Uso de la calidad de servicio de almacenamiento de Windows Server 
Cuando Azure File Sync se hospeda en una máquina virtual que se ejecuta en un host de virtualización de Windows Server, puede usar la calidad de servicio de almacenamiento (QoS de almacenamiento) para regular el consumo de E/S de almacenamiento. La directiva de QoS de almacenamiento puede establecerse como un valor máximo (o límite, como el límite StorageSyncNetwork aplicado anteriormente) o como un valor mínimo (o reserva). Al establecer un mínimo en lugar de un máximo, Azure File Sync puede usar el ancho de banda de almacenamiento disponible si otras cargas de trabajo no lo están utilizando. Para más información, consulte [Calidad de servicio de almacenamiento](https://docs.microsoft.com/windows-server/storage/storage-qos/storage-qos-overview).

## <a name="see-also"></a>Otras referencias
- [Planeamiento de una implementación de Azure File Sync (versión preliminar)](storage-sync-files-planning.md)
- [Implementar Azure File Sync (versión preliminar)](storage-sync-files-deployment-guide.md) 
- [Solución de problemas de Azure File Sync (versión preliminar)](storage-sync-files-troubleshoot.md)
