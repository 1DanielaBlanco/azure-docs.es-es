---
title: Creación de una factoría de datos de Azure con la interfaz de usuario de Azure Data Factory | Microsoft Docs
description: Cree una factoría de datos con una canalización que copie los datos de una ubicación de Azure Blob Storage a otra.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: hero-article
ms.date: 06/20/2018
ms.author: jingwang
ms.openlocfilehash: 69c0661f515f062a6a99b0692130d52eb23d20d6
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/20/2018
ms.locfileid: "36285906"
---
# <a name="create-a-data-factory-by-using-the-azure-data-factory-ui"></a>Creación de una factoría de datos con la interfaz de usuario de Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service that you are using:"]
> * [Versión 1: Disponibilidad general](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Versión 2: versión preliminar](quickstart-create-data-factory-portal.md)

En esta guía de inicio rápido se describe cómo usar la interfaz de usuario de Azure Data Factory para crear y supervisar una factoría de datos. La canalización que ha creado en esta factoría de datos *copia* los datos de una carpeta a otra en Azure Blob Storage. Para ver un tutorial acerca de cómo *transformar* datos mediante Azure Data Factory, consulte [Transformación de datos en la nube mediante la actividad de Spark en Azure Data Factory](tutorial-transform-data-spark-portal.md). 


