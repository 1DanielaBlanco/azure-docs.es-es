---
title: Uso de Apache Kafka con Storm en HDInsight - Azure | Microsoft Docs
description: Apache Kafka se instala con Apache Storm en HDInsight. Vea cómo escribir en Kafka y después leer de él con los componentes KafkaBolt y KafkaSpout proporcionados Storm. Aprenda también a usar el entorno de Flux para definir y enviar topologías de Storm.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: e4941329-1580-4cd8-b82e-a2258802c1a7
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/08/2018
ms.author: larryfr
ms.openlocfilehash: 0c74e46f37319a9d1eb0ea1587087e24312de451
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/09/2018
---
# <a name="use-apache-kafka-with-storm-on-hdinsight"></a>Uso de Apache Kafka con Storm en HDInsight

Obtenga información sobre cómo usar Apache Storm para leer y escribir en Apache Kafka. En este ejemplo también se muestra cómo guardar datos de una topología de Storm en el sistema de archivos compatible con HDFS que usa HDInsight.

> [!NOTE]
> Los pasos que se describen en este documento crean un grupo de recursos de Azure que contiene un clúster Storm en HDInsight y un clúster Kafka en HDInsight. Estos dos clústeres se encuentran en una instancia de Azure Virtual Network, lo que permite al clúster Storm comunicarse directamente con el clúster Kafka.
> 
> Cuando haya terminado los pasos indicados en este documento, no olvide eliminar los clústeres para evitar gastos innecesarios.

## <a name="get-the-code"></a>Obtención del código

