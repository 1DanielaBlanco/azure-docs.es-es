---
title: Supervisión del mantenimiento de Azure Kubernetes Service (AKS) (versión preliminar) | Microsoft Docs
description: En este artículo se describe cómo puede revisar fácilmente el rendimiento del contenedor de AKS para comprender rápidamente el uso de su entorno hospedado de Kubernetes.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/16/2018
ms.author: magoedte
ms.openlocfilehash: 1fd5ac0f9994a4dbf4365c21ac4f31ba0eccbb15
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/17/2018
ms.locfileid: "39069158"
---
# <a name="monitor-azure-kubernetes-service-aks-container-health-preview"></a>Supervisión del mantenimiento de contenedores de Azure Kubernetes Service (AKS) (versión preliminar)

En este artículo se describe cómo configurar y usar la solución de mantenimiento de contenedores de Azure Monitor para supervisar el rendimiento de las cargas de trabajo implementadas en entornos de Kubernetes hospedados en Azure Kubernetes Service (AKS).  La supervisión de su clúster de Kubernetes y de los contenedores es fundamental, sobre todo al ejecutar un clúster de producción, a escala, con varias aplicaciones.

La solución de mantenimiento de contenedores le ofrece posibilidad de supervisar el rendimiento mediante la recopilación de métricas de procesador y memoria de controladores, nodos y contenedores disponibles en Kubernetes mediante Métrica de API.  Después de habilitar el mantenimiento de contenedores, estas métricas se recopilan automáticamente mediante una versión en contenedor del Agente de OMS para Linux y se almacenan en su área de trabajo de [Log Analytics](../log-analytics/log-analytics-overview.md).  Las vistas predefinidas incluidas muestran las cargas residentes en el contenedor y lo que afecta al mantenimiento del rendimiento del clúster Kubernetes a fin de que pueda:  

* Comprender qué contenedores se ejecutan en el nodo y su uso medio de procesador y memoria para identificar los cuellos de botella en los recursos
* Identificar donde reside el contenedor en un controlador o pod para ver el rendimiento global de un controlador o pod 
* Revisar el uso de recursos de las cargas de trabajo que se ejecutan en el host no relacionadas con los procesos estándar que admite el pod
* Comprender el comportamiento del clúster bajo cargas medias y más pesadas para ayudar a identificar las necesidades de capacidad y determinar la carga máxima que puede sostener 

Si está interesado en la supervisión y la administración de hosts de contenedor de Docker y Windows y desea más información sobre la configuración, la auditoría y la utilización de recursos, consulte la [solución de contenedor](../log-analytics/log-analytics-containers.md).

## <a name="requirements"></a>Requisitos 
Antes de comenzar, revise los detalles siguientes a fin de entender los requisitos previos admitidos.

