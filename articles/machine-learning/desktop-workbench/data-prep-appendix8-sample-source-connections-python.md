---
title: Ejemplos de conexiones de datos de origen adicionales posibles con la preparación de datos de Azure Machine Learning | Microsoft Docs
description: En este documento se proporciona un conjunto de ejemplos de conexiones de datos de origen que son posibles con la preparación de datos de Azure Machine Learning
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: ''
ms.devlang: ''
ms.topic: article
ms.date: 02/01/2018
ROBOTS: NOINDEX
ms.openlocfilehash: ed0e6bc4c506535fcb679fc2b4015a61274a77f3
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2018
ms.locfileid: "46967700"
---
# <a name="sample-of-custom-source-connections-python"></a>Ejemplo de conexiones de origen personalizadas (Python) 

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 


Antes de leer este apéndice, vea la [Introducción a la extensibilidad de Python](data-prep-python-extensibility-overview.md).

## <a name="load-data-from-dataworld"></a>Carga de datos desde data.world

### <a name="prerequisites"></a>Requisitos previos

#### <a name="register-yourself-at-dataworld"></a>Registrarse en data.world
Necesita un token de la API desde el sitio web data.world.

#### <a name="install-dataworld-library"></a>Instalar la biblioteca de data.world

Abra la interfaz de línea de comandos de Azure Machine Learning Workbench seleccionando **File** > **Open command-line interface** (Archivo->Abrir interfaz de línea de comandos).

```console
pip install git+git://github.com/datadotworld/data.world-py.git
```

A continuación ejecute `dw configure` en la línea de comandos. Se le pedirá el token. Cuando escriba el token, se creará un directorio .dw/ en el directorio particular y el token se almacenará allí.

```
API token (obtained at: https://data.world/settings/advanced): <enter API token here>
```
Ahora ya debería poder importar bibliotecas de data.world.

#### <a name="load-data-into-data-preparation"></a>Cargar datos en la preparación de datos

Cree una transformación de Transformar el flujo de datos (script). A continuación, use el siguiente script para cargar los datos de data.world.

```python
#paths = df['Path'].tolist()

import datadotworld as dw

#Load the dataset.
lds = dw.load_dataset('data-society/the-simpsons-by-the-data')

#Load specific data frame from the dataset.
df = lds.dataframes['simpsons_episodes']

```
## <a name="azure-cosmos-db-as-a-data-source-connection"></a>Azure Cosmos DB como una conexión de origen de datos
Para un ejemplo de Azure Cosmos DB como una conexión de datos, lea el artículo sobre la [cargar de Azure Cosmos DB como una conexión de datos de origen](data-prep-load-azure-cosmos-db.md)