El código del ejemplo que se usa en este documento está disponible en [https://github.com/Azure-Samples/hdinsight-storm-java-kafka](https://github.com/Azure-Samples/hdinsight-storm-java-kafka).

Para compilar este proyecto, necesitará la siguiente configuración para su entorno de desarrollo:

* [Java JDK 1.8](http://www.oracle.com/technetwork/pt/java/javase/downloads/jdk8-downloads-2133151.html) o versiones posteriores. HDInsight 3.5 y versiones posteriores requieren Java 8.

* [Maven 3.x](https://maven.apache.org/download.cgi)

* Un cliente de SSH (precisa de los comandos `ssh` y `scp`). Para obtener información, consulte [Uso de SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

* Un editor de texto o IDE.

Pueden establecer las siguientes variables de entorno al instalar Java y el JDK en la estación de trabajo de desarrollo. Sin embargo, debe comprobar que existen y que contienen los valores correctos para su sistema.

* `JAVA_HOME`: debe apuntar al directorio en el que esté instalado JDK.
* `PATH`: debe contener las rutas de acceso siguientes:
  
    * `JAVA_HOME` (o la ruta de acceso equivalente).
    * `JAVA_HOME\bin` (o la ruta de acceso equivalente).
    * Directorio en el que esté instalado Maven.

## <a name="create-the-clusters"></a>Creación de los clústeres

Apache Kafka en HDInsight no proporciona acceso a los agentes de Kafka a través de Internet. Cualquier comunicación con Kafka debe realizarse en la misma red virtual de Azure que utilizan los nodos del clúster Kafka. En este ejemplo, los clústeres Kafka y Storm se encuentran en una red virtual de Azure. En el diagrama siguiente, se muestra cómo fluye la comunicación entre los clústeres:

![Diagrama de clústeres Storm y Kafka en una red virtual de Azure](./media/hdinsight-apache-storm-with-kafka/storm-kafka-vnet.png)

> [!NOTE]
> Se puede tener acceso a otros servicios del clúster, como SSH y Ambari, a través de Internet. Para más información sobre los puertos públicos disponibles en HDInsight, consulte [Puertos e identificadores URI usados en HDInsight](hdinsight-hadoop-port-settings-for-services.md).

Aunque puede crear manualmente la red virtual de Azure y los clústeres Kafka y Storm, resulta más sencillo utilizar una plantilla de Azure Resource Manager. Siga los pasos que se indican a continuación para implementar una red virtual de Azure y los clústeres Kafka y Storm en la suscripción de Azure.

1. Utilice el siguiente botón para iniciar sesión en Azure y abrir la plantilla en Azure Portal.
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fhdinsight-storm-java-kafka%2Fmaster%2Fcreate-kafka-storm-clusters-in-vnet.json" target="_blank"><img src="./media/hdinsight-apache-storm-with-kafka/deploy-to-azure.png" alt="Deploy to Azure"></a>
   
    La plantilla de Azure Resource Manager está ubicada en **https://github.com/Azure-Samples/hdinsight-storm-java-kafka/blob/master/create-kafka-storm-clusters-in-vnet.json**. Crea estos recursos:
    
    * Grupo de recursos de Azure
    * Azure Virtual Network
    * Cuenta de Azure Storage
    * Kafka en HDInsight versión 3.6 (tres nodos de trabajo)
    * Storm en HDInsight versión 3.6 (tres nodos de trabajo)

  > [!WARNING]
  > Para garantizar la disponibilidad de Kafka en HDInsight, el clúster debe contener al menos tres nodos de trabajo. Esta plantilla crea un clúster de Kafka que contiene tres nodos de trabajo.

2. Utilice la guía siguiente para rellenar las entradas de la sección **Implementación personalizada**:
   
    ![Implementación personalizada de HDInsight](./media/hdinsight-apache-storm-with-kafka/parameters.png)

    * **Grupo de recursos**: cree un nuevo grupo de recursos o seleccione uno existente. Este grupo contiene el clúster de HDInsight.
   
    * **Ubicación**: seleccione una ubicación geográfica próxima a usted.

    * **Nombre base del clúster**: este valor se utiliza como nombre base para los clústeres Storm y Kafka. Por ejemplo, si especifica **hdi**, creará un clúster Storm llamado **storm-hdi** y un clúster Kafka llamado **kafka-hdi**.
   
    * **Nombre de usuario de inicio de sesión del clúster**: nombre de usuario del administrador de los clústeres Storm y Kafka.
   
    * **Contraseña de inicio de sesión de clúster**: contraseña del usuario administrador para los clústeres Storm y Kafka.
    
    * **Nombre de usuario SSH**: usuario SSH que se va a crear para los clústeres Storm y Kafka.
    
    * **Contraseña SSH**: contraseña del usuario SSH para los clústeres Storm y Kafka.

3. Consulte los **Términos y condiciones** y seleccione **Acepto los términos y condiciones indicados anteriormente**.

4. Por último, active **Anclar al panel** y seleccione **Adquirir**. Se tarda aproximadamente 20 minutos en crear los clústeres.

Cuando se hayan creado los recursos, se muestra la sección del grupo de recursos.

![Sección del grupo de recursos de la red virtual y los clústeres](./media/hdinsight-apache-storm-with-kafka/groupblade.png)

> [!IMPORTANT]
> Observe que los nombres de los clústeres de HDInsight son **storm-BASENAME** y **kafka-BASENAME**, donde BASENAME es el nombre que indicó en la plantilla. Estos nombres se utilizarán más adelante al establecer la conexión con los clústeres.

## <a name="understanding-the-code"></a>Descripción del código

Este proyecto contiene dos topologías:

* **KafkaWriter**: definida por el archivo **writer.yaml**, esta topología escribe frases aleatorias en Kafka con el KafkaBolt proporcionado con Apache Storm.

    Esta topología usa un componente **SentenceSpout** personalizado para generar frases aleatorias.

* **KafkaReader**: definida por el archivo **reader.yaml**, esta topología lee los datos de Kafka con el KafkaSpout proporcionado con Apache Storm y, después, registra los datos en stdout.

    Esta topología usa HdfsBolt de Storm para escribir datos en el almacenamiento predeterminado para el clúster de Storm.
### <a name="flux"></a>Flux

Las topologías se definen mediante [Flux](https://storm.apache.org/releases/1.1.2/flux.html). Flux se incorporó por primera vez a Storm 0.10.x y permite separar la configuración de la topología del código. Para las topologías que usan el entorno Flux, la topología se define en un archivo YAML. El archivo YAML se puede incluir como parte de la topología. También puede ser un archivo independiente que se usa al enviar la topología. Flux también admite la sustitución de variables en tiempo de ejecución, que se usa en este ejemplo.

Los siguientes parámetros se establecen en tiempo de ejecución para estas topologías:

* `${kafka.topic}`: el nombre del tema de Kafka donde las topologías leen y escriben.

* `${kafka.broker.hosts}`: los hosts donde se ejecutan los agentes de Kafka. KafkaBolt usa la información de los agentes al escribir en Kafka.

* `${kafka.zookeeper.hosts}`: los hosts donde se ejecuta Zookeeper en el clúster de Kafka.

Para más información sobre las topologías de Flux, vea [https://storm.apache.org/releases/1.1.2/flux.html](https://storm.apache.org/releases/1.1.2/flux.html).

## <a name="download-and-compile-the-project"></a>Descarga y compilación del proyecto

1. En el entorno de desarrollo, descargue el proyecto de [https://github.com/Azure-Samples/hdinsight-storm-java-kafka](https://github.com/Azure-Samples/hdinsight-storm-java-kafka), abra una línea de comandos y cambie los directorios a la ubicación en la que descargó el proyecto.

2. Desde el directorio **hdinsight-storm-java-kafka**, use el siguiente comando para compilar el proyecto y crear un paquete de implementación:

  ```bash
  mvn clean package
  ```

    El proceso de empaquetado crea un archivo llamado `KafkaTopology-1.0-SNAPSHOT.jar` en el directorio `target`.

3. Use los siguientes comandos para copiar el paquete en el clúster de Storm en HDInsight. Reemplace **USERNAME** por el nombre de usuario SSH para el clúster. Reemplace **BASENAME** por el nombre base que utilizó al crear el clúster.

  ```bash
  scp ./target/KafkaTopology-1.0-SNAPSHOT.jar USERNAME@storm-BASENAME-ssh.azurehdinsight.net:KafkaTopology-1.0-SNAPSHOT.jar
  ```

    Cuando se le solicite, escriba la contraseña que utilizó al crear los clústeres.

## <a name="configure-the-topology"></a>Configurar la topología

1. Use uno de los métodos siguientes para detectar los hosts de agente de Kafka para **Kafka** en el clúster de HDInsight:

    ```powershell
    $creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
    $clusterName = Read-Host -Prompt "Enter the Kafka cluster name"
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $brokerHosts = $respObj.host_components.HostRoles.host_name[0..1]
    ($brokerHosts -join ":9092,") + ":9092"
    ```

    > [!IMPORTANT]
    > En el siguiente ejemplo de Bash se da por supuesto que `$CLUSTERNAME` contiene el nombre del clúster de __Kafka__. También se supone que está instalado [jq](https://stedolan.github.io/jq/) versión 1.5 o posterior. Cuando se le solicite, escriba la contraseña de la cuenta de inicio de sesión del clúster.

    ```bash
    curl -su admin -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER" | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2
    ```

    El valor que se devuelve es similar al texto siguiente:

        wn0-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:9092,wn1-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:9092

    > [!IMPORTANT]
    > Aunque puede haber más de dos hosts de agente para el clúster, no es necesario proporcionar una lista completa de los hosts a los clientes. Con uno o dos es suficiente.

2. Use uno de los métodos siguientes para detectar los hosts de agente de Zookeeper para __Kafka__ en el clúster de HDInsight:

    ```powershell
    $creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
    $clusterName = Read-Host -Prompt "Enter the Kafka cluster name"
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $zookeeperHosts = $respObj.host_components.HostRoles.host_name[0..1]
    ($zookeeperHosts -join ":2181,") + ":2181"
    ```

    > [!IMPORTANT]
    > En el siguiente ejemplo de Bash se da por supuesto que `$CLUSTERNAME` contiene el nombre del clúster de __Kafka__. También se supone que [jq](https://stedolan.github.io/jq/) está instalado. Cuando se le solicite, escriba la contraseña de la cuenta de inicio de sesión del clúster.

    ```bash
    curl -su admin -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2
    ```

    El valor que se devuelve es similar al texto siguiente:

        zk0-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:2181,zk2-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:2181

    > [!IMPORTANT]
    > Aunque haya más de dos nodos de Zookeeper, no es necesario proporcionar una lista completa de los hosts a los clientes. Con uno o dos es suficiente.

    Guarde este valor, porque se usará más adelante.

3. Edite el archivo `dev.properties` en la raíz del proyecto. Agregue la información de hosts de agente y Zookeeper para el clúster de __Kafka__ a las líneas correspondientes en este archivo. El siguiente ejemplo se configura con los valores de ejemplo de los pasos anteriores:

        kafka.zookeeper.hosts: zk0-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:2181,zk2-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:2181
        kafka.broker.hosts: wn0-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:9092,wn1-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:9092
        kafka.topic: stormtopic

4. Guarde el archivo `dev.properties` y, después, use el siguiente comando para cargarlo en el clúster de **Storm**:

     ```bash
    scp dev.properties USERNAME@storm-BASENAME-ssh.azurehdinsight.net:dev.properties
    ```

    Reemplace **USERNAME** por el nombre de usuario SSH para el clúster. Reemplace **BASENAME** por el nombre base que utilizó al crear el clúster.

## <a name="start-the-writer"></a>Inicio del escritor

> [!IMPORTANT]
> En los pasos descritos en esta sección se supone que ha usado el vínculo de la plantilla de Azure Resource Manager de este documento para crear los clústeres de Storm y Kafka. Esta plantilla permite la creación automática de temas para el clúster de Kafka.
>
> De forma predeterminada, Kafka en HDInsight no permite la creación automática de temas, por lo que si usó otro método para crear el clúster de Kafka, debe crearlos manualmente. Para más información sobre la creación manual de un tema, consulte el documento [Inicio de Apache Kafka en HDInsight](./kafka/apache-kafka-get-started.md).

1. Para conectarse al clúster de **Storm** mediante SSH, use lo siguiente. Reemplace **USERNAME** por el nombre de usuario SSH que usó al crear el clúster. Reemplace **BASENAME** por el nombre base que se utilizó al crear el clúster.

  ```bash
  ssh USERNAME@storm-BASENAME-ssh.azurehdinsight.net
  ```

    Cuando se le solicite, escriba la contraseña que utilizó al crear los clústeres.
   
    Para más información, consulte [Uso de SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Desde la conexión de SSH al clúster Storm, use el siguiente comando para iniciar la topología del escritor:

    ```bash
    storm jar KafkaTopology-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /writer.yaml --filter dev.properties
    ```

    Los parámetros que se usan en este comando son los siguientes:

    * `org.apache.storm.flux.Flux`: use Flux para configurar y ejecutar esta topología.

    * `--remote`: envíe la topología a Nimbus. La topología se distribuye entre los nodos de trabajo del clúster.

    * `-R /writer.yaml`: use el archivo `writer.yaml` para configurar la topología. `-R` indica que este recurso está incluido en el archivo jar. Se encuentra en la raíz de jar, por lo que `/writer.yaml` es la ruta de acceso a él.

    * `--filter`: rellene las entradas de la topología `writer.yaml` mediante los valores del archivo `dev.properties`. Por ejemplo, el valor de la entrada `kafka.topic` del archivo se usa para reemplazar la entrada `${kafka.topic}` en la definición de topología.

5. Una vez que se ha iniciado la topología, use el siguiente comando para comprobar que está escribiendo datos en el tema de Kafka:

    > [!IMPORTANT]
    > Reemplace `$KAFKAZKHOSTS` por la información del host de Zookeeper en el clúster de __Kafka__.

  ```bash
  /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --zookeeper $KAFKAZKHOSTS --from-beginning --topic stormtopic
  ```

    Este comando usa un script incluido con Kafka para supervisar el tema. Tras unos instantes, empezará a devolver frases aleatorias que se han escrito en el tema. La salida es similar a la del ejemplo siguiente:

        i am at two with nature             
        an apple a day keeps the doctor away
        snow white and the seven dwarfs     
        the cow jumped over the moon        
        an apple a day keeps the doctor away
        an apple a day keeps the doctor away
        the cow jumped over the moon        
        an apple a day keeps the doctor away
        an apple a day keeps the doctor away
        four score and seven years ago      
        snow white and the seven dwarfs     
        snow white and the seven dwarfs     
        i am at two with nature             
        an apple a day keeps the doctor away

    Use Ctrl+C para detener el script.

## <a name="start-the-reader"></a>Inicio del lector

> [!NOTE]
> Al ver el lector en la interfaz de usuario de Storm, verá una sección de __error de Topology spouts lag__. En este ejemplo, puede hacer caso omiso de este error.

1. Desde la sesión de SSH al clúster Storm, use el siguiente comando para iniciar la topología del lector:

  ```bash
  storm jar KafkaTopology-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /reader.yaml --filter dev.properties
  ```

2. Cuando se inicie la topología, abra la interfaz de usuario de Storm. La interfaz de usuario de este sitio web se encuentra en `https://storm-BASENAME.azurehdinsight.net/stormui`. Reemplace __BASENAME__ por el nombre base que utilizó al crear el clúster. 

    Cuando se le pida, use el nombre de inicio de sesión de administrador (de forma predeterminada, `admin`) y la contraseña que utilizó al crear el clúster. Verá una página web similar a la siguiente:

    ![UI de Storm](./media/hdinsight-apache-storm-with-kafka/stormui.png)

3. En la interfaz de usuario de Storm, seleccione el vínculo __kafka-reader__ en la sección __Resumen de la topología__ para mostrar información sobre la topología __kafka-reader__.

    ![Sección Resumen de la topología en la interfaz de usuario web de Storm](./media/hdinsight-apache-storm-with-kafka/topology-summary.png)

4. Para mostrar información sobre las instancias del componente logger-bolt, seleccione el vínculo __logger-bolt__ en la sección __Bolts (All time)__.

    ![Vínculo Logger-bolt en la sección Bolt](./media/hdinsight-apache-storm-with-kafka/bolts.png)

5. En la sección __Executors__ (Ejecutores), seleccione un vínculo en la columna __Port__ (Puerto) para mostrar la información de registro de esta instancia del componente.

    ![Vínculo Executors](./media/hdinsight-apache-storm-with-kafka/executors.png)

    El registro contiene un registro de los datos leídos desde el tema de Kafka. La información del registro es similar a la siguiente:

        2016-11-04 17:47:14.907 c.m.e.LoggerBolt [INFO] Received data: four score and seven years ago
        2016-11-04 17:47:14.907 STDIO [INFO] the cow jumped over the moon
        2016-11-04 17:47:14.908 c.m.e.LoggerBolt [INFO] Received data: the cow jumped over the moon
        2016-11-04 17:47:14.911 STDIO [INFO] snow white and the seven dwarfs
        2016-11-04 17:47:14.911 c.m.e.LoggerBolt [INFO] Received data: snow white and the seven dwarfs
        2016-11-04 17:47:14.932 STDIO [INFO] snow white and the seven dwarfs
        2016-11-04 17:47:14.932 c.m.e.LoggerBolt [INFO] Received data: snow white and the seven dwarfs
        2016-11-04 17:47:14.969 STDIO [INFO] an apple a day keeps the doctor away
        2016-11-04 17:47:14.970 c.m.e.LoggerBolt [INFO] Received data: an apple a day keeps the doctor away

## <a name="stop-the-topologies"></a>Detención de las topologías

Desde una sesión de SSH al clúster Storm, use los siguientes comandos para detener las topologías de Storm:

  ```bash
  storm kill kafka-writer
  storm kill kafka-reader
  ```

## <a name="delete-the-cluster"></a>Eliminación del clúster

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Como el procedimiento descrito en este documento crea los dos clústeres en el mismo grupo de recursos de Azure, puede eliminar el grupo de recursos de Azure Portal. Al eliminar el grupo de recursos, se quitan todos los recursos creados siguiendo este documento.

## <a name="next-steps"></a>Pasos siguientes

Para ver más ejemplos de topologías que pueden utilizarse con Storm en HDInsight, consulte [Topologías y componentes de ejemplo de Storm para Apache Storm en HDInsight](storm/apache-storm-example-topology.md).

Para más información sobre la implementación y supervisión de topologías en HDInsight basado en Linux, consulte [Implementación y administración de topologías de Apache Storm en HDInsight basado en Linux](storm/apache-storm-deploy-monitor-topology-linux.md).