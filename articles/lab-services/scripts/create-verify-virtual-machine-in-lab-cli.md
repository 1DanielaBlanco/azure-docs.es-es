---
title: 'Ejemplo de script de la CLI de Azure: Creación y comprobación de una máquina virtual en un laboratorio | Microsoft Docs'
description: Este script de la CLI de Azure crea una máquina virtual en un laboratorio y comprueba que está disponible.
services: lab-services
author: spelluru
manager: ''
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/12/2018
ms.author: spelluru
ms.custom: mvc
ms.openlocfilehash: 225e62cedfea79875d56a50b4b04bdf0ad4c7892
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/26/2019
ms.locfileid: "56872036"
---
# <a name="use-azure-cli-to-create-and-verify-availability-of-a-virtual-machine-in-a-lab-in-azure-devtest-labs"></a>Uso de la CLI de Azure para crear y comprobar la disponibilidad de una máquina virtual en un laboratorio de Azure DevTest Labs

Este script de la CLI de Azure crea una máquina virtual en un laboratorio. La máquina virtual basada en una imagen de Marketplace con autenticación ssh. El script comprueba entonces que la máquina virtual está disponible para su uso. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de ejemplo

[!code-azurecli-interactive[main](../../../cli_scripts/devtest-lab/create-verify-virtual-machine-in-lab/create-verify-virtual-machine-in-lab.sh "Create and verify availability of a VM")]

## <a name="clean-up-deployment"></a>Limpieza de la implementación 

Ejecute el siguiente comando para quitar el grupo de recursos, la máquina virtual y todos los recursos relacionados.

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explicación del script

Este script usa los siguientes comandos:

| Get-Help | Notas |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Crea un grupo de recursos en el que se almacenan todos los recursos. |
| [az lab vm create](/cli/azure/lab/vm?view=azure-cli-latest#az-lab-vm-create) | Crea una máquina virtual en un laboratorio. |
| [az lab vm show](/cli/azure/lab/vm?view=azure-cli-latest#az-lab-vm-show) | Muestra el estado de la máquina virtual en un laboratorio. |

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre la CLI de Azure, consulte la [documentación de la CLI de Azure](https://docs.microsoft.com/cli/azure).

Encontrará más ejemplos de scripts de la CLI de Azure Lab Services en los [ejemplos de la CLI de Azure Lab Services](../samples-cli.md).
