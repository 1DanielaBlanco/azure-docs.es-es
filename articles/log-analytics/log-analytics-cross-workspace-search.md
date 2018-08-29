---
title: Búsqueda entre recursos con Azure Log Analytics | Microsoft Docs
description: En este artículo se describe cómo se puede consultar los recursos de varias áreas de trabajo y en la aplicación de App Insights de la suscripción.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/17/2018
ms.author: magoedte
ms.component: na
ms.openlocfilehash: 75116e0ba50c3f195d528d33822af0c446acd5fe
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/20/2018
ms.locfileid: "42143771"
---
# <a name="perform-cross-resource-log-searches-in-log-analytics"></a>Realización de búsquedas de registros entre recursos en Log Analytics  

Anteriormente, con Azure Log Analytics, solo podía analizar datos desde dentro del área de trabajo actual, lo que limitaba su capacidad de consulta a través de varias áreas de trabajo definidas en la suscripción.  Además, solo puede buscar los elementos de telemetría recopilados de la aplicación basada en web con Application Insights directamente en Application Insights o desde Visual Studio.  Esto también convirtió en un desafío analizar de manera nativa los datos operativos y de aplicación en conjunto.   

Ahora puede consultar no solo a través de varias áreas de trabajo de Log Analytics, sino también datos desde una aplicación específica de Application Insights del mismo grupo de recursos, otro grupo de recursos u otra suscripción. Gracias a esto se consigue una vista total del sistema de datos.  Solo se pueden realizar este tipo de consultas en la [página de Log Analytics (versión preliminar)](log-analytics-log-search-portals.md#log-analytics-page-preview), no en Azure Portal. El número de recursos (áreas de trabajo de Log Analytics y aplicación de Application Insights) que se pueden incluir en una sola consulta se limita a 100. 

## <a name="querying-across-log-analytics-workspaces-and-from-application-insights"></a>Consultas a través de áreas de trabajo de Log Analytics y desde Application Insights
Para hacer referencia a otra área de trabajo en la consulta, use el identificador del [*área de trabajo*](https://docs.loganalytics.io/docs/Language-Reference/Scope-functions/workspace()), y, para una aplicación de Application Insights, use el identificador de la [*aplicación*](https://docs.loganalytics.io/docs/Language-Reference/Scope-functions/app()).  

### <a name="identifying-workspace-resources"></a>Identificación de los recursos del área de trabajo
En los ejemplos siguientes se muestran consultas en las áreas de trabajo de Log Analytics que devuelven recuentos de registros resumidos desde la tabla Update en un área de trabajo denominada *contosoretail-it*. 

La identificación de un área de trabajo se puede lograr de varias maneras:

* Nombre del recurso:- es un nombre legible del área de trabajo; a veces se denomina *nombre del componente*. 

    `workspace("contosoretail").Update | count`
 
    >[!NOTE]
    >En la identificación de un área de trabajo por nombre, se da por supuesto que es único en todas las suscripciones accesibles. Si tiene varias aplicaciones con el nombre especificado, la consulta produce un error debido a la ambigüedad. En este caso debe usar uno de los otros identificadores.

* Nombre completo: es el nombre completo del área de trabajo, compuesto por el nombre de la suscripción, el grupo de recursos y el nombre del componente con este formato: *nombreSuscripción/grupoDeRecursos/nombreDeComponente*. 

    `workspace('contoso/contosoretail/contosoretail-it').Update | count `

    >[!NOTE]
    >Dado que los nombres de suscripción de Azure no son únicos, este identificador podría ser ambiguo. 
    >

* Identificador del área de trabajo: es el identificador único e inmutable asignado a cada área de trabajo que se representa como identificador único global (GUID).

    `workspace("b459b4u5-912x-46d5-9cb1-p43069212nb4").Update | count`

* Identificador de recurso de Azure: identidad única definida por Azure del área de trabajo. Se usa cuando el nombre del recurso es ambiguo.  Para las áreas de trabajo, el formato es: */subscriptions/subscriptionId/resourcegroups/resourceGroup/providers/microsoft.OperationalInsights/workspaces/componentName*.  

    Por ejemplo: 
    ``` 
    workspace("/subscriptions/e427519-5645-8x4e-1v67-3b84b59a1985/resourcegroups/ContosoAzureHQ/providers/Microsoft.OperationalInsights/workspaces/contosoretail").Update | count
    ```

### <a name="identifying-an-application"></a>Identificación de una aplicación
Los ejemplos siguientes devuelven un recuento resumido de las solicitudes realizadas en una aplicación denominada *fabrikamapp* de Application Insights. 

Una aplicación de Application Insights se puede identificar con la expresión *app(Identifier)*.  El argumento *Identifier* especifica la aplicación que usa uno de los siguientes:

* Nombre del recurso: es el nombre legible de la aplicación; a veces se denomina *nombre del componente*.  

    `app("fabrikamapp")`

* Nombre completo: es el nombre completo de la aplicación, compuesto por el nombre de la suscripción, el grupo de recursos y el nombre del componente con este formato: *nombreSuscripción/grupoDeRecursos/nombreDeComponente*. 

    `app("AI-Prototype/Fabrikam/fabrikamapp").requests | count`

     >[!NOTE]
    >Dado que los nombres de suscripción de Azure no son únicos, este identificador podría ser ambiguo. 
    >

* ID: el identificador único global de la aplicación.

    `app("b459b4f6-912x-46d5-9cb1-b43069212ab4").requests | count`

* Identificador de recurso de Azure: identidad única definida por Azure de la aplicación. Se usa cuando el nombre del recurso es ambiguo. El formato es: */subscriptions/IdSuscripción/resourcegroups/grupoDeRecursos/providers/microsoft.OperationalInsights/components/nombreDeComponente*.  

    Por ejemplo: 
    ```
    app("/subscriptions/b459b4f6-912x-46d5-9cb1-b43069212ab4/resourcegroups/Fabrikam/providers/microsoft.insights/components/fabrikamapp").requests | count
    ```

### <a name="performing-a-query-across-multiple-resources"></a>Realizar una consulta en varios recursos
Puede consultar varios recursos desde cualquiera de las instancias de recursos; estas pueden ser áreas de trabajo y aplicaciones combinadas.
    
Ejemplo de consulta en dos áreas de trabajo:    
    ```
    union Update, workspace("contosoretail-it").Update, workspace("b459b4u5-912x-46d5-9cb1-p43069212nb4").Update
    | where TimeGenerated >= ago(1h)
    | where UpdateState == "Needed"
    | summarize dcount(Computer) by Classification
    ```

## <a name="next-steps"></a>Pasos siguientes

Revise la [referencia sobre búsqueda de registros de Log Analytics](https://docs.loganalytics.io/docs/Language-Reference) para ver todas las opciones de sintaxis de consulta en Log Analytics.    
