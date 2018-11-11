---
title: Log Analytics para Apache Kafka en Azure HDInsight
description: Obtenga información acerca de cómo usar Log Analytics para analizar los registros de clúster de Apache Kafka en Azure HDInsight.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/15/2018
ms.openlocfilehash: 809196411df56c7448a9d0f983c17c60d5d3b4ae
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2018
ms.locfileid: "51015424"
---
# <a name="analyze-logs-for-apache-kafka-on-hdinsight"></a>Análisis de registros para Apache Kafka en HDInsight

Obtenga información acerca de cómo usar Log Analytics para analizar los registros generados por Apache Kafka en Azure HDInsight.

## <a name="enable-log-analytics-for-kafka"></a>Habilitación de Log Analytics para Kafka

Los pasos para habilitar Log Analytics para HDInsight son los mismos para todos los clústeres de HDInsight. Use los vínculos siguientes para aprender a crear y configurar los servicios necesarios:

1. Cree un área de trabajo de Log Analytics. Para obtener más información, consulte el documento [Introducción a un área de trabajo de Log Analytics](https://docs.microsoft.com/azure/log-analytics).

2. Cree Kafka en un clúster de HDInsight. Para obtener más información, consulte el documento [Introducción a Apache Kafka (versión preliminar) en HDInsight](apache-kafka-get-started.md).

3. Configure el clúster de Kafka para usar Log Analytics. Para obtener más información, vea el documento [Use Log Analytics to monitor HDInsight](../hdinsight-hadoop-oms-log-analytics-tutorial.md) (Uso de Log Analytics para supervisar HDInsight).

    > [!NOTE]
    > También puede configurar el clúster para usar Log Analytics mediante el cmdlet `Enable-AzureRmHDInsightOperationsManagementSuite`. Este cmdlet requiere la información siguiente:
    >
    > * El nombre del clúster de HDInsight.
    > * El identificador de área de trabajo de Log Analytics. Puede encontrar el identificador de área de trabajo de su área de trabajo de Log Analytics.
    > * La clave principal para la conexión de Log Analytics. Para encontrar la clave principal, abra el área de trabajo en Azure Portal, seleccione __Configuración avanzada__ en el menú de la izquierda. En Configuración avanzada, seleccione __Orígenes conectados__>__Servidores Linux__.


> [!IMPORTANT]
> Pueden pasar unos 20 minutos hasta que los datos estén disponibles para Log Analytics.

## <a name="query-logs"></a>Registros de consultas

1. En [Azure Portal](https://portal.azure.com), seleccione el área de trabajo de Log Analytics.

2. Seleccione __Búsqueda de registros__. Aquí podrá buscar los datos recopilados de Kafka. A continuación se presentan algunos ejemplos de búsqueda:

    * Uso de disco: `Perf | where ObjectName == "Logical Disk" and CounterName == "Free Megabytes" and InstanceName == "_Total" and ((Computer startswith_cs "hn" and Computer contains_cs "-") or (Computer startswith_cs "wn" and Computer contains_cs "-")) | summarize AggregatedValue = avg(CounterValue) by Computer, bin(TimeGenerated, 1h)`

    * Uso de CPU: `Perf | where CounterName == "% Processor Time" and InstanceName == "_Total" and ((Computer startswith_cs "hn" and Computer contains_cs "-") or (Computer startswith_cs "wn" and Computer contains_cs "-")) | summarize AggregatedValue = avg(CounterValue) by Computer, bin(TimeGenerated, 1h)`

    * Mensajes entrantes por segundo: `metrics_kafka_CL | where ClusterName_s == "your_kafka_cluster_name" and InstanceName_s == "kafka-BrokerTopicMetrics-MessagesInPerSec-Count" | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_MessagesInPerSec_Count_value_d) by HostName_s, bin(TimeGenerated, 1h)`

    * Bytes entrantes por segundo: `metrics_kafka_CL | where HostName_s == "wn0-kafka" and InstanceName_s == "kafka-BrokerTopicMetrics-BytesInPerSec-Count" | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_BytesInPerSec_Count_value_d) by bin(TimeGenerated, 1h)`

    * Bytes salientes por segundo: `metrics_kafka_CL | where ClusterName_s == "your_kafka_cluster_name" and InstanceName_s == "kafka-BrokerTopicMetrics-BytesOutPerSec-Count" | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_BytesOutPerSec_Count_value_d) by bin(TimeGenerated, 1h)`

    > [!IMPORTANT]
    > Reemplace los valores de consulta por la información específica de su clúster. Por ejemplo, `ClusterName_s` debe establecerse en el nombre del clúster. `HostName_s` debe establecerse en el nombre de dominio de un nodo de trabajo en el clúster.

    También puede escribir `*` para buscar todos los tipos registrados. Actualmente están disponibles los registros siguientes para las consultas:

    | Tipo de registro | DESCRIPCIÓN |
    | ---- | ---- |
    | log\_kafkaserver\_CL | Kafka broker server.log |
    | log\_kafkacontroller\_CL | Kafka broker controller.log |
    | metrics\_kafka\_CL | Kafka JMX metrics |

    ![Imagen de la búsqueda de uso de CPU](./media/apache-kafka-log-analytics-operations-management/kafka-cpu-usage.png)
 
 ## <a name="next-steps"></a>Pasos siguientes

Para más información sobre Log Analytics, consulte el documento [Introducción a un área de trabajo de Log Analytics](../../log-analytics/log-analytics-get-started.md).

Para obtener información sobre cómo trabajar con Kafka, consulte los documentos siguientes:

 * [Mirror Kafka between HDInsight clusters](apache-kafka-mirroring.md) (Reflejar Kafka entre clústeres de HDInsight)
 * [Increase the scalability of Kafka on HDInsight](apache-kafka-scalability.md) (Aumentar la escalabilidad de Kafka en HDInsight)
 * [Ejemplo de streaming de Apache Spark (DStream) con Kafka (versión preliminar) en HDInsight](../hdinsight-apache-spark-with-kafka.md)
 * [Uso del flujo estructurado de Spark con Kafka (versión preliminar) en HDInsight](../hdinsight-apache-kafka-spark-structured-streaming.md)
