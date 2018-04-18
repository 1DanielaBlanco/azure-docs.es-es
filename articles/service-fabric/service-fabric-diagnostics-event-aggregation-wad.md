---
title: Agregación de eventos de Azure Service Fabric con Azure Diagnostics de Windows | Microsoft Docs
description: Obtenga información sobre la agregación y la recopilación de eventos con WAD para la supervisión y el diagnóstico de clústeres de Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/19/2018
ms.author: dekapur;srrengar
ms.openlocfilehash: 65e5e45300e66cd8c3acc44a91335de45a919eb5
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/06/2018
---
# <a name="event-aggregation-and-collection-using-windows-azure-diagnostics"></a>Recopilación y agregación de eventos con Azure Diagnostics de Windows
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-event-aggregation-wad.md)
> * [Linux](service-fabric-diagnostics-event-aggregation-lad.md)
>
>

Cuando se ejecuta un clúster de Azure Service Fabric, es conveniente recopilar los registros de todos los nodos en una ubicación central. La presencia de los registros en una ubicación central facilita el análisis y la solución de los problemas del clúster o de las aplicaciones y los servicios que se ejecutan en ese clúster.

Uno de los métodos para cargar y recopilar registros es usar la extensión Azure Diagnostics de Windows (WAD), que carga registros en Azure Storage, y también tiene la opción de enviar registros a Azure Application Insights o Event Hubs. También puede usar un proceso externo para leer los eventos desde el almacenamiento y colocarlos en un producto de plataforma de análisis, como [Log Analytics](../log-analytics/log-analytics-service-fabric.md) u otra solución de análisis de registros.

## <a name="prerequisites"></a>requisitos previos
Estas herramientas se usan para realizar algunas de las operaciones que se describen en este documento:

