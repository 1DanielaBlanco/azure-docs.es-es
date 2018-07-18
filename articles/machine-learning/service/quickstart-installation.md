---
title: Guía de inicio rápido de instalación de servicios de Azure Machine Learning | Microsoft Docs
description: En esta guía de inicio rápido, aprenderá cómo crear recursos de Azure Machine Learning y cómo instalar Azure Machine Learning Workbench y empezar a trabajar con él.
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: mvc
ms.topic: quickstart
ms.date: 3/7/2018
ms.openlocfilehash: 30795f542bca52159f2ff0fe052a94de3743f0e8
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2018
ms.locfileid: "38767160"
---
# <a name="quickstart-install-and-get-started-with-azure-machine-learning-services"></a>Inicio rápido: Instalar los servicios de Machine Learning y empezar a trabajar con ellos
Los servicios de Azure Machine Learning (versión preliminar) son una solución integrada y completa de análisis avanzado y ciencia de datos. Ayuda a los científicos de datos profesionales a preparar datos, desarrollar experimentos e implementar modelos a escala de nube.

Esta guía de inicio rápido le muestra cómo:

* Crear cuentas de servicio para los servicios de Azure Machine Learning
* Instalar Azure Machine Learning Workbench e iniciar sesión
* Crear un proyecto en Workbench
* Ejecutar un script en el proyecto  
* Acceder a la interfaz de la línea de comandos (CLI)


Como parte de la cartera de Microsoft Azure, los servicios de Azure Machine Learning requieren una suscripción a Azure. Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

Además, debe tener los permisos adecuados para crear recursos como grupos de recursos, máquinas virtuales, etc. 

<a name="prerequisites"></a>La aplicación Azure Machine Learning Workbench se puede instalar en los siguientes sistemas operativos:
- Windows 10 o Windows Server 2016
- macOS Sierra o High Sierra

