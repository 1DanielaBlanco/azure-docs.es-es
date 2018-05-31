---
title: Administración de clústeres de Hadoop en HDInsight con el portal de Azure | Microsoft Azure
description: Aprenda a crear y administrar clústeres de HDInsight mediante el portal de Azure.
services: hdinsight
documentationcenter: ''
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 5a76f897-02e8-4437-8f2b-4fb12225854a
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 05/18/2018
ms.author: jgao
ms.openlocfilehash: 90261e090f87a5ca0d92b86c33addce2449cfd24
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/20/2018
ms.locfileid: "34361978"
---
# <a name="manage-hadoop-clusters-in-hdinsight-by-using-the-azure-portal"></a>Administración de clústeres de Hadoop en HDInsight mediante el Portal de Azure

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

En el [portal de Azure][azure-portal], puede administrar clústeres de Hadoop en Azure HDInsight. Use el selector de pestañas anterior para más información sobre cómo administrar clústeres de Hadoop en HDInsight con otras herramientas.

**Requisito previo**

Para seguir los pasos de este artículo, necesitará una **suscripción de Azure**. Consulte [Obtención de una versión de evaluación gratuita](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

## <a name="open-the-azure-portal"></a>Abra Azure Portal.
1. Inicie sesión en [https://portal.azure.com](https://portal.azure.com).
2. Después de abrir el portal, puede:

   * Hacer clic en **Crear un recurso** en el menú de la izquierda para crear un nuevo clúster:

       ![botón nuevo clúster de HDInsight](./media/hdinsight-administer-use-portal-linux/azure-portal-new-button.png)

       Escriba **HDInsight** en **Buscar en el Marketplace**, haga clic en **HDInsight**y, luego, haga clic en **Crear**.

   * Haga clic en **Clústeres de HDInsight** en el menú de la izquierda para mostrar los clústeres existentes.

       ![Portal de Azure botón clúster de HDInsight](./media/hdinsight-administer-use-portal-linux/azure-portal-hdinsight-button.png)

       Si no ve el botón **Clústeres de HDInsight** y luego haga clic en **Clústeres de HDInsight** en la sección **Inteligencia y análisis**.


## <a name="create-clusters"></a>Creación de clústeres
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

HDInsight trabaja con una amplia gama de componentes de Hadoop. Para ver la lista de los componentes que han sido comprobados y admitidos, consulte [¿Qué versión de Hadoop tiene Azure HDInsight?](hdinsight-component-versioning.md). Para información general sobre la creación de clústeres, consulte [Creación de clústeres de Hadoop en HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

### <a name="access-control-requirements"></a>Requisitos de control de acceso

Debe especificar una suscripción de Azure cuando cree un clúster de HDInsight. El clúster se puede crear en un nuevo grupo de recursos de Azure o en un grupo de recursos existente. Puede usar los pasos siguientes para comprobar sus permisos para la creación de clústeres de HDInsight:

- Para crear un nuevo grupo de recursos:

    1. Inicie sesión en el [Azure Portal](https://portal.azure.com).
    2. Haga clic en **Suscripción** en el menú izquierdo. Tiene un icono amarillo de una llave amarilla. Verá una lista de suscripciones.
    3. Haga clic en la suscripción que usa para crear clústeres. 
    4. Haga clic en **Mis permisos**.  Muestra el [rol](../role-based-access-control/overview.md#built-in-roles) en la suscripción. Debe tener al menos acceso de colaborador para crear un clúster de HDInsight.

- Para usar un grupo de recursos existente:

    1. Inicie sesión en el [Azure Portal](https://portal.azure.com).
    2. Haga clic en **Grupos de recursos** en el menú izquierdo para ver los grupos de recursos.
    3. Haga clic en el grupo de recursos que desee usar para crear el clúster de HDInsight.
    4. Haga clic en **Control de acceso (IAM)** y compruebe que usted (o un grupo al que pertenezca) tenga al menos acceso de colaborador al grupo de recursos.

Si recibe un error de NoRegisteredProviderFound o MissingSubscriptionRegistration, consulte [Solución de errores comunes de implementación de Azure con Azure Resource Manager](../azure-resource-manager/resource-manager-common-deployment-errors.md).

## <a name="list-and-show-clusters"></a>Enumeración y visualización de clústeres
1. Inicie sesión en [https://portal.azure.com](https://portal.azure.com).
2. Haga clic en **Clústeres de HDInsight** en el menú de la izquierda para mostrar los clústeres existentes. Si no ve **Clústeres de HDInsight**, primero haga clic en **Todos los servicios**.
3. Haga clic en el nombre del clúster. Si la lista de clústeres es extensa, puede usar el filtro de la parte superior de la página.
4. Haga clic en un clúster de la lista para ver la página de información general:

    ![Aspectos básicos de clústeres de HDInsight de Azure Portal](./media/hdinsight-administer-use-portal-linux/hdinsight-essentials.png)**Menú de información general:**
    * **Panel**: abre la interfaz de usuario web de Ambari para el clúster.
    * **Secure Shell**: muestra las instrucciones para conectarse al clúster mediante la conexión de Secure Shell (SSH).
    * **Escalar clúster**: Permite cambiar el número de nodos de trabajo para este clúster.
    * **Mover**: mueve el clúster a otro grupo de recursos o a otra suscripción.
    * **Eliminar**: elimina el clúster.

    **Menú de la izquierda:**
    * **Registros de actividad**: consultar y mostrar registros de actividad.
    * **Access Control (IAM)**: usar las asignaciones de roles.  Vea [Uso de asignaciones de roles para administrar el acceso a los recursos de la suscripción de Azure](../role-based-access-control/role-assignments-portal.md).
    * **Etiquetas**: las etiquetas permiten establecer pares clave-valor para definir una taxonomía personalizada de Cloud Services. Por ejemplo, puede crear una clave denominada **proyecto**y luego usar un valor común para todos los servicios asociados a un proyecto específico.
    * **Diagnosticar y solucionar problemas**: mostrar información sobre solución de problemas.
    * **Bloqueos**: agregar bloqueos para evitar la modificación o eliminación del clúster.
    * **Script de automatización**: mostrar y exportar la plantilla de Azure Resource Manager para el clúster. Actualmente, solo se puede exportar la cuenta de Azure Storage dependiente. Vea [Creación de clústeres de Hadoop basados en Linux en HDInsight con plantillas de Azure Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md).
    * **Inicio rápido**: muestra información que le ayuda a empezar a usar HDInsight.
    * **Herramientas para HDInsight**: información de ayuda para herramientas relacionadas con HDInsight.
    * **Subscription Core Usage** (Uso de núcleo de suscripción): mostrar los núcleos usados y disponibles para la suscripción.
    * **Escalar clúster**: aumente o disminuya el número de nodos de trabajo del clúster. Vea [Escalado de clústeres](hdinsight-administer-use-management-portal.md#scale-clusters).
    * **Inicio de sesión mediante Secure Shell y el clúster**: muestra las instrucciones para conectarse al clúster mediante la conexión de Secure Shell (SSH). Para más información, consulte [Uso SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).
    * **Asociado de HDInsight**: agrega o quita el asociado actual de HDInsight.
    * **Tiendas de metadatos externas**: consulte las tiendas de metadatos de Hive y Oozie. Las tiendas de metadatos solo pueden configurarse durante el proceso de creación del clúster. Vea [Uso de Hive/Oozie Metastore](hdinsight-hadoop-provision-linux-clusters.md#use-hiveoozie-metastore).
    * **Acciones de script**: ejecuta scripts de Bash en el clúster. Consulte [Personalización de clústeres de HDInsight mediante la acción de scripts (Linux)](hdinsight-hadoop-customize-cluster-linux.md).
    * **Aplicaciones**: agregar o quitar aplicaciones de HDInsight.  Vea [Instalación de aplicaciones de HDInsight personalizadas](hdinsight-apps-install-custom-applications.md).
    * **Supervisión**: supervise el clúster en Azure Log Analytics.
    * **Propiedades**: muestra las propiedades del clúster.
    * **Cuentas de almacenamiento**: ver las cuentas de almacenamiento y las claves. Las cuentas de almacenamiento se configuran durante el proceso de creación del clúster.
    * **Acceso a Data Lake Store**: configure el acceso a Data Lake Store.  Consulte [Creación de clústeres de HDInsight con Data Lake Store mediante Azure Portal](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).
    * **Estados de los recursos**: consulte [Información general sobre Estado de los recursos de Azure](../service-health/resource-health-overview.md).
    * **Nueva solicitud de soporte técnico**: permite crear una incidencia de soporte técnico con el soporte técnico de Microsoft.
    
6. Haga clic en **Propiedades**.

    Las propiedades son:

   * **Nombre del clúster**: nombre del clúster.
   * **URL del clúster**: la dirección URL de la interfaz web de Ambari.
   * **Secure Shell (SSH)**: el nombre de usuario y el nombre de host que se usarán para acceder al clúster mediante SSH.
   * **Estado**: puede ser Aborted, Accepted, ClusterStorageProvisioned, AzureVMConfiguration, HDInsightConfiguration, Operational, Running, Error, Deleting, Deleted, Timedout, DeleteQueued, DeleteTimedout, DeleteError, PatchQueued, CertRolloverQueued, ResizeQueued o ClusterCustomization.
   * **Región**: ubicación de Azure. Para una lista de ubicaciones de Azure admitidas, vea el cuadro de lista desplegable **Región** en [Precios de HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).
   * **Fecha de creación**: la fecha en que se implementó el clúster.
   * **Sistema operativo**: **Windows** o **Linux**.
   * **Tipo**: Hadoop, HBase, Storm, Spark.
   * **Versión**. Consulte [Versiones de HDInsight](hdinsight-component-versioning.md).
   * **Suscripción**: nombre de la suscripción.
   * **Origen de datos predeterminado**: el sistema de archivos predeterminado del clúster.
   * **Tamaño de los nodos de trabajo**: el tamaño de máquina virtual seleccionado de los nodos de trabajo.
   * **Tamaño de nodo principal**: el tamaño de máquina virtual seleccionado de los nodos principales.
   * **Red virtual**: el nombre de la red virtual en la que se implementa el clúster (en caso de haber seleccionado uno durante la implementación).

## <a name="delete-clusters"></a>Eliminación de clústeres
Al eliminar un clúster, no se elimina la cuenta de almacenamiento predeterminada ni otras cuentas de almacenamiento vinculadas. Puede volver a crear el clúster con las mismas cuentas de almacenamiento y las mismas tiendas de metadatos. Se recomienda usar un nuevo contenedor de blobs predeterminado cuando vuelva a crear el clúster.

1. Inicie sesión en el [portal][azure-portal].
2. Haga clic en **Clústeres de HDInsight** en el menú de la izquierda. Si no ve **Clústeres de HDInsight**, primero haga clic en **Todos los servicios**.
3. Haga clic en el clúster que desea eliminar.
4. Haga clic en **Eliminar** en el menú superior y luego siga las instrucciones.

Vea también [Pausa o apagado de clústeres](#pauseshut-down-clusters).

## <a name="add-additional-storage-accounts"></a>Adición de cuentas de almacenamiento adicionales

Puede agregar más cuentas de Azure Storage y cuentas de Azure Data Lake Store una vez creado un clúster. Para más información, consulte [Adición de más cuentas de almacenamiento a HDInsight](./hdinsight-hadoop-add-storage.md).

## <a name="scale-clusters"></a>Escalado de clústeres
La característica de escalado de clústeres permite cambiar el número de nodos de trabajo que usa un clúster de Azure HDInsight sin necesidad de volver a crear el clúster.

> [!NOTE]
> Solo son compatibles los clústeres con la versión 3.1.3 de HDInsight, o superior. Si no está seguro de la versión del clúster, puede comprobar la página de propiedades.  Consulte [Enumeración y visualización de clústeres](#list-and-show-clusters).
>
>

A continuación se muestra cómo el efecto de cambiar el número de nodos de datos varía para cada tipo de clúster compatible con HDInsight:

* Hadoop

    Puede aumentar sin ningún problema la cantidad de nodos de trabajo en un clúster de Hadoop que se encuentre en ejecución, sin que afecte a ningún trabajo pendiente o en ejecución. También se pueden enviar trabajos nuevos mientras la operación está en curso. Los errores que puedan surgir en una operación de escalado se enfrentan oportunamente, por lo que el clúster siempre queda en estado funcional.

    Cuando se realiza la reducción vertical de un clúster de Hadoop al disminuir la cantidad de nodos de datos, se reinician algunos de los servicios del clúster. Este comportamiento hace que todos los trabajos pendientes y en ejecución fallen al completarse la operación de escalado. Sin embargo, puede volver a enviar los trabajos una vez finalizada la operación.
* hbase

    Puede agregar nodos sin problemas al clúster de HBase mientras se encuentra en ejecución, así como eliminarlos. Los servidores regionales se equilibran automáticamente en unos pocos minutos tras completar la operación de escalado. Sin embargo, puede equilibrar manualmente los servidores regionales iniciando sesión en el nodo principal del clúster y ejecutando los comandos siguientes desde una ventana del símbolo del sistema:

    ```bash
    >pushd %HBASE_HOME%\bin
    >hbase shell
    >balancer
    ```

    Para más información sobre el uso del shell de HBase, vea [Introducción a un ejemplo de Apache HBase en HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md).

* Storm

    Puede agregar o quitar sin problemas nodos de datos de su clúster de Storm mientras se encuentra en ejecución. Sin embargo, después de finalizar correctamente la operación de escalado, deberá volver a equilibrar la topología.

    Esto se puede realizar de dos formas:

  * La interfaz de usuario web de Storm
  * La herramienta de la interfaz de línea de comandos (CLI)

    Consulte la [documentación de Apache Storm](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) para obtener más detalles.

    La interfaz de usuario web de Storm se encuentra disponible en el clúster de HDInsight:

    ![Reequilibrio de escalado de HDInsight Storm](./media/hdinsight-administer-use-portal-linux/hdinsight-portal-scale-cluster-storm-rebalance.png)

    El siguiente es un comando de la CLI de ejemplo para volver a equilibrar la topología de Storm:

    ```cli
    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10
    ```

**Para escalar clústeres**

1. Inicie sesión en el [portal][azure-portal].
2. Haga clic en **Clústeres de HDInsight** en el menú de la izquierda.
3. Haga clic en el clúster que desea escalar.
3. Haga clic en **Escalar clúster**.
4. Escriba el **Número de nodos de trabajo**. El límite del número de nodos del clúster varía según las suscripciones de Azure. Puede ponerse en contacto con el servicio de soporte relacionado con la facturación para aumentar el límite.  La información del costo refleja los cambios realizados en el número de nodos.

    ![HDInsight hadoop hbase storm spark escalar](./media/hdinsight-administer-use-portal-linux/hdinsight-portal-scale-cluster.png)

## <a name="pauseshut-down-clusters"></a>Pausa o apagado de clústeres

La mayoría de los trabajos de Hadoop son trabajos por lotes que se ejecutan solo ocasionalmente. En la mayoría de los clústeres de Hadoop, hay grandes períodos de tiempo en los que el clúster no se usa para el procesamiento. Con HDInsight, los datos se almacenan en Azure Storage, por lo que puede eliminar un clúster de forma segura cuando no está en uso.
También se le cargará por un clúster de HDInsight aunque no esté en uso. Como en muchas ocasiones los cargos por el clúster son más que los cargos por el almacenamiento, desde el punto de vista económico tiene sentido eliminar clústeres cuando no estén en uso.

Hay muchas maneras de programar el proceso:

* Usar Azure Data Factory. Consulte [Creación de clústeres de Hadoop basados en Linux en HDInsight a petición con Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md) para crear servicios vinculados a HDInsight a petición.
* Usar Azure PowerShell.  Vea [Análisis de datos de retrasos de vuelos](hdinsight-analyze-flight-delay-data.md).
* Uso de CLI de Azure. Consulte [Administrar clústeres de HDInsight con la CLI de Azure](hdinsight-administer-use-command-line.md).
* Usar .NET SDK de HDInsight. Vea [Envío de trabajos de Hadoop](hadoop/submit-apache-hadoop-jobs-programmatically.md).

Para información sobre precios, vea [Precios de HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/). Para eliminar un clúster desde el portal, vea [Eliminación de clústeres](#delete-clusters)

## <a name="move-cluster"></a>Movimiento de un clúster

Puede mover un clúster de HDInsight a otro grupo de recursos de Azure u otra suscripción.  Consulte [Enumeración y visualización de clústeres](#list-and-show-clusters).

## <a name="upgrade-clusters"></a>Actualización de clústeres

Vea [Actualización del clúster de HDInsight a una versión más reciente](./hdinsight-upgrade-cluster.md).

## <a name="open-the-ambari-web-ui"></a>Apertura de la interfaz de usuario web de Ambari

Ambari proporciona una intuitiva y sencilla interfaz de usuario web de administración de Hadoop respaldada por sus API RESTful. Ambari permite a los administradores de sistema administrar y supervisar clústeres de Hadoop.

1. Abra un clúster de HDInsight en Azure Portal.  Consulte [Enumeración y visualización de clústeres](#list-and-show-clusters).
2. Haga clic en **Panel de clúster**.

    ![Menú de clúster de Hadoop de HDInsight](./media/hdinsight-administer-use-portal-linux/hdinsight-azure-portal-cluster-menu.png)

1. Escriba el nombre de usuario y la contraseña del clúster.  El nombre de usuario predeterminado del clúster es _admin_. La interfaz de usuario web de Ambari presentará el aspecto siguiente:

    ![Interfaz de usuario web de Ambari para Hadoop de HDInsight](./media/hdinsight-administer-use-portal-linux/hdinsight-hadoop-ambari-web-ui.png)

Para más información, consulte [Administración de clústeres de HDInsight con la interfaz de usuario web de Ambari](hdinsight-hadoop-manage-ambari.md).

## <a name="change-passwords"></a>Cambio de contraseñas
Un clúster de HDInsight puede tener dos cuentas de usuario. La cuenta de usuario del clúster de HDInsight (también conocida como cuenta de usuario HTTP) y la cuenta de usuario SSH se crean durante el proceso de creación. Puede usar la interfaz de usuario web de Ambari para cambiar el nombre de usuario y la contraseña de la cuenta de usuario del clúster y las acciones de script para cambiar la cuenta de usuario de SSH

### <a name="change-the-cluster-user-password"></a>Cambio de la contraseña de usuario del clúster
Puede utilizar la interfaz de usuario web de Ambari para cambiar el nombre de usuario y la contraseña del clúster. Para iniciar sesión en Ambari, debe usar el nombre de usuario y la contraseña de clúster existente.

> [!NOTE]
> El cambio de la contraseña de usuario (admin) del clúster puede provocar que las acciones de script que se ejecutan en este clúster no lo hagan correctamente. Si tiene cualquier acción de script persistente cuyo destino son nodos de trabajo, estos scripts pueden producir un error al agregar nodos al clúster a través de operaciones de cambio de tamaño. Para más información sobre acciones de script, consulte [Personalización de clústeres de HDInsight mediante la acción de scripts (Linux)](hdinsight-hadoop-customize-cluster-linux.md).
>
>

1. Inicie sesión en la IU web de Ambari con las credenciales de usuario del clúster de HDInsight. El nombre de usuario predeterminado es **admin**. La dirección URL es **https://&lt;nombre del clúster de HDInsight > azurehdinsight.net**.
2. Haga clic en **Administración** en el menú superior y después en "Administrar Ambari".
3. En el menú de la izquierda, haga clic en **Usuarios**.
4. Haga clic en **Administrador**.
5. Haga clic en **Cambiar contraseña**.

A continuación, Ambari cambia la contraseña en todos los nodos del clúster.

### <a name="change-the-ssh-user-password"></a>Cambio de la contraseña de usuario de SSH
1. Con un editor de texto, guarde el texto siguiente como un archivo llamado **changepassword.sh**.

    > [!IMPORTANT]
    > Debe utilizar un editor que use LF como final de líneas. Si el editor utiliza CRLF, el script no funcionará.

    ```bash
    #! /bin/bash
    USER=$1
    PASS=$2
    usermod --password $(echo $PASS | openssl passwd -1 -stdin) $USER
    ```

2. Cargue el archivo en una ubicación de almacenamiento a la que pueda accederse desde HDInsight con una dirección HTTP o HTTPS. Por ejemplo, un almacén de archivos público como OneDrive o Almacenamiento de blobs de Azure. Guarde el identificador URI (dirección HTTP o HTTPS) en el archivo, ya que este URI se necesitará en el paso siguiente.
3. En Azure Portal, haga clic en **clústeres de HDInsight**.
4. Haga clic en el clúster de HDInsight.
4. Haga clic en **Acciones de scripts**.
4. En la hoja **Acciones de script**, seleccione **Enviar nuevo**. Cuando aparezca la hoja **Enviar acción de script**, especifique la siguiente información:

   | Campo | Valor |
   | --- | --- |
   | NOMBRE |Cambio de contraseña de SSH |
   | URI de script de Bash |El identificador URI del archivo changepassword.sh |
   | Nodos (principal, de trabajo, nimbus, supervisor, Zookeeper, etc.) |✓ para todos los tipos de nodo enumerados |
   | Parámetros |Escriba el nombre de usuario de SSH y la contraseña nueva. Debe haber un espacio entre el nombre de usuario y la contraseña. |
   | Conservar esta acción de script... |Deje este campo en sin activar. |
5. Seleccione **Crear** para aplicar el script. Una vez que finalice el script, puede conectarse al clúster mediante SSH con la nueva contraseña.

## <a name="grantrevoke-access"></a>Concesión o revocación del acceso
Los clústeres de HDInsight tienen los siguientes servicios web HTTP (todos estos servicios tienen extremos RESTful):

* ODBC
* JDBC
* Ambari
* Oozie
* Templeton

De manera predeterminada, estos servicios se conceden para el acceso. Puede revocar o conceder el acceso mediante la [CLI de Azure](hdinsight-administer-use-command-line.md#enabledisable-http-access-for-a-cluster) y [Azure PowerShell](hdinsight-administer-use-powershell.md#grantrevoke-access).

## <a name="find-the-subscription-id"></a>Búsqueda del identificador de la suscripción

**Para buscar los identificadores de la suscripción de Azure**

1. Inicie sesión en el [portal][azure-portal].
2. Haga clic en **Suscripciones**. Cada suscripción tiene un nombre y un identificador.

Cada clúster está asociado a una suscripción de Azure. El identificador de la suscripción se muestra en el icono **Esencial** del clúster. Vea [Enumeración y visualización de clústeres](#list-and-show-clusters).

## <a name="find-the-resource-group"></a>Búsqueda del grupo de recursos
En el modo de Azure Resource Manager, cada clúster de HDInsight se crea con un grupo de Azure Resource Manager. El grupo de Resource Manager al que pertenece un clúster aparece en:

* La lista de clústeres tiene una columna **Grupo de recursos** .
* Icono **Esencial** del clúster.  

Consulte [Enumeración y visualización de clústeres](#list-and-show-clusters).

## <a name="find-the-storage-accounts"></a>Búsqueda de las cuentas de almacenamiento

Los clústeres de HDInsight usan una cuenta de Azure Storage o Azure Data Lake Store para almacenar datos. Cada clúster de HDInsight puede tener una cuenta de almacenamiento predeterminada y una serie de cuentas de almacenamiento vinculadas. Para enumerar las cuentas de almacenamiento, primero abra el clúster desde el portal y luego haga clic en **Cuentas de almacenamiento**:

![Cuentas de almacenamiento de clúster de HDInsight](./media/hdinsight-administer-use-portal-linux/hdinsight-storage-accounts.png)

En la captura de pantalla anterior hay una columna __Default__ que indica si la cuenta es la cuenta de almacenamiento predeterminada.

Para enumerar las cuentas de Data Lake Store, haga clic en **Acceso a Data Lake Store** en la captura de pantalla anterior.

## <a name="run-hive-queries"></a>Ejecución de consultas de Hive
No puede ejecutar un trabajo de Hive directamente desde el Portal de Azure, pero puede usar la vista de Hive en la IU web de Ambari.

**Para ejecutar las consultas de Hive mediante la vista de Hive de Ambari**

1. Inicie sesión en la IU web de Ambari con las credenciales de usuario del clúster de HDInsight. El nombre de usuario predeterminado es **admin**. La dirección URL es **https://&lt;nombre del clúster de HDInsight > azurehdinsight.net**.
2. Abra la vista de Hive como se muestra en la siguiente captura de pantalla:  

    ![Vista de hive de HDInsight](./media/hdinsight-administer-use-portal-linux/hdinsight-hive-view.png)

3. Haga clic en **Query** (Consulta) en el menú superior.
4. Escriba una consulta de Hive en el **Editor de consultas** y haga clic en **Execute** (Ejecutar).

## <a name="monitor-jobs"></a>Supervisión de trabajos
Consulte [Administración de clústeres de HDInsight con la interfaz de usuario web de Ambari](hdinsight-hadoop-manage-ambari.md#monitoring).

## <a name="browse-files"></a>Examinar archivos
Con el Portal de Azure, puede examinar el contenido del contenedor predeterminado.

1. Inicie sesión en [https://portal.azure.com](https://portal.azure.com).
2. Haga clic en **Clústeres de HDInsight** en el menú de la izquierda para mostrar los clústeres existentes.
3. Haga clic en el nombre del clúster. Si la lista de clústeres es extensa, puede usar el filtro de la parte superior de la página.
4. Haga clic en **Cuentas de almacenamiento** en el menú izquierdo del clúster.
5. Haga clic en una cuenta de almacenamiento.
7. Haga clic en el icono **Blobs** .
8. Haga clic en el nombre del contenedor predeterminado.

## <a name="monitor-cluster-usage"></a>Supervisión del uso de los clústeres
La sección **Uso** de la hoja del clúster de HDInsight muestra información sobre el número de núcleos disponibles con la suscripción para su uso con HDInsight, así como el número de núcleos asignados al clúster y cómo se asignan a los nodos de este clúster. Vea [Enumeración y visualización de clústeres](#list-and-show-clusters).

> [!IMPORTANT]
> Para supervisar los servicios que proporciona el clúster de HDInsight, debe usar la web de Ambari o la API de REST de Ambari. Para obtener más información sobre el uso de Ambari, consulte [Administración de clústeres de HDInsight con Ambari](hdinsight-hadoop-manage-ambari.md)

## <a name="connect-to-a-cluster"></a>Conectarse a un clúster

* [Uso de Hive con HDInsight](hadoop/apache-hadoop-use-hive-ambari-view.md)
* [Uso de SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="next-steps"></a>Pasos siguientes

En este artículo ha aprendido algunas funciones administrativas básicas. Para obtener más información, consulte los artículos siguientes:

* [Administración de HDInsight con PowerShell de Azure](hdinsight-administer-use-powershell.md)
* [Administración de HDInsight con la CLI de Azure](hdinsight-administer-use-command-line.md)
* [Creación de clústeres de HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
* [Lea más sobre el uso de la interfaz de usuario web de Ambari](hdinsight-hadoop-manage-ambari.md)
* [Detalles sobre el uso de la API de REST de Ambari](hdinsight-hadoop-manage-ambari-rest-api.md)
* [Uso de Hive en HDInsight](hadoop/hdinsight-use-hive.md)
* [Uso de Pig en HDInsight](hadoop/hdinsight-use-pig.md)
* [Uso de Sqoop en HDInsight](hadoop/hdinsight-use-sqoop.md)
* [Introducción a HDInsight de Azure](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [¿Qué versión de Hadoop tiene HDInsight de Azure?](hdinsight-component-versioning.md)

[azure-portal]: https://portal.azure.com
[image-hadoopcommandline]: ./media/hdinsight-administer-use-portal-linux/hdinsight-hadoop-command-line.png "Línea de comandos de Hadoop"
