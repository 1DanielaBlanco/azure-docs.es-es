---
title: 'Acceso a los registros de aplicación de YARN de Hadoop mediante programación: Azure'
description: La aplicación de Access se registra mediante programación en un clúster de Hadoop en HDInsight.
services: hdinsight
author: jasonwhowell
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/25/2017
ms.author: jasonh
ROBOTS: NOINDEX
ms.openlocfilehash: e92f9f7bb49b0b7cc33c73a9c5eb2d0ca7532592
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2018
ms.locfileid: "43094408"
---
# <a name="access-yarn-application-logs-on-windows-based-hdinsight"></a>Acceso a registros de aplicación de YARN en HDInsight basado en Windows
En este documento se explica cómo acceder a los registros de aplicaciones de YARN que finalicen en un clúster Hadoop en HDInsight de Azure basado en Windows.

> [!IMPORTANT]
> La información contenida en este documento es específica de los clústeres de HDInsight basados en Windows. Linux es el único sistema operativo que se usa en la versión 3.4 de HDInsight, o en las superiores. Consulte la información sobre la [retirada de HDInsight en Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement). Para obtener información sobre cómo acceder a registros de YARN en clústeres de HDInsight basados en Linux, vea [Acceso a registros de aplicación de YARN en Hadoop basado en Linux en HDInsight](hdinsight-hadoop-access-yarn-app-logs-linux.md)
>


### <a name="prerequisites"></a>Requisitos previos
* Un clúster de HDInsight basado en Windows  Consulte [Creación de clústeres de Hadoop basados en Windows en HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

## <a name="yarn-timeline-server"></a>Servidor de escala de tiempo de YARN
El <a href="http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html" target="_blank">Servidor de escala de tiempo de YARN</a> proporciona información genérica acerca de las aplicaciones completadas, así como información de aplicaciones específicas de marco a través de dos interfaces diferentes. Concretamente:

* Se ha habilitado el almacenamiento y la recuperación de información de aplicaciones genéricas en clústeres de HDInsight con la versión 3.1.1.374 o superiores.
* El componente de información de aplicaciones específica del marco del servidor de la escala de tiempo no está disponible actualmente en los clústeres de HDInsight.

La información genérica sobre aplicaciones incluye los siguientes tipos de datos:

* El ID de la aplicación, que es el identificador único de una aplicación.
* El usuario que ha iniciado la aplicación.
* La información acerca de los intentos realizados para completar la aplicación.
* Los contenedores usados por cualquier intento de aplicación concreto.

En los clústeres de HDInsight, esta información la almacena Azure Resource Manager. La información se guarda en el almacén de historial del almacenamiento predeterminado del clúster. Estos datos genéricos sobre aplicaciones completadas se pueden recuperar a través de una API de REST:

    GET on https://<cluster-dns-name>.azurehdinsight.net/ws/v1/applicationhistory/apps


## <a name="yarn-applications-and-logs"></a>Registros y aplicaciones de YARN
YARN admite varios modelos de programación al desacoplar la administración de recursos de la programación/supervisión de aplicaciones. Así, usa una instancia de *Resource Manager* (RM) global por nodo de trabajo *NodeManagers* (NM) y por aplicación *ApplicationMasters* (AM). El AM por aplicación negocia recursos (CPU, memoria, disco, red) para ejecutar la aplicación con el RM. El RM funciona con NM para conceder estos recursos como *contenedores*. El AM es responsable del seguimiento del progreso de los contenedores asignados a él por el RM. Una aplicación puede requerir muchos contenedores según la naturaleza de la aplicación.

* Cada aplicación puede constar de varios *intentos de aplicación*. 
* Los contenedores se conceden a un intento específico de una aplicación. 
* Un contenedor proporciona el contexto para una unidad de trabajo básica. 
* El trabajo que se realiza en el contexto de un contenedor se realiza solo en el nodo de trabajo en el que se asignó el contenedor. 

Para más información, consulte los [conceptos de YARN][YARN-concepts].

Los registros de aplicación (y los registros de contenedor asociados) son fundamentales en la depuración de las aplicaciones de Hadoop problemáticas. YARN proporciona un buen marco para recopilar, agregar y almacenar registros de aplicaciones con la característica de [agregación de registros][log-aggregation]. La característica Agregación de registro hace que el acceso a los registros de aplicaciones sea más determinante, ya que agrega los registros en todos los contenedores en un nodo de trabajo y los almacena como un archivo de registro agregado por nodo de trabajo en el sistema de archivos predeterminado después de que se complete una aplicación. Su aplicación puede utilizar cientos o miles de contenedores, pero los registros para todos los contenedores que se ejecutan en un nodo de trabajo único se agregan siempre en un archivo único, lo que da como resultado un archivo por nodo de trabajo que usa la aplicación. La agregación de registro está habilitada de forma predeterminada en los clústeres de HDInsight (versión 3.0 y posteriores). Los registros agregados se pueden encontrar en el contenedor predeterminado del clúster en la siguiente ubicación:

    wasb:///app-logs/<user>/logs/<applicationId>

En dicha ubicación, *usuario* es el nombre del usuario que inició la aplicación. *applicationId*, por su parte, es el identificador único de una aplicación según la asignación del RM de YARN.

Los registros agregados no son legibles directamente tal como se escriben en un [TFile][T-file], [formato binario][binary-format] indexado por el contenedor. YARN ofrece herramientas CLI para volcar estos registros como texto sin formato para aplicaciones o contenedores de interés. Puede ver estos registros como texto sin formato al ejecutar uno de los siguiente comandos de YARN directamente en los nodos del clúster (después de conectarse a él a través de RDP):

    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>


## <a name="yarn-resourcemanager-ui"></a>Interfaz de usuario de ResourceManager de YARN
La interfaz de usuario de ResourceManager de YARN se ejecuta en el nodo principal del clúster, y puede tener acceso a ella mediante el panel del Portal de Azure:

1. Inicie sesión en el [portal de Azure](https://portal.azure.com/).
2. En el menú de la izquierda, haga clic en **Examinar**, haga clic en **Clústeres de HDInsight** y haga clic en un clúster basado en Windows que desea que tenga acceso a los registros de aplicación de YARN.
3. En el menú superior, haga clic en **Panel**. Verá una página abierta en una nueva pestaña del explorador denominada **Consola de consultas de HDInsight**.
4. Desde **Consola de consultas de HDInsight**, haga clic en **IU de YARN**.

[YARN-timeline-server]:http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html
[log-aggregation]:http://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/
[T-file]:https://issues.apache.org/jira/secure/attachment/12396286/TFile%20Specification%2020081217.pdf
[binary-format]:https://issues.apache.org/jira/browse/HADOOP-3315
[YARN-concepts]:http://hortonworks.com/blog/apache-hadoop-yarn-concepts-and-applications/
