---
title: Conexión de Excel a Hadoop con el controlador ODBC de Hive (Azure HDInsight)
description: Aprenda a configurar y usar el controlador ODBC de Microsoft Hive para que Excel consulte datos en un clúster de HDInsight desde Microsoft Excel
keywords: Excel en Hadoop, Excel en Hive, ODBC de Hive
services: hdinsight
author: jasonwhowell
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: jasonh
ms.openlocfilehash: b21863d7a91c14f9795d72a13575e33485ba7d2b
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2018
ms.locfileid: "43041831"
---
# <a name="connect-excel-to-hadoop-in-azure-hdinsight-with-the-microsoft-hive-odbc-driver"></a>Conexión de Excel a Hadoop en Azure HDInsight con el controlador ODBC de Microsoft Hive

[!INCLUDE [ODBC-JDBC-selector](../../../includes/hdinsight-selector-odbc-jdbc.md)]

La solución de datos de gran tamaño de Microsoft integra componentes de inteligencia empresarial (BI) de Microsoft con clústeres Apache Hadoop implementados por HDInsight de Azure. Un ejemplo de esta integración es la capacidad de conectar Excel al almacenamiento de datos de Hive de un clúster Hadoop en HDInsight usando Microsoft Hive Open Database Connectivity (ODBC) Driver.

También es posible conectar desde Excel los datos asociados con un clúster de HDInsight y otros orígenes de datos, incluidos otros clústeres Hadoop (que no sean de HDInsight), con la utilización del complemento Microsoft Power Query para Excel. Para obtener información acerca de la instalación y el uso de Power Query, consulte [Conexión de Excel a HDInsight con Power Query][hdinsight-power-query].



**Requisitos previos**:

Antes de empezar este artículo, debe tener los siguientes elementos:

* **Un clúster de HDInsight**. Para crear uno, vea [Introducción a HDInsight de Azure](apache-hadoop-linux-tutorial-get-started.md).
* **Un equipo** con Office Professional Plus 2013, Office 365 Pro Plus, Excel 2013 Standalone u Office Professional Plus 2010.

## <a name="install-microsoft-hive-odbc-driver"></a>Instalación de Microsoft Hive ODBC Driver
Descargue e instale Microsoft Hive ODBC Driver desde el [Centro de descarga][hive-odbc-driver-download].

Este controlador se puede instalar en las versiones de 32 bits o 64 bits de Windows 7, Windows 8, Windows 10, Windows Server 2008 R2 y Windows Server 2012. El controlador permite la conexión a Azure HDInsight. Debe instalar la versión que coincida con la de la aplicación en la que va a usar el controlador ODBC. Para este tutorial, el controlador se usará desde Office Excel.

## <a name="create-hive-odbc-data-source"></a>Creación de un origen de datos de Hive ODBC
En los siguientes pasos se explica cómo crear un origen de datos de Hive ODBC.

1. En Windows 8 o Windows 10, presione la tecla Windows para abrir la pantalla Inicio y luego escriba **orígenes de datos**.
2. Haga clic en **Configurar orígenes de datos ODBC (32 bits)** o **Configurar orígenes de datos ODBC (64 bits)**, en función de la versión de Office de que se trate. Si usa Windows 7, elija **Orígenes de datos ODBC (32 bits)** u **Orígenes de datos ODBC (64 bits)** en **Herramientas administrativas**. Después, se abrirá el cuadro de diálogo **Administrador de orígenes de datos ODBC**.
   
    ![Administrador de orígenes de datos ODBC](./media/apache-hadoop-connect-excel-hive-odbc-driver/HDI.SimbaHiveOdbc.DataSourceAdmin1.png "Configuración de un DSN mediante el Administrador de orígenes de datos ODBC")