- Un clúster AKS nuevo o existente
- Un agente OMS en contenedor para la versión de Linux microsoft / oms:ciprod04202018 y posterior. El número de versión se representa mediante una fecha con el formato siguiente: *mmddaaaa*.  Se instala automáticamente durante la incorporación de la solución de mantenimiento de contenedores.  
- Un área de trabajo de Log Analytics.  Se puede crear al habilitar la supervisión del nuevo clúster de AKS, o puede crear una mediante [Azure Resource Manager](../log-analytics/log-analytics-template-workspace-configuration.md), [PowerShell](https://docs.microsoft.com/azure/log-analytics/scripts/log-analytics-powershell-sample-create-workspace?toc=%2fpowershell%2fmodule%2ftoc.json), o desde [Azure Portal](../log-analytics/log-analytics-quick-create-workspace.md).
- Miembro del rol de colaborador de Log Analytics para habilitar la supervisión de contenedores.  Para más información sobre cómo controlar el acceso a un área de trabajo de Log Analytics, consulte [Administración de áreas de trabajo](../log-analytics/log-analytics-manage-access.md).

## <a name="components"></a>Componentes 

Esta funcionalidad se basa en un Agente de OMS para Linux en contenedor para recopilar datos de rendimiento y eventos de todos los nodos del clúster.  El agente se implementa y registra automáticamente con el área de trabajo de Log Analytics especificada después de habilitar la supervisión del contenedor. 

>[!NOTE] 
>Si ya ha implementado un clúster de AKS, puede habilitar la supervisión mediante una plantilla de Azure Resource Manager proporcionada, como se muestra más adelante en este artículo. No puede usar `kubectl` para actualizar, eliminar, volver a implementar o implementar el agente.  
>

## <a name="sign-in-to-azure-portal"></a>Inicio de sesión en Azure Portal
Inicie sesión en Azure Portal en [https://portal.azure.com](https://portal.azure.com). 

## <a name="enable-container-health-monitoring-for-a-new-cluster"></a>Habilitación de la supervisión del mantenimiento de contenedores para un nuevo clúster
Puede habilitar la supervisión de un nuevo clúster de AKS durante la implementación desde Azure Portal.  Siga los pasos descritos en el artículo de inicio rápido [Implementación de un clúster de Azure Container Service (AKS)](../aks/kubernetes-walkthrough-portal.md).  Cuando se encuentre en la página **Supervisión**, seleccione **Sí** en la opción **Habilitar supervisión** para habilitarla y, a continuación, seleccione un área de trabajo de Log Analytics existente o cree una nueva.  

Después de habilitar la supervisión, todas las tareas de configuración se completan correctamente y puede supervisar el rendimiento de su clúster de dos maneras:

1. Directamente desde el clúster de AKS mediante la selección de **Health** (Mantenimiento) en el panel izquierdo.<br><br> 
2. Haciendo clic en el icono **Monitor container health** (Supervisar mantenimiento de contenedores) en la página del clúster de AKS correspondiente al clúster seleccionado.  En Azure Monitor, seleccione **Health** (Mantenimiento) en el panel izquierdo.  

![Opciones para seleccionar el mantenimiento de contenedores en AKS](./media/monitoring-container-health/container-performance-and-health-select-01.png)

Después de habilitar la supervisión, pueden transcurrir unos 15 minutos antes de que pueda ver los datos de funcionamiento del clúster.  

## <a name="enable-container-health-monitoring-for-existing-managed-clusters"></a>Habilitación de la supervisión del mantenimiento de contenedores para clústeres administrados existentes
Puede habilitar la supervisión de un clúster de AKS ya implementado desde Azure Portal o mediante la plantilla de Azure Resource Manager proporcionada mediante el cmdlet **New-AzureRmResourceGroupDeployment** de PowerShell o la CLI de Azure.  


### <a name="enable-from-azure-portal"></a>Habilitar desde Azure Portal
Realice los pasos siguientes para habilitar la supervisión del contenedor de AKS desde Azure Portal.

1. En Azure Portal, haga clic en **Todos los servicios**. En la lista de recursos, escriba **Containers**. Cuando comience a escribir, la lista se filtrará en función de la entrada. Seleccione **servicios de Kubernetes**.<br><br> ![Azure Portal](./media/monitoring-container-health/azure-portal-01.png)<br><br>  
2. En la lista de contenedores, seleccione un contenedor.
3. En la página de información general del contenedor, seleccione **Supervisar estado del contenedor** y aparecerá la página **Incorporación a los registros y al estado del contenedor**.
4. En la página **Incorporación a los registros y al estado del contenedor**, si tiene un área de trabajo de Log Analytics ya existente en la misma suscripción que el clúster, selecciónela de la lista desplegable.  La lista preselecciona el área de trabajo y la ubicación predeterminadas en las que se implementa el contenedor de AKS en la suscripción.<br><br> ![Habilitar la supervisión de estado del contenedor de AKS](./media/monitoring-container-health/container-health-enable-brownfield-02.png) 

>[!NOTE]
>Si quiere crear una nueva área de trabajo de Log Analytics para almacenar los datos de supervisión del clúster, siga los pasos que se describen en [Creación de un área de trabajo de Log Analytics en Azure Portal](../log-analytics/log-analytics-quick-create-workspace.md) y asegúrese de crear el área de trabajo en la misma suscripción en que está implementado el contenedor de AKS.  
>
 
Después de habilitar la supervisión, pueden transcurrir unos 15 minutos antes de que pueda ver los datos de funcionamiento del clúster. 

### <a name="enable-using-azure-resource-manager-template"></a>Habilitar el uso de la plantilla de Azure Resource Manager
Este método incluye dos plantillas JSON, una plantilla especifica la configuración para habilitar la supervisión y la otra plantilla JSON contiene valores de parámetros que se configuran para especificar lo siguiente:

* Identificador de recurso de contenedor de AKS 
* El grupo de recursos en el que se implementa el clúster 
* El área de trabajo de Log Analytics y la región en la que se crea el área de trabajo 

El área de trabajo de Log Analytics se debe crear manualmente.  Para crear el área de trabajo, puede establecer una mediante [Azure Resource Manager](../log-analytics/log-analytics-template-workspace-configuration.md), [PowerShell](https://docs.microsoft.com/azure/log-analytics/scripts/log-analytics-powershell-sample-create-workspace?toc=%2fpowershell%2fmodule%2ftoc.json) o [Azure Portal](../log-analytics/log-analytics-quick-create-workspace.md).

Si no está familiarizado con los conceptos de implementar recursos mediante una plantilla con PowerShell, consulte [Implementación de recursos con las plantillas de Resource Manager y Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md) o, si usa la CLI de Azure, consulte [Implementación de recursos con plantillas de Resource Manager y la CLI de Azure](../azure-resource-manager/resource-group-template-deploy-cli.md).

Si decide usar la CLI de Azure, primero debe instalar y usar la CLI localmente.  Es necesario que ejecute la versión 2.0.27 o posterior de la CLI de Azure. Ejecute `az --version` para identificar la versión. Si necesita instalarla o actualizarla, vea [Instalación de la CLI de Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). 

#### <a name="create-and-execute-template"></a>Creación y ejecución de la plantilla

1. Copie y pegue la siguiente sintaxis JSON en el archivo:

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "aksResourceId": {
        "type": "string",
        "metadata": {
           "description": "AKS Cluster Resource ID"
           }
    },
    "aksResourceLocation": {
    "type": "string",
     "metadata": {
        "description": "Location of the AKS resource e.g. \"East US\""
       }
    },
    "workspaceResourceId": {
      "type": "string",
      "metadata": {
         "description": "Azure Monitor Log Analytics Resource ID"
       }
    },
    "workspaceRegion": {
    "type": "string",
    "metadata": {
       "description": "Azure Monitor Log Analytics workspace region"
      }
     }
    },
    "resources": [
      {
    "name": "[split(parameters('aksResourceId'),'/')[8]]",
    "type": "Microsoft.ContainerService/managedClusters",
    "location": "[parameters('aksResourceLocation')]",
    "apiVersion": "2018-03-31",
    "properties": {
      "mode": "Incremental",
      "id": "[parameters('aksResourceId')]",
      "addonProfiles": {
        "omsagent": {
          "enabled": true,
          "config": {
            "logAnalyticsWorkspaceResourceID": "[parameters('workspaceResourceId')]"
          }
         }
       }
      }
     },
    {
        "type": "Microsoft.Resources/deployments",
        "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
        "apiVersion": "2017-05-10",
        "subscriptionId": "[split(parameters('workspaceResourceId'),'/')[2]]",
        "resourceGroup": "[split(parameters('workspaceResourceId'),'/')[4]]",
        "properties": {
            "mode": "Incremental",
            "template": {
                "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                "contentVersion": "1.0.0.0",
                "parameters": {},
                "variables": {},
                "resources": [
                    {
                        "apiVersion": "2015-11-01-preview",
                        "type": "Microsoft.OperationsManagement/solutions",
                        "location": "[parameters('workspaceRegion')]",
                        "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
                        "properties": {
                            "workspaceResourceId": "[parameters('workspaceResourceId')]"
                        },
                        "plan": {
                            "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
                            "product": "[Concat('OMSGallery/', 'ContainerInsights')]",
                            "promotionCode": "",
                            "publisher": "Microsoft"
                        }
                    }
                ]
            },
            "parameters": {}
        }
       }
     ]
    }
    ```

2. Guarde este archivo como **existingClusterOnboarding.json** en una carpeta local.
3. Copie y pegue la siguiente sintaxis JSON en el archivo:

    ```json
    {
       "$schema": "https://schema.management.azure.com/  schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
         "aksResourceId": {
           "value": "/subscriptions/<SubscroptiopnId>/resourcegroups/<ResourceGroup>/providers/Microsoft.ContainerService/managedClusters/<ResourceName>"
       },
       "aksResourceLocation": {
         "value": "East US"
       },
       "workspaceResourceId": {
         "value": "/subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroup>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>"
       },
       "workspaceRegion": {
         "value": "eastus"
       }
     }
    }
    ```

4. Reemplace el valor de **aksResourceId** y **aksResourceLocation** con los valores que puede encontrar en la página de **introducción a AKS** del clúster de AKS.  El valor de **workspaceResourceId** es el identificador de recurso completo del área de trabajo de Log Analytics e incluye el nombre del área de trabajo.  Especifique también la ubicación en la que está el área de trabajo en **workspaceRegion**.    
5. Guarde este archivo como **existingClusterParam.json** en una carpeta local.
6. Está listo para implementar esta plantilla. 

    * Use los siguientes comandos de PowerShell desde la carpeta que contenga la plantilla:

        ```powershell
        New-AzureRmResourceGroupDeployment -Name OnboardCluster -ResourceGroupName ClusterResourceGroupName -TemplateFile .\existingClusterOnboarding.json -TemplateParameterFile .\existingClusterParam.json
        ```
        El cambio de configuración puede tardar unos minutos en completarse. Cuando termine, verá un mensaje similar al siguiente que incluye el resultado:

        ```powershell
        provisioningState       : Succeeded
        ```

    * Para ejecutar el comando siguiente con la CLI de Azure en Linux:
    
        ```azurecli
        az login
        az account set --subscription "Subscription Name"
        az group deployment create --resource-group <ResourceGroupName> --template-file ./existingClusterOnboarding.json --parameters @./existingClusterParam.json
        ```

        El cambio de configuración puede tardar unos minutos en completarse. Cuando termine, verá un mensaje similar al siguiente que incluye el resultado:

        ```azurecli
        provisioningState       : Succeeded
        ```
Después de habilitar la supervisión, pueden transcurrir unos 15 minutos antes de que pueda ver los datos de funcionamiento del clúster.  

## <a name="verify-agent-and-solution-deployment"></a>Comprobar la implementación del agente y la solución
Con la versión del agente *06072018* o posterior, se puede comprobar que tanto el agente como la solución se implementaron correctamente.  Con versiones anteriores del agente, solo se puede comprobar la implementación del agente.

### <a name="agent-version-06072018-and-higher"></a>Versión del agente 06072018 o posterior
Ejecute el siguiente comando para comprobar que el agente se ha implementado correctamente.   

```
kubectl get ds omsagent --namespace=kube-system
```

La salida debería ser similar a la siguiente que indica que se implementó correctamente:

```
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

