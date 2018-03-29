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
ms.openlocfilehash: e021a1b109f735dee16d75c05c26ab35e599a3d9
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/23/2018
---
### <a name="to-view-local-network-gateways"></a>Para ver las puertas de enlace de la red local

Para ver una lista de las puertas de enlace de red local, use el comando [az network local-gateway list](https://docs.microsoft.com/cli/azure/network/local-gateway#az_network_local_gateway_list).

```azurecli
az network local-gateway list --resource-group TestRG1
```

[!INCLUDE [modify-prefix](vpn-gateway-modify-ip-prefix-cli-include.md)]

[!INCLUDE [modify-gateway-IP](vpn-gateway-modify-lng-gateway-ip-cli-include.md)]

### <a name="to-verify-the-shared-key-values"></a>Para comprobar los valores de la clave compartida

Compruebe que el valor de la clave compartida es el mismo valor que utilizó para la configuración del dispositivo VPN. Si no es así, ejecute de nuevo la conexión con el valor del dispositivo, o actualice el dispositivo con el valor devuelto. Los valores deben coincidir. Para ver la clave compartida, use [az network vpn-connection-list](https://docs.microsoft.com/cli/azure/network/vpn-connection#az_network_vpn_connection_list).

```azurecli
az network vpn-connection shared-key show --connection-name VNet1toSite2 --resource-group TestRG1
```
### <a name="to-view-the-vpn-gateway-public-ip-address"></a>Para ver la dirección IP pública de la puerta de enlace de VPN

Para buscar la dirección IP pública de la puerta de enlace de red virtual, use el comando [az network public-ip list](https://docs.microsoft.com/cli/azure/network/public-ip#az_network_public_ip_list). Para facilitar la lectura, la salida de este ejemplo tiene el formato preciso para mostrar la lista de direcciones IP públicas en formato de tabla.

```azurecli
az network public-ip list --resource-group TestRG1 --output table
```
