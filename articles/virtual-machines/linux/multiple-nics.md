---
title: Creación de una máquina virtual Linux en Azure con varias NIC | Microsoft Docs
description: Aprenda a crear una máquina virtual Linux con varias NIC conectadas a ella mediante la CLI de Azure 2.0 o las plantillas de Resource Manager.
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
ms.assetid: 5d2d04d0-fc62-45fa-88b1-61808a2bc691
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/26/2017
ms.author: iainfou
ms.openlocfilehash: 2ad04b17fc4c5bafb7fa0b7eea946832430a4f17
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/25/2018
ms.locfileid: "36936758"
---
# <a name="how-to-create-a-linux-virtual-machine-in-azure-with-multiple-network-interface-cards"></a>Cómo crear una máquina virtual Linux en Azure con red varias tarjetas de interfaz de red
Puede crear una máquina virtual (VM) en Azure que tenga asociadas varias interfaces de red virtual (NIC). Un escenario común es tener distintas subredes para la conectividad front-end y back-end o una red dedicada a una solución de supervisión o copia de seguridad. En este artículo describe cómo crear una máquina virtual con varias NIC asociadas a ella y cómo agregar o quitar las NIC de una máquina virtual existente. Diferentes [tamaños de máquina virtual](sizes.md) admiten un número distinto de NIC, así que ajuste el tamaño de su máquina virtual teniendo esto en cuenta.

En este artículo se describe cómo crear una máquina virtual con varias NIC con la CLI de Azure 2.0. 


