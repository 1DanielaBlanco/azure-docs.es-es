---
title: Tutorial de preparación de los datos para la clasificación de Iris en Azure Machine Learning Service (versión preliminar) | Microsoft Docs
description: A lo largo de este tutorial se muestra cómo aprovechar al máximo Azure Machine Learning Service (versión preliminar). Esta es la primera parte y describe la preparación de los datos.
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs, gcampanella
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: mvc
ms.topic: tutorial
ms.date: 03/07/2018
ROBOTS: NOINDEX
ms.openlocfilehash: dd10581888da64114debec40cba8564023033864
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/12/2018
ms.locfileid: "53278515"
---
# <a name="tutorial-1-classify-iris---preparing-the-data"></a>Tutorial 1: Clasificación de Iris: preparación de los datos

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)]

El servicio Azure Machine Learning (versión preliminar) es una solución integrada de análisis avanzado y ciencia de datos de un extremo a otro para que los científicos de datos profesionales preparen datos, desarrollen experimentos e implementar modelos a escala de la nube.

Este tutorial es la **primera de una serie de tres partes**. En este tutorial, recorrerá los aspectos básicos de Azure Machine Learning Service (versión preliminar) y aprenderá a:

> [!div class="checklist"]
> * Crear un proyecto en Azure Machine Learning Workbench.
> * Generar un paquete de preparación de datos.
> * Generación de código de Python/PySpark para invocar el paquete de preparación de datos

