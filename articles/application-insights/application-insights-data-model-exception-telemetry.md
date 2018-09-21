---
title: 'Modelo de datos de telemetría de Azure Application Insights: telemetría de excepciones | Microsoft Docs'
description: Modelo de datos de Application Insights para la telemetría de excepciones
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.author: mbullwin
ms.openlocfilehash: 88e521a04d69d4ca169e33a80ac15620568c5282
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/12/2018
ms.locfileid: "35638360"
---
# <a name="exception-telemetry-application-insights-data-model"></a>Telemetría de excepciones: modelo de datos de Application Insights

En [Application Insights](app-insights-overview.md), una instancia de Exception representa una excepción controlada o no controlada que se produjo durante la ejecución de la aplicación supervisada.

## <a name="problem-id"></a>Identificador del problema

Identificador de dónde se produjo la excepción en el código. Se usa para el agrupamiento de las excepciones. Normalmente es una combinación del tipo de excepción y una función de la pila de llamadas.

Longitud máxima: 1024 caracteres

## <a name="severity-level"></a>Nivel de gravedad

El nivel de gravedad del seguimiento. El valor puede ser `Verbose`, `Information`, `Warning`, `Error`, `Critical`.

## <a name="exception-details"></a>Detalles de la excepción

(Se ampliará)

## <a name="custom-properties"></a>Propiedades personalizadas

[!INCLUDE [application-insights-data-model-properties](../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Medidas personalizadas

[!INCLUDE [application-insights-data-model-measurements](../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>Pasos siguientes

- Vea [modelo de datos](application-insights-data-model.md) para los tipos y el modelo de datos de Application Insights.
- Obtenga información para el [diagnóstico de excepciones en aplicaciones web con Application Insights](app-insights-asp-net-exceptions.md).
- Consulte las [plataformas](app-insights-platforms.md) compatibles con Application Insights.
