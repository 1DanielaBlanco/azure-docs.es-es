---
title: 'Exportación y eliminación de los datos: Custom Vision Service'
titlesuffix: Azure Cognitive Services
description: Obtenga información acerca de cómo exportar o eliminar los datos en Custom Vision Service.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 05/25/2018
ms.author: pafarley
ms.openlocfilehash: 7f27170a5e97d7860bcb059f16273b38757de747
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/07/2019
ms.locfileid: "55867042"
---
# <a name="export-or-delete-user-data-in-custom-vision"></a>Exportación o eliminación de los datos del usuario en Custom Vision

Content Moderator recopila los datos del usuario para prestar el servicio, pero los clientes tienen control total sobre la visualización, la exportación y la eliminación de sus datos mediante Custom Vision Service [Training API](https://go.microsoft.com/fwlink/?linkid=865446).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Para obtener más información acerca de cómo exportar y eliminar datos del usuario en Custom Vision, consulte la siguiente tabla.

| Datos | Operación de exportación | Elimina una operación |
| ---- | ---------------- | ---------------- |
| Información de cuenta (claves de suscripción) | [GetAccountInfo](https://go.microsoft.com/fwlink/?linkid=865446) | Eliminación mediante Azure Portal (suscripciones de Azure). O bien, mediante el botón "Eliminar cuenta" en la página de configuración de CustomVision.ai (suscripciones de la cuenta de Microsoft). | 
| Detalles de la iteración | [GetIteration](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) |
| Detalles de rendimiento de la iteración | [GetIterationPerformance](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Lista de iteraciones | [GetIterations](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) |
| Proyectos y detalles del proyecto | [GetProject](https://go.microsoft.com/fwlink/?linkid=865446) y [GetProjects](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteProject](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Etiquetas de imagen | [GetTag](https://go.microsoft.com/fwlink/?linkid=865446) y [GetTags](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteTag](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Imágenes | [GetTaggedImages](https://go.microsoft.com/fwlink/?linkid=865446) (proporciona el URI para la descarga de la imagen) y [GetUntaggedImages](https://go.microsoft.com/fwlink/?linkid=865446) (proporciona el URI para la descarga de la imagen) | [DeleteImages](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Modelos exportados | [GetExports](https://go.microsoft.com/fwlink/?linkid=865446) | Eliminación tras la eliminación de la cuenta | 
