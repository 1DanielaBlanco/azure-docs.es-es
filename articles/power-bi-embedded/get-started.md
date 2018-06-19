---
title: Introducción a Microsoft Power BI Embedded | Microsoft Docs
description: Power BI Embedded en su aplicación de inteligencia empresarial
services: power-bi-embedded
documentationcenter: ''
author: markingmyname
manager: kfile
editor: ''
tags: ''
ms.assetid: 4787cf44-5d1c-4bc3-b3fd-bf396e5c1176
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 01/11/2018
ms.author: maghan
ms.openlocfilehash: 7a860742192227e92a14d9bb5622dc9b61f6cb99
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/04/2018
ms.locfileid: "34716030"
---
# <a name="get-started-with-microsoft-power-bi-embedded"></a>Introducción a Microsoft Power BI Embedded

**Power BI Embedded** ofrece a los proveedores independientes de software (ISV) y a los desarrolladores una manera de agregar rápidamente sorprendentes objetos visuales, informes y paneles a sus aplicaciones mediante un modelo de medición por horas basado en la capacidad.

![Diagrama de flujo de inserción](media/get-started/introduction.png)

Power BI Embedded presenta ventajas para los ISV, sus desarrolladores y clientes. Por ejemplo, un ISV puede comenzar a crear objetos visuales de forma gratuita con Power BI Desktop. Los ISV pueden conseguir acceso más rápido al mercado dado que se reducen los esfuerzos de desarrollo de análisis de objetos visuales y destacan con respecto a sus competidores gracias a sus experiencias de datos diferenciadoras. Los ISV también pueden elegir cobrar una prima por el valor adicional creado con análisis insertados.

Por su parte, los desarrolladores pueden centrarse en construir la competencia básica de sus aplicaciones, en lugar de dedicar tiempo a desarrollar objetos visuales y análisis. Además, pueden satisfacer rápidamente las exigencias de informes y paneles del cliente y pueden insertarlos fácilmente con las API y los SDK totalmente documentados. Por último, gracias a la exploración de datos sencilla en sus aplicaciones, los ISV permiten que sus clientes tomen decisiones rápidas orientadas a los datos en contexto y con confianza desde cualquier dispositivo.

## <a name="register-an-application-within-azure-active-directory"></a>Registro de una aplicación en Azure Active Directory

Se necesita una aplicación registrada en Azure Active Directory (AAD) para realizar inserciones en una aplicación personalizada. La aplicación registrada requiere que el inquilino sea un inquilino de Power BI. Un inquilino de Power BI significa que al menos un usuario de la organización se ha registrado en Power BI. Tener un usuario registrado en Power BI permite que las API de Power BI se muestren dentro de la aplicación registrada.

Para más información sobre cómo registrar una aplicación en AAD, consulte [Registro de una aplicación de Azure AD para insertar contenido de Power BI](https://powerbi.microsoft.com/documentation/powerbi-developer-register-app/).

## <a name="embed-content-in-your-application"></a>Inserción de contenido en la aplicación

Una vez que tiene la aplicación registrada en AAD, inserte en ella contenido de Power BI. Inserte contenido mediante la API de REST junto con las API de JavaScript.

Tenemos ejemplos para ayudarle a empezar. Para ver un tutorial de los ejemplos, consulte [Integración de un panel, icono o informe en la aplicación](https://powerbi.microsoft.com/documentation/powerbi-developer-embed-sample-app-owns-data/).

## <a name="get-capacity-and-move-to-production"></a>Obtención de capacidad y movimiento a producción

Cree capacidad de Power BI Embedded en Microsoft Azure para mover la aplicación a producción. Para información sobre cómo crear capacidad, consulte [Create Power BI Embedded capacity in the Azure portal](create-capacity.md) (Creación de capacidad de Power BI Embedded en Azure Portal).

> [!IMPORTANT]
> Debido a que los tokens insertados están pensados únicamente para pruebas de desarrolladores, el número de este tipo de tokens que puede generar una cuenta maestra de Power BI es limitado. [Debe adquirirse una capacidad](https://docs.microsoft.com/power-bi/developer/embedded-faq#technical) para los escenarios de inserción de producción. No hay límite para una generación de tokens de inserción cuando se adquiere una capacidad. Vaya a [Get Available Features](https://msdn.microsoft.com/en-us/library/mt846473.aspx) (Obtener funciones disponibles) para comprobar el uso insertado actual en porcentaje.

Administre la capacidad en el portal de administración de Power BI. Asigne un administrador de áreas de trabajo para que le ayude con las áreas de trabajo de la aplicación. Para obtener más información, consulte [Administración de la capacidad en Power BI Premium y Power BI Embedded](https://powerbi.microsoft.com/documentation/powerbi-admin-premium-manage/).

## <a name="next-steps"></a>Pasos siguientes

Si está listo para crear capacidad de Power BI Embedded, consulte [Create Power BI Embedded capacity in the Azure portal](create-capacity.md) (Creación de capacidad de Power BI Embedded en Azure Portal).

Si busca un tutorial de ejemplos, consulte [Integración de un panel, icono o informe en la aplicación](https://powerbi.microsoft.com/documentation/powerbi-developer-embed-sample-app-owns-data/).

¿Tiene más preguntas? [Pruebe la comunidad de Power BI](http://community.powerbi.com/)
