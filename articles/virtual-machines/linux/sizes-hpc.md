---
title: 'Tamaños de las máquinas virtuales Linux en Azure: HPC | Microsoft Docs'
description: Enumera los tamaños diferentes disponibles para las máquinas virtuales de informática de alto rendimiento Linux en Azure. Se proporciona información sobre el número de unidades vCPU, discos de datos y NIC, así como sobre el rendimiento de almacenamiento y el ancho de banda de red para los tamaños de esta serie.
services: virtual-machines-linux
documentationcenter: ''
author: jonbeck7
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/06/2018
ms.author: jonbeck
ms.openlocfilehash: 748cb4612b2b5aed26ba8197cfad0782f2645e1e
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/10/2018
ms.locfileid: "37902136"
---
# <a name="high-performance-compute-virtual-machine-sizes"></a>Tamaños de máquina virtual de proceso de alto rendimiento

[!INCLUDE [virtual-machines-common-sizes-hpc](../../../includes/virtual-machines-common-sizes-hpc.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]


### <a name="mpi"></a>MPI 

Se admiten solo las versiones Intel MPI 5.x. Las versiones posteriores (2017 y 2018) de la biblioteca en tiempo de ejecución de Intel MPI no son compatibles con los controladores Azure Linux RDMA.


### <a name="distributions"></a>Distribuciones
 
Implemente una máquina virtual de proceso intensivo desde una de las imágenes de Azure Marketplace que sea compatible con la conectividad RDMA:
  
* **Ubuntu**: Ubuntu Server 16.04 LTS. Configure controladores RDMA en la máquina virtual y regístrese en Intel para descargar Intel MPI:

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../../includes/virtual-machines-common-ubuntu-rdma.md)]

* **SUSE Linux Enterprise Server**: SLES 12 SP3 para HPC, SLES 12 SP3 para HPC (Premium), SLES 12 SP1 para HPC, SLES 12 SP1 para HPC (Premium). Los controladores RDMA se instalan y se distribuyen los paquetes Intel MPI en la máquina virtual. Instale MPI mediante la ejecución del comando siguiente:

  ```bash
  sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
  ```
    
* **HPC basada en CentOS**: HPC basada en CentOS 6.5 o una versión posterior (para H-series, se recomienda la versión 7.1 o posterior). Los controladores RDMA e Intel MPI 5.1 están instalados en la máquina virtual.  
 
  > [!NOTE]
  > En las imágenes de HPC basadas en CentOS, las actualizaciones del núcleo están deshabilitadas en el archivo de configuración **yum** . Esto se debe a que los controladores Linux RDMA se distribuyen en forma de paquete RPM y sus actualizaciones de estos podrían no funcionar si se actualiza el kernel.
  > 
 
### <a name="cluster-configuration"></a>Configuración del clúster 
    
Se necesita configuración adicional del sistema para ejecutar trabajos MPI en máquinas virtuales en clúster. Por ejemplo, en un clúster de máquinas virtuales, debe establecer la confianza entre los nodos de proceso. Para configuraciones típicas, consulte [Configuración de un clúster de Linux RDMA para ejecutar aplicaciones MPI](classic/rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

### <a name="network-topology-considerations"></a>Consideraciones sobre la topología de red
* En las máquinas virtuales con Linux compatibles con RDMA de Azure, Eth1 se reserva para el tráfico de red RDMA. No cambie ninguna configuración Eth1 ni ninguna información del archivo de configuración que haga referencia a esta red. Eth0 se reserva para el tráfico de red regular de Azure.

* La red RDMA en Azure reserva el espacio de direcciones 172.16.0.0/16. 


## <a name="using-hpc-pack"></a>Uso de HPC Pack
[HPC Pack](https://technet.microsoft.com/library/jj899572.aspx), la solución gratuita de administración de clústeres y trabajos de HPC de Microsoft, es una opción para usar las instancias de proceso intensivo con Linux. Las últimas versiones de HPC Pack admiten varias distribuciones de Linux en nodos de ejecución implementados en máquinas virtuales de Azure, administradas por un nodo principal de Windows Server. Con los nodos de proceso de Linux compatibles con RDMA que ejecutan Intel MPI, HPC Pack puede programar y ejecutar aplicaciones que tienen acceso a la red RDMA de Linux MPI. Consulte [Introducción a los nodos de proceso de Linux en un clúster de HPC Pack en Azure](classic/hpcpack-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

## <a name="other-sizes"></a>Otros tamaños
- [Uso general](sizes-general.md)
- [Proceso optimizado](sizes-compute.md)
- [Memoria optimizada](sizes-memory.md)
- [Almacenamiento optimizado](sizes-storage.md)
- [GPU](../windows/sizes-gpu.md)
- [Generaciones anteriores](sizes-previous-gen.md)

## <a name="next-steps"></a>Pasos siguientes

- Para empezar a implementar y usar los tamaños de proceso intensivo con RDMA en Linux, consulte [Configuración de un clúster de Linux RDMA para ejecutar aplicaciones MPI](classic/rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

- Obtenga más información sobre cómo las [unidades de proceso de Azure (ACU)](acu.md) pueden ayudarlo a comparar el rendimiento en los distintos SKU de Azure.