> [!NOTE]
> Si no está familiarizado con Azure Data Factory, consulte [Introduction to Azure Data Factory](data-factory-introduction.md) antes de seguir los pasos de esta guía de inicio rápido. 
>
> Este artículo se aplica a la versión 2 de Data Factory, que actualmente se encuentra en versión preliminar. Si usa la versión 1 del servicio, que está disponible con carácter general, consulte el [tutorial de la versión 1 de Data Factory](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

[!INCLUDE [data-factory-quickstart-prerequisites](../../includes/data-factory-quickstart-prerequisites.md)] 

### <a name="video"></a>Vídeo 
Ver este vídeo le ayudará a conocer la interfaz de usuario de Data Factory: 
>[!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Visually-build-pipelines-for-Azure-Data-Factory-v2/Player]

## <a name="create-a-data-factory"></a>Crear una factoría de datos

1. Inicie el explorador web **Microsoft Edge** o **Google Chrome**. Actualmente, la interfaz de usuario de Data Factory solo se admite en los exploradores web Microsoft Edge y Google Chrome.
2. Vaya a [Azure Portal](https://portal.azure.com). 
3. Seleccione **Nuevo** en el menú de la izquierda, seleccione **Datos y análisis** y, después, seleccione **Data Factory**. 
   
   ![Selección de la factoría de datos en el panel Nuevo](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)
2. En la página **Nueva factoría de datos**, escriba **ADFTutorialDataFactory** en **Nombre**. 
      
   ![Página de la nueva factoría de datos](./media/quickstart-create-data-factory-portal/new-azure-data-factory.png)
 
   El nombre de Azure Data Factory debe ser *único de forma global*. Si ve el siguiente error, cambie el nombre de la factoría de datos (por ejemplo, **&lt;suNombre&gt;ADFTutorialDataFactory**) e intente crearlo de nuevo. Para conocer las reglas de nomenclatura de los artefactos de Data Factory, consulte el artículo [Azure Data Factory: reglas de nomenclatura](naming-rules.md).
  
   ![Mensaje de error cuando un nombre no está disponible](./media/quickstart-create-data-factory-portal/name-not-available-error.png)
3. En **Suscripción**, seleccione la suscripción de Azure donde desea crear la factoría de datos. 
4. Para **Grupo de recursos**, realice uno de los siguientes pasos:
     
   - Seleccione en primer lugar **Usar existente** y después un grupo de recursos de la lista. 
   - Seleccione **Crear nuevo**y escriba el nombre de un grupo de recursos.   
         
   Para obtener más información sobre los grupos de recursos, consulte [Uso de grupos de recursos para administrar los recursos de Azure](../azure-resource-manager/resource-group-overview.md).  
4. En **Versión**, seleccione **V2 (versión preliminar)**.
5. En **Ubicación**, seleccione la ubicación de la factoría de datos.

   La lista muestra solo las ubicaciones que admite Data Factory. Los almacenes de datos (como Azure Storage y Azure SQL Database) y los procesos (como Azure HDInsight) que usan Data Factory pueden encontrarse en otras ubicaciones.
6. Seleccione **Anclar al panel**.     
7. Seleccione **Crear**.
8. En el panel, verá el icono siguiente con el estado **Deploying Data Factory** (Implementando Data Factory): 

   ![Icono "Deploying Data Factory" (Implementando Data Factory)](media//quickstart-create-data-factory-portal/deploying-data-factory.png)
9. Una vez completada la creación, verá la página **Data Factory**. Seleccione el icono **Author & Monitor** (Creación y supervisión) para iniciar la aplicación de interfaz de usuario de Azure Data Factory en una pestaña independiente.
   
   ![Página principal de la factoría de datos, con el icono Author & Monitor (Creación y supervisión)](./media/quickstart-create-data-factory-portal/data-factory-home-page.png)
10. En la página de **introducción**, cambie a la pestaña **Creador** del panel izquierdo. 

    ![Página de introducción](./media/quickstart-create-data-factory-portal/get-started-page.png)

## <a name="create-a-linked-service"></a>Creación de un servicio vinculado
En este procedimiento, creará un servicio vinculado para vincular la cuenta de Azure Storage con la factoría de datos. El servicio vinculado tiene la información de conexión que usa el servicio Data Factory en el entorno de tiempo de ejecución para conectarse a él.

1. Seleccione **Conexiones** y, a continuación, seleccione el botón **Nueva** en la barra de herramientas. 

   ![Botones para crear una conexión nueva](./media/quickstart-create-data-factory-portal/new-connection-button.png)    
2. En la página **New Linked Service** (Nuevo servicio vinculado), seleccione **Azure Blob Storage** y después **Continue** (Continuar). 

   ![Selección del icono Azure Blob Storage](./media/quickstart-create-data-factory-portal/select-azure-blob-linked-service.png)
3. Complete los siguientes pasos: 

   a. En **Name** (Nombre), escriba **AzureStorageLinkedService**.

   b. En **Storage account name** (Nombre de la cuenta de Storage), seleccione el nombre de la cuenta correspondiente.

   c. Seleccione **Test connection** (Probar conexión) para confirmar que el servicio Data Factory puede conectarse a la cuenta de almacenamiento. 

   d. Seleccione **Save** (Guardar) para guardar el servicio vinculado. 

   ![Configuración del servicio vinculado de Azure Storage](./media/quickstart-create-data-factory-portal/azure-storage-linked-service.png) 

## <a name="create-datasets"></a>Creación de conjuntos de datos
En este procedimiento, va a crear dos conjuntos de datos: **InputDataset** y **OutputDataset**. Estos conjuntos de datos son de tipo **AzureBlob**. Hacen referencia al servicio vinculado de Azure Storage que creó en la sección anterior. 

El conjunto de datos de entrada representa los datos de origen en la carpeta de entrada. En la definición del conjunto de datos de entrada, se especifica el contenedor de blobs (**adftutorial**), la carpeta (**input**) y el archivo (**emp.txt**) que contiene los datos de origen. 

El conjunto de datos de salida representa los datos que se copian en el destino. En la definición del conjunto de datos de salida, se especifica el contenedor de blobs (**adftutorial**), la carpeta (**output**) y el archivo en el que se copian los datos. Cada ejecución de una canalización tiene un identificador único asociado a ella. Puede tener acceso a este identificador mediante el uso de la variable del sistema **RunId**. El nombre del archivo de salida se evalúa dinámicamente según el identificador de ejecución de la canalización.   

En la configuración del servicio vinculado se especifica la cuenta de Azure Storage que contiene los datos de origen. En la configuración del conjunto de datos de origen se especifica dónde residen exactamente los datos de origen (contenedor de blobs, carpeta y archivo). En la configuración del conjunto de datos receptor se especifica dónde se copian los datos (contenedor de blobs, carpeta y archivo). 
 
1. Haga clic en el botón **+** (Más) y seleccione **Dataset** (Conjunto de datos).

   ![Menú para crear un conjunto de datos](./media/quickstart-create-data-factory-portal/new-dataset-menu.png)
2. En la página **New Dataset** (Nuevo conjunto de datos), seleccione **Azure Blob Storage** y después **Finish** (Finalizar). 

   ![Selección de Azure Blob Storage](./media/quickstart-create-data-factory-portal/select-azure-blob-dataset.png)
3. En la pestaña **General** del conjunto de datos, escriba **InputDataset** en **Nombre**. 

4. Cambie a la pestaña **Connection** (Conexión) y complete los pasos siguientes: 

    a. En **Linked service** (Servicio vinculado), seleccione **AzureStorageLinkedService**.

    b. En **File path** (Ruta del archivo), seleccione el botón **Browse** (Examinar).

    ![Pestaña de conexión y botón para examinar](./media/quickstart-create-data-factory-portal/file-path-browse-button.png) c. En la ventana **Choose a file or folder** (Elegir un archivo o carpeta), vaya a la carpeta **input** del contenedor **adftutorial**, seleccione el archivo **emp.txt** y seleccione **Finish** (Finalizar).

    ![Búsqueda del archivo de entrada](./media/quickstart-create-data-factory-portal/choose-file-folder.png)
    
   d. (opcional) Seleccione **Preview data** (Vista previa de los datos) para obtener una vista previa de los datos del archivo emp.txt.     
5. Repita los pasos para crear el conjunto de datos de salida:  

   a. Haga clic en el botón **+** (Más) y seleccione **Dataset** (Conjunto de datos).

   b. En la página **New Dataset** (Nuevo conjunto de datos), seleccione **Azure Blob Storage** y después **Finish** (Finalizar).

   c. En la tabla **General**, especifique **OutputDataset** como nombre.

   d. En la pestaña **Conexión**, seleccione **AzureStorageLinkedService** como servicio vinculado y escriba **adftutorial/output** para la carpeta. Si la carpeta **output** no existe, la actividad de copia la crea en tiempo de ejecución.

## <a name="create-a-pipeline"></a>Crear una canalización 
En este procedimiento, va a crear y comprobar una canalización con una actividad de copia que utiliza los conjuntos de datos de entrada y de salida. La actividad de copia realiza una copia de los datos desde el archivo especificado en la configuración del conjunto de datos de entrada hasta el archivo especificado en la configuración del conjunto de datos de salida. Si el conjunto de datos de entrada especifica solo una carpeta (no el nombre de archivo), la actividad de copia realiza una copia de todos los archivos de la carpeta de origen al destino. 

1. Haga clic en el botón **+** (Más) y seleccione **Pipeline** (Canalización). 

   ![Menú para crear una canalización](./media/quickstart-create-data-factory-portal/new-pipeline-menu.png)
2. En la pestaña **General**, especifique **CopyPipeline** en **Nombre**. 

3. En el cuadro de herramientas **Activities** (Actividades), expanda **Data Flow** (Flujo de datos). Arrastre la actividad **Copy** (Copia) del cuadro de herramientas **Activities** (Actividades) a la superficie del diseñador de canalizaciones. También puede buscar actividades en el cuadro de herramientas **Activities** (Actividades). Especifique **CopyFromBlobToBlob** en **Name** (Nombre).

   ![Configuración general de la actividad de copia](./media/quickstart-create-data-factory-portal/copy-activity-general-settings.png)
4. Cambie a la pestaña **Source** (Origen) en la configuración de la actividad de copia y seleccione **InputDataset** para **Source Dataset** (Conjunto de datos de origen).

5. Cambie a la pestaña **Sink** (Receptor) en la configuración de la actividad de copia y seleccione **OutputDataset** para **Sink Dataset** (Conjunto de datos receptor).

6. Haga clic en **Validar** en la barra de herramientas de la canalización situada en la parte superior del lienzo para validar la configuración de la canalización. Confirme que la canalización se ha validado correctamente. Para cerrar la salida de la validación, haga clic en el botón **>>** (fecha derecha). 

## <a name="debug-the-pipeline"></a>Depuración de la canalización
En este paso va a depurar la canalización antes de implementarla en Data Factory. 

1. En la barra de herramientas de la canalización situada en la parte superior del lienzo, haga clic en **Depurar** para desencadenar una serie de pruebas. 
    
2. Confirme que ve el estado de ejecución de la canalización en la pestaña **Output** (Salida) de la configuración de la canalización situada en la parte inferior. 

3. Confirme que ve un archivo de salida en la carpeta **output** del contenedor **adftutorial**. Si no existe la carpeta de salida, el servicio Data Factory la crea automáticamente. 

## <a name="trigger-the-pipeline-manually"></a>Desencadenamiento manual de la canalización
En este procedimiento se implementan las entidades (servicios vinculados, conjuntos de datos, canalizaciones) en Azure Data Factory. A continuación, desencadenará manualmente una ejecución de la canalización. También puede publicar entidades en su propio repositorio de GIT de Visual Studio Team Services, lo cual se trata en [otro tutorial](tutorial-copy-data-portal.md?#configure-code-repository).

1. Antes de desencadenar una canalización, debe publicar las entidades en Data Factory. Seleccione **Publicar todo** en la parte superior para realizar la publicación. 

   ![Botón Publicar](./media/quickstart-create-data-factory-portal/publish-button.png)
2. Para desencadenar la canalización de forma manual, seleccione **Trigger** (Desencadenar) en la barra de herramientas de la canalización y seleccione **Trigger Now** (Desencadenar ahora). 

## <a name="monitor-the-pipeline"></a>Supervisar la canalización

1. Cambie a la pestaña **Monitor** (Supervisar) de la izquierda. Use el botón **Refresh** (Actualizar) para actualizar la lista.

   ![Pestaña para supervisar las ejecuciones de la canalización, con el botón Actualizar](./media/quickstart-create-data-factory-portal/monitor-trigger-now-pipeline.png)
2. Seleccione el vínculo **View Activity Runs** (Ver ejecuciones de actividad) en **Actions** (Acciones). En esta página puede ver el estado de la ejecución de la actividad de copia. 

   ![Ejecuciones de actividad de canalización](./media/quickstart-create-data-factory-portal/pipeline-activity-runs.png)
3. Para más información sobre la operación de copia, seleccione el vínculo **Details** (Detalles) (imagen de gafas) de la columna **Actions** (Acciones). Para más información sobre las propiedades, consulte [Introducción a la actividad de copia](copy-activity-overview.md). 

   ![Detalles de la operación de copia](./media/quickstart-create-data-factory-portal/copy-operation-details.png)
4. Confirme que ve un archivo nuevo en la carpeta **output** (salida). 
5. Puede volver a la vista **Pipeline Runs** (Ejecuciones de la canalización) desde la vista **Activity Runs** (Ejecuciones de la actividad). Para ello, seleccione el vínculo **Pipelines** (Canalizaciones). 

## <a name="trigger-the-pipeline-on-a-schedule"></a>Desencadenamiento de la canalización de forma programada
Este procedimiento es opcional en este tutorial. Puede crear un *programador de desencadenador* para programar la ejecución de la canalización periódicamente (cada hora, a diario, y así sucesivamente). En este procedimiento, va a crear un desencadenador que se ejecutará cada minuto hasta la fecha y hora de finalización que se especifique. 

1. Cambie a la pestaña **Creador**. 

2. Vaya a la canalización, seleccione **Trigger** (Desencadenar) en la barra de herramientas de la misma y, después, seleccione **Nuevo/Editar**. 

2. En la página **Add Triggers** (Agregar desencadenadores), seleccione **Choose trigger** (Elegir desencadenador) y, después, seleccione **New** (Nuevo). 

3. En la página **New Trigger** (Nuevo desencadenador), para el campo **End** (Final), seleccione **On Date** (En fecha), especifique la hora de finalización unos minutos después de la hora actual y seleccione **Apply** (Aplicar). 

   Hay un costo asociado a cada ejecución de la canalización. Por lo tanto, especifique la hora de finalización tan solo unos minutos después de la hora de inicio. Asegúrese de que sea el mismo día. No obstante, asegúrese de que hay tiempo suficiente para que la canalización se ejecute entre la hora de publicación y la hora de finalización. El desencadenador entra en vigor después de publicar la solución en Data Factory, no cuando se guarda el desencadenador en la interfaz de usuario. 

   ![Configuración del desencadenador](./media/quickstart-create-data-factory-portal/trigger-settings.png)
4. En la página **New Trigger** (Nuevo desencadenador), active la casilla **Activated** (Activado) y después seleccione **Next** (Siguiente). 

   ![Casilla de verificación Activated (Activado) y botón Next (Siguiente)](./media/quickstart-create-data-factory-portal/trigger-settings-next.png)
5. Revise el mensaje de advertencia y seleccione **Finish** (Finalizar).

   ![Botón de advertencia y de finalización](./media/quickstart-create-data-factory-portal/new-trigger-finish.png)
6. Seleccione **Publish All** (Publicar todo) para publicar los cambios en Data Factory. 

8. Cambie a la pestaña **Monitor** (Supervisar) de la izquierda. Seleccione **Refresh** (Actualizar) para actualizar la lista. Verá que la canalización se ejecuta una vez cada minuto desde la hora de publicación hasta la hora de finalización. 

   Observe los valores de la columna **Triggered By** (Desencadenado por). La ejecución manual del desencadenador se realizó en el paso (**Trigger Now**) [Desencadenar ahora] que llevó a cabo antes. 

   ![Lista de las ejecuciones desencadenadas](./media/quickstart-create-data-factory-portal/monitor-triggered-runs.png)
9. Seleccione la flecha hacia abajo junto a **Pipeline Runs** (Ejecuciones de la canalización) para cambiar a la vista **Trigger Runs** (Ejecuciones del desencadenador). 

   ![Cambio a la vista Ejecuciones de desencadenador](./media/quickstart-create-data-factory-portal/monitor-trigger-runs.png)    
10. Confirme que se crea un archivo de salida para cada ejecución de la canalización hasta la fecha y hora de finalización especificadas en la carpeta **output** (salida). 

## <a name="next-steps"></a>Pasos siguientes
La canalización de este ejemplo copia los datos de una ubicación a otra en una instancia de Azure Blob Storage. Para más información sobre el uso de Data Factory en otros escenarios, consulte los siguientes [tutoriales](tutorial-copy-data-portal.md). 