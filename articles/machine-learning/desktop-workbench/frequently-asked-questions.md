---
title: Preguntas más frecuentes de la versión preliminar de Azure Machine Learning 2017 | Microsoft Docs
description: Este artículo contiene las preguntas más frecuentes y las respuestas para características de la versión preliminar de Azure Machine Learning
services: machine-learning
author: serinakaye
ms.author: serinak
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 08/30/2017
ROBOTS: NOINDEX
ms.openlocfilehash: 45cf987d9af7b7dd0e8f05056b49ba56835603e7
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/26/2018
ms.locfileid: "52313952"
---
# <a name="azure-machine-learning-frequently-asked-questions"></a>Preguntas más frecuentes de Azure Machine Learning

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 

Azure Machine Learning es un servicio de Azure totalmente administrado que le permite crear, probar, administrar e implementar modelos de aprendizaje automático y de inteligencia artificial. Nuestros servicios y la aplicación descargable ofrecen un enfoque de Code First que aprovecha la nube, el entorno local y el borde para proporcionar el entrenamiento, la implementación, la administración y la supervisión de los modelos con eficacia, velocidad y flexibilidad. Como alternativa, Azure Machine Learning Studio ofrece un entorno visual de creación basado en explorador, de arrastrar y colocar, donde no se requiere ningún código. 

## <a name="general-product-questions"></a>Preguntas generales sobre el producto

**¿Qué otros servicios de Azure son necesarios?**