## <a name="create-azure-machine-learning-services-accounts"></a>Creación de cuentas de Azure Machine Learning
Use Azure Portal para aprovisionar las cuentas de Azure Machine Learning: 
1. Inicie sesión en [Azure Portal](https://portal.azure.com/) con las credenciales de la suscripción de Azure que se va a utilizar. Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) ahora. 

   ![Azure Portal](./media/quickstart-installation/portal-dashboard.png)

1. Seleccione el botón **Crear un recurso** (+) de la esquina superior izquierda del portal.

   ![Creación de un recurso en Azure Portal](./media/quickstart-installation/portal-create-a-resource.png)

1. Escriba **Machine Learning** en la barra de búsqueda. Seleccione el resultado de la búsqueda llamado **Experimentación de Machine Learning**. 

   ![Búsqueda de Azure Machine Learning](./media/quickstart-installation/portal-more-services.png)

1. En el panel de **Experimentación de Machine Learning**, desplácese hasta la parte inferior y seleccione **Crear** para empezar a definir su cuenta experimentación.  

   ![Azure Machine Learning: creación de una cuenta de experimentación](./media/quickstart-installation/portal-create-account.png)

1. En el panel de **Experimentación de Machine Learning**, configure su cuenta de Experimentación de Machine Learning. 

   Configuración|Valor recomendado para el tutorial|DESCRIPCIÓN
   ---|---|---
   Nombre de cuenta de Experimentación | _Nombre único_ |Elija un nombre único que identifique la cuenta. Puede usar su propio nombre o el nombre de departamento o proyecto que mejor identifique el experimento. El nombre debe tener entre 2 y 32 caracteres. Debe incluir solo caracteres alfanuméricos y el carácter de guión (-). 
   La suscripción | _Su suscripción_ |Elija la suscripción de Azure que desee usar para el experimento. Si tiene varias suscripciones, elija en la que se factura el recurso.
   Grupos de recursos | _El grupo de recursos_ | Use un grupo de recursos existente en su suscripción o escriba un nombre para crear un nuevo grupo de recursos para esta cuenta de experimentación. 
   Ubicación | _Región más cercana a los usuarios_ | Elija la ubicación más cercana a los usuarios y los recursos de datos.
   Número de puestos | 2 | Escriba el número de puestos. Obtenga información acerca de cómo los [puestos afectan a los precios](https://azure.microsoft.com/pricing/details/machine-learning/).<br/><br/>Para esta guía de inicio rápido, solo necesita dos puestos. Los puestos se pueden agregar o quitar según sea necesario en Azure Portal.
   Cuenta de almacenamiento | _Nombre único_ | Seleccione **Crear nuevo** y especifique un nombre para crear una [cuenta de Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=portal). El nombre debe tener entre 3 y 24 caracteres e incluir únicamente caracteres alfanuméricos. Además, seleccione **Usar existente** y, después, elija una de las cuentas de almacenamiento de la lista desplegable. La cuenta de almacenamiento es necesaria y se utiliza para almacenar los artefactos de proyecto y ejecutar datos de historial. 
   Área de trabajo para Experimentación | IrisGarden<br/>(el nombre usado en los tutoriales) | Proporcione el nombre de un área de trabajo para esta cuenta. El nombre debe tener entre 2 y 32 caracteres. Debe incluir solo caracteres alfanuméricos y el carácter de guión (-). Esta área de trabajo contiene las herramientas que necesita para crear, administrar y publicar experimentos.
   Asignar propietario para el área de trabajo | _Su cuenta_ | Seleccione su propia cuenta como propietario del área de trabajo.
   Creación de cuenta de Administración de modelos | **activar** |Ahora, cree una cuenta de Administración de modelos para que este recurso esté disponible cuando quiera implementar y administrar sus modelos como servicios web en tiempo real. <br/><br/>Aunque es opcional, se recomienda crear la cuenta de Administración de modelos al mismo tiempo que la cuenta de Experimentación.
   Nombre de cuenta | _Nombre único_ | Elija un nombre único que identifique la cuenta de Administración de modelos. Puede usar su propio nombre o el nombre de departamento o proyecto que mejor identifique el experimento. El nombre debe tener entre 2 y 32 caracteres. Debe incluir solo caracteres alfanuméricos y el carácter de guión (-). 
   Plan de tarifa de Administración de modelos | **DEVTEST** | Seleccione **No se ha seleccionado ningún plan de tarifa** para especificar el plan de tarifa para la nueva cuenta de Administración de modelos. Para ahorrar costos, seleccione el plan de tarifa **DEVTEST**, en caso de que esté disponible para su suscripción. De lo contrario, seleccione el plan de tarifa S1. Haga clic en **Seleccionar** para guardar este plan de tarifa. 
   Anclar al panel | _activar_ | Seleccione la opción **Anclar al panel** para permitir realizar un seguimiento fácil de la cuenta de Experimentación de Machine Learning en la página del panel frontal de Azure Portal.

   ![Configuración de la cuenta de Experimentación de Machine Learning](./media/quickstart-installation/portal-create-experimentation.png)

5. Seleccione **Crear** para comenzar el proceso de creación de la cuenta de Experimentación junto con la cuenta de Administración de modelos.

   ![Configuración de la cuenta de Experimentación de Machine Learning](./media/quickstart-installation/portal-create-experimentation-button.png)

   La cuenta puede tardar unos momentos en crearse. Para comprobar el estado del proceso de implementación, haga clic en el icono de notificaciones (campana) en la barra de herramientas de Azure Portal.
   
   ![Notificaciones de Azure Portal](./media/quickstart-installation/portal-notification.png)


## <a name="install-and-log-in-to-workbench"></a>Instale e inicie sesión en Workbench

Azure Machine Learning Workbench está disponible para Windows o Mac OS. Consulte la lista de [plataformas admitidas](#prerequisites).

>[!WARNING]
>La instalación puede tardar hasta 30 minutos en completarse. 

1. Descargue e inicie el instalador de Workbench más reciente. 
   >[!IMPORTANT]
   >Descargue el instalador por completo en el disco y ejecútelo desde allí. No lo ejecute directamente desde el widget de descarga del explorador.

   **En Windows:** 

   &nbsp;&nbsp;&nbsp;&nbsp;A. Descargue [AmlWorkbenchSetup.msi](https://aka.ms/azureml-wb-msi).  <br/>
   &nbsp;&nbsp;&nbsp;&nbsp;B. Haga doble clic en el instalador descargado en el Explorador de archivos.

   **En macOS:** 

   &nbsp;&nbsp;&nbsp;&nbsp;A. Descargue [AmlWorkbench.dmg](https://aka.ms/azureml-wb-dmg). <br/>
   &nbsp;&nbsp;&nbsp;&nbsp;B. Haga doble clic en el instalador descargado en Finder.<br/><br/>

1. Siga las instrucciones en pantalla en el instalador hasta su finalización. 

   **La instalación puede tardar hasta 30 minutos en completarse.**  
   
   | |Ruta de instalación de Azure Machine Learning Workbench|
   |--------|------------------------------------------------|
   |Windows|C:\Users\\<user\>\AppData\Local\AmlWorkbench|
   |macOS|/Applications/Azure ML Workbench.app|

   El instalador descargará e instalará todas las dependencias necesarias, como Python, Miniconda y otras bibliotecas relacionadas. Esta instalación incluye también la herramienta de línea de comandos multiplataforma de Azure, o la CLI de Azure.

1. Para iniciar Workbench, seleccione el botón **Launch Workbench** (Iniciar Workbench) en la última pantalla del instalador. 

   Si ha cerrado el instalador:
   + En Windows, puede iniciarlo con el acceso directo del escritorio **Machine Learning Workbench**. 
   + En Mac OS, seleccione **Azure ML Workbench** en el Launchpad.

1. En la primera pantalla, seleccione **Iniciar sesión con Microsoft** para autenticarse en Azure Machine Learning Workbench. Use las mismas credenciales que usó en Azure Portal para crear las cuentas de Administración de modelos y Experimentación. 

   Cuando haya iniciado sesión, Workbench usa la primera cuenta de Experimentación que encuentra en las suscripciones de Azure y muestra todas las áreas de trabajo y los proyectos asociados a esa cuenta. 

   >[!TIP]
   > Para cambiar a otra cuenta de Experimentación, use el icono situado en la esquina inferior izquierda de la ventana de la aplicación Workbench.

## <a name="create-a-project-in-workbench"></a>Crear un proyecto en Workbench

En Azure Machine Learning, un proyecto es el contenedor lógico de todo el trabajo realizado para resolver un problema. Se asigna a una sola carpeta en el disco local y puede agregar archivos o subcarpetas a él. 

Aquí, vamos a crear un nuevo proyecto de Workbench con una plantilla que incluye el [conjunto de datos Iris Flower](https://en.wikipedia.org/wiki/Iris_flower_data_set). Los tutoriales que siguen esta guía de inicio rápido dependen de estos datos para crear un modelo que predice el tipo de flor en función de algunas de sus características físicas.  

1. Con Azure Machine Learning Workbench abierto, seleccione el signo más (+) en el panel **PROJECTS** (PROYECTOS) y elija **New Project** (Nuevo proyecto).  

   ![Nueva área de trabajo](./media/quickstart-installation/new_ws.png)

1. Rellene los campos del formulario y seleccione el botón **Create** (Crear) para crear un nuevo proyecto en Workbench.

   Campo|Valor recomendado para el tutorial|DESCRIPCIÓN
   ---|---|---
   Nombre de proyecto | myIris |Elija un nombre único que identifique la cuenta. Puede usar su propio nombre o el nombre de departamento o proyecto que mejor identifique el experimento. El nombre debe tener entre 2 y 32 caracteres. Debe incluir solo caracteres alfanuméricos y el carácter de guión (-). 
   Directorio del proyecto | c:\Temp\ | Especifique el directorio en el que se creará el proyecto.
   Descripción del proyecto | _déjelo en blanco_ | Campo opcional útil para describir los proyectos.
   Dirección URL de repositorio de GIT de VisualStudio.com |_déjelo en blanco_ | Campo opcional. Si lo desea, el proyecto puede asociarse con un repositorio de Git para el control de código fuente y la colaboración en Visual Studio Team Services. [Vea cómo configurarlo](../desktop-workbench/using-git-ml-project.md#step-3-set-up-a-machine-learning-project-and-git-repo). 
   Área de trabajo seleccionada | IrisGarden (si existe) | Elija un área de trabajo que ha creado para su cuenta de Experimentación en Azure Portal. <br/>Si ha seguido la guía de inicio rápido, tendrá un área de trabajo con el nombre IrisGarden. Si no es así, seleccione el que creó cuando creó su cuenta de Experimentación o cualquier otro que desee utilizar.
   Plantilla de proyecto | Clasificación de iris | Las plantillas contienen scripts y datos que puede usar para explorar el producto. Esta plantilla contiene los scripts y los datos que necesita para esta guía de inicio rápido y otros tutoriales en este sitio de documentación. 

   ![Nuevo proyecto](./media/quickstart-installation/new_project.png)
 
 Se crea un nuevo proyecto y se abre el panel de proyecto con ese proyecto. En este momento, ya puede explorar la página principal, los orígenes de datos, los cuadernos y los archivos de código fuente del proyecto. 

>[!TIP]
>Puede configurar Workbench para trabajar con un IDE de Python para una experiencia de desarrollo de ciencia de datos sin problemas. Después, podrá interactuar con el proyecto en el IDE. [Más información](../desktop-workbench/how-to-configure-your-ide.md). 

## <a name="run-a-python-script"></a>Ejecutar un script de Python

Ahora, puede ejecutar el script **iris_sklearn.py** en el equipo local. Este script se incluye de forma predeterminada con la plantilla de proyecto **Classifying Iris**. El script crea un modelo de [regresión logística](https://en.wikipedia.org/wiki/Logistic_regression) con la popular biblioteca de Python [scikit-learn](http://scikit-learn.org/stable/index.html).

1. En la barra de comandos que hay en la parte superior de la página **Project Dashboard** (Panel del proyecto), seleccione **local** como destino de ejecución y seleccione **iris_sklearn.py** como script para ejecutar. Estos valores ya están seleccionados de forma predeterminada. 

   El ejemplo incluye otros archivos que puede consultar más adelante pero, para esta guía de inicio rápido, solo nos interesa **iris_sklearn.py**. 

   ![Barra de comandos](./media/quickstart-installation/run_control.png)

1. En el cuadro de texto **Arguments** (Argumentos), escriba **0,01**. Este número corresponde a la velocidad de regularización y se utiliza en el script para configurar el modelo de regresión logística. 

1. Seleccione **Run** (Ejecutar) para iniciar la ejecución del script en el equipo. El trabajo **iris_sklearn.py** aparece inmediatamente en el panel **Jobs** (Trabajos) de la derecha para que pueda supervisar la ejecución del script.

   Felicidades. Ha ejecutado correctamente un script de Python en Azure Machine Learning Workbench.

1. Repita los pasos 2 y 3 varias veces utilizando diferentes valores de argumento entre **0,001** y **10** (por ejemplo, con potencias de 10). Cada ejecución aparece en el panel **Jobs** (Trabajos).

1. Para inspeccionar el historial de ejecución, seleccione la vista **Runs** (Ejecuciones) y, a continuación, **iris_sklearn.py** en la lista de ejecuciones. 

   ![Panel del historial de ejecución](./media/quickstart-installation/run_view.png)

   En esta vista se muestra todas las ejecuciones que se realizaron en **iris_sklearn.py**. El panel del historial de ejecución también muestra las métricas principales, un conjunto de grafos predeterminados y una lista de métricas para cada ejecución. 

1. Para personalizar esta vista, ordene, filtre y ajuste las configuraciones con los iconos de engranaje y de filtro.

   ![Métrica y gráficos](./media/quickstart-installation/run_dashboard.png)

3. Seleccione una ejecución finalizada en el panel Jobs (Trabajos) para obtener una vista detallada de la misma. Los detalles incluyen otras métricas, los archivos que produce y otros registros potencialmente útiles.

## <a name="start-the-cli"></a>Iniciar la CLI

También se instala la interfaz de línea de comandos (CLI) de Azure Machine Learning. La interfaz CLI le permite acceder a sus servicios de Azure Machine Learning e interactuar con ellos mediante los comandos `az` para realizar todas las tareas necesarias para un flujo de trabajo de ciencia de datos de principio a fin. [Más información.](../desktop-workbench/tutorial-iris-azure-cli.md)

Para iniciar la CLI de Azure Machine Learning desde la barra de herramientas de Workbench, use **Archivo --> Abrir símbolo del sistema**.

También puede obtener ayuda acerca de los comandos de la CLI de Azure Machine Learning con el argumento --help.

```az ml --help```

## <a name="clean-up-resources"></a>Limpieza de recursos

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Pasos siguientes
Ya ha creado las cuentas de Azure Machine Learning necesarias y ha instalado la aplicación Azure Machine Learning Workbench. También ha creado un proyecto, ha ejecutado un script y ha explorado el historial de ejecuciones del script.

Para obtener una experiencia más detallada de este flujo de trabajo, incluida la forma de implementar el modelo Iris como servicio web, siga el completo tutorial de *clasificación de Iris*. El tutorial contiene los pasos detallados para la [preparación de datos](../desktop-workbench/tutorial-classifying-iris-part-1.md), [experimentación](../desktop-workbench/tutorial-classifying-iris-part-2.md) y [administración de modelos](../desktop-workbench/tutorial-classifying-iris-part-3.md). 

> [!div class="nextstepaction"]
> [Tutorial: Clasificación de iris (parte 1)](../desktop-workbench/tutorial-classifying-iris-part-1.md)

>[!NOTE]
> Aunque ha creado una cuenta de administración de modelos, el entorno aún no está configurado para implementar servicios web. Vea cómo configurar el [entorno de implementación](../desktop-workbench/deployment-setup-configuration.md).