Para comprobar la implementación de la solución, ejecute el siguiente comando:

```
kubectl get deployment omsagent-rs -n=kube-system
```

La salida debería ser similar a la siguiente que indica que se implementó correctamente:

```
User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system 
NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
omsagent   1         1         1            1            3h
```

### <a name="agent-version-earlier-than-06072018"></a>Versión del agente anterior a 06072018

Para comprobar que una versión de agente de OMS anterior a *06072018* se implementó correctamente, ejecute el comando siguiente:  

```
kubectl get ds omsagent --namespace=kube-system
```

La salida debería ser similar a la siguiente que indica que se implementó correctamente:  

```
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

## <a name="view-performance-utilization"></a>Ver el uso del rendimiento
Cuando se abre el mantenimiento de contenedores, la página presenta inmediatamente el uso del rendimiento de todo el clúster.  La vista de información sobre el clúster de AKS está organizada en cuatro perspectivas:

- Clúster
- Nodos 
- Controladores  
- Contenedores

En la pestaña Clúster, los gráficos de rendimiento de línea muestran las métricas de rendimiento clave del clúster.  

![Gráficos de rendimiento de ejemplo de la pestaña Clúster](./media/monitoring-container-health/container-health-cluster-perfview.png)

A continuación se incluye un desglose de las métricas de rendimiento presentadas:

- Node CPU Utilization % (% de uso de CPU de nodo): este gráfico representa una perspectiva agregada del uso de la CPU para todo el clúster.  Puede filtrar los resultados para el intervalo de tiempo seleccionando *Avg (Promedio)*, *Min (Mínimo)*, *Max (Máximo)* , *50th (50)*, *90th (90)* y *95th (95)* desde el selector de percentiles situado encima del gráfico, ya sea individualmente o de forma combinada. 
- Node memory utilization % (% de uso de memoria de nodo): este gráfico representa una perspectiva agregada del uso de memoria para todo el clúster.  Puede filtrar los resultados para el intervalo de tiempo seleccionando *Avg (Promedio)*, *Min (Mínimo)*, *Max (Máximo)* , *50th (50)*, *90th (90)* y *95th (95)* desde el selector de percentiles situado encima del gráfico, ya sea individualmente o de forma combinada. 
- Número de nodos: este gráfico representa el número de nodos y el estado de Kubernetes.  Los estados de los nodos de clúster representados son *Todo*, *Listo* y  *	No está listo* y se pueden filtrar de forma individual o combinada desde el selector situado encima del gráfico.    
- Activity pod count (Recuento de pods de actividad):	este gráfico representa el recuento de pods y el estado de Kubernetes.  Los estados de los pods representados son *Todo*, *Pendientes*, *En ejecución* y *Desconocidos* y se pueden filtrar de forma individual o combinada desde el selector situado encima del gráfico.  

Al cambiar a la pestaña Nodos, la jerarquía de filas sigue el modelo de objetos de Kubernetes a partir de un nodo del clúster.  Expanda el nodo y verá que hay uno o más pods que se ejecutan en el nodo; si hay más de un contenedor agrupado en un pod, se muestran como la última fila de la jerarquía. También puede ver cuántas cargas de trabajo no relacionadas con pods se están ejecutando en el host, en caso de que el host tenga presión de memoria o procesador.

![Ejemplo de jerarquía de nodos de Kubernetes en la vista de rendimiento](./media/monitoring-container-health/container-health-nodes-view.png)

Puede seleccionar controladores o contenedores en la parte superior de la página y revisar el estado y el uso de recursos de esos objetos.  Use los cuadros de lista desplegable de la parte superior de la pantalla para filtrar por espacio de nombres, servicio y nodo. Si en su lugar quiere revisar el uso de memoria, en la lista desplegable **Metric** (Métrica), seleccione **Memory RSS** (Memoria RSS) o **Memory working set** (Conjunto de trabajo de memoria).  La **memoria RSS** solo se admite para Kubernetes versión 1.8 y posterior. En caso contrario, verá los valores de **MIN %** que se muestran como *NaN%*, que es un valor de tipo de datos numérico que representa un valor no definido o no representable. 

![Vista de rendimiento de los nodos del contenedor](./media/monitoring-container-health/container-health-node-metric-dropdown.png)

De forma predeterminada, los datos de rendimiento se basan en las últimas seis horas, pero puede cambiar la ventana la lista desplegable **Time Range** (Intervalo de tiempo) que se encuentra en la esquina superior derecha de la página. En este momento, la página no se actualiza automáticamente, sino que debe hacerlo manualmente. También puede filtrar los resultados del intervalo de tiempo seleccionando *Avg (Promedio)*, *Min (Mínimo)*, *Max (Máximo)* , *50th (50)*, *90th (90)* y *95th (95)* desde el selector de percentiles. 

![Selección de percentil para el filtrado de datos](./media/monitoring-container-health/container-health-metric-percentile-filter.png)

En el ejemplo siguiente, se observa que, en el nodo *aks-nodepool-3977305*, el valor de **Containers** (Contenedores) es 5, que es un resumen del número total de contenedores implementados.

![Ejemplo de acumulación de contenedores por nodo](./media/monitoring-container-health/container-health-nodes-containerstotal.png)

Este resumen puede ayudarle a identificar rápidamente si no tiene el equilibrio adecuado de contenedores entre los nodos del clúster.  

En la tabla siguiente se describe la información presentada al ver los nodos.

| Columna | DESCRIPCIÓN | 
|--------|-------------|
| NOMBRE | El nombre del host |
| Status | Vista de Kubernetes del estado del nodo |
| AVG% (% PROMEDIO), MIN% (% MÍNIMO), MAX% (% MÁXIMO), 50TH% (50 %), 90TH% (90 %) | Porcentaje medio de nodos basado en el percentil durante la duración seleccionada. |
| AVG (PROMEDIO), MIN (MÍNIMO), MAX (MÁXIMO), 50TH (50), 90TH (90) | Valor real de promedio de nodos basado en el percentil para la duración seleccionada.  El valor medio se mide desde el límite de CPU/memoria establecido para un nodo; para los pods y los contenedores es el valor medio notificado por el host. |
| Contenedores | Número de contenedores. |
| Tiempo de actividad | Representa la hora desde un nodo iniciado o que se reinició. |
| Controladores | Solo para los contenedores y pods. Muestra el controlador en el que reside. No todos los pods están en un controlador, así que algunos pueden mostrar N/D. | 
| Trend AVG% (Tendencia de % PROMEDIO), MIN% (% MÍNIMO), MAX% (% MÁXIMO), 50TH% (50 %), 90TH% (90 %) | Tendencia de gráfico de barras que presenta el % de métricas de percentil del controlador. |


En el selector, elija **Controllers** (Controladores).

![Selección de la vista de controladores](./media/monitoring-container-health/container-health-controllers-tab.png)

Aquí puede ver el mantenimiento del rendimiento de los controladores.

![Vista de rendimiento de los controladores <nombre>](./media/monitoring-container-health/container-health-controllers-view.png)

La jerarquía de filas comienza con un controlador y expande el controlador, y verá uno o varios contenedores.  Expanda un pod y verá la última fila que muestra el contenedor agrupado en el pod.  

En la tabla siguiente se describe la información presentada al ver los controladores.

| Columna | DESCRIPCIÓN | 
|--------|-------------|
| NOMBRE | El nombre del controlador|
| Status | Estado de acumulación de los contenedores cuando ha terminado de ejecutarse con un estado, como *OK (Correcto)*, *Terminated (Finalizado)*, *Failed (Error)* *Stopped (Detenido)* o *Paused (En pausa)*. Si el contenedor se está ejecutando, pero el estado no se presentó correctamente o el agente no lo seleccionó y no ha respondido durante más de 30 minutos, el estado es *Unknown* (Desconocido). En la tabla siguiente se proporcionan detalles adicionales del icono de estado.|
| AVG% (% PROMEDIO), MIN% (% MÍNIMO), MAX% (% MÁXIMO), 50TH% (50 %), 90TH% (90 %) | Promedio acumulado del porcentaje medio de cada entidad para la métrica y el percentil seleccionados. |
| AVG (PROMEDIO), MIN (MÍNIMO), MAX (MÁXIMO), 50TH (50), 90TH (90)  | Acumulación del rendimiento medio de memoria o CPU de millares de núcleos del contenedor para el percentil seleccionado.  El valor medio se mide a partir del límite de CPU/memoria establecido para un pod. |
| Contenedores | Número total de contenedores para el controlador o pod. |
| Reinicios | Resumen del número de reinicios desde los contenedores. |
| Tiempo de actividad | Representa el tiempo desde que se inició un contenedor. |
| Nodo | Solo para los contenedores y pods. Muestra el controlador en el que reside. | 
| Trend AVG% (Tendencia de % PROMEDIO), MIN% (% MÍNIMO), MAX% (% MÁXIMO), 50TH% (50 %), 90TH% (90 %)| Tendencia de gráfico de barras que representa las métricas de percentil del controlador. |

Los iconos del campo de estado indican el estado de conexión de los contenedores:
 
| Icono | Status | 
|--------|-------------|
| ![Icono de estado de ejecución listo](./media/monitoring-container-health/container-health-ready-icon.png) | En ejecución (Listo)|
| ![Icono de estado de espera o en pausa](./media/monitoring-container-health/container-health-waiting-icon.png) | En pausa o en espera|
| ![Icono de último estado de ejecución notificado](./media/monitoring-container-health/container-health-grey-icon.png) | Última notificación: en ejecución (pero no ha respondido en más de 30 minutos)|
| ![Icono de estado finalizado](./media/monitoring-container-health/container-health-green-icon.png) | Se detuvo correctamente o no se pudo detener|

El icono de estado muestra un número en función de lo que proporciona el pod. Muestra los dos peores estados y, al pasar el ratón sobre el estado, muestra un estado de acumulación de todos los pods del contenedor.  Si no hay un estado listo, el valor de estado mostrará un **(0)**.  

En el selector, elija **Containers** (Contenedores).

![Selección de la vista de contenedores](./media/monitoring-container-health/container-health-containers-tab.png)

Aquí podemos ver el mantenimiento del rendimiento de los contenedores.

![Vista de rendimiento de los controladores <nombre>](./media/monitoring-container-health/container-health-containers-view.png)

En la tabla siguiente se describe la información presentada en la vista de contenedores.

| Columna | DESCRIPCIÓN | 
|--------|-------------|
| NOMBRE | El nombre del controlador|
| Status | Estado de los contenedores, si lo hay. En la tabla siguiente se proporcionan detalles adicionales del icono de estado.|
| AVG% (% PROMEDIO), MIN% (% MÍNIMO), MAX% (% MÁXIMO), 50TH% (50 %), 90TH% (90 %) | Promedio acumulado del porcentaje medio de cada entidad para la métrica y el percentil seleccionados. |
| AVG (PROMEDIO), MIN (MÍNIMO), MAX (MÁXIMO), 50TH (50), 90TH (90)  | Acumulación del rendimiento medio de memoria o CPU de millares de núcleos del contenedor para el percentil seleccionado.  El valor medio se mide a partir del límite de CPU/memoria establecido para un pod. |
| Pod | Contenedor en el que reside el pod.| 
| Nodo |  Nodo donde reside el contenedor. | 
| Reinicios | Representa el tiempo desde que se inició un contenedor. |
| Tiempo de actividad | Representa el tiempo desde que se inicia o reinicia un contenedor. |
| Trend AVG% (Tendencia de % PROMEDIO), MIN% (% MÍNIMO), MAX% (% MÁXIMO), 50TH% (50 %), 90TH% (90 %) | Tendencia de gráfico de barras que representa el % medio de métrica del contenedor. |

Los iconos del campo de estado indican el estado de conexión de los pods:
 
| Icono | Status | 
|--------|-------------|
| ![Icono de estado de ejecución listo](./media/monitoring-container-health/container-health-ready-icon.png) | En ejecución (Listo)|
| ![Icono de estado de espera o en pausa](./media/monitoring-container-health/container-health-waiting-icon.png) | En pausa o en espera|
| ![Icono de último estado de ejecución notificado](./media/monitoring-container-health/container-health-grey-icon.png) | Última notificación: en ejecución (pero no ha respondido en más de 30 minutos)|
| ![Icono de estado finalizado](./media/monitoring-container-health/container-health-terminated-icon.png) | Se detuvo correctamente o no se pudo detener|
| ![Icono de estado de error](./media/monitoring-container-health/container-health-failed-icon.png) | Estado de error |

## <a name="container-data-collection-details"></a>Información detallada sobre la recopilación de datos en contenedores
La solución de mantenimiento de contenedores recopila diversas métricas de rendimiento y datos de registro de los hosts de contenedor y los contenedores. Los datos se recopilan cada tres minutos.

### <a name="container-records"></a>Registros de contenedor

En la tabla siguiente se muestran ejemplos de registros recopilados por la solución de mantenimiento de contenedores y los tipos de datos que aparecen en los resultados de las búsquedas de registros.

| Tipo de datos | Tipo de datos en la búsqueda de registros | Fields |
| --- | --- | --- |
| Rendimiento de hosts y contenedores | `Perf` | Computer, ObjectName, CounterName &#40;%Processor Time, Disk Reads MB, Disk Writes MB, Memory Usage MB, Network Receive Bytes, Network Send Bytes, Processor Usage sec, Network&#41;, CounterValue, TimeGenerated, CounterPath, SourceSystem |
| Inventario de contenedor | `ContainerInventory` | TimeGenerated, Computer, container name, ContainerHostname, Image, ImageTag, ContainerState, ExitCode, EnvironmentVar, Command, CreatedTime, StartedTime, FinishedTime, SourceSystem, ContainerID, ImageID |
| Inventario de imágenes de contenedor | `ContainerImageInventory` | TimeGenerated, Computer, Image, ImageTag, ImageSize, VirtualSize, Running, Paused, Stopped, Failed, SourceSystem, ImageID, TotalContainer |
| Registro de contenedor | `ContainerLog` | TimeGenerated, Computer, image ID, container name, LogEntrySource, LogEntry, SourceSystem, ContainerID |
| Registro del servicio de contenedores | `ContainerServiceLog`  | TimeGenerated, Computer, TimeOfCommand, Image, Command, SourceSystem, ContainerID |
| Inventario de nodo de contenedor | `ContainerNodeInventory_CL`| TimeGenerated, Computer, ClassName_s, DockerVersion_s, OperatingSystem_s, Volume_s, Network_s, NodeRole_s, OrchestratorType_s, InstanceID_g, SourceSystem|
| Proceso del contenedor | `ContainerProcess_CL` | TimeGenerated, Computer, Pod_s, Namespace_s, ClassName_s, InstanceID_s, Uid_s, PID_s, PPID_s, C_s, STIME_s, Tty_s, TIME_s, Cmd_s, Id_s, Name_s, SourceSystem |
| Inventario de pods en un clúster de Kubernetes | `KubePodInventory` | TimeGenerated, Computer, ClusterId, ContainerCreationTimeStamp, PodUid, PodCreationTimeStamp, ContainerRestartCount, PodRestartCount, PodStartTime, ContainerStartTime, ServiceName, ControllerKind, ControllerName, ContainerStatus, ContainerID, ContainerName, Name, PodLabel, Namespace, PodStatus, ClusterName, PodIp, SourceSystem |
| Inventario de la parte de nodos de un clúster de Kubernetes | `KubeNodeInventory` | TimeGenerated, Computer, ClusterName, ClusterId, LastTransitionTimeReady, Labels, Status, KubeletVersion, KubeProxyVersion, CreationTimeStamp, SourceSystem | 
| Eventos de Kubernetes | `KubeEvents_CL` | TimeGenerated, Computer, ClusterId_s, FirstSeen_t, LastSeen_t, Count_d, ObjectKind_s, Namespace_s, Name_s, Reason_s, Type_s, TimeGenerated_s, SourceComponent_s, ClusterName_s, Message, SourceSystem | 
| Servicios en el clúster de Kubernetes | `KubeServices_CL` | TimeGenerated, ServiceName_s, Namespace_s, SelectorLabels_s, ClusterId_s, ClusterName_s, ClusterIP_s, ServiceType_s, SourceSystem | 
| Métricas de rendimiento de la parte de nodos del clúster de Kubernetes | Perf &#124; donde ObjectName == “K8SNode” | Computer, ObjectName, CounterName &#40;cpuUsageNanoCores, , memoryWorkingSetBytes, memoryRssBytes, networkRxBytes, networkTxBytes, restartTimeEpoch, networkRxBytesPerSec, networkTxBytesPerSec, cpuAllocatableNanoCores, memoryAllocatableBytes, cpuCapacityNanoCores, memoryCapacityBytes&#41;,CounterValue, TimeGenerated, CounterPath, SourceSystem | 
| Métricas de rendimiento de la parte de contenedores del clúster de Kubernetes | Perf &#124; donde ObjectName == “K8SContainer” | CounterName &#40;cpuUsageNanoCores, memoryWorkingSetBytes, memoryRssBytes, restartTimeEpoch, cpuRequestNanoCores, memoryRequestBytes, cpuLimitNanoCores, memoryLimitBytes&#41;,CounterValue, TimeGenerated, CounterPath, SourceSystem | 

## <a name="search-logs-to-analyze-data"></a>Búsqueda de registros para analizar datos
Log Analytics puede ayudarle a buscar tendencias, diagnosticar cuellos de botellas, realizar previsiones o correlacionar datos, que pueden servirle para determinar si la configuración actual del clúster funciona óptimamente.  Las búsquedas de registros predefinidas se proporcionan para comenzar a usarlas inmediatamente o para personalizarlas con el fin de devolver la información de la forma deseada. 

Puede realizar una análisis interactivo de los datos en el área de trabajo mediante la selección de la opción **View Log** (Ver registro), disponible en el extremo derecho al expandir un controlador o contenedor.  La página **Log Search** (Búsqueda de registros) aparece junto encima de la página en la que estaba en el portal.

![Análisis de los datos en Log Analytics](./media/monitoring-container-health/container-health-view-logs.png)   

La salida de los registros de contenedor reenviada a Log Analytics es STDOUT y STDERR. Como la solución de mantenimiento de contenedores supervisa Azure Kubernetes Service (AKS), el sistema Kube no se recopila hoy-debido al gran volumen de datos generados.     

### <a name="example-log-search-queries"></a>Ejemplos de consultas de búsqueda de registros
Con frecuencia, resulta útil crear consultas a partir de un ejemplo o dos y luego modificarlas para adaptarlas a sus requisitos. Puede experimentar con las siguientes consultas de ejemplo para ayudarle a crear consultas más avanzadas.

| Consultar | DESCRIPCIÓN | 
|-------|-------------|
| ContainerInventory<br> &#124; project Computer, Name, Image, ImageTag, ContainerState, CreatedTime, StartedTime, FinishedTime<br> &#124; render table | Se muestra toda la información del ciclo de vida del contenedor| 
| KubeEvents_CL<br> &#124; where not(isempty(Namespace_s))<br> &#124; sort by TimeGenerated desc<br> &#124; render table | Eventos de Kubernetes|
| ContainerImageInventory<br> &#124; summarize AggregatedValue = count() by Image, ImageTag, Running | Inventario de imagen | 
| **En Análisis avanzado, seleccione gráficos de líneas**:<br> Perf<br> &#124; where ObjectName == "Container" and CounterName == "% Processor Time"<br> &#124; summarize AvgCPUPercent = avg(CounterValue) by bin(TimeGenerated, 30m), InstanceName | CPU de contenedor | 
| **En Análisis avanzado, seleccione gráficos de líneas**:<br> Perf &#124; where ObjectName == "Container" and CounterName == "Memory Usage MB"<br> &#124; summarize AvgUsedMemory = avg(CounterValue) by bin(TimeGenerated, 30m), InstanceName | Memoria de contenedor |

## <a name="how-to-stop-monitoring-with-container-health"></a>Cómo detener la supervisión con la solución de mantenimiento de contenedor
Después de habilitar la supervisión del contenedor de AKS que ha decidido que ya no quiere supervisar, puede *descartarlo* mediante las plantillas de Azure Resource Manager proporcionadas con el cmdlet **New-AzureRmResourceGroupDeployment** de PowerShell o la CLI de Azure.  Una plantilla JSON especifica la configuración para *descartar* y la otra plantilla JSON contiene valores de parámetros que configura para especificar el identificador de recursos del clúster de AKS y el grupo de recursos en el que se implementa el clúster.  Si no está familiarizado con los conceptos de implementar recursos mediante una plantilla con PowerShell, consulte [Implementación de recursos con las plantillas de Resource Manager y Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md) o, si usa la CLI de Azure, consulte [Implementación de recursos con plantillas de Resource Manager y la CLI de Azure](../azure-resource-manager/resource-group-template-deploy-cli.md).

Si decide usar la CLI de Azure, primero debe instalar y usar la CLI localmente.  Es necesario que ejecute la versión 2.0.27 o posterior de la CLI de Azure. Ejecute `az --version` para identificar la versión. Si necesita instalarla o actualizarla, vea [Instalación de la CLI de Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). 

### <a name="create-and-execute-template"></a>Creación y ejecución de la plantilla

1. Copie y pegue la siguiente sintaxis JSON en el archivo:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "aksResourceId": {
           "type": "string",
           "metadata": {
             "description": "AKS Cluster Resource ID"
           }
       },
      "aksResourceLocation": {
        "type": "string",
        "metadata": {
           "description": "Location of the AKS resource e.g. \"East US\""
         }
       }
    },
    "resources": [
      {
        "name": "[split(parameters('aksResourceId'),'/')[8]]",
        "type": "Microsoft.ContainerService/managedClusters",
        "location": "[parameters('aksResourceLocation')]",
        "apiVersion": "2018-03-31",
        "properties": {
          "mode": "Incremental",
          "id": "[parameters('aksResourceId')]",
          "addonProfiles": {
            "omsagent": {
              "enabled": false,
              "config": {
                "logAnalyticsWorkspaceResourceID": null
              }
            }
           }
         }
       }
      ]
    }
    ```

