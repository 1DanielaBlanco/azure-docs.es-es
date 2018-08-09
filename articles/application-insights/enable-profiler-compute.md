---
title: Habilitación de Application Insights Profiler para aplicaciones que se hospedan en recursos de Azure Cloud Services | Microsoft Docs
description: Aprenda a configurar Application Insights Profiler en aplicaciones que se ejecutan en Azure Cloud Services.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 10/16/2017
ms.reviewer: ramach
ms.author: mbullwin
ms.openlocfilehash: 2da281f52a85992c6fade360c94fbf473c38dc20
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/02/2018
ms.locfileid: "39424031"
---
# <a name="enable-application-insights-profiler-for-azure-vms-service-fabric-and-azure-cloud-services"></a>Habilitación de Application Insights Profiler en máquinas virtuales de Azure, Service Fabric y Azure Cloud Services

En este artículo se muestra cómo habilitar Azure Application Insights Profiler en una aplicación de ASP.NET hospedada por recursos de Azure Cloud Services.

Los ejemplos de este artículo incluyen compatibilidad con Azure Virtual Machines, conjuntos de escalado de máquinas virtuales, Azure Service Fabric y Azure Cloud Services. Todos los ejemplos se basan en plantillas que admiten el modelo de implementación de [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).  


## <a name="overview"></a>Información general

En la imagen siguiente se muestra cómo funciona Application Insights Profiler con aplicaciones hospedadas en recursos de Azure Cloud Services. Los recursos de Azure Cloud Services incluyen Virtual Machines, conjuntos de escalado, servicios en la nube y clústeres de Service Fabric. La imagen usa una máquina virtual de Azure como ejemplo.  

  ![Diagrama que muestra cómo funciona Application Insights Profiler con recursos de Azure Cloud Services](./media/enable-profiler-compute/overview.png)

Para habilitar completamente Profiler, debe cambiar la configuración en tres ubicaciones:

* El panel de la instancia de Application Insights en Azure Portal.
* El código fuente de la aplicación (por ejemplo, una aplicación web ASP.NET).
* El código fuente de la definición de la implementación del entorno (por ejemplo, una plantilla de Azure Resource Manager en el archivo .json).


## <a name="set-up-the-application-insights-instance"></a>Configuración de la instancia de Application Insights

1. [Cree un nuevo recurso de Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-create-new-resource) o seleccione uno existente. 

1. Vaya al recurso de Application Insights y copie la clave de instrumentación.

   ![Ubicación de la clave de instrumentación](./media/enable-profiler-compute/CopyAIKey.png)

1. Para finalizar la configuración de la instancia de Application Insights para Profiler, realice el procedimiento que se describe en Habilitación de Profiler. No es necesario vincular las aplicaciones web, porque los pasos son específicos del recurso de servicios de aplicación. Asegúrese de que Profiler esté habilitado en el panel **Configure Profiler** (Configurar Profiler).


## <a name="set-up-the-application-source-code"></a>Configuración del código fuente de la aplicación

### <a name="aspnet-web-applications-azure-cloud-services-web-roles-or-the-service-fabric-aspnet-web-front-end"></a>Aplicaciones web de ASP.NET, roles web de Azure Cloud Services o el servidor front-end web de ASP.NET de Service Fabric
Configure la aplicación para que envíe datos de telemetría a una instancia de Application Insights en cada operación `Request`.  

