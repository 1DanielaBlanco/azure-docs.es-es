---
title: archivo de inclusión
description: archivo de inclusión
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: bf9d9368207f8791c33db84e1467d44201d6a5af
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/06/2018
---
En Azure Portal, puede ver el estado de la conexión de una instancia de VPN Gateway de Resource Manager navegando a la conexión. Los pasos siguientes muestran una manera de navegar a su conexión y realizar las comprobaciones necesarias.

1. En [Azure Portal](http://portal.azure.com), haga clic en **Todos los recursos** y navegue a la puerta de enlace de la red virtual.
2. En la hoja de la puerta de enlace de la red virtual, haga clic en **Conexiones**. Puede ver el estado de cada conexión.
3. Haga clic en el nombre de la conexión que desee comprobar para abrir **Essentials**. En Essentials, puede ver más información acerca de la conexión. El valor de **Estado** será "Correcto" y "Conectado" cuando haya realizado una conexión satisfactoria.

  ![Comprobación de la conexión de VPN Gateway mediante Azure Portal](./media/vpn-gateway-verify-connection-portal-rm-include/connection-succeeded.png)