2. Guarde este archivo como **OptOutTemplate.json** en una carpeta local.
3. Copie y pegue la siguiente sintaxis JSON en el archivo:

    ```json
    {
     "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
     "contentVersion": "1.0.0.0",
     "parameters": {
       "aksResourceId": {
         "value": "/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroup>/providers/Microsoft.ContainerService/managedClusters/<ResourceName>"
      },
      "aksResourceLocation": {
        "value": "<aksClusterRegion>"
        }
      }
    }
    ```

4. Reemplace el valor de **aksResourceId** y **aksResourceLocation** por los valores del clúster de AKS, que puede encontrar en la página de **propiedades** del clúster seleccionado.

    ![Página de propiedades del contenedor](./media/monitoring-container-health/container-properties-page.png)

    Mientras está en la página de **propiedades**, copie también el **identificador de recurso del área de trabajo**.  Este valor es necesario si decide que quiere eliminar el área de trabajo de Log Analytics más adelante, lo que no se realiza como parte de este proceso.  

5. Guarde este archivo como **OptOutParam.json** en una carpeta local.
6. Está listo para implementar esta plantilla. 

    * Use los siguientes comandos de PowerShell desde la carpeta que contenga la plantilla:

        ```powershell
        Connect-AzureRmAccount
        Select-AzureRmSubscription -SubscriptionName <yourSubscriptionName>
        New-AzureRmResourceGroupDeployment -Name opt-out -ResourceGroupName <ResourceGroupName> -TemplateFile .\OptOutTemplate.json -TemplateParameterFile .\OptOutParam.json
        ```

        El cambio de configuración puede tardar unos minutos en completarse. Cuando termine, verá un mensaje similar al siguiente que incluye el resultado:

        ```powershell
        ProvisioningState       : Succeeded
        ```

    * Para ejecutar el comando siguiente con la CLI de Azure en Linux:

        ```azurecli
        az login   
        az account set --subscription "Subscription Name" 
        az group deployment create --resource-group <ResourceGroupName> --template-file ./OptOutTemplate.json --parameters @./OptOutParam.json  
        ```

        El cambio de configuración puede tardar unos minutos en completarse. Cuando termine, verá un mensaje similar al siguiente que incluye el resultado:

        ```azurecli
        ProvisioningState       : Succeeded
        ```

