---
title: 'Lección 7 del tutorial de Azure Analysis Services: Creación de indicadores clave de rendimiento | Microsoft Docs'
description: Se describe cómo crear indicadores clave de rendimiento en el proyecto del tutorial de Azure Analysis Services.
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 8534640822030a7aa93c8eafe2a1b1a4a8bc5bc4
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2018
---
# <a name="create-key-performance-indicators"></a>Creación de indicadores clave de rendimiento

En esta lección, creará indicadores clave de rendimiento (KPI). Los KPI se usan para medir el rendimiento de un valor, definido por una medida *Base*, con un valor de *Destino*, también definido por una medida o por un valor absoluto. En aplicaciones cliente de generación de informes, los KPI pueden proporcionar a los profesionales de empresa una manera rápida y sencilla de comprender un resumen de logros empresariales o para identificar tendencias. Para aprender más, consulte [KPI](https://docs.microsoft.com/sql/analysis-services/tabular-models/kpis-ssas-tabular)
  
Tiempo estimado para completar esta lección: **15 minutos**  
  
## <a name="prerequisites"></a>requisitos previos  
Este tema forma parte de un tutorial de modelado tabular, que se debe completar en orden. Antes de realizar las tareas de esta lección, debe haber completado la lección anterior: [Lección 6: Creación de medidas](../tutorials/aas-lesson-6-create-measures.md).   
  
## <a name="create-key-performance-indicators"></a>Creación de indicadores clave de rendimiento  
  
#### <a name="to-create-an-internetcurrentquartersalesperformance-kpi"></a>Para crear un KPI InternetCurrentQuarterSalesPerformance, siga estos pasos:  
  
1.  En el diseñador de modelos, haga clic en la tabla **FactInternetSales**.  
  
2.  En la cuadrícula de medidas, haga clic en una celda vacía.  
  
3.  En la barra de fórmulas, encima de la tabla, escriba la fórmula siguiente: 
 
    ```  
    InternetCurrentQuarterSalesPerformance :=DIVIDE([InternetCurrentQuarterSales]/[InternetPreviousQuarterSalesProportionToQTD],BLANK())  
    ```

    Esta medida sirve como medida base del KPI.  
  
4.  En la cuadrícula de medidas, haga clic con el botón derecho en **InternetCurrentQuarterSalesPerformance** > **Crear KPI**.   
  
5.  En el cuadro de diálogo Indicador clave de rendimiento (KPI), en **Destino**, seleccione **Valor absoluto** y luego escriba **1.1**.  
  
7.  En el campo izquierdo de control deslizante (bajo), escriba **1** y luego, en el campo derecho de control deslizante (alto), escriba **1.07**.  
  
8.  En **Seleccionar el estilo de icono**, seleccione el tipo de icono de rombo (rojo), triángulo (amarillo) o círculo (verde).
  
    ![aas-lesson7-kpi](../tutorials/media/aas-lesson7-kpi.png)
    
    > [!TIP]  
    > Observe la etiqueta **Descripciones** debajo de los estilos de icono disponibles. Utilice descripciones para los distintos elementos de KPI para que sean más fáciles de identificar en las aplicaciones cliente.  
  
9. Haga clic en **Aceptar** para completar el KPI.  
  
    En la cuadrícula de medidas, observe el icono situado junto a la medida **InternetCurrentQuarterSalesPerformance**. Este icono indica que esta medida sirve como un valor base para un KPI.  
  
#### <a name="to-create-an-internetcurrentquartermarginperformance-kpi"></a>Para crear un KPI InternetCurrentQuarterMarginPerformance, siga estos pasos:  
  
1.  En la cuadrícula de medidas de la tabla **FactInternetSales**, haga clic en una celda vacía.  
  
2.  En la barra de fórmulas, encima de la tabla, escriba la fórmula siguiente:  

    ```
    InternetCurrentQuarterMarginPerformance :=IF([InternetPreviousQuarterMarginProportionToQTD]<>0,([InternetCurrentQuarterMargin]-[InternetPreviousQuarterMarginProportionToQTD])/[InternetPreviousQuarterMarginProportionToQTD],BLANK())  
    ```
 
3.  Haga clic con el botón derecho en **InternetCurrentQuarterMarginPerformance** > **Crear KPI**.  
  
4.  En el cuadro de diálogo Indicador clave de rendimiento (KPI), en **Destino**, seleccione **Valor absoluto** y luego escriba **1.25**.   
  
5.  En el campo izquierdo de control deslizante (bajo), deslice hasta que el campo muestre **0.8** y, luego, deslice el campo de control deslizante derecho (alto) hasta que muestre **1.03**.  
  
6.  En **Seleccionar el estilo de icono**, seleccione el tipo de icono de rombo (rojo), triángulo (amarillo) o círculo (verde) y luego haga clic en **Aceptar**.  
  
## <a name="whats-next"></a>Pasos siguientes
[Lección 8: Creación de perspectivas](../tutorials/aas-lesson-8-create-perspectives.md)
  
  
