---
title: archivo de inclusión
description: archivo de inclusión
services: service-bus-messaging
author: sethmanheim
ms.service: service-bus-messaging
ms.topic: include
ms.date: 06/29/2018
ms.author: sethm
ms.custom: include file
ms.openlocfilehash: bad7e87cb194fb7d8846e7e675881658a1d33b1e
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/29/2018
ms.locfileid: "37138167"
---
Asegúrese de haber creado antes un espacio de nombres de Service Bus, como se muestra [aquí][namespace-how-to].

1. Inicie sesión en [Azure Portal][azure-portal].
2. En el panel de navegación izquierdo del portal, haga clic en **Service Bus** (si no ve **Service Bus**, haga clic en **Todos los servicios**).
3. Haga clic en el espacio de nombres en el que desea crear la cola. En este caso es **sbnstest1**.
   
    ![Creación de una cola][createqueue1]
4. En la ventana del espacio de nombres, haga clic en **Colas** y, después, en la ventana **Colas**, haga clic en **+ Cola**.
   
    ![Seleccionar Colas][createqueue2]
5. Escriba el **nombre** de la cola y deje los restantes valores con sus valores predeterminados.
   
    ![Seleccionar Nuevo][createqueue3]
6. En la parte inferior de la ventana, haga clic en **Crear**.

[createqueue1]: ./media/service-bus-create-queue-portal/create-queue1.png
[createqueue2]: ./media/service-bus-create-queue-portal/create-queue2.png
[createqueue3]: ./media/service-bus-create-queue-portal/create-queue3.png

[namespace-how-to]: ../articles/service-bus-messaging/service-bus-create-namespace-portal.md
[azure-portal]: https://portal.azure.com
