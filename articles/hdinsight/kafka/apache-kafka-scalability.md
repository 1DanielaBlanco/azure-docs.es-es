---
title: Aumento de escala de Apache Kafka en Azure HDInsight
description: Aprenda a configurar discos administrados para clústeres de Apache Kafka en Azure HDInsight para aumentar la escalabilidad.
services: hdinsight
ms.service: hdinsight
author: jasonwhowell
ms.author: jasonh
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/30/2018
ms.openlocfilehash: f74bc5732bbcf05423698603113998204b7514a9
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2018
ms.locfileid: "43046681"
---
# <a name="configure-storage-and-scalability-for-apache-kafka-on-hdinsight"></a>Configuración del almacenamiento y la escalabilidad de Apache Kafka en HDInsight

Aprenda a configurar el número de discos administrados usados por Apache Kafka en HDInsight.

Kafka en HDInsight utiliza el disco local de las máquinas virtuales del clúster de HDInsight. Como Kafka tiene muchas E/S, [Azure Managed Disks](../../virtual-machines/windows/managed-disks-overview.md) se utiliza para proporcionar un alto rendimiento y un mayor espacio de almacenamiento por nodo. Si los discos duros virtuales (VHD) tradicionales se utilizaron para Kafka, cada nodo se limita a 1 TB. Con Managed Disks, puede utilizar varios discos para lograr hasta 16 TB para cada nodo del clúster.

El diagrama siguiente proporciona una comparación entre Kafka en HDInsight antes de usar Managed Disks y Kafka en HDInsight ya con este:

![Diagrama que muestra a Kafka en HDInsight con un solo disco duro virtual por cada máquina virtual frente al uso de varios discos administrados por máquina virtual](./media/apache-kafka-scalability/kafka-with-managed-disks-architecture.png)

## <a name="configure-managed-disks-azure-portal"></a>Configuración de Managed Disks: Azure Portal

1. Siga los pasos que se explican en [Creación de un clúster de HDInsight](../hdinsight-hadoop-create-linux-clusters-portal.md) para comprender los pasos habituales para crear un clúster mediante el portal. No termine el proceso de creación del portal.

2. En la sección __Tamaño del clúster__, use el campo __Disks per worker node__ (Discos por nodo de trabajo) para configurar el número de discos.

    > [!NOTE]
    > El tipo de disco administrado puede ser __Estándar__ (HDD) o __Premium__ (SSD). Los discos Premium se utilizan con máquinas virtuales de las series DS y GS. Todos los otros tipos de máquina virtual usan discos estándar.

    ![Imagen de la sección Tamaño de clúster con los discos por nodo de trabajo resaltados](./media/apache-kafka-scalability/set-managed-disks-portal.png)

## <a name="configure-managed-disks-resource-manager-template"></a>Configuración de Managed Disks: Plantilla de Resource Manager

Para controlar el número de discos usados por los nodos de trabajo en un clúster de Kafka, utilice la siguiente sección de la plantilla:

```json
"dataDisksGroups": [
    {
        "disksPerNode": "[variables('disksPerWorkerNode')]"
    }
    ],
```

Puede encontrar una plantilla completa que muestra cómo configurar discos administrados en [https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-mirror-cluster-in-vnet-v2.1.json](https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-mirror-cluster-in-vnet-v2.1.json).

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre cómo trabajar con Kafka en HDInsight, consulte los documentos siguientes:

* [Uso de MirrorMaker para crear una réplica de Kafka en HDInsight](apache-kafka-mirroring.md)
* [Uso de Apache Kafka con Storm en HDInsight](../hdinsight-apache-storm-with-kafka.md)
* [Uso de Apache Spark con Kafka en HDInsight](../hdinsight-apache-spark-with-kafka.md)
* [Conexión a Kafka a través de una instancia de Azure Virtual Network](apache-kafka-connect-vpn-gateway.md)

* [Blog de HDInsight en Managed Disks con Kafka](https://azure.microsoft.com/blog/announcing-public-preview-of-apache-kafka-on-hdinsight-with-azure-managed-disks/)
