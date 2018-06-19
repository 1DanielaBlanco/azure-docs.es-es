---
title: Acerca de Azure Migrate | Microsoft Docs
description: Proporciona información general acerca del servicio Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: overview
ms.date: 06/08/2018
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 68f335762e1fdd68296d7056ef5826f69c868d70
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/08/2018
ms.locfileid: "35236372"
---
# <a name="about-azure-migrate"></a>Acerca de Azure Migrate

El servicio Azure Migrate evalúa las cargas de trabajo locales para su migración a Azure. El servicio evalúa la idoneidad de la migración de las máquinas locales y el ajuste de tamaño basado en el rendimiento, y proporciona estimaciones del costo que supone la ejecución de máquinas locales en Azure. Si está pensando en migrar mediante lift-and-shift o se encuentra en las primeras fases de la evaluación de la migración, este es el servicio que debe elegir. Tras la evaluación, puede usar servicios como [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) y [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview) para migrar las máquinas a Azure.

## <a name="why-use-azure-migrate"></a>¿Por qué usar Azure Migrate?

Azure Migrate le ayuda a:

- **Evaluar la preparación para Azure**: evalúe si las máquinas locales son apropiadas para ejecutarse en Azure.
- **Obtener recomendaciones de tamaño**: averigüe el tamaño recomendado de las máquinas virtuales de Azure en función del historial de rendimiento de las máquinas virtuales locales.
- **Calcular los costos mensuales**: calcule el costo estimado de la ejecución de máquinas locales en Azure.  
- **Migrar con una confianza alta**: vea las dependencias de los equipos locales para crear los grupos de equipos que va a evaluar y migrar a la vez.

## <a name="current-limitations"></a>Limitaciones actuales

