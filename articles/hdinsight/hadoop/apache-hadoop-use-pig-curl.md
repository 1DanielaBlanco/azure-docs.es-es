---
title: Uso de Pig en Hadoop con REST en HDInsight (Azure)
description: Aprenda a usar REST para ejecutar trabajos de Pig Latin en un clúster de Hadoop en Azure HDInsight.
services: hdinsight
author: jasonwhowell
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/10/2018
ms.author: jasonh
ms.openlocfilehash: e97dcd5a15fa1a58782fb8225e8b3381d7061bb6
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2018
ms.locfileid: "43051730"
---
# <a name="run-pig-jobs-with-hadoop-on-hdinsight-by-using-rest"></a>Ejecución de trabajos de Pig con Hadoop en HDInsight con REST

[!INCLUDE [pig-selector](../../../includes/hdinsight-selector-use-pig.md)]

Aprenda a ejecutar trabajos de Pig Latin mediante la creación de solicitudes de REST a un clúster de Azure HDInsight. Curl se usa para demostrar cómo puede interactuar con HDInsight mediante la API de REST WebHCat.

> [!NOTE]
> Si ya está familiarizado con el uso de servidores de Hadoop basados en Linux, pero no conoce HDInsight, consulte [Información sobre el uso de HDInsight en Linux](../hdinsight-hadoop-linux-information.md).

## <a id="prereq"></a>Requisitos previos

* Un clúster de HDInsight de Azure (Hadoop en HDInsight) (basado en Windows o en Linux)

  > [!IMPORTANT]
  > Linux es el único sistema operativo que se usa en la versión 3.4 de HDInsight, o en las superiores. Consulte la información sobre la [retirada de HDInsight en Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* [Curl](http://curl.haxx.se/)

* [jq](http://stedolan.github.io/jq/)

## <a id="curl"></a>Ejecución de trabajos de Pig con Curl

> [!NOTE]
> La API de REST se protege con la [autenticación de acceso básico](http://en.wikipedia.org/wiki/Basic_access_authentication). Para garantizar que las credenciales se envían de manera segura al servidor, siempre debe crear solicitudes usando HTTP segura (HTTPS).
>
> Cuando se usen los comandos de esta sección, reemplace `USERNAME` por el usuario para autenticación en el clúster y `PASSWORD` por la contraseña de la cuenta de usuario. Reemplace `CLUSTERNAME` por el nombre del clúster.
>


1. Desde una línea de comandos, utilice el siguiente comando para comprobar que puede conectarse al clúster de HDInsight.

    ```bash
    curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status
    ```

    Recibirá la siguientes respuesta JSON:

        {"status":"ok","version":"v1"}

    Los parámetros que se utilizan en este comando son los siguientes:

    * **-u**: el nombre de usuario y la contraseña que se utilizan para autenticar la solicitud.
    * **-G**: indica que esta es una solicitud GET.

     El principio de la dirección URL, **https://CLUSTERNAME.azurehdinsight.net/templeton/v1**, es el mismo para todas las solicitudes. La ruta de acceso, **/status**, indica que la solicitud debe devolver el estado de WebHCat (también conocido como Templeton) al servidor.

2. Utilice el siguiente código para enviar un trabajo de Pig Latin al clúster:

    ```bash
    curl -u USERNAME:PASSWORD -d user.name=USERNAME -d execute="LOGS=LOAD+'/example/data/sample.log';LEVELS=foreach+LOGS+generate+REGEX_EXTRACT($0,'(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)',1)+as+LOGLEVEL;FILTEREDLEVELS=FILTER+LEVELS+by+LOGLEVEL+is+not+null;GROUPEDLEVELS=GROUP+FILTEREDLEVELS+by+LOGLEVEL;FREQUENCIES=foreach+GROUPEDLEVELS+generate+group+as+LOGLEVEL,COUNT(FILTEREDLEVELS.LOGLEVEL)+as+count;RESULT=order+FREQUENCIES+by+COUNT+desc;DUMP+RESULT;" -d statusdir="/example/pigcurl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/pig
    ```

    Los parámetros que se utilizan en este comando son los siguientes:

    * **-d**: como `-G` no se usa, la solicitud establece como valor predeterminado el método POST. `-d` especifica los valores de datos que se envían con la solicitud.

    * **user.name**: el usuario que ejecuta el comando.
    * **execute**: las instrucciones de Pig Latin que se ejecutarán.
    * **statusdir**: el directorio donde se escribe el estado de este trabajo.

    > [!NOTE]
    > Observe que los espacios en las instrucciones de Pig Latin se reemplazan por el carácter `+` cuando se utilizan con Curl.

    Este comando debe devolver un identificador de trabajo que se pueda usar para comprobar el estado del trabajo, por ejemplo:

        {"id":"job_1415651640909_0026"}

3. Para revisar el estado del trabajo, use el siguiente comando:

     ```bash
    curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state
    ```

     Reemplace `JOBID` por el valor devuelto en el paso anterior. Por ejemplo, si el valor devuelto fue `{"id":"job_1415651640909_0026"}`, entonces `JOBID` es `job_1415651640909_0026`.

    Si se ha completado el trabajo, el estado es **SUCCEEDED**.

    > [!NOTE]
    > Esta solicitud de Curl devuelve un documento de notación de objetos JavaScript (JSON) con información acerca del trabajo; jq se usa para recuperar solo el valor de estado.

## <a id="results"></a>Visualización de resultados

Cuando el estado del trabajo haya cambiado a **SUCCEEDED**, puede recuperar los resultados del trabajo. El parámetro `statusdir` pasado con la consulta contiene la ubicación del archivo de salida; en este caso, `/example/pigcurl`.

HDInsight puede usar Azure Storage o Azure Data Lake Store como almacén de datos predeterminado. Hay varias maneras de obtener los datos, en función de la que use. Para más información, consulte la sección sobre almacenamiento del documento [Información sobre el uso de HDInsight en Linux](../hdinsight-hadoop-linux-information.md#hdfs-azure-storage-and-data-lake-store).

## <a id="summary"></a>Resumen

Como se muestra en este documento, puede utilizar la solicitud HTTP sin procesar para ejecutar, supervisar y ver los resultados de los trabajos de Hive en el clúster de HDInsight.

Para obtener más información sobre la interfaz REST utilizada en este artículo, consulte la [referencia de WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference).

## <a id="nextsteps"></a>Pasos siguientes

Para obtener información general sobre Pig en HDInsight:

* [Uso de Pig con Hadoop en HDInsight](hdinsight-use-pig.md)

Para obtener información sobre otras maneras de trabajar con Hadoop en HDInsight:

* [Uso de Hive con Hadoop en HDInsight](hdinsight-use-hive.md)
* [Uso de MapReduce con Hadoop en HDInsight](hdinsight-use-mapreduce.md)