3. En la pestaña DNS de usuario, haga clic en **Agregar** para abrir el asistente **Crear nuevo origen de datos**.
4. Elija **Microsoft Hive ODBC Driver** y, después, haga clic en **Finalizar**. Se abrirá el cuadro de diálogo **Microsoft Hive ODBC Driver DNS Setup** (Configuración de DNS del controlador ODBC de Microsoft Hive).
5. Escriba o seleccione los valores siguientes:
   
   | Propiedad | DESCRIPCIÓN |
   | --- | --- |
   |  Data Source Name |Asigne un nombre al origen de datos |
   |  Host |Escriba &lt;NombreClústerHDInsight>.azurehdinsight.net. Por ejemplo, myHDICluster.azurehdinsight.net |
   |  Port |Use <strong>443</strong>. (Este puerto se ha cambiado de 563 a 443). |
   |  Base de datos |Use el <strong>valor predeterminado</strong> |
   |  Mechanism |Seleccione <strong>Azure HDInsight Service</strong> |
   |  User Name |Escriba el nombre de usuario HTTP del clúster de HDInsight. El nombre de usuario predeterminado es <strong>admin</strong>. |
   |  Contraseña |Escriba la contraseña del usuario del clúster de HDInsight. |
   
    </table>
   
    Hay algunos parámetros importantes que se deben tener en cuenta al hacer clic en **Opciones avanzadas**:
   
   | Parámetro | DESCRIPCIÓN |
   | --- | --- |
   |  Use Native Query |Cuando esta opción está seleccionada, el controlador ODBC NO trata de convertir TSQL en HiveQL. Solo debe usarla si está totalmente seguro de que va a enviar instrucciones de HiveQL puras. Al conectarse a SQL Server o a Azure SQL Database, debe dejar esta opción desactivada. |
   |  Rows fetched per block |Al capturar un gran volumen de registros, es posible que sea necesario ajustar este parámetro para garantizar un rendimiento óptimo. |
   |  Default string column length, Binary column length, Decimal column scale |La longitud y precisión del tipo de datos pueden afectar a la forma en que se devuelven los datos. Pueden dar lugar a que se devuelva información incorrecta debido a la pérdida de precisión o al truncamiento. |

    ![Opciones avanzadas](./media/apache-hadoop-connect-excel-hive-odbc-driver/HDI.HiveOdbc.DataSource.AdvancedOptions1.png "Opciones de configuración avanzada de DSN")

1. Haga clic en **Probar** para probar el origen de datos. Cuando el origen de datos esté configurado correctamente, aparecerá el mensaje *Pruebas completadas correctamente*.
2. Haga clic en **Aceptar** para cerrar el cuadro de diálogo de prueba. Ahora el nuevo origen de datos debe aparecer en el **Administrador de orígenes de datos ODBC**.
3. Haga clic en **Aceptar** para salir del asistente.

## <a name="import-data-into-excel-from-hdinsight"></a>Importación de datos en Excel desde HDInsight
En los pasos siguientes se describe cómo importar datos desde una tabla de Hive a un libro de Excel mediante el origen de datos ODBC creado en la sección anterior.

1. Abra un libro de Excel nuevo o existente.
2. En la pestaña **Datos**, haga clic sucesivamente en **Obtener datos**, en **Desde otras fuentes** y, después, en **De ODBC** para iniciar el **Asistente para la conexión de datos**.
   
    ![Apertura del Asistente para la conexión de datos](./media/apache-hadoop-connect-excel-hive-odbc-driver/HDI.SimbaHiveOdbc.Excel.DataConnection1.png "Apertura del Asistente para la conexión de datos")
4. Seleccione el nombre del origen de datos que ha creado en la sección anterior y haga clic en **Aceptar**.
5. Escriba el nombre de usuario de Hadoop (el nombre predeterminado es admin) y la contraseña y, a continuación, haga clic en **Conectar**.
6. En el explorador, expanda **HIVE**, expanda **predeterminado**, haga clic en **hivesampletable** y, finalmente, haga clic en **Cargar**. La importación de los datos a Excel tarda algunos segundos.

    ![Navegador de ODBC de Hive de HDInsight](./media/apache-hadoop-connect-excel-hive-odbc-driver/hdinsight.hive.odbc.navigator.png "Abrir Asistente para la conexión de datos")


## <a name="next-steps"></a>Pasos siguientes
En este artículo se proporciona información sobre cómo usar el controlador ODBC de Microsoft Hive para recuperar datos del servicio HDInsight en Excel. De manera similar, puede recuperar datos del servicio HDInsight en la SQL Database. Es posible cargar datos en un servicio HDInsight. Para obtener más información, consulte:

* [Visualize Hive data with Microsoft Power BI in Azure HDInsight](apache-hadoop-connect-hive-power-bi.md) (Visualización de datos de Hive con Microsoft Power BI en Azure HDInsight)
* [Visualización de datos de Interactive Query Hive con Power BI en Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Usar Zeppelin para ejecutar consultas de Hive en Azure HDInsight](./../hdinsight-connect-hive-zeppelin.md)
* [Conexión de Excel a Hadoop con Power Query](apache-hadoop-connect-excel-power-query.md).
* [Conectarse a Azure HDInsight y ejecutar consultas de Hive con Herramientas de Data Lake para Visual Studio](apache-hadoop-visual-studio-tools-get-started.md).
* [Uso de Herramientas de Azure HDInsight para Visual Studio Code](../hdinsight-for-vscode.md).
* [Carga de datos en HDInsight](./../hdinsight-upload-data.md).

[hdinsight-use-sqoop]:hdinsight-use-sqoop.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-hive]:hdinsight-use-hive.md
[hdinsight-upload-data]: ../hdinsight-upload-data.md
[hdinsight-power-query]: ../hdinsight-connect-excel-power-query.md
[hive-odbc-driver-download]: http://go.microsoft.com/fwlink/?LinkID=286698


