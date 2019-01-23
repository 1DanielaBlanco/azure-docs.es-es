---
title: Uso de acciones de script para instalar Solr en un clúster de Hadoop en Azure
description: Obtenga información acerca de cómo personalizar un clúster de HDInsight con Solr mediante la acción de script.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/05/2016
ms.author: hrasheed
ROBOTS: NOINDEX
ms.openlocfilehash: 749a599936825f5f69ae18affad0fa89a4f1118f
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/14/2019
ms.locfileid: "54259638"
---
# <a name="install-and-use-apache-solr-on-windows-based-hdinsight-clusters"></a>Instalación y uso de Apache Solr en clústeres de HDInsight para Windows

Aprenda a personalizar un clúster de HDInsight basado en Windows con Apache Solr mediante la acción de script, y cómo usar Solr para buscar datos.

> [!IMPORTANT]  
> Los pasos de este tutorial solo se aplican a los clústeres de HDInsight basados en Windows. HDInsight solo está disponible en Windows en versiones inferiores a la 3.4. Linux es el único sistema operativo que se usa en la versión 3.4 de HDInsight, o en las superiores. Consulte la información sobre la [retirada de HDInsight en Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement). Para obtener información acerca del uso de Solr con un clúster basado en Linux, consulte [Instalación y uso de Solr en clústeres de Apache Hadoop para HDinsight (Linux)](hdinsight-hadoop-solr-install-linux.md).


Puede instalar R en cualquier tipo de clúster (Hadoop, Storm, HBase, Spark) en HDInsight de Azure mediante la *acción de script*. Un script de ejemplo para instalar Solr en un clúster de HDInsight se encuentra disponible desde un blob de solo lectura de Azure Storage en [https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1).

El script de ejemplo solo funciona con el clúster de HDInsight versión 3.1. Para obtener más información acerca de las versiones de clústeres de HDInsight, consulte las [versiones de clústeres de HDInsight](hdinsight-component-versioning.md).

El script de ejemplo que se usa en este tema crea un clúster de Solr con una configuración concreta. Si desea configurar el clúster de Solr con distintas colecciones, particiones, esquemas o réplicas, entre otras, debe modificar el script y los archivos binarios de Solr en consecuencia.

**Artículos relacionados**

* [Instalación y uso de Apache Solr en clústeres de Hadoop para HDInsight (Linux)](hdinsight-hadoop-solr-install-linux.md)
* [Creación de clústeres de Hadoop en HDInsight](hdinsight-provision-clusters.md): información general sobre la creación de clústeres de HDInsight.
* [Personalización de un clúster de HDInsight mediante la acción de script][hdinsight-cluster-customize]: información general sobre la personalización de clústeres de HDInsight mediante la acción de script.
* [Desarrollo de acciones de script con HDInsight](hdinsight-hadoop-script-actions.md).

## <a name="what-is-solr"></a>¿Qué es Solr?
<a href="https://lucene.apache.org/solr/features.html" target="_blank">Apache Solr</a> es una plataforma de búsqueda empresarial que permite una eficaz búsqueda de texto completo en los datos. Si bien Hadoop permite almacenar y administrar grandes cantidades de datos, Apache Solr proporciona las capacidades de búsqueda para recuperar rápidamente los datos.

## <a name="install-solr-using-portal"></a>Instalación de Solr mediante el portal
1. Comience a crear un clúster mediante la opción **CREACIÓN PERSONALIZADA**, como se describe en [Creación de clústeres de Hadoop en HDInsight](hdinsight-provision-clusters.md).
2. En la página **Acciones de script** del asistente, haga clic en **Agregar acción de script** para proporcionar detalles acerca de la acción de script, como se muestra a continuación:

    ![Uso de la acción de script para personalizar un clúster](./media/hdinsight-hadoop-solr-install/hdi-script-action-solr.png "Uso de la acción de script para personalizar un clúster")

    <table border='1'>
        <tr><th>Propiedad</th><th>Valor</th></tr>
        <tr><td>NOMBRE</td>
            <td>Especifique un nombre para la acción de script. Por ejemplo, <b>Instalar Solr</b>.</td></tr>
        <tr><td>Identificador URI de script</td>
            <td>Especifique el Identificador uniforme de recursos (URI) al script que se ha invocado para personalizar el clúster. Por ejemplo: <i>https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1</i>.</td></tr>
        <tr><td>Tipo de nodo</td>
            <td>Especifique los nodos en los que se ejecuta el script de personalización. Puede elegir <b>Todos los nodos</b>, <b>Solo nodos principales</b> o <b>Solo nodos de trabajo</b>.
        <tr><td>Parámetros</td>
            <td>Especifique los parámetros, si lo requiere el script. El script para instalar Solr no requiere ningún parámetro, por lo que puede dejarlo en blanco.</td></tr>
    </table>

    Puede agregar más de una acción de script para instalar varios componentes en el clúster. Después de haber agregado los scripts, haga clic en la marca de verificación para comenzar a crear el clúster.