Este tutorial usa el [conjunto de datos Iris](https://en.wikipedia.org/wiki/Iris_flower_data_set) atemporal. 

[!INCLUDE [aml-preview-note](../../../includes/aml-preview-note.md)]

## <a name="prerequisites"></a>Requisitos previos

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

Para realizar este tutorial, necesitará lo siguiente:
- Una cuenta de Experimentación de Azure Machine Learning.
- Azure Machine Learning Workbench instalado.

Si aún no cumple estos requisitos previos, siga los pasos de la [guía de inicio rápido de instalación e introducción](quickstart-installation.md) para configurar las cuentas e instalar la aplicación Azure Machine Learning Workbench. 

## <a name="create-a-new-project-in-workbench"></a>Creación de un proyecto nuevo en Workbench

Si ha seguido los pasos descritos en la [guía de inicio rápido de instalación e introducción](quickstart-installation.md), ya tendrá este proyecto y puede ir a la siguiente sección.

1. Abra la aplicación Azure Machine Learning Workbench e inicie sesión si es necesario. 
   
   + En Windows, puede iniciarlo con el acceso directo del escritorio **Machine Learning Workbench**. 
   + En Mac OS, seleccione **Azure ML Workbench** en el Launchpad.

1. En el panel **PROJECTS** (PROYECTOS), seleccione el signo más (+) y elija **New Project** (Nuevo proyecto).  

   ![Nueva área de trabajo](./media/tutorial-classifying-iris/new_ws.png)

1. Rellene los campos del formulario y seleccione el botón **Create** (Crear) para crear un nuevo proyecto en Workbench.

   Campo|Valor recomendado para el tutorial|DESCRIPCIÓN
   ---|---|---
   Nombre de proyecto | myIris |Elija un nombre único que identifique la cuenta. Puede usar su propio nombre o el nombre de departamento o proyecto que mejor identifique el experimento. El nombre debe tener entre 2 y 32 caracteres. Debe incluir solo caracteres alfanuméricos y el carácter de guión (-). 
   Directorio del proyecto | c:\Temp\ | Especifique el directorio en el que se creará el proyecto.
   Descripción del proyecto | _déjelo en blanco_ | Campo opcional útil para describir los proyectos.
   Dirección URL de repositorio de GIT de VisualStudio.com |_déjelo en blanco_ | Campo opcional. Puede asociar un proyecto con un repositorio de Git en Azure DevOps para el control de código fuente y la colaboración. [Aprenda a configurarlo](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/using-git-ml-project#step-3-set-up-a-machine-learning-project-and-git-repo). 
   Área de trabajo seleccionada | IrisGarden (si existe) | Elija un área de trabajo que ha creado para su cuenta de Experimentación en Azure Portal. <br/>Si ha seguido la guía de inicio rápido, tendrá un área de trabajo con el nombre IrisGarden. Si no es así, seleccione el que creó cuando creó su cuenta de Experimentación o cualquier otro que desee utilizar.
   Plantilla de proyecto | Clasificación de iris | Las plantillas contienen scripts y datos que puede usar para explorar el producto. Esta plantilla contiene los scripts y los datos que necesita para esta guía de inicio rápido y otros tutoriales en este sitio de documentación. 

   ![Nuevo proyecto](media/tutorial-classifying-iris/new_project.png)
 
 Se crea un nuevo proyecto y se abre el panel de proyecto con ese proyecto. En este momento, ya puede explorar la página principal, los orígenes de datos, los cuadernos y los archivos de código fuente del proyecto. 

   ![Abrir proyecto](media/tutorial-classifying-iris/project-open.png)
 

## <a name="create-a-data-preparation-package"></a>Generar un paquete de preparación de datos.

A continuación, puede explorar los datos y empezar a prepararlos en Azure Machine Learning Workbench. Todas las transformaciones que se realizan en Workbench se almacenan en formato JSON en un paquete de preparación de datos local (archivo *.dprep). Este paquete de preparación de datos es el contenedor principal del trabajo de preparación de datos en Workbench.

Este paquete de preparación de datos se puede entregar posteriormente a un runtime, por ejemplo, local-C#/CoreCLR, Scala/Spark o Scala/HDI. 

1. Seleccione el icono de carpeta para abrir la vista Files (Archivos) y seleccione **iris.csv** para abrirlo.

   Este archivo contiene una tabla con 5 columnas y 50 filas. Cuatro columnas son columnas de características numéricas. La quinta columna es una columna de destino de cadena. Ninguna de las columnas tienen nombres de encabezado.

   ![iris.csv](media/tutorial-classifying-iris/show_iris_csv.png)

   >[!NOTE]
   > No incluya archivos de datos en la carpeta del proyecto, especialmente cuando el tamaño del archivo sea grande. Dado que el archivo de datos **iris.csv** es muy pequeño, se incluyó en esta plantilla para fines de demostración. Para obtener más información, consulte [How to read and write large data files](how-to-read-write-files.md) (Procedimiento para leer y escribir archivos de datos de gran tamaño).

2. En la **Data View** (Vista de datos), seleccione el signo más (**+**) para agregar un nuevo origen de datos. Se abre la página **Add Data Source** (Agregar origen de datos). 

   ![Vista de datos en Azure Machine Learning Workbench](media/tutorial-classifying-iris/data_view.png)

3. Seleccione **Text Files (\*.csv, \*.json, \*.txt.,...)** (Archivos de texto ) y haga clic en **Next** (Siguiente).
   ![Origen de datos en Azure Machine Learning Workbench](media/tutorial-classifying-iris/data-source.png)

4. Vaya al archivo **iris.csv** y haga clic en **Finish** (Finalizar). Se usarán los valores predeterminados para los parámetros como el separador y los tipos de datos.

   >[!IMPORTANT]
   >Asegúrese de seleccionar el archivo **iris.csv** desde el directorio del proyecto actual de este ejercicio. De lo contrario, los pasos posteriores podrían dar error.
 
   ![Selección de Iris](media/tutorial-classifying-iris/select_iris_csv.png)
   
5. Se crea un nuevo archivo denominado **iris-1.dsource**. Se asigna un nombre único al archivo que contiene "-1", porque el proyecto de ejemplo ya contiene un archivo **iris.dsource** sin numerar.  

   Se abre el archivo y se muestran los datos. De forma automática, se agrega una serie de encabezados de columna, de **Column1** a **Column5**, a este conjunto de datos. Desplácese hasta el final y observe que la última fila del conjunto de datos está vacía. La fila está vacía porque no hay un salto de línea adicional en el archivo CSV.

   ![Vista de datos de Iris](media/tutorial-classifying-iris/iris_data_view.png)

1. Seleccione el botón **Metrics** (Métricas). Se generan y se muestran histogramas.

   Puede volver a la vista de datos, para lo que debe seleccionar el botón **Data** (Datos).
   
   ![Vista de datos de Iris](media/tutorial-classifying-iris/iris_data_view_metrics.png)

1. Observe los histogramas. Se ha calculado un conjunto completo de las estadísticas para cada columna. 

   ![Vista de datos de Iris](media/tutorial-classifying-iris/iris_metrics_view.png)

8. Empiece a crear un paquete de preparación de datos, para lo que debe seleccionar el botón **Prepare** (Preparar). Se abre el cuadro de diálogo **Prepare** (Preparar). 

   El proyecto de ejemplo contiene un archivo de preparación de datos, **iris.dprep** que está seleccionado de forma predeterminada. 

   ![Vista de datos de Iris](media/tutorial-classifying-iris/prepare.png)

1. Cree un archivo de preparación de datos nuevo, para lo que debe seleccionar **+ New Data Preparation Package** (Nuevo paquete de preparación de datos) en el menú desplegable.

   ![Vista de datos de Iris](media/tutorial-classifying-iris/prepare_new.png)

1. Escriba un valor nuevo como nombre del paquete (use **iris-1**) y, luego, pulse **Aceptar**.

   Se crea un nuevo paquete de preparación de datos denominado **iris-1.dprep** y se abre en el editor de preparación de datos.

   ![Vista de datos de Iris](media/tutorial-classifying-iris/prepare_iris-1.png)

   Ahora vamos a realizar una preparación de datos básica. 

1. Seleccione cada encabezado de columna para poder modificar su texto. Después, cambie el nombre de cada columna como se indica a continuación: 

   Por orden, escriba **Sepal Length** (Longitud del sépalo), **Sepal Width** (Anchura del sépalo), **Petal Length** (Longitud del pétalo), **Petal Width** (Anchura del pétalo) y **Species** (Especie) para las cinco columnas respectivamente.

   ![Cambio del nombre de las columnas](media/tutorial-classifying-iris/rename_column.png)

1. Recuente los distintos valores:
   1. Seleccione la columna **Species** (Especie).
   1. Haga clic con el botón derecho para seleccionarla. 
   1. Seleccione **Value Counts** (Recuentos de valores) en el menú desplegable. 

   Se abre el panel **Inspectores** debajo de los datos. Aparece un histograma con cuatro barras. La columna de destino tiene cuatro valores distintos: **Iris-virginica**, **Iris-versicolor**, **Iris-setosa** y un valor **(null)**.

   ![Selección de Value Counts (Recuentos de valores)](media/tutorial-classifying-iris/value_count.png)

   ![Histograma de recuento de valores](media/tutorial-classifying-iris/filter_out.png)

1. Para filtrar los valores null, seleccione la barra "(null)" y después seleccione el signo menos (**-**). 

   Luego, la fila (null) pasa a ser de color gris para indicar que se filtró. 

   ![Filtrar valores null](media/tutorial-classifying-iris/filter_out2.png)

1. Fíjese en los pasos de la preparación de datos individuales que se detallan en el panel **STEPS** (PASOS). Cuando cambió el nombre de las columnas y filtró las filas con valor null, todas las acciones se registraron como pasos de la preparación de datos. Puede editar los pasos individuales para ajustar su configuración, reordenar los pasos y eliminarlos.

   ![Pasos](media/tutorial-classifying-iris/steps.png)

1. Cierre el editor de preparación de datos. Seleccione el icono de la **x** en la pestaña **iris-1** con el icono del gráfico para cerrar la pestaña. El trabajo se guarda automáticamente en el archivo **iris-1.dprep** que aparece bajo el encabezado **Data Preparations** (Preparaciones de datos).

   ![cierre](media/tutorial-classifying-iris/close.png)

## <a name="generate-pythonpyspark-code-to-invoke-a-data-preparation-package"></a>Generación de código de Python/PySpark para invocar el paquete de preparación de datos

 La salida de un paquete de preparación de datos se puede explorar directamente en Python o en Jupyter Notebook. Los paquetes se puede ejecutar en varios runtimes, entre los que se incluyen Python, Spark (incluido en Docker) y HDInsight locales. 

1. Busque el archivo **1.dprep iris** en la pestaña Data Preparations (Preparaciones de datos).

1. Haga clic con el botón derecho en el archivo **iris-1.dprep** y seleccione **Generate Data Access Code File** (Generar archivo de código de acceso a datos) en el menú contextual. 

   ![Generación de código](media/tutorial-classifying-iris/generate_code.png)

   Se abre un nuevo archivo llamado **iris-1.py** con las siguientes líneas de código para invocar la lógica que creó como paquete de preparación de datos:

   ```python
   # Use the Azure Machine Learning data preparation package
   from azureml.dataprep import package

   # Use the Azure Machine Learning data collector to log various metrics
   from azureml.logging import get_azureml_logger
   logger = get_azureml_logger()

   # This call will load the referenced package and return a DataFrame.
   # If run in a PySpark environment, this call returns a
   # Spark DataFrame. If not, it will return a Pandas DataFrame.
   df = package.run('iris-1.dprep', dataflow_idx=0)

   # Remove this line and add code that uses the DataFrame
   df.head(10)
   ```

   En función del contexto en el que se ejecuta este código, `df` representa un tipo de DataFrame diferente:
   + Cuando se ejecuta en un runtime de Python, se usa un [DataFrame de pandas](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html).
   + Cuando se ejecuta en un runtime de Spark, se usa un [DataFrame de Spark](https://spark.apache.org/docs/latest/sql-programming-guide.html). 
   
   Para más información acerca de cómo preparar datos en Azure Machine Learning Workbench, consulte la guía [Introducción a Preparación de datos](data-prep-getting-started.md).

## <a name="clean-up-resources"></a>Limpieza de recursos

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha usado Azure Machine Learning Workbench para:
> [!div class="checklist"]
> * Creación de un nuevo proyecto
> * Generar un paquete de preparación de datos.
> * Generación de código de Python/PySpark para invocar el paquete de preparación de datos

Está listo para pasar a la siguiente parte de la serie de tutoriales, en la que aprenderá a crear un modelo de Azure Machine Learning:
> [!div class="nextstepaction"]
> [Tutorial 2: creación de modelos](tutorial-classifying-iris-part-2.md)