Azure Machine Learning usa Azure Blob Storage y Azure Container Registry. Además, debe aprovisionar los recursos de proceso, como un clúster de HDInsight o una máquina virtual de ciencia de datos. El proceso y el hospedaje también son necesarios para implementar servicios web, como [Azure Container Service](https://docs.microsoft.com/azure/aks).

**¿Cómo se relaciona Azure Machine Learning con Microsoft Machine Learning Services en SQL Server 2017?**   

Machine Learning Services en SQL Server 2017 es una plataforma extensible y escalable para integrar tareas de aprendizaje automático en los flujos de trabajo de bases de datos. Es ideal para escenarios donde se necesita una solución local, por ejemplo, donde el movimiento de datos es caro o insostenible. En cambio, las cargas de trabajo en la nube o híbridas son una excelente elección para nuestros nuevos servicios de Azure. 

**¿Cómo se relaciona Azure Machine Learning con Microsoft Machine Learning para Spark?**

MMLSpark proporciona herramientas de aprendizaje profundo y de ciencia de datos para Apache Spark, con especial hincapié en la productividad, la facilidad de experimentación y algoritmos de última generación. MMLSpark ofrece integración de canalizaciones de Spark Machine Learning con el kit de herramientas de Microsoft Cognitive y OpenCV. Puede crear modelos de predicción y análisis eficaces, potentes y altamente escalables para los datos de imagen y texto. MMLSpark está disponible en una licencia de código abierto y se incluye en AML Workbench como un conjunto de algoritmos y modelos consumibles. Para obtener más información sobre MMLSpark, visite nuestra documentación del producto. 

**¿Qué versiones de Spark son compatibles con las nuevas herramientas y servicios?**

Workbench actualmente incluye y admite MMLSpark versión 0.8, que es compatible con Apache Spark 2.1. También tiene una opción para usar la imagen de Docker basada en GPU de MMLSpark 0,8 en máquinas virtuales con Linux.

## <a name="experimentation-service"></a>Servicio Experimentación

**¿Qué es el servicio Experimentación de Azure Machine Learning?**

El servicio Experimentación es un servicio administrado de Azure que lleva los experimentos de aprendizaje automático al siguiente nivel. Los experimentos pueden crearse de forma local o en la nube. Cree prototipos rápidamente en un equipo de escritorio y luego escale a máquinas virtuales o clústeres de Spark. Azure VM con la tecnología de GPU más reciente le permite participar en aprendizaje profundo de forma rápida y eficaz. También hemos incluido una fuerte integración con GIT, por lo que puede conectarse fácilmente en flujos de trabajo existentes para el seguimiento del código, la configuración y la colaboración. 

**¿Cómo se cobra el servicio Experimentación?**

Los primeros dos usuarios asociados con el servicio Experimentación de Azure Machine Learning son gratuitas. Los usuarios adicionales se cobrarán a la tarifa de la versión preliminar pública de $50/mes. Para obtener más información sobre precios y facturación, visite nuestra página de precios.

**¿Los cargos van en función del número de experimentos que se ejecuten?**

No, el servicio Experimentación permite tantos experimentos como necesite. Los cargos se realizan únicamente en función del número de usuarios. Los recursos de proceso de Experimentación se cobran aparte. Le recomendamos realizar varios experimentos para poder encontrar el modelo que mejor se ajuste a su solución.   

**¿Qué tipos concretos de recursos de proceso y almacenamiento pueden usarse?**

El servicio Experimentación puede ejecutar los experimentos en equipos locales (directos o basados en Docker), [Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/) y [HDInsight](https://azure.microsoft.com/services/hdinsight/). El servicio también tiene acceso a una cuenta de [Azure Storage](https://azure.microsoft.com/services/storage/) para almacenar los resultados de ejecución, y puede aprovechar una cuenta de [Visual Studio Team Service](https://azure.microsoft.com/services/visual-studio-team-services/) para el almacenamiento de GIT y control de versiones. Tenga en cuenta que los recursos de proceso y almacenamiento se facturan por separado según los precios de cada uno.  


## <a name="model-management"></a>Administración de modelos

**¿Qué es la Administración de modelos de Azure Machine Learning?**

Administración de modelos de Azure Machine Learning es un servicio administrado de Azure que permite a los científicos de datos y equipos de operaciones de desarrollo implementar modelos predictivos de forma confiable en una amplia variedad de entornos. Los repositorios de GIT y contenedores de Docker proporcionan repetibilidad y rastreabilidad. Los modelos pueden implementarse de forma confiable en el borde, en local o en la nube. Una vez en producción, puede administrar el rendimiento de los modelos y luego reciclar de manera proactiva si se degrada el rendimiento. Puede implementar modelos en equipos locales, en [Azure VM](https://azure.microsoft.com/services/virtual-machines/), en Spark en [HDInsight](https://azure.microsoft.com/services/hdinsight/) o en clústeres de [Azure Container Service](https://azure.microsoft.com/services/container-service/) organizados por Kubernetes.  

**¿Qué es un “modelo”?**

Un modelo es el resultado de una ejecución de experimentación que se ha promovido para la administración de modelos. Un modelo que se registra en la cuenta de hospedaje se cuenta para su plan, incluidos los modelos actualizados a través del reciclaje o la iteración de versiones.

**¿Qué es un “modelo administrado”?**

Un modelo es el resultado de un proceso de entrenamiento y consiste en la aplicación de un algoritmo de aprendizaje automático a los datos de entrenamiento. Administración de modelos permite implementar modelos en forma de servicios web, administrar varias versiones de los modelos y supervisar su rendimiento y sus métricas. Los modelos “administrados” se han registrado en una cuenta de Administración de modelos de Azure Machine Learning. Por ejemplo, imagine que está intentando hacer una previsión de ventas. Durante la fase de experimentación, genera muchos modelos usando diferentes conjuntos de datos y algoritmos. Ha generado cuatro modelos con valores de precisión diferentes, pero opta por registrar solo el modelo que ofrece la precisión más alta. El modelo que se ha registrado se convierte en el primer modelo administrado.
 
**¿Qué es una “implementación”?**

Administración de modelos permite implementar modelos como contenedores de servicios web empaquetados en Azure. Estos servicios web se pueden invocar mediante las API de REST. Cada servicio web cuenta como una sola implementación, y el número total de implementaciones activas cuenta para su plan. Usando el ejemplo de previsión de ventas, al implementar el modelo más preciso, se incrementa el plan en una implementación. Si, a continuación, recicla e implementa otra versión, tiene dos implementaciones. Si determina que el modelo nuevo es mejor y elimina el original, se reduce en uno el número de implementaciones.  

**¿Qué recursos de proceso específicos están disponibles para las implementaciones?** 

Administración de modelos puede ejecutar las implementaciones de contenedores de Docker registradas en [Azure Container Service](https://azure.microsoft.com/services/container-service/), como [Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/), o en equipos locales. En breve se agregarán destinos de implementación adicionales. Tenga en cuenta que los recursos de proceso se facturan por separado según los precios de cada uno.     

**¿Puedo usar Administración de modelos de Azure Machine Learning para implementar modelos creados con herramientas que no sean el servicio Experimentación?**

Obtiene la mejor experiencia al implementar modelos creados mediante el servicio Experimentación. Sin embargo, puede implementar modelos generados mediante otras herramientas y marcos. Se admite una variedad de modelos, que incluyen MMLSpark, TensorFlow, Microsoft Cognitive Toolkit, scikit-learn, Keras, etc. 

**¿Pueden usarse recursos de Azure propios?**

Sí, el servicio Experimentación y Administración de modelos funcionan en conjunto con varios almacenes de datos de Azure, cargas de trabajo de proceso y otros servicios. Consulte la documentación técnica para obtener más información sobre los servicios de Azure necesarios.

**¿Se admiten escenarios de implementación tanto de forma local como en la nube?**

Sí. Se admiten escenarios de implementación locales y en la nube a través de contenedores Docker. Los destinos de ejecución locales incluyen: implementaciones de Docker de nodo único, [Microsoft SQL Server con servicios de ML](https://docs.microsoft.com/sql/advanced-analytics/r/r-services), Hadoop o Spark. También se admiten implementaciones en la nube a través de Docker, incluidas: implementaciones en clúster a través de Azure Container Service, y clústeres de Kubernetes, HDInsight o Spark. Los escenarios de borde se admiten a través de contenedores de Docker y Azure IoT Edge. 

**¿Es posible ejecutar una imagen de Docker que se creó mediante la CLI de Azure Machine Learning en otro host?**

Sí. Puede usar la imagen como servicio web en cualquier host de Docker siempre y cuando el host tenga suficientes recursos de proceso para hospedar la imagen de Docker.

**¿Se admite el reciclaje de los modelos implementados?**

Sí, puede implementar varias versiones del mismo modelo. Administración de modelos será compatible con las actualizaciones del servicio para todos los modelos e imágenes actualizados.

## <a name="workbench"></a>Workbench

**¿Qué es Azure Machine Learning Workbench?**

Azure Machine Learning Workbench es una aplicación complementaria creada para científicos de datos profesionales. Disponible para Windows y Mac, Machine Learning Workbench proporciona información general, administración y control para las soluciones de aprendizaje automático. Machine Learning Workbench incluye el acceso a marcos de inteligencia artificial de vanguardia de Microsoft y de la comunidad de código abierto. Se han incluido los kits de herramientas más populares para la ciencia de datos, tales como TensorFlow, Microsoft Cognitive Toolkit, Spark ML, scikit-learn y muchos más. También se ha habilitado la integración con IDE populares de la ciencia de datos, como Jupyter Notebook, PyCharm y Visual Studio Code. Machine Learning Workbench tiene funcionalidades integradas de preparación de datos para el rápido muestreo, comprensión y preparación de los datos, estén estructurados o no. La nueva herramienta de preparación de datos, denominada [PROSE](https://microsoft.github.io/prose/), se basa en tecnología de vanguardia de Microsoft Research.  

**¿Es Workbench un IDE?**

No. Machine Learning Workbench se ha diseñado como complemento de los IDE populares, como Jupyter Notebook, Visual Studio Code y PyCharm, pero no es un IDE totalmente funcional. Machine Learning Workbench ofrece algunas funcionalidades básicas de edición de texto, pero la depuración, IntelliSense y otras funcionalidades de IDE normalmente usadas no son compatibles. Se recomienda usar el IDE favorito para el desarrollo, la edición y la depuración de código. Puede que también quiera probar [Visual Studio Code Tools for AI](https://www.visualstudio.com/downloads/ai-tools-vscode).

**¿Hay un cargo por usar Azure Machine Learning Workbench?**

No. Azure Machine Learning Workbench es una aplicación gratuita. Puede descargarla en tantas máquinas y para tantos usuarios como necesite. Para usar Azure Machine Learning Workbench, debe tener una cuenta de Experimentación. .  

**¿Se admiten las funcionalidades de línea de comandos?**

Sí, Azure Machine Learning ofrece una completa interfaz CLI. La CLI de Machine Learning se instala de manera predeterminada con Azure Machine Learning Workbench. También se proporciona como parte de Data Science Virtual Machine de Linux en Azure y se integrarán en la [CLI de Azure](https://docs.microsoft.com/cli/azure?view=azure-cli-latest)


**¿Puede usarse Jupyter Notebook con Workbench?**

Sí. Puede ejecutar Jupyter Notebook en Workbench, con Workbench como aplicación de hospedaje cliente, tal como utilizaría un explorador como cliente. 

**¿Qué kernels de Jupyter Notebook se admiten?**

La versión actual de Jupyter incluida con Workbench inicia un kernel de Python 3 y un kernel adicional para cada archivo de "runconfig" en la carpeta aml_config. Entre las configuraciones admitidas se incluyen:
- Python local
- Python en Docker local o remoto

## <a name="data-formats-and-capabilities"></a>Formatos de datos y funcionalidades

**¿Qué formatos de archivo se admiten actualmente para la ingesta de datos en Workbench?**

Las herramientas de preparación de datos de Workbench admiten actualmente la ingesta de los siguientes formatos: 
- Archivos delimitados, como CSV, TSV, etc.  
- Archivos con ancho fijo
- Texto sin formato
- Excel (.xls/.xlsx)
- Archivos JSON
- Archivos de Parquet 
- Archivos personalizados (scripts). Si su solución requiere la ingesta de datos de orígenes adicionales, puede utilizarse el código de Python para… 

**¿Actualmente qué ubicaciones de almacenamiento de datos se admiten?**

Para la versión preliminar pública, Workbench admite la ingesta de datos de: 
- Unidad de disco duro local o ubicación de almacenamiento de red asignada
- Azure Blob o Azure Storage (requiere una suscripción a Azure)
- Azure SQL Server
- Microsoft SQL Server


**¿Qué tipos de tratamientos, preparación y transformaciones de datos están disponibles?**

Para la versión preliminar pública, Workbench admite “Derivación de columnas por ejemplos”, “División de columnas por ejemplos”, “Text Clustering”, “Handle Missing Values” y muchos otros.  Workbench también admite la conversión de tipos de datos, la agregación de datos (RECUENTO, MEDIA, VARIANZA, etc.) y combinaciones de datos complejas. Para obtener una lista completa de las funcionalidades admitidas, consulte la documentación del producto. 

**¿Existen límites para el tamaño de datos, exigidos por Azure Machine Learning Workbench, Experimentación o Administración de modelos?**

No, los nuevos servicios no imponen ninguna limitación de datos. Sin embargo, existen limitaciones introducidas por el entorno en el que va a realizar la preparación de datos, el entrenamiento del modelo, la experimentación o la implementación. Por ejemplo, si tiene como destino un entorno local para el entrenamiento, está limitado por el espacio disponible en el disco duro. O bien, si el destino es HDInsight, está limitado por cualquier tamaño asociado o restricciones de proceso. 

## <a name="algorithms-and-libraries"></a>Algoritmos y bibliotecas

**¿Qué algoritmos se admiten en Azure Machine Learning Workbench?**

Nuestros productos y servicios en versión preliminar incluyen lo mejor de la comunidad de código abierto. Se admite una amplia variedad de algoritmos y bibliotecas, incluidos TensorFlow, scikit-learn, Apache Spark y Microsoft Cognitive Toolkit. Azure Machine Learning Workbench también empaqueta el paquete [revoscalepy de Microsoft](https://docs.microsoft.com/sql/advanced-analytics/python/what-is-revoscalepy).

**¿Cómo se relaciona Azure Machine Learning con Microsoft Cognitive Toolkit?**

[Microsoft Cognitive Toolkit](https://docs.microsoft.com/cognitive-toolkit/) es uno de los muchos marcos compatibles con nuestras nuevas herramientas y servicios. Cognitive Toolkit es un kit de herramientas unificado de aprendizaje profundo que le permite consumir y combinar modelos populares de aprendizaje automático, incluidas redes neuronales profundas feedforward, redes convolucionales, de secuencia a secuencia y redes recurrentes. Para obtener más información sobre Microsoft Cognitive Toolkit, visite nuestra [documentación del producto](https://docs.microsoft.com/cognitive-toolkit/). 