## <a name="use-solr"></a>Uso de Solr
Debe comenzar con la indización de Solr con algunos archivos de datos. A continuación, puede utilizar Solr para ejecutar consultas de búsqueda en los datos indizados. Realice los pasos siguientes para utilizar Solr en un clúster de HDInsight:

1. **Use el protocolo de Escritorio remoto (RDP) para conectarse de manera remota con el clúster de HDInsight con Solr instalado**. En el portal de Azure, habilite el Escritorio remoto para el clúster que ha creado con Solr instalado y, a continuación, conéctese de manera remota con el clúster. Para obtener instrucciones, vea [Conexión a los clústeres de HDInsight con RDP](hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp).
2. **Indexe Solr mediante la carga de archivos de datos**. Al indizar Solr, colocar en él aquellos documentos que tenga que buscar. Para indexar Solr, use RDP para conectarse de manera remota al clúster, vaya al escritorio, abra la línea de comandos de Hadoop y vaya a **C:\apps\dist\solr-4.7.2\example\exampledocs**. Ejecute el siguiente comando:

        java -jar post.jar solr.xml monitor.xml

    Debería ver estos resultados en la consola:

        POSTing file solr.xml
        POSTing file monitor.xml
        2 files indexed.
        COMMITting Solr index changes to http://localhost:8983/solr/update..
        Time spent: 0:00:01.624

    La utilidad post.jar indexa Solr con dos documentos de muestra, **solr.xml** y **monitor.xml**. La utilidad post.jar y los documentos de muestra están disponibles con la instalación de Solr.
3. **Utilice el panel de Solr para buscan dentro de los documentos indexados**. En la sesión RDP con el clúster de HDInsight, abra Internet Explorer e inicie el panel de Solr en **http://headnodehost:8983/solr/#/**. En el panel izquierdo, en la lista desplegable **Core Selector** (Selector principal), seleccione **collection1** y, dentro de ella, haga clic en **Query** (Consulta). Por ejemplo, para seleccionar y devolver a todos los documentos de Solr, indique los valores siguientes:

   * En el cuadro de texto **q**, escriba **\*:**\*. Se devolverán todos los documentos indizados en Solr. Si desea buscar una cadena específica dentro de los documentos, puede especificar esa cadena aquí.
   * En el cuadro de texto **wt** , seleccione el formato de salida. El valor predeterminado es **json**. Haga clic en **Ejecutar consulta**.

     ![Uso de la acción de script para personalizar un clúster](./media/hdinsight-hadoop-solr-install/hdi-solr-dashboard-query.png "Ejecución de una consulta en el panel de Solr")

     La salida devuelve los dos documentos utilizados para indizar el Solr. La salida debe ser similar a la siguiente:

           "response": {
               "numFound": 2,
               "start": 0,
               "maxScore": 1,
               "docs": [
                 {
                   "id": "SOLR1000",
                   "name": "Solr, the Enterprise Search Server",
                   "manu": "Apache Software Foundation",
                   "cat": [
                     "software",
                     "search"
                   ],
                   "features": [
                     "Advanced Full-Text Search Capabilities using Lucene",
                     "Optimized for High Volume Web Traffic",
                     "Standards Based Open Interfaces - XML and HTTP",
                     "Comprehensive HTML Administration Interfaces",
                     "Scalability - Efficient Replication to other Solr Search Servers",
                     "Flexible and Adaptable with XML configuration and Schema",
                     "Good unicode support: héllo (hello with an accent over the e)"
                   ],
                   "price": 0,
                   "price_c": "0,USD",
                   "popularity": 10,
                   "inStock": true,
                   "incubationdate_dt": "2006-01-17T00:00:00Z",
                   "_version_": 1486960636996878300
                 },
                 {
                   "id": "3007WFP",
                   "name": "Dell Widescreen UltraSharp 3007WFP",
                   "manu": "Dell, Inc.",
                   "manu_id_s": "dell",
                   "cat": [
                     "electronics and computer1"
                   ],
                   "features": [
                     "30\" TFT active matrix LCD, 2560 x 1600, .25mm dot pitch, 700:1 contrast"
                   ],
                   "includes": "USB cable",
                   "weight": 401.6,
                   "price": 2199,
                   "price_c": "2199,USD",
                   "popularity": 6,
                   "inStock": true,
                   "store": "43.17614,-90.57341",
                   "_version_": 1486960637584081000
                 }
               ]
             }
