---
title: 'Paso 1: Creación de un área de trabajo: Azure Machine Learning Studio | Microsoft Docs'
description: 'Paso 1 del tutorial de desarrollo de una solución predictiva: Aprenda a configurar una nueva área de trabajo de Azure Machine Learning Studio.'
services: machine-learning
documentationcenter: ''
author: garyericson
ms.custom: seodec18
ms.author: garye
editor: cgronlun
ms.assetid: b3c97e3d-16ba-4e42-9657-2562854a1e04
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2017
ms.openlocfilehash: bc83fa6e3fa7d5ef31515309f5c1cd0b025c8906
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2018
ms.locfileid: "53256373"
---
# <a name="walkthrough-step-1-create-an-azure-machine-learning-studio-workspace"></a>Paso 1 del tutorial: Creación de un área de trabajo de Azure Machine Learning Studio
Este es el primer paso del tutorial [Desarrollo de una solución de análisis predictiva para la evaluación del riesgo de crédito en Azure Machine Learning](walkthrough-develop-predictive-solution.md).

1. **Creación de un área de trabajo de Machine Learning**
2. [Carga de los datos existentes](walkthrough-2-upload-data.md)
3. [Crear un experimento nuevo](walkthrough-3-create-new-experiment.md)
4. [Entrenamiento y evaluación de los modelos](walkthrough-4-train-and-evaluate-models.md)
5. [Implementación del servicio web](walkthrough-5-publish-web-service.md)
6. [Acceso al servicio web](walkthrough-6-access-web-service.md)

- - -
<!-- This needs to be updated to refer to the new way of creating workspaces in the Ibiza portal -->

Para usar Machine Learning Studio, debe tener un área de trabajo de Microsoft Azure Machine Learning. Esta área de trabajo contiene las herramientas que necesita para crear, administrar y publicar experimentos.  

El administrador de su suscripción de Azure debe crear el área de trabajo y, a continuación, agregarlo como un propietario o colaborador. Para obtener más información, consulte [Creación y uso compartido de un área de trabajo de Azure Machine Learning](create-workspace.md).

Una vez haya creado el área de trabajo, abra Machine Learning Studio ([https://studio.azureml.net/Home](https://studio.azureml.net/Home)). Si tiene más de un área de trabajo, puede seleccionar la que desee en la barra de herramientas de la esquina superior derecha de la ventana.

![Selección del área de trabajo en Studio][2]

> [!TIP]
> Si le han convertido en el propietario del área de trabajo, puede compartir los experimentos en los que esté trabajando invitando a otros al área. Para ello, en Machine Learning Studio, vaya a la página **CONFIGURACIÓN** . Solo necesita la cuenta Microsoft o la cuenta de organización de cada usuario.
> 
> En la página **CONFIGURACIÓN**, haga clic en **USUARIOS** y, después, haga clic en **INVITE MORE USERS** (INVITAR A MÁS USUARIOS) en la parte inferior de la ventana.
> 
> 

- - -
**Siguiente: [Cargar los datos existentes](walkthrough-2-upload-data.md)**

[1]: ./media/walkthrough-1-create-ml-workspace/create1.png
[2]: ./media/walkthrough-1-create-ml-workspace/open-workspace.png