* [Diagnósticos de Azure](../cloud-services/cloud-services-dotnet-diagnostics.md) (relacionado con Azure Cloud Services, aunque con información y ejemplos adecuados)
* [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md)
* [Azure PowerShell](/powershell/azure/overview)
* [Cliente de Azure Resource Manager](https://github.com/projectkudu/ARMClient)
* [Plantilla de Azure Resource Manager](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="log-and-event-sources"></a>Orígenes de eventos y registros

### <a name="service-fabric-platform-events"></a>Eventos de plataforma de Service Fabric
Como se describe en [este artículo](service-fabric-diagnostics-event-generation-infra.md), Service Fabric configura algunos canales de registro predefinidos. Los que se indican a continuación se configuran fácilmente con WAD para que envíen datos de supervisión y diagnóstico a una tabla de almacenamiento u otro lugar:
  * Eventos operativos: operaciones de nivel superior realizadas por la plataforma Service Fabric. Algunos ejemplos son la creación de aplicaciones y servicios, los cambios de estado de nodo y la información sobre la actualización. Se emiten como registros de Seguimiento de eventos para Windows (ETW).
  * [Eventos del modelo de programación de Reliable Actors](service-fabric-reliable-actors-diagnostics.md)
  * [Eventos del modelo de programación de Reliable Services](service-fabric-reliable-services-diagnostics.md)

### <a name="application-events"></a>Eventos de aplicación
 Son los eventos que se emiten desde el código de las aplicaciones y los servicios y que se escriben mediante la clase auxiliar EventSource proporcionada en las plantillas de Visual Studio. Para obtener más información sobre cómo escribir registros EventSource de la aplicación, vea [Supervisión y diagnóstico de servicios en una configuración de desarrollo de máquina local](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md).

## <a name="deploy-the-diagnostics-extension"></a>Implementación de la extensión de Diagnósticos
El primer paso para recopilar registros será implementar la extensión WAD en cada una de las máquinas virtuales del clúster de Service Fabric. La extensión de Diagnósticos recopila registros en cada máquina virtual y los carga a la cuenta de almacenamiento que especifique. Los pasos varían ligeramente en función de si se utiliza Azure Portal o Azure Resource Manager. Los pasos varían también en función de si la implementación forma parte de la creación del clúster o si es para un clúster que ya existe. Echemos un vistazo a los pasos para cada escenario.

### <a name="deploy-the-diagnostics-extension-as-part-of-cluster-creation-through-azure-portal"></a>Implementación de la extensión Diagnostics como parte de la creación del clúster a través de Azure Portal
Para implementar la extensión Diagnostics en las máquinas virtuales del clúster como parte de la creación de dicho clúster, se usa el panel Configuración de diagnóstico que se muestra en la imagen siguiente. Asegúrese de que Diagnostics esté establecido en **Activado** (valor predeterminado). Después de crear el clúster, no puede cambiar esta configuración mediante el portal.

![Configuración de Diagnósticos de Azure en el portal para crear un clúster](media/service-fabric-diagnostics-event-aggregation-wad/azure-enable-diagnostics.png)

Al crear un clúster mediante el portal, es muy recomendable descargar la plantilla **antes de hacer clic en Aceptar** para crear el clúster. Para más información, vea [Configuración de un clúster de Service Fabric con una plantilla de Azure Resource Manager](service-fabric-cluster-creation-via-arm.md). Necesitará la plantilla para realizar cambios más adelante, porque no se pueden realizar algunos cambios a través del portal.

### <a name="deploy-the-diagnostics-extension-as-part-of-cluster-creation-by-using-azure-resource-manager"></a>Implementación de la extensión de Diagnósticos como parte de la creación del clúster a través del Administrador de recursos de Azure
Para crear un clúster mediante el Resource Manager, tiene que agregar el JSON de la configuración de Diagnósticos a la plantilla de Resource Manager del clúster completo antes de crear el clúster. Dentro de los ejemplos de plantillas del Administrador de recursos, proporcionamos una plantilla de ejemplo del Administrador de recursos de clúster de cinco máquinas virtuales con la configuración de Diagnósticos añadida. Puede verlo en: [Ejemplo de plantilla de clúster de cinco nodos con el Administrador de recursos de Diagnósticos](https://azure.microsoft.com/en-in/resources/templates/service-fabric-secure-cluster-5-node-1-nodetype/)en la galería de ejemplos de Azure.

Para ver la configuración de Diagnósticos en la plantilla de Resource Manager, abra el archivo azuredeploy.json y busque **IaaSDiagnostics**. Para crear un clúster con esta plantilla, presione el botón **Deploy to Azure** (Implementar en Azure) disponible en el vínculo anterior.

También puede descargar el ejemplo de Resource Manager, modificarlo y crear un clúster con la plantilla modificada mediante el comando `New-AzureRmResourceGroupDeployment` en una ventana de Azure PowerShell. Vea el código siguiente para los parámetros que se pasan al comando. Para más información sobre cómo implementar un grupo de recursos con PowerShell, vea el artículo sobre la [implementación de un grupo de recursos con la plantilla de Azure Resource Manager](../azure-resource-manager/resource-group-template-deploy.md).

### <a name="deploy-the-diagnostics-extension-to-an-existing-cluster"></a>Implementación de la extensión de Diagnósticos en un clúster existente
Si tiene un clúster existente que no tiene implementada la extensión Diagnósticos, o bien quiere modificar una configuración existente, puede agregarla o actualizarla. Modifique la plantilla de Resource Manager usada para crear el clúster existente o descargue la plantilla desde el portal, tal como se describió anteriormente. Modifique el archivo template.json mediante la realización de las siguientes tareas.

Agregue un nuevo recurso de almacenamiento a la plantilla mediante una adición en la sección de recursos.

```json
{
  "apiVersion": "2015-05-01-preview",
  "type": "Microsoft.Storage/storageAccounts",
  "name": "[parameters('applicationDiagnosticsStorageAccountName')]",
  "location": "[parameters('computeLocation')]",
  "properties": {
    "accountType": "[parameters('applicationDiagnosticsStorageAccountType')]"
  },
  "tags": {
    "resourceType": "Service Fabric",
    "clusterName": "[parameters('clusterName')]"
  }
},
```

 A continuación, realice la adición en la sección de parámetros justo después de las definiciones de la cuenta de almacenamiento, entre `supportLogStorageAccountName` y `vmNodeType0Name`. Reemplace el texto de marcador de posición *aquí va el nombre de la cuenta de almacenamiento* por el nombre de la cuenta de almacenamiento.

```json
    "applicationDiagnosticsStorageAccountType": {
      "type": "string",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS"
      ],
      "defaultValue": "Standard_LRS",
      "metadata": {
        "description": "Replication option for the application diagnostics storage account"
      }
    },
    "applicationDiagnosticsStorageAccountName": {
      "type": "string",
      "defaultValue": "storage account name goes here",
      "metadata": {
        "description": "Name for the storage account that contains application diagnostics data from the cluster"
      }
    },
```
Luego, actualice la sección `VirtualMachineProfile` del archivo template.json. Para ello, agregue el siguiente código dentro de la matriz de extensiones. Asegúrese de agregar una coma al principio o al final, según donde se inserte.

```json
{
    "name": "[concat(parameters('vmNodeType0Name'),'_Microsoft.Insights.VMDiagnosticsSettings')]",
    "properties": {
        "type": "IaaSDiagnostics",
        "autoUpgradeMinorVersion": true,
        "protectedSettings": {
        "storageAccountName": "[parameters('applicationDiagnosticsStorageAccountName')]",
        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('applicationDiagnosticsStorageAccountName')),'2015-05-01-preview').key1]",
        "storageAccountEndPoint": "https://core.windows.net/"
        },
        "publisher": "Microsoft.Azure.Diagnostics",
        "settings": {
        "WadCfg": {
            "DiagnosticMonitorConfiguration": {
            "overallQuotaInMB": "50000",
            "EtwProviders": {
                "EtwEventSourceProviderConfiguration": [
                {
                    "provider": "Microsoft-ServiceFabric-Actors",
                    "scheduledTransferKeywordFilter": "1",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricReliableActorEventTable"
                    }
                },
                {
                    "provider": "Microsoft-ServiceFabric-Services",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricReliableServiceEventTable"
                    }
                }
                ],
                "EtwManifestProviderConfiguration": [
                {
                    "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
                    "scheduledTransferLogLevelFilter": "Information",
                    "scheduledTransferKeywordFilter": "4611686018427387904",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricSystemEventTable"
                    }
                }
                ]
            }
            }
        },
        "StorageAccount": "[parameters('applicationDiagnosticsStorageAccountName')]"
        },
        "typeHandlerVersion": "1.5"
    }
}
```

Después de modificar el archivo template.json tal como se indicó, vuelva a publicar la plantilla de Resource Manager. En caso de que la plantilla se haya exportado, si ejecuta el archivo deploy.ps1 , se vuelve a publicar dicha plantilla. Después de realizar la implementación, asegúrese de que el estado **ProvisioningState** sea **Correcto**.

> [!TIP]
> Si va a implementar contenedores en el clúster, habilite WAD para recopilar estadísticas de Docker. Para ello, agregue lo siguiente a la sección **WadCfg > DiagnosticMonitorConfiguration**.
>
>```json
>"DockerSources": {
>    "Stats": {
>        "enabled": true,
>        "sampleRate": "PT1M"
>    }
>},
>```

## <a name="log-collection-configurations"></a>Configuraciones de recopilación de registros
Los registros de los canales adicionales también están disponibles para la recopilación, estas son algunas de las configuraciones más comunes que puede realizar en la plantilla para los clústeres que se ejecutan en Azure.

* Canal operativo, básico: operaciones de alto nivel, habilitadas de manera predeterminada, que realiza Service Fabric y el clúster, incluidos eventos para un nodo próximo, una nueva aplicación que se implementa o la reversión de una actualización, etc. Para obtener una lista de eventos, consulte los [eventos de canal operativo](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-diagnostics-event-generation-operational).
  
```json
      scheduledTransferKeywordFilter: "4611686018427387904"
  ```
* Canal operativo, detallado: esto incluye informes de estado y decisiones de equilibrio de carga, además de todos los elementos de canal operativo básico. Estos eventos son generados por el sistema o por el código mediante las API de generación de informes de estado de mantenimiento o carga, como [ReportPartitionHealth](https://msdn.microsoft.com/library/azure/system.fabric.iservicepartition.reportpartitionhealth.aspx) o [ReportLoad](https://msdn.microsoft.com/library/azure/system.fabric.iservicepartition.reportload.aspx). Para ver estos eventos en el Visor de eventos de diagnóstico de Visual Studio, agregue "Microsoft-ServiceFabric:4:0x4000000000000008" a la lista de proveedores de ETW.

```json
      scheduledTransferKeywordFilter: "4611686018427387912"
  ```

* Canal de datos y mensajería, básico: registros y eventos críticos generados en la ruta de acceso a mensajería (actualmente solo ReverseProxy) y a datos, además de los registros del canal operativo detallado. Estos eventos son errores de procesamiento de solicitudes y otros problemas críticos en ReverseProxy y en las solicitudes procesadas. **Esta es nuestra recomendación para el registro completo**. Para ver estos eventos en el Visor de eventos de diagnóstico de Visual Studio, agregue "Microsoft-ServiceFabric:4:0x4000000000000010" a la lista de proveedores de ETW.

```json
      scheduledTransferKeywordFilter: "4611686018427387928"
  ```

* Canal de datos y mensajería, detallado: canal detallado que contiene todos los registros no críticos de datos y mensajería del clúster, y el canal operativo detallado. Para obtener información sobre solución de problemas de todos los eventos de proxy inverso, consulte la [Guía de diagnóstico de proxy inverso](service-fabric-reverse-proxy-diagnostics.md).  Para ver estos eventos en el Visor de eventos de diagnóstico de Visual Studio, agregue "Microsoft-ServiceFabric:4:0x4000000000000020" a la lista de proveedores de ETW.

```json
      scheduledTransferKeywordFilter: "4611686018427387944"
  ```

>[!NOTE]
>Este canal tiene un volumen muy grande de eventos; permitir la recopilación desde este canal detallado provoca la generación rápida de muchos seguimientos, lo que puede consumir capacidad de almacenamiento. Solo active esta opción si es absolutamente necesario.


Para habilitar el **Canal de datos y mensajería básico**, nuestra recomendación para el registro completo, `EtwManifestProviderConfiguration` en `WadCfg` de la plantilla se vería similar al siguiente:

```json
  "WadCfg": {
        "DiagnosticMonitorConfiguration": {
          "overallQuotaInMB": "50000",
          "EtwProviders": {
            "EtwEventSourceProviderConfiguration": [
              {
                "provider": "Microsoft-ServiceFabric-Actors",
                "scheduledTransferKeywordFilter": "1",
                "scheduledTransferPeriod": "PT5M",
                "DefaultEvents": {
                  "eventDestination": "ServiceFabricReliableActorEventTable"
                }
              },
              {
                "provider": "Microsoft-ServiceFabric-Services",
                "scheduledTransferPeriod": "PT5M",
                "DefaultEvents": {
                  "eventDestination": "ServiceFabricReliableServiceEventTable"
                }
              }
            ],
            "EtwManifestProviderConfiguration": [
              {
                "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
                "scheduledTransferLogLevelFilter": "Information",
                "scheduledTransferKeywordFilter": "4611686018427387928",
                "scheduledTransferPeriod": "PT5M",
                "DefaultEvents": {
                  "eventDestination": "ServiceFabricSystemEventTable"
                }
              }
            ]
          }
        }
      },
```

## <a name="collect-from-new-eventsource-channels"></a>Recopilar desde canales EventSource nuevos

Para actualizar Diagnostics de forma que recopile registros de canales EventSource nuevos que representan una nueva aplicación que vaya a implementar, siga los mismos pasos que se han descrito anteriormente para configurar Diagnostics para un clúster existente.

Actualice la sección `EtwEventSourceProviderConfiguration` del archivo template.json para agregar entradas para los canales EventSource nuevos antes de aplicar la actualización de la configuración mediante el comando `New-AzureRmResourceGroupDeployment` de PowerShell. El nombre del origen del evento se define como parte del código en el archivo generado en Visual Studio ServiceEventSource.cs.

Por ejemplo, si el origen del evento se denomina My-Eventsource, agregue el código siguiente para colocar los eventos de My-Eventsource en una tabla denominada MyDestinationTableName.

```json
        {
            "provider": "My-Eventsource",
            "scheduledTransferPeriod": "PT5M",
            "DefaultEvents": {
            "eventDestination": "MyDestinationTableName"
            }
        }
```

Para recopilar registros de eventos o contadores de rendimiento, modifique la plantilla de Resource Manager con los ejemplos proporcionados en [Creación de una máquina virtual de Windows con supervisión y diagnóstico mediante una plantilla de Azure Resource Manager](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Luego, vuelva a publicar la plantilla de Resource Manager.

## <a name="collect-performance-counters"></a>Recopilar contadores de rendimiento

Para recopilar métricas de rendimiento del clúster, agregue los contadores de rendimiento a "WadCfg > DiagnosticMonitorConfiguration" en la plantilla de Resource Manager para el clúster. Vea [Supervisión del rendimiento con WAD](service-fabric-diagnostics-perf-wad.md) para conocer los pasos acerca de cómo modificar `WadCfg` para recopilar contadores de rendimiento específicos. Consulte en [Service Fabric Performance Counters](service-fabric-diagnostics-event-generation-perf.md) (Contadores de rendimiento de Service Fabric) una lista de contadores de rendimiento que se recomienda recopilar.
  
Si usa un receptor de Application Insights, como se describe en la siguiente sección, y quiere que estas métricas aparezcan en Application Insights, asegúrese de agregar el nombre del receptor en la sección "receptores", como se indicó anteriormente. Esto enviará automáticamente los contadores de rendimiento que se configuran individualmente para el recurso de Application Insights.


## <a name="send-logs-to-application-insights"></a>Enviar registros a Application Insights

Como parte de la configuración de WAD, puede enviar datos de supervisión y diagnóstico a Application Insights (AI). Si decide usar AI para el análisis y la visualización de eventos, lea [Event Analysis and Visualization with Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md) (Análisis y visualización de eventos con Application Insights) para configurar un receptor de AI como parte de "WadCfg".

## <a name="next-steps"></a>Pasos siguientes

Una vez que haya configurado correctamente Azure Diagnostics, verá los datos en las tablas de almacenamiento de los registros de ETW y EventSource. Si decide usar Log Analytics, Kibana u otra plataforma de análisis y visualización de datos que no se configura directamente en la plantilla de Resource Manager, asegúrese de configurar la plataforma de su elección para leer los datos de estas tablas de almacenamiento. Es relativamente fácil hacerlo para Log Analytics, como se explica en [Event and log analysis](service-fabric-diagnostics-event-analysis-oms.md) (Análisis de eventos y registro). Application Insights es un caso especial en este sentido, ya que puede configurarse como parte de la configuración de la extensión de Diagnostics, por lo que debe leer el [artículo correspondiente](service-fabric-diagnostics-event-analysis-appinsights.md) si opta por usar AI.

>[!NOTE]
>Actualmente no existe ninguna manera de filtrar o limpiar los eventos que se envían a la tabla. Si no se implementa un proceso para quitar eventos de la tabla, la tabla seguirá aumentando. Actualmente, hay un ejemplo de un servicio de limpieza de datos en ejecución en el [ejemplo de guardián](https://github.com/Azure-Samples/service-fabric-watchdog-service). Se recomienda que escriba uno para sí mismo, a menos que tenga una buena razón para almacenar los registros durante más de 30 o 90 días.

* [Información sobre cómo recopilar registros o contadores de rendimiento mediante la extensión de Diagnósticos](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Análisis y visualización de eventos con Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md)
* [Análisis y visualización de eventos con Log Analytics](service-fabric-diagnostics-event-analysis-oms.md)