4. **se recomienda: cree una copia de seguridad de los datos indexados de Solr en el almacenamiento de blobs de Azure asociado con el clúster de HDInsight**. Se recomienda que cree una copia de seguridad de los datos indexados desde los nodos del clúster de Solr en el almacenamiento de blobs de Azure. Realice los pasos siguientes para ello:

   1. Desde la sesión RDP, abra Internet Explorer y apunte a la dirección URL siguiente:

           http://localhost:8983/solr/replication?command=backup

       Debería obtener una respuesta similar a la siguiente:
            
      ```xml
      <?xml version="1.0" encoding="UTF-8"?>
          <response>
             <lst name="responseHeader">
               <int name="status">0</int>
               <int name="QTime">9</int>
             </lst>
            <str name="status">OK</str>
          </response>
      ```
      
   2. En la sesión remota, vaya a {SOLR_HOME}\{Collection}\data. Para el clúster creado a través del script de comandos, debe ser `C:\apps\dist\solr-4.7.2\example\solr\collection1\data`. En esta ubicación, verá una carpeta de instantáneas creada con un nombre similar a **snapshot.\*timestamp**\*.
   
   3. Comprima la carpeta de instantáneas y cárguela al almacenamiento de blobs de Azure. En la línea de comandos de Hadoop, use el comando siguiente para ir a la ubicación de la carpeta de instantáneas:

      ```
      hadoop fs -CopyFromLocal snapshot._timestamp_.zip /example/data
      ```

   Este comando copia la instantánea en /example/data/ en el contenedor dentro de la cuenta de almacenamiento predeterminada asociada con el clúster.

## <a name="install-solr-using-azure-powershell"></a>Instalación de Solr mediante Azure PowerShell
Consulte [Personalización de clústeres de HDInsight mediante la acción de scripts](hdinsight-hadoop-customize-cluster.md#call-scripts-using-azure-powershell).  El ejemplo muestra cómo instalar Apache Spark mediante Azure PowerShell. Debe personalizar el script para usar [https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1).

## <a name="install-solr-using-net-sdk"></a>Instalación de Solr con el SDK de .NET
Consulte [Personalización de clústeres de HDInsight mediante la acción de scripts](hdinsight-hadoop-customize-cluster.md#call-scripts-using-azure-powershell). El ejemplo muestra cómo instalar Apache Spark con .NET SDK. Debe personalizar el script para usar [https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1).

## <a name="see-also"></a>Otras referencias
* [Instalación y uso de Apache Solr en clústeres de Hadoop para HDInsight (Linux)](hdinsight-hadoop-solr-install-linux.md)
* [Creación de clústeres de Apache Hadoop en HDInsight](hdinsight-provision-clusters.md): información general acerca de la creación de clústeres de HDInsight.
* [Personalización de un clúster de HDInsight mediante la acción de script][hdinsight-cluster-customize]: información general sobre la personalización de clústeres de HDInsight mediante la acción de script.
* [Desarrollo de acciones de script con HDInsight](hdinsight-hadoop-script-actions.md).
* [Instalación y uso de Apache Spark en clústeres de HDInsight][hdinsight-install-spark]: Ejemplo de acción de script acerca de la instalación de Spark.
* [Instalación de Apache Giraph en clústeres de HDInsight](hdinsight-hadoop-giraph-install.md): Ejemplo de acción de script acerca de la instalación de Giraph.

[powershell-install-configure]: /powershell/azureps-cmdlets-docs
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster.md