- Actualmente, solo puede evaluar máquinas virtuales de VMware locales para la migración a máquinas virtuales de Azure. Las máquinas virtuales de VMware las debe administrar vCenter Server (versiones 5.5, 6.0 o 6.5).
- La compatibilidad con Hyper-V está en nuestra hoja de ruta. Mientras tanto, se recomienda usar [Azure Site Recovery Deployment Planner](http://aka.ms/asr-dp-hyperv-doc) para planear la migración de las cargas de trabajo de Hyper-V.
- Puede detectar hasta 1500 máquinas virtuales en una sola detección y hasta 1500 en un solo proyecto. Además, puede evaluar hasta 1500 máquinas virtuales en una valoración única.
- Los proyectos de Azure Migrate solo se pueden crear en la región Centro occidental de EE. UU. o Este de EE. UU. Sin embargo, esto no afecta a su capacidad para planear la migración de otra ubicación de Azure de destino. La ubicación del proyecto de migración solo se usa para almacenar los metadatos que se detectan desde el entorno local.
- Azure Migrate solo admite discos administrados para la valoración de la migración.


## <a name="what-do-i-need-to-pay-for"></a>¿Por qué conceptos tengo qué pagar?

[Aquí](https://azure.microsoft.com/en-in/pricing/details/azure-migrate/) puede encontrar más información sobre los precios de Azure Migrate.


## <a name="whats-in-an-assessment"></a>¿Qué es una evaluación?

Una evaluación le ayuda a identificar si las máquinas virtuales locales son idóneas para Azure, le ofrece recomendaciones sobre el tamaño adecuado y calcula el costo de la ejecución de las máquinas virtuales en Azure. Las evaluaciones se pueden personalizar según sus necesidades cambiando las propiedades de la evaluación. A continuación se muestran las propiedades que se tienen en cuenta al crear una evaluación.

**Propiedad** | **Detalles**
--- | ---
**Ubicación de destino** | La ubicación de Azure a la que desea realizar la migración.<br/><br/>Azure Migrate admite actualmente 30 regiones entre las que se incluyen: Este de Australia, Sudeste de Australia, Sur de Brasil, Centro de Canadá, Este de Canadá, India central, Centro de EE. UU., Este de China, Norte de China, Asia Oriental, Este de EE. UU., Centro de Alemania, Noreste de Alemania, Este de EE. UU. 2, Japón Oriental, Japón Occidental, Centro de Corea del Sur, Corea del Sur, Centro y norte de EE. UU., Europa del Norte, Centro y sur de EE. UU., Sudeste Asiático, India del Sur, Sur del Reino Unido, Oeste del Reino Unido, US Gov Arizona, US Gov Texas, US Gov Virginia, Centro occidental de EE. UU., Europa Occidental, India occidental, Oeste de EE. UU. y Oeste de EE. UU. 2. De forma predeterminada, la ubicación de destino es la región Oeste de EE. UU. 2.
**Tipo de almacenamiento** | Puede especificar el tipo de discos que desea asignar en Azure. Esta propiedad se aplica cuando el criterio de tamaño es como local. Puede especificar el tipo de disco de destino como Managed Disks Premium o Managed Disks Estándar. El valor predeterminado es Managed Disks Premium. Para el tamaño basado en el rendimiento, la recomendación del disco se realiza automáticamente en función de los datos de rendimiento de las máquinas virtuales. Tenga en cuenta que Azure Migrate solo admite discos administrados para la valoración de la migración.
**Criterio de ajuste de tamaño** | El criterio que debe utilizar Azure Migrate para ajustar el tamaño de las máquinas virtuales para Azure. Puede ajustar el tamaño en función del *historial de rendimiento* de las máquinas virtuales locales o ajustar las máquinas virtuales *como locales* para Azure sin tener en cuenta el historial de rendimiento. El valor predeterminado es el tamaño como local.
**Panes de tarifa** | Para calcular los costos, una evaluación tiene en cuenta si se dispone de Software Assurance y si se puede optar por [Ventaja híbrida de Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/). También tiene en cuenta las [Ofertas de Azure](https://azure.microsoft.com/support/legal/offer-details/) a las que se haya suscrito y le permite especificar los descuentos específicos de cualquier suscripción (%), que puede obtener además de la oferta.
**Plan de tarifa** | Puede especificar el [plan de tarifa (Básico o Estándar)](../virtual-machines/windows/sizes-general.md) de las máquinas virtuales de Azure de destino. Por ejemplo, si va a migrar un entorno de producción, debería tener en cuenta el plan Estándar, que proporciona máquinas virtuales con una latencia baja aunque con un costo más alto. Por otro lado, si tiene un entorno de desarrollo y pruebas, quizá debería considerar el plan Básico que tiene máquinas virtuales con una latencia mayor y un costo más bajo. De forma predeterminada se usa el plan [Estándar](../virtual-machines/windows/sizes-general.md).
**Historial de rendimiento** | De forma predeterminada, Azure Migrate usa el historial de rendimiento del último día para evaluar el rendimiento de las máquinas locales, con un valor de percentil del 95 %. Puede modificar estos valores en las propiedades de la evaluación.
**Series de VM** | Puede especificar la series de VM que quiera tener en cuenta para determinar el tamaño adecuado. Por ejemplo, si tiene un entorno de producción que no vaya a migrar a la serie A de las máquinas virtuales de Azure, puede excluir la serie A de la lista o serie, y el ajuste de tamaño correcto se realizará solo en la serie seleccionada.  
**Factor de confort** | Azure Migrate tiene en cuenta un búfer (factor de confort) durante la evaluación. Dicho búfer se aplica además de los datos de uso de la máquina en las máquinas virtuales (CPU, memoria, disco y red). El factor de confort se tiene en cuenta en problemas como el uso estacional, un historial de rendimiento corto y los posibles aumentos en el uso futuro.<br/><br/> Por ejemplo, una máquina virtual de 10 núcleos con un uso del 20 % normalmente genera una máquina virtual de 2 núcleos. Sin embargo, con un factor de confort de 2.0 x, el resultado es una máquina virtual de 4 núcleos. El valor de confort predeterminado es 1.3 x.


## <a name="how-does-azure-migrate-work"></a>¿Cómo funciona Azure Migrate?

1.  Cree un proyecto de Azure Migrate.
2.  Azure Migrate usa una máquina virtual local, llamada el dispositivo recopilador, para detectar información acerca de las máquinas locales. Para crear el dispositivo, descargue el archivo de instalación en formato Open Virtualization Appliance (.ova) e impórtelo como una máquina virtual en la instancia local de vCenter Server.
3.  Para conectarse a la máquina virtual, use la conexión de la consola en vCenter Server, especifique una nueva contraseña para la máquina virtual durante la conexión y, a continuación, ejecute el dispositivo recopilador en la máquina virtual para iniciar la detección.
4.  El recopilador recoge metadatos de máquinas virtuales con los cmdlets de PowerCLI para VMware. La detección se realiza sin agente y no instala nada en los hosts ni en las máquinas virtuales de VMware. Los metadatos recopilados incluyen información acerca de la máquina virtual (núcleos, memoria, discos, tamaños de disco y adaptadores de red). También recopila datos de rendimiento de las máquinas virtuales, incluyendo el uso de la CPU y de la memoria, el IOPS de disco, el rendimiento del disco (MBps) y la red de salida (MBps).
5.  Los metadatos se insertan en el proyecto de Azure Migrate. Puede verlo en Azure Portal.
6.  Para la evaluación, las máquinas virtuales detectadas se reúnen en grupos. Por ejemplo, se pueden agrupar las máquinas virtuales que ejecutan la misma aplicación. Para obtener un agrupamiento más preciso, puede usar la visualización de dependencias para ver las dependencias de una máquina específica o de todas las máquinas de un grupo, y refinar el grupo.
7.  Una vez formado el grupo, se crea una evaluación para el grupo.
8.  Una vez finalizada la evaluación, se puede ver en el portal, o bien descargar en formato de Excel.



  ![Arquitectura de Azure Planner](./media/migration-planner-overview/overview-1.png)

## <a name="what-are-the-port-requirements"></a>¿Cuáles son los requisitos de puertos?

La tabla resumen los puertos necesarios para las comunicaciones de Azure Migrate.

|Componente          |Para comunicarse con     |Puerto requerido  |Motivo   |
|-------------------|------------------------|---------------|---------|
|Recopilador          |Servicio Azure Migrate   |TCP 443        |El recolector se conecta al servicio a través del puerto 443 de SSL|
|Recopilador          |vCenter Server          |443 predeterminado   | De manera predeterminada, el recopilador se conecta a vCenter Server en el puerto 443. Si el servidor escucha otro puerto, debe configurarse como puerto de salida en la máquina virtual del recopilador. |
|Máquina virtual local     | Área de trabajo de Log Analytics          |[TCP 443](../log-analytics/log-analytics-windows-agent.md) |El agente MMA usa TCP 443 para conectarse a Log Analytics. Este puerto solo se necesita si se usa la característica de visualización de dependencias y se instala Microsoft Monitoring Agent (MMA). |



## <a name="what-happens-after-assessment"></a>¿Qué pasa después de la evaluación?

Una vez que haya evaluado las máquinas locales para la migración con el servicio Azure Migrate, puede usar un par de herramientas para realizar la migración:

- **Azure Site Recovery**: puede usar Azure Site Recovery para realizar la migración a Azure, como se indica a continuación:
  - Prepare los recursos de Azure, entre los que se incluyen una suscripción a Azure, una red virtual de Azure y una cuenta de almacenamiento.
  - Prepare los servidores VMware locales para la migración. Compruebe los requisitos de compatibilidad de VMware con Site Recovery, prepare los servidores de VMware para la detección y prepárelos para instalar el servicio Site Recovery Mobility en las máquinas virtuales que desea migrar.
  - Configure la migración. Configure un almacén de Recovery Services, los valores de origen y destino de la migración y una directiva de replicación, y habilite la replicación. Puede ejecutar una un simulacro de recuperación ante desastres para comprobar que la migración de una máquina virtual a Azure funciona correctamente.
  - Ejecute una conmutación por error para migrar máquinas locales a Azure.
  - Para [más información](../site-recovery/tutorial-migrate-on-premises-to-azure.md), consulte el tutorial de migración de Site Recovery.

- **Azure Database Migration**: si las máquinas locales ejecutan una base de datos como SQL Server, MySQL u Oracle, puede usar Azure Database Migration Service para migrarlas a Azure. [Más información](https://azure.microsoft.com/campaigns/database-migration/).



## <a name="next-steps"></a>Pasos siguientes

- [Siga un tutorial](tutorial-assessment-vmware.md) para crear una evaluación de una máquina virtual de VMware local.
- [Más información](resources-faq.md) sobre las preguntas más frecuentes sobre Azure Migrate