## <a name="create-supporting-resources"></a>Creación de recursos de apoyo
Instale la última versión de la [CLI de Azure 2.0](/cli/azure/install-az-cli2) e inicie sesión en una cuenta de Azure con [az login](/cli/azure/reference-index#az_login).

En los ejemplos siguientes, reemplace los nombres de parámetros de ejemplo por los suyos propios. Los nombres de parámetro de ejemplo incluyen *myResourceGroup*, *mystorageaccount* y *myVM*.

En primer lugar, cree un grupo de recursos con [az group create](/cli/azure/group#az_group_create). En el ejemplo siguiente, se crea un grupo de recursos denominado *myResourceGroup* en la ubicación *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Cree la red virtual con [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create). En el ejemplo siguiente se crea una red virtual denominada *myVnet* y una subred *mySubnetFrontEnd*:

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnetFrontEnd \
    --subnet-prefix 192.168.1.0/24
```

Cree una subred para el tráfico de back-end con [az network vnet subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create). En el ejemplo siguiente se crea una subred denominada *mySubnetBackEnd*:

```azurecli
az network vnet subnet create \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnetBackEnd \
    --address-prefix 192.168.2.0/24
```

Cree un grupo de seguridad de red con [az network nsg create](/cli/azure/network/nsg#az_network_nsg_create). En el ejemplo siguiente se crea un grupo de seguridad de red denominado *myNetworkSecurityGroup*:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

## <a name="create-and-configure-multiple-nics"></a>Creación y configuración de varias NIC
Creación de dos NIC con [az network nic create](/cli/azure/network/nic#az_network_nic_create). En el ejemplo siguiente se crean dos NIC, denominadas *myNic1* y *myNic2*, conectadas al grupo de seguridad de red, con una NIC conectada con cada subred:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic1 \
    --vnet-name myVnet \
    --subnet mySubnetFrontEnd \
    --network-security-group myNetworkSecurityGroup
az network nic create \
    --resource-group myResourceGroup \
    --name myNic2 \
    --vnet-name myVnet \
    --subnet mySubnetBackEnd \
    --network-security-group myNetworkSecurityGroup
```

## <a name="create-a-vm-and-attach-the-nics"></a>Creación de una máquina virtual y conexión de las NIC
Cuando cree la máquina virtual, especifique las NIC creadas con `--nics`. También debe tener cuidado al seleccionar el tamaño de la máquina virtual. Existen límites para el número total de NIC que se pueden agregar a una máquina virtual. Más información sobre los [tamaños de máquina virtual Linux](sizes.md). 

Cree la máquina virtual con [az vm create](/cli/azure/vm#az_vm_create). En el ejemplo siguiente se crea una máquina virtual denominada *myVM*:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --size Standard_DS3_v2 \
    --admin-username azureuser \
    --generate-ssh-keys \
    --nics myNic1 myNic2
```

Agregue tablas de enrutamiento al sistema operativo invitado completando los pasos descritos en [Cómo crear una máquina virtual Linux en Azure con red varias tarjetas de interfaz de red](#configure-guest-os-for- multiple-nics).

## <a name="add-a-nic-to-a-vm"></a>Adición de una NIC a una máquina virtual
Los pasos anteriores crean una máquina virtual con varias NIC. También puede agregar varias NIC a una máquina virtual existente con la versión 2.0 de la CLI de Azure. Diferentes [tamaños de máquina virtual](sizes.md) admiten un número distinto de NIC, así que ajuste el tamaño de su máquina virtual teniendo esto en cuenta. Si es necesario, puede [cambiar el tamaño de una máquina virtual](change-vm-size.md).

Cree otra NIC con [az network nic create](/cli/azure/network/nic#az_network_nic_create). En el ejemplo siguiente se crea una NIC denominada *myNic3* conectada a la subred de back-end y al grupo de seguridad de red creado en los pasos anteriores:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic3 \
    --vnet-name myVnet \
    --subnet mySubnetBackEnd \
    --network-security-group myNetworkSecurityGroup
```

Para agregar una NIC a una máquina virtual existente, en primer lugar desasigne la máquina virtual con [az vm deallocate](/cli/azure/vm#az_vm_deallocate). En el ejemplo siguiente se desasigna la máquina virtual denominada *myVM*:


```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

Agregue la NIC con [az vm nic add](/cli/azure/vm/nic#az_vm_nic_add). En el ejemplo siguiente se agrega *myNic3* a *myVM*:

```azurecli
az vm nic add \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --nics myNic3
```

Inicie la máquina virtual con [az vm start](/cli/azure/vm#az_vm_start):

```azurecli
az vm start --resource-group myResourceGroup --name myVM
```

Agregue tablas de enrutamiento al sistema operativo invitado completando los pasos descritos en [Cómo crear una máquina virtual Linux en Azure con red varias tarjetas de interfaz de red](#configure-guest-os-for- multiple-nics).

## <a name="remove-a-nic-from-a-vm"></a>Eliminación de una NIC de una máquina virtual
Para quitar una NIC de una máquina virtual existente, en primer lugar desasigne la máquina virtual con [az vm deallocate](/cli/azure/vm#az_vm_deallocate). En el ejemplo siguiente se desasigna la máquina virtual denominada *myVM*:

```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

Quite la NIC con [az vm nic remove](/cli/azure/vm/nic#az_vm_nic_remove). En el ejemplo siguiente se quita *myNic3* de *myVM*:

```azurecli
az vm nic remove \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --nics myNic3
```

Inicie la máquina virtual con [az vm start](/cli/azure/vm#az_vm_start):

```azurecli
az vm start --resource-group myResourceGroup --name myVM
```


## <a name="create-multiple-nics-using-resource-manager-templates"></a>Creación de varias NIC con plantillas de Resource Manager
Las plantillas de Azure Resource Manager emplean archivos JSON declarativos para definir el entorno. Puede leer la [introducción a Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md). Las plantillas de Resource Manager ofrecen una manera de crear varias instancias de un recurso durante la implementación; por ejemplo, se pueden crear varias NIC. Utilizará el comando *copy* para especificar el número de instancias que se crearán:

```json
"copy": {
    "name": "multiplenics"
    "count": "[parameters('count')]"
}
```

Más información sobre la [creación de varias instancias mediante *copia*](../../resource-group-create-multiple.md). 

También puede utilizar `copyIndex()` para anexar un número a un nombre de recurso, lo que le permite crear `myNic1`, `myNic2`, etc. A continuación se muestra un ejemplo de cómo anexar el valor de índice:

```json
"name": "[concat('myNic', copyIndex())]", 
```

Puede leer un ejemplo completo de [cómo crear varias NIC con plantillas de Resource Manager](../../virtual-network/template-samples.md).

Agregue tablas de enrutamiento al sistema operativo invitado completando los pasos descritos en [Cómo crear una máquina virtual Linux en Azure con red varias tarjetas de interfaz de red](#configure-guest-os-for- multiple-nics).

## <a name="configure-guest-os-for-multiple-nics"></a>Configuración del sistema operativo invitado para varias NIC
Al agregar varios NIC a una máquina virtual de Linux, debe crear reglas de enrutamiento. Estas reglas permiten a la máquina virtual enviar y recibir tráfico que pertenece a una NIC específica. En caso contrario, el tráfico que pertenece a *eth1* no se puede procesar correctamente debido a la ruta predeterminada definida.

Para corregir este problema de enrutamiento, agregue primero dos tablas de enrutamiento a */etc/iproute2/rt_tables* tal como se indica a continuación:

```bash
echo "200 eth0-rt" >> /etc/iproute2/rt_tables
echo "201 eth1-rt" >> /etc/iproute2/rt_tables
```

Para hacer que el cambio sea persistente y se aplique durante la activación de la pila de red, es necesario modificar los archivos */etc/sysconfig/network-srcipts/ifcfg-eth0* y */etc/sysconfig/network-scripts/ifcfg-eth1*. Cambie la línea *"NM_CONTROLLED = yes"* por *"NM_CONTROLLED = no"*. Sin este paso, las reglas o rutas adicionales no se aplicarán automáticamente.
 
El siguiente paso es extender las tablas de enrutamiento. Supongamos que tenemos la siguiente configuración:

*Enrutamiento*

```bash
default via 10.0.1.1 dev eth0 proto static metric 100
10.0.1.0/24 dev eth0 proto kernel scope link src 10.0.1.4 metric 100
10.0.1.0/24 dev eth1 proto kernel scope link src 10.0.1.5 metric 101
168.63.129.16 via 10.0.1.1 dev eth0 proto dhcp metric 100
169.254.169.254 via 10.0.1.1 dev eth0 proto dhcp metric 100
```

*Interfaces*

```bash
lo: inet 127.0.0.1/8 scope host lo
eth0: inet 10.0.1.4/24 brd 10.0.1.255 scope global eth0    
eth1: inet 10.0.1.5/24 brd 10.0.1.255 scope global eth1
```

Debe crear los siguientes archivos y agregar las reglas y rutas correspondientes a cada uno de ellos:

- */etc/sysconfig/network-scripts/rule-eth0*

    ```bash
    from 10.0.1.4/32 table eth0-rt
    to 10.0.1.4/32 table eth0-rt
    ```

- */etc/sysconfig/network-scripts/route-eth0*

    ```bash
    10.0.1.0/24 dev eth0 table eth0-rt
    default via 10.0.1.1 dev eth0 table eth0-rt
    ```

- */etc/sysconfig/network-scripts/rule-eth1*

    ```bash
    from 10.0.1.5/32 table eth1-rt
    to 10.0.1.5/32 table eth1-rt
    ```

- */etc/sysconfig/network-scripts/route-eth1*

    ```bash
    10.0.1.0/24 dev eth1 table eth1-rt
    default via 10.0.1.1 dev eth1 table eth1-rt
    ```

Para aplicar los cambios, reinicie el servicio de *red* tal como se indica a continuación:

```bash
systemctl restart network
```

Las reglas de enrutamiento ahora están en su lugar y ya puede conectar con cualquier interfaz que necesite.


## <a name="next-steps"></a>Pasos siguientes
Revise los [tamaños de máquina virtual Linux](sizes.md) al intentar crear una máquina virtual con varias NIC. Preste atención al número máximo de NIC que admite cada tamaño de máquina virtual. 
