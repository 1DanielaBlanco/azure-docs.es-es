---
title: Destinos o salidas de ejemplo posibles con preparación de datos de Azure Machine Learning | Microsoft Docs
description: En este documento se proporciona un conjunto de ejemplos de destinos o salidas de datos personalizados con preparación de datos de Azure Machine Learning
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: ''
ms.devlang: ''
ms.topic: article
ms.date: 02/01/2018
ms.openlocfilehash: a9476b9ac3a5cc1f82ab1c09678972fb6b78b894
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/19/2018
---
# <a name="sample-of-destination-connections-python"></a>Ejemplo de conexiones de destino (Python) 
Antes de leer este apéndice, vea la [Introducción a la extensibilidad de Python](data-prep-python-extensibility-overview.md).


## <a name="write-to-excel"></a>Escribir en Excel 


Para escribir en Excel se requiere una biblioteca adicional. La forma de agregar nuevas bibliotecas se documenta en la introducción a la extensibilidad. `openpyxl` es la biblioteca que debe agregar.

Antes de escribir en Excel, es posible que se deban efectuar otros cambios. Algunos de los tipos de datos usados en la preparación de datos no se admiten en algunos formatos de destino. Por ejemplo, si existen objetos "Error", estos no se serializarán correctamente en Excel. Por lo tanto, antes de intentar escribir en Excel, se debe efectuar una transformación "Reemplazar valores de error", que quita los errores de todas las columnas.

Si se han llevado a cabo todas las tareas anteriores, la línea siguiente escribe la tabla de datos en una sola hoja de un documento de Excel. Agregue una transformación de Transformar el flujo de datos (script). A continuación, escriba el código siguiente en una sección de expresión.


### <a name="on-windows"></a>En Windows 
```python
df.to_excel('c:\dev\data\Output\Customer.xlsx', sheet_name='Sheet1')
```

### <a name="on-macosos-x"></a>En macOS/OS X ###
```python
df.to_excel('c:/dev/data/Output/Customer.xlsx', sheet_name='Sheet1')
```
