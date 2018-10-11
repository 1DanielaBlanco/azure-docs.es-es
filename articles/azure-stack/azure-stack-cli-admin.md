---
title: Habilitación de la CLI de Azure para usuarios de Azure Stack | Microsoft Docs
description: Obtenga información sobre cómo usar la interfaz de la línea de comandos (CLI) multiplataforma para administrar e implementar recursos en Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: f576079c-5384-4c23-b5a4-9ae165d1e3c3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/28/2018
ms.author: mabrigg
ms.openlocfilehash: e9309f8cb46b31ded46b705308465ac6f6c89204
ms.sourcegitcommit: 5843352f71f756458ba84c31f4b66b6a082e53df
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2018
ms.locfileid: "47585193"
---
# <a name="enable-azure-cli-for-azure-stack-users"></a>Habilitación de la CLI de Azure para usuarios de Azure Stack

*Se aplica a: sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

Puede proporcionar el certificado de CA raíz a los usuarios de Azure Stack para que puedan usar la CLI de Azure en sus equipos de desarrollo. Los usuarios necesitan el certificado para administrar recursos a través de la CLI.

* **El certificado raíz de la entidad de certificación de Azure Stack** es necesario si los usuarios usan la CLI desde una estación de trabajo fuera del Kit de desarrollo de Azure Stack.  

* **El punto de conexión de los alias de máquina virtual** proporciona un alias, como "UbuntuLTS" o "Win2012Datacenter", que hace referencia a un publicador de imágenes, una oferta, una SKU y una versión como parámetro único al implementar máquinas virtuales.  

En las secciones siguientes se explica cómo obtener estos valores.

## <a name="export-the-azure-stack-ca-root-certificate"></a>Exportación del certificado raíz de CA de Azure Stack

El certificado de CA raíz de Azure Stack se encuentra en el kit de desarrollo y en una máquina virtual del inquilino que se ejecuta en el entorno del kit de desarrollo. Para exportar el certificado raíz de Azure Stack en formato PEM, inicie sesión en el kit de desarrollo o la máquina virtual del inquilino y ejecute el script siguiente:

```powershell
$label = "AzureStackSelfSignedRootCert"
Write-Host "Getting certificate from the current user trusted store with subject CN=$label"
$root = Get-ChildItem Cert:\CurrentUser\Root | Where-Object Subject -eq "CN=$label" | select -First 1
if (-not $root)
{
    Log-Error "Certificate with subject CN=$label not found"
    return
}

Write-Host "Exporting certificate"
Export-Certificate -Type CERT -FilePath root.cer -Cert $root

Write-Host "Converting certificate to PEM format"
certutil -encode root.cer root.pem
```

## <a name="set-up-the-virtual-machine-aliases-endpoint"></a>Configuración del punto de conexión de alias de máquina virtual

Los operadores de Azure Stack deben configurar un punto de conexión accesible públicamente que hospede un archivo de alias de máquina virtual. El archivo de alias de máquina virtual es un archivo JSON que proporciona un nombre común para una imagen. Ese nombre se especifica después cuando se implementa una máquina virtual como parámetro de la CLI de Azure.  

Antes de agregar una entrada a un archivo de alias, asegúrese de que [descarga las imágenes de Azure Marketplace](azure-stack-download-azure-marketplace-item.md) o de que ha [publicado su propia imagen personalizada](azure-stack-add-vm-image.md). Si publica una imagen personalizada, anote la información del publicador, la oferta, la SKU y la versión que especificó en la publicación. Si es una imagen de Marketplace, la información se puede ver mediante el cmdlet ```Get-AzureVMImage```.  

Hay un [archivo de alias de ejemplo](https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json) con muchos alias de imágenes comunes disponible, puede usarlo como punto de partida. Hospede este archivo en un espacio al que puedan acceder los clientes de la CLI. Una forma de hacerlo es hospedar el archivo en una cuenta de Blob Storage y compartir la dirección URL con los usuarios:

1. Descargue el [archivo de ejemplo](https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json) de GitHub.
2. Cree una cuenta de almacenamiento nueva en Azure Stack. Cuando haya terminado, cree un nuevo contenedor de blobs. Establezca la directiva de acceso en "público".  
3. Cargue el archivo JSON en el nuevo contenedor. Cuando haya terminado, puede ver la dirección URL del blob si selecciona el nombre del blob y luego la dirección URL en las propiedades del blob.

## <a name="next-steps"></a>Pasos siguientes

- [Implementación de plantillas con la CLI de Azure](azure-stack-deploy-template-command-line.md)
- [Conexión con PowerShell](azure-stack-connect-powershell.md)
- [Administración de permisos de usuario](azure-stack-manage-permissions.md)
