---
title: Usar el kit de herramientas de Marketplace para crear y publicar elementos de Marketplace | Microsoft Docs
description: Aprenda a crear rápidamente elementos de Marketplace con el kit de herramientas de publicación
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2018
ms.author: sethm
ms.reviewer: ''
ms.openlocfilehash: fabc72e6dc31bb7f244cda9634af3b2556ba23a9
ms.sourcegitcommit: f6050791e910c22bd3c749c6d0f09b1ba8fccf0c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50023800"
---
#  <a name="add-marketplace-items-using-publishing-tool"></a>Agregar elementos de Marketplace con la herramienta de publicación

Agregar el contenido a [Azure Stack Marketplace](azure-stack-marketplace.md) hace que sus soluciones estén disponibles para implementación por su parte y la de sus inquilinos. El kit de herramientas de Marketplace crea archivos de paquete de Azure Marketplace (.azpkg) basados en extensiones de VM o plantillas de Azure Resource Manager de IaaS. También puede usar el kit de herramientas de Marketplace para publicar archivos .azpkg creados con la herramienta o mediante pasos [manuales](azure-stack-create-and-publish-marketplace-item.md). En este tema se explica la descarga de la herramienta, la creación de un elemento de Marketplace basado en una plantilla de VM y luego la publicación de ese elemento en Azure Stack Marketplace.     

## <a name="prerequisites"></a>Requisitos previos

 - Debe ejecutar el kit de herramientas en el host de Azure Stack o tener conectividad [VPN](.\asdk\asdk-connect.md#connect-to-azure-stack-with-vpn) al host de ASDK desde el equipo en que se ejecuta la herramienta.

 - Descargue las [plantillas de inicio rápidos de Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates/archive/master.zip) y extráigalas.

 - Descargue la [herramienta de empaquetado de la Galería de Azure](http://aka.ms/azurestackmarketplaceitem) (AzureGalleryPackage.exe). 

 - La publicación en el Marketplace requiere iconos y un archivo de miniaturas. Puede utilizar los suyos o guardar archivos de [muestra](azure-stack-marketplace-publisher.md#support-files) localmente para este ejemplo.

## <a name="download-the-tool"></a>Descargar la herramienta

Puede descargar el kit de herramientas de Marketplace [desde el repositorio de herramientas de Azure Stack](azure-stack-powershell-download.md).

##  <a name="create-marketplace-items"></a>Crear elementos de Marketplace

En esta sección, se usa el kit de herramientas de Marketplace para crear un paquete de elementos de Marketplace en formato .azpkg.  

### <a name="provide-marketplace-information-with-wizard"></a>Proporcionar información de Marketplace con el Asistente

1. Ejecute el kit de herramientas de Marketplace desde una sesión de PowerShell:
   ```PowerShell
   .\MarketplaceToolkit.ps1
   ```

2. Haga clic en la pestaña **Solución**. Esta pantalla acepta información sobre el elemento de Marketplace. Escriba información sobre el elemento como desee que aparezca en Marketplace. También puede especificar un [archivo de parámetros](azure-stack-marketplace-publisher.md#use-a-parameters-file) para rellenar previamente el formulario.  
    
    ![captura de pantalla de la primera pantalla del kit de herramientas de Marketplace](./media/azure-stack-marketplace-publisher/image7.png)
3. Haga clic en **Examinar** y seleccione un archivo de imagen para cada campo de icono y captura de pantalla. Puede utilizar sus propios iconos o los iconos de muestra de la sección [Archivos auxiliares](azure-stack-marketplace-publisher.md#support-files).
4. Una vez que rellene todos los campos, seleccione “Preview Solution” para obtener una vista previa de la solución en Marketplace. Puede revisar y editar el texto, las imágenes y las capturas de pantalla antes de hacer clic en **Siguiente**.  

### <a name="import-template-and-create-package"></a>Importar la plantilla y crear el paquete

En esta sección, importe la plantilla y trabaje con entradas para la solución.

1.  Haga clic en **Examinar** y seleccione *azuredeploy.json* desde la carpeta 101-Simple-Windows-VM en las plantillas descargadas.

    ![captura de pantalla de la segunda pantalla del kit de herramientas de Marketplace](./media/azure-stack-marketplace-publisher/image8.png)
2.  El Asistente para implementar se rellena con un paso *Básico* y elementos de entrada para cada parámetro especificado en la plantilla. Puede agregar pasos adicionales y mover entradas entre pasos. Por ejemplo, puede que quiera pasos “Configuración de front-end” y “Configuración de back-end” para la solución.
3.  Especifique la ruta de acceso a AzureGalleryPackager.exe.  
4.  Haga clic en **Create**(Crear). El kit de herramientas de Marketplace empaqueta la solución en un archivo .azpkg. Cuando termina, el asistente muestra la ruta de acceso al archivo de la solución y le ofrece la opción de continuar publicando el paquete en Azure Stack.

## <a name="publish-marketplace-items"></a>Publicar elementos de Marketplace

En esta sección, debe publicar el elemento de Marketplace en Azure Stack Marketplace.

![captura de pantalla de la primera pantalla del kit de herramientas de Marketplace](./media/azure-stack-marketplace-publisher/image9.png)

1.  El asistente necesita información para publicar la solución:
    
    |Campo|DESCRIPCIÓN|
    |-----|-----|
    | Service Admin Name (Nombre del administrador de servicios) | Cuenta del administrador del servicio  Ejemplo: ServiceAdmin@mydomain.onmicrosoft.com |
    | Contraseña | Contraseña de cuenta del administrador de servicios. |
    | Punto de conexión de API | Punto de conexión de Azure Resource Manager de Azure Stack. Ejemplo: management.local.azurestack.external |
2.  Cuando haga clic en **Publicar**, aparecerá el registro de publicación.
3.  Ahora es posible implementar el elemento publicado a través del portal de Azure Stack.

## <a name="use-a-parameters-file"></a>Usar un archivo de parámetros

También puede usar un archivo de parámetros para completar la información del elemento de Marketplace.  

El kit de herramientas de Marketplace incluye un archivo *solution.parameters.ps1* que puede usar para crear su propio archivo de parámetros.

## <a name="support-files"></a>Archivos auxiliares

| DESCRIPCIÓN | Muestra |
| ----- | ----- |
| Icono .png de 40 × 40 | ![](./media/azure-stack-marketplace-publisher/image1.png) |
| Icono .png de 90 × 90 | ![](./media/azure-stack-marketplace-publisher/image2.png) |
| Icono .png de 115 × 115 | ![](./media/azure-stack-marketplace-publisher/image3.png) |
| Icono .png de 255 × 115 | ![](./media/azure-stack-marketplace-publisher/image4.png) |
| Vista en miniatura .png de 533 × 324 | ![](./media/azure-stack-marketplace-publisher/image5.png) |

## <a name="next-steps"></a>Pasos siguientes

[Descarga de elementos de Marketplace](azure-stack-download-azure-marketplace-item.md)  
[Creación y publicación de un producto en Marketplace](azure-stack-create-and-publish-marketplace-item.md)