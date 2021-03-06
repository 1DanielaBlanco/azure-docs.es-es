---
title: Uso del portal de Azure Stack | Microsoft Docs
description: Obtenga información sobre cómo acceder al portal del usuario y usar el mismo en Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2019
ms.author: mabrigg
ms.reviewer: efemmano
ms.lastreviewed: 01/25/2019
ms.openlocfilehash: 026e37534169fda8dbbf5a568f72046bc6ff55f7
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/30/2019
ms.locfileid: "55252054"
---
# <a name="use-the-azure-stack-portal"></a>Uso del portal de Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

Puede usar el portal de Azure Stack para suscribirse a ofertas públicas y utilizar los servicios que proporcionan dichas ofertas. Si ha utilizado la versión general de Azure Portal, ya está familiarizado con la manera en que funciona el sitio.

## <a name="access-the-portal"></a>Acceso al portal

El operador de Azure Stack (un proveedor de servicios o un administrador de su organización), le permitirá conocer la dirección URL correcta para acceder al portal.

- En un sistema integrado, la dirección URL varía en función de la región y el nombre de dominio externo del operador y tendrá el formato https://portal.&lt;*región*&gt;.&lt;*FQDN*&gt;.
- Si usa el Kit de desarrollo de Azure Stack, la dirección del portal es https://portal.local.azurestack.external.
- La zona horaria predeterminada para todas las implementaciones de Azure Stack se establece conforme a la Hora universal coordinada (UTC). Aunque puede seleccionar una zona horaria al instalar Azure Stack, se revertirá automáticamente a la hora UTC de forma predeterminada durante la instalación.

## <a name="customize-the-dashboard"></a>Personalización del panel

El panel contiene un conjunto predeterminado de iconos. Puede seleccionar **Edit dashboard** (Editar panel) para modificar el panel predeterminado o **New dashboard** (Nuevo panel) para crear un panel personalizado. Puede personalizar fácilmente un panel agregando o quitando iconos. Por ejemplo, para agregar un icono de Compute, seleccione **+ Crear un recurso**. Haga clic con el botón derecho en **Compute** y, luego, seleccione **Pin to dashboard** (Anclar al panel).

![Captura de pantalla del portal del usuario de Azure Stack](media/azure-stack-use-portal/userportal.png)

## <a name="create-subscription-and-browse-available-resources"></a>Creación de una suscripción y examen de los recursos disponibles

Si todavía no tiene una suscripción, lo primero que debe hacer es suscribirse a una oferta. Luego, puede examinar los recursos disponibles. Para examinar y crear recursos, use cualquiera de las siguientes estrategias:

- Seleccione el icono **Marketplace** del panel.
- En el icono **Todos los recursos**, seleccione **Crear recursos**.
- En el panel de navegación izquierdo, seleccione **+ Crear un recurso**.

## <a name="learn-how-to-use-available-services"></a>Aprender a usar los servicios disponibles

Si necesita instrucciones sobre cómo usar los servicios disponibles, puede haber diferentes opciones disponibles.

- Su organización o un proveedor de servicios puede proporcionar su propia documentación, que suele ser el caso si ofrecen servicios o aplicaciones personalizados.
- Las aplicaciones de terceros tienen su propia documentación.
- Para servicios coherentes con Azure, es muy recomendable que primero revise la documentación de Azure Stack. Para acceder a la documentación de usuario de Azure Stack, seleccione el icono Ayuda y luego seleccione **Ayuda y soporte técnico**.

    ![Ayuda y opción de soporte técnico en la interfaz de usuario](media/azure-stack-use-portal/HelpAndSupport.png)

    En concreto, se recomienda que revise los artículos siguientes para empezar a trabajar:

    - [Consideraciones clave: uso de servicios o compilación de aplicaciones para Azure Stack](azure-stack-considerations.md)
    - En la sección **Uso de servicios** de la documentación, hay un artículo de consideraciones para cada servicio. En la página de consideraciones se describen las diferencias entre el servicio que se ofrece en Azure y el mismo servicio pero ofrecido en Azure Stack. Para un ejemplo, vea [VM considerations](azure-stack-vm-considerations.md) (Consideraciones para VM). Puede haber otra información en la sección **Uso de servicios** que sea única para Azure Stack.

      Puede usar la documentación de Azure como referencia general para un servicio, pero debe tener en cuenta estas diferencias. Sepa que los vínculos de la documentación del icono **Tutoriales de inicio rápido** apuntan a la documentación de Azure.

## <a name="get-support"></a>Obtención de soporte técnico

Si necesita ayuda, póngase en contacto con su organización o proveedor de servicios.

Si usa el Kit de desarrollo de Azure Stack, el [foro de Azure Stack](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack) es el único origen de soporte técnico.

## <a name="next-steps"></a>Pasos siguientes

[Consideraciones clave: uso de servicios o compilación de aplicaciones para Azure Stack](azure-stack-considerations.md)
