---
title: Información acerca del almacenamiento de discos no administrados (blobs de página) y administrados de las VM de Linux de Microsoft Azure | Microsoft Docs
description: Obtenga información acerca de los conceptos básicos del almacenamiento de discos no administrados (blobs de página) y administrados de las máquinas virtuales de Linux en Azure.
services: virtual-machines-linux,storage
author: roygara
ms.service: virtual-machines-linux
ms.tgt_pltfrm: linux
ms.topic: article
ms.date: 11/15/2017
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 07d26590799f169e8e252557287b5c7e0003ea87
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/31/2019
ms.locfileid: "55469420"
---
# <a name="about-disks-storage-for-azure-linux-vms"></a>Acerca del almacenamiento de discos para VM de Linux en Azure
Como cualquier otro equipo, las máquinas virtuales de Azure usan discos como un lugar para almacenar un sistema operativo, aplicaciones y datos. Todas las máquinas virtuales de Azure tienen al menos dos discos: un disco del sistema operativo Linux y un disco temporal. El disco de sistema operativo se crea a partir de una imagen, y tanto el disco de sistema operativo como la imagen son discos duros virtuales (VHD) almacenados en una cuenta de Azure Storage. Las máquinas virtuales también pueden tener uno o más discos de datos que también se almacenan en discos duros virtuales.

En este artículo, se tratan los diferentes usos de los discos y después se describen los diferentes tipos de discos que puede crear y utilizar. Este artículo también está disponible para [máquinas virtuales Windows](../windows/about-disks-and-vhds.md).

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="disks-used-by-vms"></a>Discos usados por las máquinas virtuales

Observemos cómo las máquinas virtuales utilizan los discos.

## <a name="operating-system-disk"></a>Disco del sistema operativo

Cada máquina virtual tiene un disco de sistema operativo acoplado. Está registrado como unidad SATA y etiquetado de forma predeterminada como /dev/sda. Este disco tiene una capacidad máxima de 2048 gigabytes (GB).

## <a name="temporary-disk"></a>Disco temporal

Cada máquina contiene un disco temporal. El disco temporal proporciona almacenamiento a corto plazo para aplicaciones y procesos, y está destinado únicamente a almacenar datos como archivos de paginación o de intercambio. Los datos del disco temporal pueden perderse durante un [evento de mantenimiento](../windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#understand-vm-reboots---maintenance-vs-downtime) o cuando [vuelva a implementar una máquina virtual](../windows/redeploy-to-new-node.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Durante un reinicio estándar de la máquina virtual, los datos de la unidad temporal deben conservarse. En cambio, hay casos donde los datos pueden no conservarse, como al moverse a un nuevo host. En consecuencia, todos los datos de la unidad temporal no deben ser datos que sean fundamentales para el sistema.

En las máquinas virtuales de Linux, el disco es normalmente **/dev/sdb** y lo formatea y lo monta en **/mnt** el agente de Linux de Azure. El tamaño del disco temporal varía, según el tamaño de la máquina virtual. Para más información, consulte [Tamaños de las máquinas virtuales Linux en Azure](../windows/sizes.md).

## <a name="data-disk"></a>Disco de datos

Un disco de datos es un disco duro virtual (VHD) que se adjunta a una máquina virtual para almacenar datos de aplicaciones u otros datos que necesita mantener. Los discos de datos se registran como unidades SCSI y se etiquetan con una letra elegida por usted. Cada disco de datos tiene una capacidad máxima de 4095 GB. El tamaño de la máquina virtual determina cuántos discos de datos puede conectar y el tipo de almacenamiento que puede usar para hospedar los discos.

> [!NOTE]
> Para obtener más información sobre las capacidades de las máquinas virtuales, consulte [Tamaños de las máquinas virtuales de Linux](./sizes.md).

Azure crea un disco del sistema operativo cuando se crea una máquina virtual desde una imagen. Si usa una imagen que incluye discos de datos, Azure también crea los discos de datos al crear la máquina virtual. De lo contrario, agregue discos de datos después de crear la máquina virtual.

Puede agregar discos de datos a una máquina virtual en cualquier momento **conectando** el disco a la máquina virtual. Puede usar un VHD cargado o copiado por usted en la cuenta de almacenamiento o uno que Azure cree para usted. Al adjuntar un disco de datos, el archivo de VHD se asocia a la máquina virtual, y se coloca una "concesión" en dicho VHD para que no pueda eliminarse del almacenamiento mientras esté adjunto.

[!INCLUDE [storage-about-vhds-and-disks-windows-and-linux](../../../includes/storage-about-vhds-and-disks-windows-and-linux.md)]

Para consultar los tamaños en versión preliminar, vea nuestras [preguntas más frecuentes](faq-for-disks.md#new-disk-sizes-managed-and-unmanaged) para obtener información sobre en qué regiones están disponibles.

## <a name="troubleshooting"></a>solución de problemas
[!INCLUDE [virtual-machines-linux-lunzero](../../../includes/virtual-machines-linux-lunzero.md)]

## <a name="next-steps"></a>Pasos siguientes

* [Conecte un disco](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) para agregar almacenamiento adicional para la máquina virtual.
* [Cree una instantánea](snapshot-copy-managed-disk.md).
* [Convierta la máquina virtual en discos administrados](convert-unmanaged-to-managed-disks.md).
