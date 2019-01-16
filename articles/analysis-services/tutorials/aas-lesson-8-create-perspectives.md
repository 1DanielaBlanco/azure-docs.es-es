---
title: 'Lección 8 del tutorial de Azure Analysis Services: Creación de perspectivas | Microsoft Docs'
description: Se describe cómo crear perspectivas en el proyecto del tutorial de Analysis Services de Azure.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: c26da5d047a1e904510f96b44d81632b6e98689e
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/10/2019
ms.locfileid: "54190976"
---
# <a name="create-perspectives"></a>Creación de perspectivas

En esta lección, creará una perspectiva de venta por Internet. Una perspectiva define un subconjunto visible de un modelo que ofrece puntos de vista centrados, específicos del negocio o específicos de la aplicación. Cuando un usuario se conecta a un modelo mediante una perspectiva, solo ve esos objetos de modelo (tablas, columnas, medidas, jerarquías y KPI) como campos definidos en esa perspectiva. Para más información, consulte [Perspectivas](https://docs.microsoft.com/sql/analysis-services/tabular-models/perspectives-ssas-tabular).
  
La perspectiva Venta por Internet que creará en esta lección excluye el objeto de tabla DimCustomer. Cuando se crea una perspectiva que excluye ciertos objetos de la vista, ese objeto todavía existe en el modelo. Sin embargo, no está visible en una lista de campos de cliente de generación de informes. Las columnas y las medidas calculadas, tanto si se incluyen en una perspectiva como si no, aún se pueden calcular a partir de los datos del objeto excluido.  
  
Esta lección tiene como objetivo describir cómo se crean las perspectivas, así como familiarizarse con las herramientas de creación de modelos tabulares. Si más tarde amplía este modelo para incluir tablas adicionales, puede crear otras perspectivas para definir puntos de vista diferentes del modelo, por ejemplo, Inventario y Ventas.  
  
Tiempo estimado para completar esta lección: **5 minutos**  
  
## <a name="prerequisites"></a>Requisitos previos  
Este tema forma parte de un tutorial de modelado tabular, que se debe completar en orden. Para poder realizar las tareas de esta lección, debe haber completado la lección anterior: [Lección 7: Creación de indicadores clave de rendimiento](../tutorials/aas-lesson-7-create-key-performance-indicators.md).  
  
## <a name="create-perspectives"></a>Creación de perspectivas  
  
#### <a name="to-create-an-internet-sales-perspective"></a>Para crear una perspectiva Venta por Internet, siga estos pasos:  
  
1.  Haga clic en el menú **Modelo** > **Perspectivas** > **Crear y administrar**.  
  
2.  En el cuadro de diálogo **Perspectivas**, haga clic en **Nueva perspectiva**.  
  
3.  Haga doble clic en el encabezado de columna **Nueva perspectiva** y llámelo **Venta por Internet**.  
  
4.  Seleccione todas las tablas *excepto* **DimCustomer**.  
  
    ![aas-lesson8-perspectives](../tutorials/media/aas-lesson8-perspectives.png)
  
    En una lección posterior, usará la característica Analizar en Excel para probar esta perspectiva. La lista de campos de tabla dinámica de Excel incluye todas las tablas, excepto DimCustomer.  

## <a name="whats-next"></a>Pasos siguientes
[Lección 9: Creación de jerarquías](../tutorials/aas-lesson-9-create-hierarchies.md).
  
  
  
  