Si el área de trabajo se creó solamente para admitir la supervisión del clúster y ya no es necesario, tiene que eliminarlo manualmente. Si no está familiarizado con la eliminación de un área de trabajo, consulte [Eliminación de una área de trabajo de Azure Log Analytics con Azure Portal](../log-analytics/log-analytics-manage-del-workspace.md).  No se olvide del **identificador de recurso del área de trabajo** que copió anteriormente en el paso 4, lo necesitará.  

## <a name="troubleshooting"></a>solución de problemas
En esta sección se proporciona información para ayudarle a solucionar problemas con el mantenimiento de contenedores.

Si el mantenimiento de contenedores se ha habilitado y configurado correctamente, pero no ve ninguna información de estado ni resultados en Log Analytics, cuando realice una búsqueda de registros, puede llevar a cabo los pasos siguientes para ayudar a diagnosticar el problema.   

1. Compruebe el estado del agente ejecutando el comando siguiente: 

    `kubectl get ds omsagent --namespace=kube-system`

    La salida debería ser similar a la siguiente que indica que se implementó correctamente:

    ```
    User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
    NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
    omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
    ```  
2. Para comprobar el estado de implementación de la solución con la versión del agente *06072018* o superior, ejecute el comando siguiente:

    `kubectl get deployment omsagent-rs -n=kube-system`

    La salida debería ser similar a la siguiente que indica que se implementó correctamente:

    ```
    User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system 
    NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
    omsagent   1         1         1            1            3h
    ```