Agregue el [SDK de Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview#get-started) al proyecto de aplicación. Asegúrese de que las versiones del paquete NuGet sean las siguientes:  
  - Para aplicaciones ASP.NET: [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) 2.3.0 o cualquier versión posterior.
  - Para aplicaciones ASP.NET Core: [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/) 2.1.0 o cualquier versión posterior.
  - Para otras aplicaciones .NET y .NET Core (por ejemplo, un servicio sin estado de Service Fabric o un rol de trabajo de Cloud Services): [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) o [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) 2.3.0, o cualquier versión posterior.  

### <a name="azure-cloud-services-worker-roles-or-the-service-fabric-stateless-back-end"></a>Roles de trabajo de Azure Cloud Services o el servidor back-end sin estado de Service Fabric
Si la aplicación *no* es una aplicación de ASP.NET o ASP.NET Core (por ejemplo, si es un rol de trabajo de Azure Cloud Services o son API sin estado de Service Fabric), además de realizar el paso anterior, haga lo siguiente:  

  1. Agregue el código siguiente al principio de la duración de la aplicación:  

        ```csharp
        using Microsoft.ApplicationInsights.Extensibility;
        ...
        // Replace with your own Application Insights instrumentation key.
        TelemetryConfiguration.Active.InstrumentationKey = "00000000-0000-0000-0000-000000000000";
        ```
      Para más información acerca de la configuración de esta clave de instrumentación global, consulte [Using Service Fabric with Application Insights](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/blob/dev/appinsights/ApplicationInsights.md) (Uso de Service Fabric con Application Insights).  

  1. Con respecto a cualquier parte del código que desee instrumentar, agregue una instrucción `StartOperation<RequestTelemetry>` **USING** a su alrededor, como se muestra en el ejemplo siguiente:

        ```csharp
        using Microsoft.ApplicationInsights;
        using Microsoft.ApplicationInsights.DataContracts;
        ...
        var client = new TelemetryClient();
        ...
        using (var operation = client.StartOperation<RequestTelemetry>("Insert_Your_Custom_Event_Unique_Name"))
        {
          // ... Code I want to profile.
        }
        ```

        No se permite llamar a `StartOperation<RequestTelemetry>` dentro de otro ámbito de `StartOperation<RequestTelemetry>`. En su lugar, puede usar `StartOperation<DependencyTelemetry>` en el ámbito anidado. Por ejemplo:   
        
        ```csharp
        using (var getDetailsOperation = client.StartOperation<RequestTelemetry>("GetProductDetails"))
        {
        try
        {
          ProductDetail details = new ProductDetail() { Id = productId };
          getDetailsOperation.Telemetry.Properties["ProductId"] = productId.ToString();
        
          // By using DependencyTelemetry, 'GetProductPrice' is correctly linked as part of the 'GetProductDetails' request.
          using (var getPriceOperation = client.StartOperation<DependencyTelemetry>("GetProductPrice"))
          {
              double price = await _priceDataBase.GetAsync(productId);
              if (IsTooCheap(price))
              {
                  throw new PriceTooLowException(productId);
              }
              details.Price = price;
          }
        
          // Similarly, note how 'GetProductReviews' doesn't establish another RequestTelemetry.
          using (var getReviewsOperation = client.StartOperation<DependencyTelemetry>("GetProductReviews"))
          {
              details.Reviews = await _reviewDataBase.GetAsync(productId);
          }
        
          getDetailsOperation.Telemetry.Success = true;
          return details;
        }
        catch(Exception ex)
        {
          getDetailsOperation.Telemetry.Success = false;
        
          // This exception gets linked to the 'GetProductDetails' request telemetry.
          client.TrackException(ex);
          throw;
        }
        }
        ```

## <a name="set-up-the-environment-deployment-definition"></a>Configuración de la definición de implementación del entorno

El entorno en que se ejecutan Profiler y su aplicación puede ser una máquina virtual, un conjunto de escalado de máquinas virtuales, un clúster de Service Fabric o una instancia de Cloud Services.  

### <a name="virtual-machines-scale-sets-or-service-fabric"></a>Máquinas virtuales, conjuntos de escalado o Service Fabric

Para obtener ejemplos completos, consulte:  
  * [Máquina virtual](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachine.json)
  * [Conjunto de escalado de máquinas virtuales](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachineScaleSet.json)
  * [Clúster de Service Fabric](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/ServiceFabricCluster.json)

Para configurar el entorno, haga lo siguiente:
1. Para asegurarse de que usa [.NET Framework 4.6.1](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) o una versión más reciente, es suficiente con confirmar que el sistema operativo implementado sea `Windows Server 2012 R2` u otro posterior.

1. Busque la extensión [Azure Diagnostics](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) en el archivo de plantilla de implementación y agregue la siguiente sección `SinksConfig` como elemento secundario de `WadCfg`. Reemplace el valor de la propiedad `ApplicationInsightsProfiler` por su propia clave de instrumentación de Application Insights:  

      ```json
      "SinksConfig": {
        "Sink": [
          {
            "name": "MyApplicationInsightsProfilerSink",
            "ApplicationInsightsProfiler": "00000000-0000-0000-0000-000000000000"
          }
        ]
      }
      ```

      Para obtener información acerca de cómo agregar la extensión de Diagnostics a una plantilla de implementación, consulte [Uso de la supervisión y el diagnóstico con una máquina virtual Windows y plantillas de Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/windows/extensions-diagnostics-template?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

> [!TIP]
> Para Virtual Machines, una alternativa a los pasos basados en JSON anteriores es navegar en Azure Portal a **Virtual Machines** > **Configuración de diagnóstico** > **Receptores** > establecer el envío de datos de diagnóstico a Application Insights en **Habilitado** y seleccione una cuenta de Application Insights o una ikey específica.

### <a name="azure-cloud-services"></a>Azure Cloud Services

1. Para tener la seguridad de que se usa [.NET Framework 4.6.1](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) o una versión posterior, es suficiente con confirmar que los archivos *ServiceConfiguration.\*.cscfg* tienen un valor de `osFamily` de "5" o posterior.

1. Busque el archivo *diagnostics.wadcfgx* de [Azure Diagnostics](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) correspondiente a su rol de aplicación, como se muestra aquí:  

   ![Ubicación del archivo de configuración de diagnósticos](./media/enable-profiler-compute/cloudservice-solutionexplorer.png)  

   Si no encuentra el archivo, para aprender a habilitar la extensión de Diagnostics en el proyecto de Azure Cloud Services, consulte [Configuración de diagnósticos para Azure Cloud Services y máquinas virtuales](https://docs.microsoft.com/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines#enable-diagnostics-in-cloud-service-projects-before-deploying-them).

1. Agregue la siguiente sección `SinksConfig` como elemento secundario de `WadCfg`:  

      ```xml
      <WadCfg>
        <DiagnosticMonitorConfiguration>...</DiagnosticMonitorConfiguration>
        <SinksConfig>
          <Sink name="MyApplicationInsightsProfiler">
            <!-- Replace with your own Application Insights instrumentation key. -->
            <ApplicationInsightsProfiler>00000000-0000-0000-0000-000000000000</ApplicationInsightsProfiler>
          </Sink>
        </SinksConfig>
      </WadCfg>
      ```

> [!NOTE]  
> Si el archivo *diagnostics.wadcfgx* también contiene otro receptor de tipo `ApplicationInsights`, las tres claves de instrumentación siguientes deben coincidir:  
>  * La clave que usa la aplicación.  
>  * La clave que usa el receptor `ApplicationInsights`.  
>  * La clave que usa el receptor `ApplicationInsightsProfiler`.  
>
> Puede encontrar el valor real de la clave de instrumentación que usa el receptor `ApplicationInsights` en los archivos *ServiceConfiguration.\*.cscfg*.  
> Después del lanzamiento de Azure SDK para Visual Studio 15.5, solo es preciso que coincidan las claves de instrumentación que usan la aplicación y el receptor `ApplicationInsightsProfiler`.


## <a name="configure-environment-deployment-and-runtime"></a>Configuraciones de tiempo de ejecución y de implementación del entorno

1. Implemente la definición de implementación de entorno modificada.  

   Para aplicar las modificaciones, usualmente se realiza una implementación de plantilla completa o una publicación basada en Cloud Services mediante cmdlets de PowerShell o Visual Studio.  

   El siguiente es un enfoque alternativo para las máquinas virtuales existentes que solo afecta a su extensión de Azure Diagnostics:  

    ```powershell
    $ConfigFilePath = [IO.Path]::GetTempFileName()
    # After you export the currently deployed Diagnostics config to a file, edit it to include the ApplicationInsightsProfiler sink.
    (Get-AzureRmVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM").PublicSettings | Out-File -Verbose $ConfigFilePath
    # Set-AzureRmVMDiagnosticsExtension might require the -StorageAccountName argument
    # If your original diagnostics configuration had the storageAccountName property in the protectedSettings section (which is not downloadable), be sure to pass the same original value you had in this cmdlet call.
    Set-AzureRmVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM" -DiagnosticsConfigurationPath $ConfigFilePath
    ```

1. Si la aplicación deseada se ejecuta mediante [IIS](https://www.microsoft.com/web/downloads/platform.aspx), habilite la característica `IIS Http Tracing` de Windows mediante los pasos siguientes:  

   a. Establezca acceso remoto al entorno y, después, use la ventana [Agregar características de Windows]( https://docs.microsoft.com/iis/configuration/system.webserver/tracing/) o ejecute el siguiente comando en PowerShell (como administrador):  

    ```powershell
    Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All
    ```  
   b. Si el establecimiento del acceso remoto supone un problema, puede usar la [CLI de Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) para ejecutar el siguiente comando:  

    ```powershell
    az vm run-command invoke -g MyResourceGroupName -n MyVirtualMachineName --command-id RunPowerShellScript --scripts "Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All"
    ```


## <a name="enable-profiler-on-on-premises-servers"></a>Habilitación de Profiler en servidores locales

La habilitación de Profiler en un servidor local también se conoce como ejecución de Application Insights Profiler en modo independiente. No está vinculada a las modificaciones de la extensión Azure Diagnostics.

No se prevé proporcionar compatibilidad con Profiler oficialmente en servidores locales. Si está interesado en probar este escenario, puede [descargar el código de compatibilidad](https://github.com/ramach-msft/AIProfiler-Standalone). *No* somos responsables del mantenimiento de ese código ni de responder a problemas y solicitudes de características en relación con él.

## <a name="next-steps"></a>Pasos siguientes

- Genere tráfico para su aplicación (por ejemplo, inicie una [prueba de disponibilidad](https://docs.microsoft.com/azure/application-insights/app-insights-monitor-web-app-availability)). A continuación, espere de 10 a 15 minutos para que se empiecen a enviar seguimientos a la instancia de Application Insights.
- Consulte [Seguimientos de Profiler](https://docs.microsoft.com/azure/application-insights/app-insights-profiler#enable-the-profiler) en Azure Portal.
- Obtenga ayuda para solucionar problemas de Profiler en la sección [Solución de problemas](app-insights-profiler.md#troubleshooting) de Profiler.
- Lea más sobre Profiler en [Application Insights Profiler](app-insights-profiler.md).
