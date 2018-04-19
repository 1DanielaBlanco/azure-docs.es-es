---
title: Archivo del registro de actividades de Azure | Microsoft Docs
description: Aprenda a archivar el registro de actividades de Azure para su retención a largo plazo en una cuenta de almacenamiento.
author: johnkemnetz
manager: orenr
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: d37d3fda-8ef1-477c-a360-a855b418de84
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/09/2016
ms.author: johnkem
ms.openlocfilehash: 6020272d79ace55041da94ee45165e557e92b80f
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/06/2018
---
# <a name="archive-the-azure-activity-log"></a>Archivo del registro de actividades de Azure
En este artículo, le mostraremos cómo puede usar Azure Portal, los cmdlets de PowerShell o la CLI multiplataforma para archivar el [**registro de actividades de Azure**](monitoring-overview-activity-logs.md) en una cuenta de almacenamiento. Esta opción es útil si desea conservar el registro de actividades más de 90 días (con control total sobre la directiva de retención) para auditorías, análisis estáticos o copias de seguridad. Si solo necesita conservar los eventos durante 90 días o menos, no es necesario configurar el archivado en una cuenta de almacenamiento, ya que los eventos del registro de actividades se conservan en la plataforma de Azure durante 90 días sin necesidad de habilitar el archivado.

