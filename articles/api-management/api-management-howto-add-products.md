---
title: "Creación y publicación de un producto en Azure API Management"
description: "Obtenga información acerca de cómo crear y publicar productos en Azure API Management."
services: api-management
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 11/19/2017
ms.author: apimpm
ms.openlocfilehash: b9e3127a6b055a1fe013fa91714676a7c56686c5
ms.sourcegitcommit: 83ea7c4e12fc47b83978a1e9391f8bb808b41f97
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/28/2018
---
# <a name="create-and-publish-a-product"></a>Crear y publicar un producto  

En Azure API Management, un producto contiene una o varias API, así como una cuota de uso y los términos de uso. Una vez publicado un producto, los desarrolladores pueden suscribirse al producto y comenzar a usar las API del producto.  

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Crear y publicar un producto
> * Agregar una API al producto

![producto agregado](media/api-management-howto-add-products/added-product.png)

## <a name="prerequisites"></a>requisitos previos

+ Completar la guía de inicio rápido siguiente: [Creación de una instancia de Azure API Management](get-started-create-service-instance.md).
+ Además, completar el tutorial siguiente: [Importación y publicación de la primera API](import-and-publish.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-and-publish-a-product"></a>Creación y publicación de un producto

1. Haga clic en **Productos** en el menú de la izquierda para mostrar la página **Productos**.
2. Haga clic en **+ Product** (+Producto).

    ![producto agregado](media/api-management-howto-add-products/add-product.png)

    Al agregar un producto, se debe proporcionar la siguiente información: 

    |NOMBRE|DESCRIPCIÓN|
    |---|---|
    |Nombre para mostrar|El nombre que desea que aparezca en el **portal para desarrolladores**.|
    |NOMBRE|Un nombre descriptivo del producto.|
    |DESCRIPCIÓN|El campo **Configuración** le permite incluir información detallada sobre el producto, por ejemplo, su finalidad, las API a las que proporciona acceso y otra información útil.|
    |Estado|Presione **Publicado** si desea publicar el producto. Para poder llamar a las API de un producto, este debe publicarse. De forma predeterminada, los nuevos productos no se publican y solo son visibles para el grupo **Administradores**.|
    |Requiere aprobación|Active **Requerir aprobación de suscripción** si desea que un administrador revise y acepte o rechace los intentos de suscripción a este producto. Si la casilla está desactivada, los intentos de suscripción se aprueban automáticamente. |
    |Límite de recuento de suscripciones|Para limitar el número de varias suscripciones simultáneas, escriba el límite de suscripciones. |
    |Términos legales|Puede incluir los términos de uso del producto que deben aceptar los suscriptores para usarlo.|
    |API existentes|Los productos son asociaciones de una o varias API. Puede incluir varias API y ofrecerlas a los desarrolladores mediante el portal para desarrolladores. <br/> Puede agregar una API existente durante la creación del producto. Puede agregar una API al producto más adelante, bien desde la página **Configuración** de los productos o durante la creación de dicha API.|<br/>Los desarrolladores tienen que suscribirse primero a un producto para acceder a la API. Al suscribirse, obtienen una clave de suscripción que funciona con cualquier API de ese producto.<br/> Si creó la instancia de APIM, ya es un administrador, así que, de forma predeterminada, está suscrito a todos los productos.|

3. Haga clic en **Crear** para crear el nuevo producto.

### <a name="add-more-configurations"></a>Adición de más configuraciones

Puede continuar con la configuración del producto después de guardarla si elige la pestaña **Configuración**. 

Vea o agregue suscriptores al producto desde la pestaña **Suscripciones**.

Establezca la visibilidad de un producto para desarrolladores o invitados desde la pestaña **Control de acceso**.

## <a name="add-apis"></a>Incorporación de API a un producto

Los productos son asociaciones de una o varias API. Puede incluir varias API y ofrecerlas a los desarrolladores mediante el portal para desarrolladores. Puede agregar una API existente durante la creación del producto. Puede agregar una API al producto más adelante, bien desde la página **Configuración** de los productos o durante la creación de dicha API.

Los desarrolladores tienen que suscribirse primero a un producto para acceder a la API. Al suscribirse, obtienen una clave de suscripción que funciona con cualquier API de ese producto. Si creó la instancia de APIM, ya es un administrador, así que, de forma predeterminada, está suscrito a todos los productos.

### <a name="add-an-api-to-an-existing-product"></a>Adición de una API a un producto existente

1. Seleccione un producto.
2. Seleccione la pestaña API.
3. Haga clic en **+API**.
4. Elija una API y haga clic en **Crear**.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial aprendió lo siguiente:

> [!div class="checklist"]
> * Crear y publicar un producto
> * Agregar una API al producto

Avance hasta el siguiente tutorial:

> [!div class="nextstepaction"]
> [Create blank API and mock API responses](mock-api-responses.md) (Creación de una API en blanco y simulación de respuestas de API)