3. Compruebe el estado de la pod para comprobar que se ejecuta con el comando siguiente: `kubectl get pods --namespace=kube-system`

    La salida debe recordar a la siguiente con un estado de *Running* (En ejecución) en omsagent:

    ```
    User@aksuser:~$ kubectl get pods --namespace=kube-system 
    NAME                                READY     STATUS    RESTARTS   AGE 
    aks-ssh-139866255-5n7k5             1/1       Running   0          8d 
    azure-vote-back-4149398501-7skz0    1/1       Running   0          22d 
    azure-vote-front-3826909965-30n62   1/1       Running   0          22d 
    omsagent-484hw                      1/1       Running   0          1d 
    omsagent-fkq7g                      1/1       Running   0          1d 
    ```

4. Compruebe los registros del agente. Cuando se implementa el agente en contenedor, se ejecuta una comprobación rápida mediante la ejecución de comandos de OMI y se muestra la versión del agente y el 
5.  proveedor. Para ver que el agente se ha incorporado correctamente, ejecute el siguiente comando: `kubectl logs omsagent-484hw --namespace=kube-system`

    La salida debe ser similar a la siguiente:

    ```
    User@aksuser:~$ kubectl logs omsagent-484hw --namespace=kube-system
    :
    :
    instance of Container_HostInventory
    {
        [Key] InstanceID=3a4407a5-d840-4c59-b2f0-8d42e07298c2
        Computer=aks-nodepool1-39773055-0
        DockerVersion=1.13.1
        OperatingSystem=Ubuntu 16.04.3 LTS
        Volume=local
        Network=bridge host macvlan null overlay
        NodeRole=Not Orchestrated
        OrchestratorType=Kubernetes
    }
    Primary Workspace: b438b4f6-912a-46d5-9cb1-b44069212abc    Status: Onboarded(OMSAgent Running)
    omi 1.4.2.2
    omsagent 1.6.0.23
    docker-cimprov 1.0.0.31
    ```

## <a name="next-steps"></a>Pasos siguientes

[Busque registros](../log-analytics/log-analytics-log-search.md) para ver información detallada del rendimiento de la aplicación y el mantenimiento del contenedor.  