## <a name="prerequisites"></a>Requisitos previos
Antes de comenzar, necesita [crear una cuenta de almacenamiento](../storage/common/storage-create-storage-account.md#create-a-storage-account) en la que poder archivar el registro de actividades. Le recomendamos encarecidamente que no utilice una cuenta de almacenamiento existente que tenga otros datos sin supervisión almacenados en ella, para que pueda controlar mejor el acceso a los datos de supervisión. Sin embargo, si también va a archivar los registros y las métricas de Diagnóstico en una cuenta de almacenamiento, puede que tenga sentido utilizar esa cuenta de almacenamiento para el registro de actividades para mantener todos los datos de supervisión en una ubicación central. La cuenta de almacenamiento que use debe ser una cuenta de almacenamiento de propósito general no una cuenta de almacenamiento de blobs. La cuenta de almacenamiento no tiene que estar en la misma suscripción que la que emite los registros, siempre que el usuario que configura la configuración tenga acceso RBAC adecuado a ambas suscripciones.

## <a name="log-profile"></a>Perfil de registro
Para archivar el registro de actividades mediante cualquiera de los métodos siguientes, debe establecer el **perfil de registro** para una suscripción. El perfil de registro define el tipo de eventos que se almacenan o transmiten y las salidas: cuenta de almacenamiento y/o centro de eventos. También define la directiva de retención (el número de días que deben conservarse) para los eventos almacenados en una cuenta de almacenamiento. Si la directiva de retención se establece en cero los eventos se almacenan indefinidamente. De lo contrario, se puede establecer en cualquier valor entre 1 y 2147483647. Las directivas de retención se aplican a diario, por lo que al final de un día (UTC) se eliminan los registros del día que quede fuera de la directiva de retención. Por ejemplo, si tuviera una directiva de retención de un día, se eliminarían los registros de anteayer al principio del día de hoy. [Puede leer más acerca de los perfiles de registro aquí](monitoring-overview-activity-logs.md#export-the-activity-log-with-a-log-profile). 

## <a name="archive-the-activity-log-using-the-portal"></a>Archivo del registro de actividades mediante el portal
1. En el portal, haga clic en el vínculo **Registro de actividades** situado en el lado izquierdo. Si no ve un vínculo para el registro de actividades, haga clic primero en el vínculo **Todos los servicios**.
   
    ![Ir a la hoja del registro de actividades](media/monitoring-archive-activity-log/act-log-portal-navigate.png)
2. En la parte superior de la hoja, haga clic en **Exportar**.
   
    ![Hacer clic en el botón Exportar](media/monitoring-archive-activity-log/act-log-portal-export-button.png)
3. En la hoja que aparece, seleccione la casilla para **exportar a una cuenta de almacenamiento** y seleccione una cuenta de almacenamiento.
   
    ![Establecer una cuenta de almacenamiento](media/monitoring-archive-activity-log/act-log-portal-export-blade.png)
4. Con el control deslizante o el cuadro de texto, defina el número de días que deben conservarse los eventos del registro de actividades en la cuenta de almacenamiento. Si prefiere que los datos se conserven en la cuenta de almacenamiento indefinidamente establezca este número en cero.
5. Haga clic en **Save**(Guardar).

## <a name="archive-the-activity-log-via-powershell"></a>Archivo del registro de actividades a través de PowerShell

   ```powershell
   # Settings needed for the new log profile
   $logProfileName = "default"
   $locations = (Get-AzureRmLocation).Location
   $locations += "global"
   $subscriptionId = "<your Azure subscription Id>"
   $resourceGroupName = "<resource group name your storage account belongs to>"
   $storageAccountName = "<your storage account name>"

   # Build the storage account Id from the settings above
   $storageAccountId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Storage/storageAccounts/$storageAccountName"

   Add-AzureRmLogProfile -Name $logProfileName -Location $locations -StorageAccountId $storageAccountId
   ```

| Propiedad | Obligatorio | DESCRIPCIÓN |
| --- | --- | --- |
| StorageAccountId |Sí |Identificador de recurso de la cuenta de almacenamiento donde se deben guardar los registros de actividades. |
| Ubicaciones |Sí |Lista separada por comas de las regiones para las que desea recopilar eventos del registro de actividad. Puede ver una lista de todas las regiones para la suscripción con `(Get-AzureRmLocation).Location`. |
| RetentionInDays |Sin  |Número de días que deben retenerse los eventos, entre 1 y 2147483647. Con el valor cero, se almacenan los registros indefinidamente. |
| Categorías |Sin  |Lista separada por comas de las categorías de eventos que deben recopilarse. Los valores posibles son Write, Delete y Action.  Si no se proporciona, se presuponen todos los valores posibles |

## <a name="archive-the-activity-log-via-cli"></a>Archivo del registro de actividades a través de CLI

   ```azurecli-interactive
   az monitor log-profiles create --name "default" --location null --locations "global" "eastus" "westus" --categories "Delete" "Write" "Action"  --enabled false --days 0 --storage-account-id "/subscriptions/<YOUR SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>"
   ```

| Propiedad | Obligatorio | DESCRIPCIÓN |
| --- | --- | --- |
| Nombre |Sí |Nombre de su perfil de registro. |
| storage-account-id |Sí |Identificador de recurso de la cuenta de almacenamiento donde se deben guardar los registros de actividades. |
| Ubicaciones |Sí |Lista separada por espacios de las regiones para las que desea recopilar eventos del registro de actividad. Puede ver una lista de todas las regiones para la suscripción con `az account list-locations --query [].name`. |
| days |Sí |Número de días que deben retenerse los eventos, entre 1 y 2147483647. Con el valor cero, se almacenarán los registros indefinidamente (de manera indefinida).  Si el valor es cero, el parámetro habilitado se debe establecer en true. |
|Enabled | Sí |True o False.  Se usa para habilitar o deshabilitar la directiva de retención.  Si el valor es True, el parámetro de días debe ser un valor mayor que 0.
| Categorías |Sí |Lista separada por espacios de las categorías de eventos que deben recopilarse. Los valores posibles son Write, Delete y Action. |

## <a name="storage-schema-of-the-activity-log"></a>Esquema de almacenamiento del registro de actividades
Una vez que haya configurado el archivado, se creará un contenedor de almacenamiento en la cuenta de almacenamiento en cuanto se produzca un evento del registro de actividades. Los blobs dentro del contenedor siguen el mismo formato en el registro de actividades y en los registros de diagnóstico. La estructura de estos blobs es:

> insights-operational-logs/name=default/resourceId=/SUBSCRIPTIONS/{subscription ID}/y={año con cuatro dígitos}/m={mes con dos dígitos}/d={día con dos dígitos}/h={hora en formato de 24 horas con dos dígitos}/m=00/PT1H.json
> 
> 

Por ejemplo, un nombre de blob podría ser:

> insights-operational-logs/name=default/resourceId=/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/y=2016/m=08/d=22/h=18/m=00/PT1H.json
> 
> 

Cada blob PT1H.json contiene un blob JSON de eventos que se produjeron en la hora especificada en la dirección URL del blob (por ejemplo h = 12). Durante la hora en cuestión, los eventos se anexan al archivo PT1H.json a medida que se producen. El valor de los minutos (m = 00) siempre es 00, ya que los eventos del registro de actividades se dividen en blobs individuales por hora.

En el archivo PT1H.json, cada evento se almacena en la matriz de "registros" con este formato:

```
{
    "records": [
        {
            "time": "2015-01-21T22:14:26.9792776Z",
            "resourceId": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
            "operationName": "microsoft.support/supporttickets/write",
            "category": "Write",
            "resultType": "Success",
            "resultSignature": "Succeeded.Created",
            "durationMs": 2826,
            "callerIpAddress": "111.111.111.11",
            "correlationId": "c776f9f4-36e5-4e0e-809b-c9b3c3fb62a8",
            "identity": {
                "authorization": {
                    "scope": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
                    "action": "microsoft.support/supporttickets/write",
                    "evidence": {
                        "role": "Subscription Admin"
                    }
                },
                "claims": {
                    "aud": "https://management.core.windows.net/",
                    "iss": "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",
                    "iat": "1421876371",
                    "nbf": "1421876371",
                    "exp": "1421880271",
                    "ver": "1.0",
                    "http://schemas.microsoft.com/identity/claims/tenantid": "1e8d8218-c5e7-4578-9acc-9abbd5d23315 ",
                    "http://schemas.microsoft.com/claims/authnmethodsreferences": "pwd",
                    "http://schemas.microsoft.com/identity/claims/objectidentifier": "2468adf0-8211-44e3-95xq-85137af64708",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "admin@contoso.com",
                    "puid": "20030000801A118C",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "9vckmEGF7zDKk1YzIY8k0t1_EAPaXoeHyPRn6f413zM",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "John",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "Smith",
                    "name": "John Smith",
                    "groups": "cacfe77c-e058-4712-83qw-f9b08849fd60,7f71d11d-4c41-4b23-99d2-d32ce7aa621c,31522864-0578-4ea0-9gdc-e66cc564d18c",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": " admin@contoso.com",
                    "appid": "c44b4083-3bq0-49c1-b47d-974e53cbdf3c",
                    "appidacr": "2",
                    "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
                    "http://schemas.microsoft.com/claims/authnclassreference": "1"
                }
            },
            "level": "Information",
            "location": "global",
            "properties": {
                "statusCode": "Created",
                "serviceRequestId": "50d5cddb-8ca0-47ad-9b80-6cde2207f97c"
            }
        }
    ]
}
```


| Nombre del elemento | DESCRIPCIÓN |
| --- | --- |
| Twitter en tiempo |Marca de tiempo de cuándo el servicio de Azure generó el evento que procesó la solicitud correspondiente al evento. |
| ResourceId |Identificador de recurso del recurso afectado. |
| operationName |Nombre de la operación. |
| categoría |Categoría de la acción, p. ej. Write, Read, Action. |
| resultType |Tipo del resultado, p. ej. Success, Failure, Start |
| resultSignature |Depende del tipo de recurso. |
| durationMs |Duración de la operación en milisegundos |
| callerIpAddress |La dirección IP del usuario que ha realizado la operación, la notificación de UPN o la notificación de SPN basada en la disponibilidad. |
| correlationId |Normalmente, un GUID en formato de cadena. Los eventos que comparten correlationId pertenecen a la misma acción general. |
| identidad |Blob JSON que describe la autorización y las notificaciones. |
| authorization |Blob de propiedades RBAC del evento. Normalmente incluye las propiedades "action", "role" y "scope". |
| level |Nivel del evento. Uno de los siguientes valores: "Critical", "Error", "Warning", "Informational" y "Verbose" |
| location |Región en la que se ha producido la ubicación (o global). |
| propiedades |Conjunto de pares `<Key, Value>` (es decir, diccionario) que describe los detalles del evento. |

> [!NOTE]
> Las propiedades y el uso de estas propiedades pueden variar según el recurso.
> 
> 

## <a name="next-steps"></a>Pasos siguientes
* [Descargar blobs para el análisis](../storage/blobs/storage-quickstart-blobs-dotnet.md)
* [Transmitir el registro de actividades a Event Hubs](monitoring-stream-activity-logs-event-hubs.md)
* [Obtener más información acerca del registro de actividades](monitoring-overview-activity-logs.md)

