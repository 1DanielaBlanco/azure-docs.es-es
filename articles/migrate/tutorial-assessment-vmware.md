---
title: Detección y evaluación de VM de VMware locales para la migración a Azure con Azure Migrate | Microsoft Docs
description: Describe cómo detectar y evaluar VM de VMware locales para la migración a Azure, utilizando el servicio Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 07/09/2018
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 0b1070e29c8dc9f088297622d16fb816a10a55c0
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/12/2018
ms.locfileid: "38970792"
---
# <a name="discover-and-assess-on-premises-vmware-vms-for-migration-to-azure"></a>Detección y evaluación de VM de VMware locales para migración a Azure

El servicio [Azure Migrate](migrate-overview.md) evalúa las cargas de trabajo locales para su migración a Azure.

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Crear una cuenta que Azure Migrate usará para detectar máquinas virtuales locales.
> * Cree un proyecto de Azure Migrate.
> * Configure una máquina virtual (VM) de recopilador local para detectar VM de VMware locales para la evaluación.
> * Agrupe las VM y cree una evaluación.


Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de empezar.


## <a name="prerequisites"></a>requisitos previos

- **VMware**: las máquinas virtuales que planea migrar deben administrarse mediante vCenter Server en la versión 5.5, 6.0 o 6.5. Además, necesita un host de ESXi que ejecute la versión 5.0 o posterior para implementar la máquina virtual del recopilador.
- **Cuenta de vCenter Server** : necesita una cuenta de solo lectura para acceder el servidor vCenter. Azure Migrate usa esta cuenta para detectar las máquinas virtuales locales.
- **Permisos**: en el servidor vCenter Server, necesitará permisos para crear una máquina virtual mediante la importación de un archivo en formato .OVA.
- **Configuración de estadísticas**: la configuración de las estadísticas del servidor vCenter Server se debe establecer en el nivel 3 antes de empezar la implementación. Si el nivel es inferior al 3, la valoración funcionará, pero no se recopilarán datos de rendimiento del almacenamiento y la red. El tamaño de las recomendaciones en este caso se hará según los datos de rendimiento de los datos de CPU y memoria, y de los datos de configuración de adaptadores de red y disco.

## <a name="create-an-account-for-vm-discovery"></a>Creación de una cuenta para la detección de máquinas virtuales

Azure Migrate necesita acceso a los servidores de VMware para detectar automáticamente las máquinas virtuales para su evaluación. Cree una cuenta de VMware con las siguientes propiedades. Esta cuenta se especifica durante la instalación de Azure Migrate.

- Tipo de usuario: al menos un usuario de solo lectura.
- Permisos: Data Center -> Propagate to Child Object, role=Read-only (Centro de datos -> Propagar a objeto secundario, rol = Solo lectura).
- Detalles: el usuario se asigna en el nivel de centro de datos y tiene acceso a todos los objetos de este.
- Para restringir el acceso, asigne el rol No access (Sin acceso) con Propagate to child object (Propagar a objeto secundario) a los objetos secundarios (hosts de vSphere, almacenes de datos, máquinas virtuales y redes).


## <a name="log-in-to-the-azure-portal"></a>Iniciar sesión en Azure Portal

Inicie sesión en [Azure Portal](https://portal.azure.com).

## <a name="create-a-project"></a>Crear un proyecto

1. En Azure Portal, haga clic en **Crear un recurso**.
2. Busque **Azure Migrate** y seleccione el servicio **Azure Migrate** en los resultados de búsqueda. A continuación, haga clic en **Crear**.
3. Especifique un nombre de proyecto y la suscripción de Azure para el proyecto.
4. Cree un nuevo grupo de recursos.
5. Especifique la ubicación en la que se va a crear el proyecto y haga clic en **Crear**. Los proyectos de Azure Migrate solo se pueden crear en la región Centro occidental de EE. UU. o Este de EE. UU. Sin embargo, todavía puede planear la migración de cualquier ubicación de Azure de destino. La ubicación especificada para el proyecto solo se utiliza para almacenar los metadatos que se recopilan a partir de máquinas virtuales locales.

    ![Azure Migrate](./media/tutorial-assessment-vmware/project-1.png)



## <a name="download-the-collector-appliance"></a>Descarga del dispositivo de recopilador

Azure Migrate crea una VM local conocida como el dispositivo de recopilador. Esta VM detecta VM de VMware locales y envía los metadatos sobre ellas para al servicio Azure Migrate. Para configurar el dispositivo de recopilador, descargue un archivo .OVA e impórtelo en el servidor vCenter local para crear la VM.

1. En el proyecto de Azure Migrate, haga clic en **Introducción** > **Detectar y evaluar** > **Detectar máquinas**.
2. En **Detectar máquinas**, haga clic en **Descargar**, para descargar el archivo .OVA.
3. En **Copiar las credenciales del proyecto**, copie la clave y el identificador del proyecto. Los necesitará cuando configure el recopilador.

    ![Descarga del archivo .ova](./media/tutorial-assessment-vmware/download-ova.png)

### <a name="verify-the-collector-appliance"></a>Comprobación del dispositivo de recopilador

Compruebe que el archivo .OVA es seguro, antes de implementarlo.

1. En la máquina en la que descargó el archivo, abra una ventana de comandos de administrador.
2. Ejecute el siguiente comando para generar el código hash para el archivo OVA:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Ejemplo de uso: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. El código hash generado debe coincidir con esta configuración.

  Para la versión 1.0.9.12 de OVA

    **Algoritmo** | **Valor del código hash**
    --- | ---
    MD5 | d0363e5d1b377a8eb08843cf034ac28a
    SHA1 | df4a0ada64bfa59c37acf521d15dcabe7f3f716b
    SHA256 | f677b6c255e3d4d529315a31b5947edfe46f45e4eb4dbc8019d68d1d1b337c2e

  Para la versión 1.0.9.8 de OVA

    **Algoritmo** | **Valor del código hash**
    --- | ---
    MD5 | b5d9f0caf15ca357ac0563468c2e6251
    SHA1 | d6179b5bfe84e123fabd37f8a1e4930839eeb0e5
    SHA256 | 09c68b168719cb93bd439ea6a5fe21a3b01beec0e15b84204857061ca5b116ff

    Para la versión 1.0.9.7 de OVA

    **Algoritmo** | **Valor del código hash**
    --- | ---
    MD5 | d5b6a03701203ff556fa78694d6d7c35
    SHA1 | f039feaa10dccd811c3d22d9a59fb83d0b01151e
    SHA256 | e5e997c003e29036f62bf3fdce96acd4a271799211a84b34b35dfd290e9bea9c

    Para la versión 1.0.9.5 de OVA

    **Algoritmo** | **Valor del código hash**
    --- | ---
    MD5 | fb11ca234ed1f779a61fbb8439d82969
    SHA1 | 5bee071a6334b6a46226ec417f0d2c494709a42e
    SHA256 | b92ad637e7f522c1d7385b009e7d20904b7b9c28d6f1592e8a14d88fbdd3241c  

    Para la versión 1.0.9.2 de OVA

    **Algoritmo** | **Valor del código hash**
    --- | ---
    MD5 | 7326020e3b83f225b794920b7cb421fc
    SHA1 | a2d8d496fdca4bd36bfa11ddf460602fa90e30be
    SHA256 | f3d9809dd977c689dda1e482324ecd3da0a6a9a74116c1b22710acc19bea7bb2  

## <a name="create-the-collector-vm"></a>Creación de la VM de recopilador

Importe el archivo descargado en el servidor vCenter Server.

1. En la consola de cliente de vSphere, haga clic en **File** (Archivo) > **Deploy OVF Template** (Implementar plantilla de OVF).

    ![Implementación de OVF](./media/tutorial-assessment-vmware/vcenter-wizard.png)

2. En el Deploy OVF Template Wizard (Asistente para implementar la plantilla de OVF) > **Source** (Origen), especifique la ubicación del archivo .ova.
3. En **Name** (Nombre) y **Location** (Ubicación), especifique un nombre descriptivo para la VM de recopilador y el objeto de inventario en el que se hospedará la VM.
5. En **Host/Cluster** (Host o clúster), especifique el host o clúster donde se ejecutará el recopilador de máquina virtual.
7. En el almacenamiento, especifique el destino de almacenamiento para la VM de recopilador.
8. En **Disk Format** (Formato de disco), especifique el tamaño y el tipo de disco.
9. En **Network Mapping** (Asignación de red), especifique la red a la que se conectará la VM de recopilador. La red necesita conectividad a Internet, para enviar metadatos a Azure.
10. Revise y confirme la configuración y haga clic en **Finish** (Finalizar).

## <a name="run-the-collector-to-discover-vms"></a>Ejecución del recopilador para detectar VM

1. En la consola del cliente de vSphere, haga clic con el botón derecho en VM > **Open Console** (Abrir consola).
2. Proporcione el idioma, la zona horaria y las preferencias de contraseña para el dispositivo.
3. En el escritorio, haga clic en el acceso directo **Run collector** (Ejecutar recopilador).
4. Haga clic en **Buscar actualizaciones** en la barra superior de la interfaz de usuario del recopilador y compruebe que este está ejecutando la versión más reciente. Si no es así, puede optar por descargar el último paquete de actualización desde el vínculo y actualizar el recopilador.
5. En Azure Migrate Collector, abra **Set Up Prerequisites** (Configurar requisitos previos).
    - Acepte los términos de licencia y lea la información de terceros.
    - El recopilador comprueba que la VM tenga acceso a Internet.
    - Si la VM tiene acceso a Internet a través de un proxy, haga clic en **Proxy settings** (Configuración de proxy) y especifique el puerto de escucha y la dirección del proxy. Especifique las credenciales si el proxy requiere autenticación. [Obtener más información](https://docs.microsoft.com/azure/migrate/concepts-collector#internet-connectivity) sobre los requisitos de conectividad de internet y la lista de direcciones URL a las que tiene acceso el recopilador.

    > [!NOTE]
    > La dirección del proxy se tiene que especificarse con el formato http://ProxyIPAddress o http://ProxyFQDN. Solo se admite un proxy HTTP.

    - El recopilador comprueba que el servicio del recopilador se está ejecutando. El servicio se instala de forma predeterminada en la VM de recopilador.
    - Descargue e instale VMware PowerCLI.

6. En **Specify vCenter Server details** (Especificar detalles de vCenter Server), haga lo siguiente:
    - Especifique el nombre (FQDN) o la dirección IP del servidor vCenter.
    - En **Nombre de usuario** y **Contraseña**, especifique las credenciales de cuenta de solo lectura que utilizará el recopilador para detectar las VM en el servidor vCenter.
    - En **Ámbito del grupo**, seleccione un ámbito para la detección de VM. El recopilador solo puede detectar VM dentro del ámbito especificado. El ámbito se puede establecer en una carpeta, centro de datos o clúster específico. No debe contener más de 1500 máquinas virtuales. [Obtener más información](how-to-scale-assessment.md) acerca de cómo puede detectar un entorno mayor.

7. En **Specify migration project** (Especificar proyecto de migración), especifique la clave y el identificador de proyecto de Azure Migrate que copió del portal. Si no los copió, abra Azure Portal desde la VM de recopilador. En la página **Introducción** del proyecto, haga clic en **Detectar máquinas** y copie los valores.  
8. En **View collection progress** (Ver progreso de recopilación), supervise la detección y compruebe que los metadatos recopilados de las máquinas virtuales se encuentran dentro del ámbito. El recopilador proporciona un tiempo de detección aproximado. [Obtener más información](https://docs.microsoft.com/azure/migrate/concepts-collector#what-data-is-collected) sobre qué datos reúne Azure Migrate Collector.

> [!NOTE]
> El recopilador solo admite "Inglés (Estados Unidos)" como el idioma del sistema operativo y el idioma de la interfaz del recopilador.
> Si modifica la configuración de una máquina que desea evaluar, desencadene la detección de nuevo antes de ejecutar la evaluación. En el recopilador, utilice la opción **Volver a iniciar la recopilación** para hacer esto. Una vez realizada la recopilación, seleccione la opción **Recalcular** para la evaluación en el portal, para obtener resultados actualizados de la evaluación.



### <a name="verify-vms-in-the-portal"></a>Comprobación de VM en el portal

El tiempo de detección depende de cuántas VM se están detectando. Normalmente, para 100 VM, después de que el recopilador finaliza la ejecución, la detección tarda alrededor de una hora en completarse.

1. En el proyecto de Migration Planner, haga clic en **Administrar** > **Máquinas**.
2. Compruebe que las VM que desea detectar aparecen en el portal.


## <a name="create-and-view-an-assessment"></a>Creación y visualización de una evaluación

Después de detectar las VM, las agrupará y creará una evaluación.

1. En la página **Introducción** del proyecto, haga clic en **+Crear evaluación**.
2. Haga clic en **View all** (Ver todo) para revisar la configuración de la valoración.
3. Cree el grupo y especifique un nombre para él.
4. Seleccione las máquinas que desee agregar al grupo.
5. Haga clic en **Crear evaluación** para crear el grupo y la evaluación.
6. Una vez creada la evaluación, puede verla en **Introducción** > **Panel**.
7. Haga clic en **Exportar la evaluación** para descargarla como un archivo de Excel.

### <a name="assessment-details"></a>Detalles de la valoración

La valoración incluye información sobre si las máquinas virtuales locales son compatibles con Azure, cuál sería el tamaño correcto de la máquina virtual para ejecutarla en Azure y cuáles serían los costos mensuales estimados de Azure.

![Informe de evaluación](./media/tutorial-assessment-vmware/assessment-report.png)

#### <a name="azure-readiness"></a>Preparación para Azure

La vista de preparación para Azure en la valoración muestra el estado de preparación de cada máquina virtual. Dependiendo de sus propiedades, cada máquina virtual se puede marcar como:
- Preparada para Azure
- Condicionalmente preparada para Azure
- No está preparada para Azure
- Preparación desconocida

Para las VM que están preparadas, Azure Migrate recomienda un tamaño de VM en Azure. La recomendación de tamaño que realiza Azure Migrate depende del criterio de tamaño que se especifica en las propiedades de la valoración. Si el criterio de tamaño se corresponde con el ajuste de tamaño basado en el rendimiento, se realiza la recomendación de tamaño teniendo en cuenta el historial de rendimiento de las máquinas virtuales (CPU y memoria) y discos (IOPS y rendimiento). Si el criterio de tamaño es "como local", Azure Migrate no tiene en cuenta los datos de rendimiento para las máquinas virtuales y los discos. La recomendación para el tamaño de la máquina virtual en Azure se hace examinando el tamaño de la máquina virtual local y el tamaño del disco se realiza basándose en el tipo de almacenamiento especificado en las propiedades de evaluación (de manera predeterminada son discos Premium). [Más información](concepts-assessment-calculation.md) acerca de cómo se realiza el ajuste de tamaño en Azure Migrate.

Para las máquinas virtuales que no están preparadas o están condicionalmente preparadas para Azure, Azure Migrate explica los problemas de preparación y proporciona los pasos para su corrección.

Las máquinas virtuales para las que Azure Migrate no puede identificar la preparación para Azure (debido a la falta de disponibilidad de datos) se consideran de preparación desconocida.

Además del ajuste de tamaño y la preparación para Azure, Azure Migrate también sugiere herramientas que puede usar para migrar la máquina virtual. Esto requiere una detección más profunda en el entorno local. [Obtenga más información](how-to-get-migration-tool.md) acerca de cómo realizar una detección más profunda mediante la instalación de agentes en los equipos locales. Si los agentes no están instalados en los equipos locales, se recomienda realizar una migración mediante lift-and-shift con [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview). Si los agentes están instalados en el equipo local, Azure Migrate examina los procesos que se ejecutan dentro del equipo e identifica si es un equipo de base de datos o no. Si es un equipo de base de datos, se sugiere [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview) como herramienta de migración; de lo contrario, se sugiere Azure Site Recovery.

  ![Preparación para Azure](./media/tutorial-assessment-vmware/assessment-suitability.png)  

#### <a name="monthly-cost-estimate"></a>Costo mensual estimado

Esta vista muestra el cálculo total y el costo de almacenamiento por ejecutar las máquinas virtuales en ejecución en Azure, además de los detalles de cada máquina. Las estimaciones de costos se calculan según las recomendaciones de tamaño realizadas por Azure Migrate para una máquina, sus discos y las propiedades de la valoración.

> [!NOTE]
> La estimación de costos que proporciona Azure Migrate es por ejecutar las máquinas virtuales locales como infraestructura de Azure como máquinas virtuales de infraestructura como servicio (IaaS) de Azure. Azure Migrate no tiene en cuenta los costos de cualquier plataforma como servicio (PaaS) o software como servicio (SaaS).

Los costos mensuales estimados para el proceso y almacenamiento se agregan para todas las VM del grupo.

![Costo de VM de evaluación](./media/tutorial-assessment-vmware/assessment-vm-cost.png)

#### <a name="confidence-rating"></a>Clasificación de confianza

Cada valoración de Azure Migrate está asociada con una clasificación de confianza que va de 1 a 5 estrellas (siendo 1 estrella la más baja y 5 estrellas la más alta). La clasificación de confianza se asigna a una valoración que se basa en la disponibilidad de puntos de datos necesarios para calcular tal valoración. La clasificación de confianza de una valoración le ayuda a calcular la confiabilidad de las recomendaciones de tamaño que proporciona Azure Migrate.

La clasificación de confianza de una valoración es más útil para valoraciones con criterio de tamaño como "tamaño basado en el rendimiento". Para ajustar el tamaño basado en el rendimiento, Azure Migrate necesita los datos de uso de la CPU y la memoria de la máquina virtual. Además, para cada disco asociado a la máquina virtual, necesita el valor de IOPS de disco y los datos de rendimiento. De forma similar, para cada adaptador de red asociado a una máquina virtual, Azure Migrate necesita la entrada o la salida de red para realizar el ajuste de tamaño basado en el rendimiento. Si alguno de los números de uso anteriores no está disponible en vCenter Server, la recomendación de tamaño que realiza Azure Migrate podría no ser de confianza. Según el porcentaje de puntos de datos disponibles, se proporciona la clasificación de confianza para la valoración, tal como se indica a continuación:

   **Disponibilidad de puntos de datos** | **Clasificación de confianza**
   --- | ---
   0 % - 20 % | 1 estrella
   21 % - 40 % | 2 estrellas
   41 % - 60 % | 3 estrellas
   61 % - 80 % | 4 estrellas
   81 % - 100 % | 5 estrellas

Debido a uno de los siguientes motivos, puede que las valoraciones no tengan todos los puntos de datos disponibles:
- La configuración de estadísticas en vCenter Server no se establece en el nivel 3. Si la configuración de estadísticas en vCenter Server es inferior al nivel 3, no se recopilan los datos de rendimiento de disco y de red de vCenter Server. En este caso, la recomendación que proporciona Azure Migrate de disco y de red no se basa en el uso. Sin tener en cuenta el valor de IOPS o del rendimiento del disco, Azure Migrate no puede saber si el disco necesitará un disco premium en Azure; por lo tanto, en este caso, Azure Migrate recomienda usar discos estándar para todos los discos.
- La configuración de estadísticas en vCenter Server se estableció en el nivel 3 durante un período más corto, antes de iniciar la detección. Por ejemplo, veamos un escenario en el que hoy cambia el nivel de configuración de estadísticas a 3 y mañana inicia la detección mediante el dispositivo recopilador (una vez pasadas 24 horas). Si va a crear una valoración durante un día, tiene todos los puntos de datos y la clasificación de confianza de la valoración sería 5 estrellas. Pero, si cambia la duración del rendimiento a un mes en las propiedades de la valoración, la clasificación de confianza descenderá dado que los datos de disco y rendimiento de la red durante el último mes no estarían disponibles. Si quisiera considerar los datos de rendimiento del último mes, se recomienda que mantenga la configuración de las estadísticas de vCenter Server en el nivel 3 durante un mes antes de iniciar la detección.
- Se apagaron algunas máquinas virtuales en el período durante el que se calcula la valoración. Si se han apagado todas las máquinas virtuales durante un tiempo, vCenter Server no tendrá los datos de rendimiento de ese período.
- Algunas máquinas virtuales se crearon en algún momento del período durante el cual se calcula la valoración. Por ejemplo, si va a crear una valoración para el historial de rendimiento del último mes, pero algunas máquinas virtuales se crearon en el entorno hace solo una semana. En tales casos, el historial de rendimiento de las nuevas máquinas virtuales no permanecerá durante toda la duración.

> [!NOTE]
> Si la clasificación de confianza de cualquier valoración es inferior a 4 estrellas, recomendamos que cambie el nivel de configuración de las estadísticas de vCenter Server a 3, espere la duración que desee considerar para la valoración (un día, una semana o un mes) y, a continuación, realice la detección y la valoración. Si no se puede realizar lo anterior, el ajuste de tamaño basado en el rendimiento podría no ser de confianza y se recomienda cambiar a *como local* cambiando las propiedades de la valoración.

## <a name="next-steps"></a>Pasos siguientes

- [Obtenga información acerca de](how-to-modify-assessment.md) cómo personalizar una valoración según los requisitos.
- Aprenda a crear grupos de evaluación de confianza elevada mediante la [asignación de dependencias de máquina](how-to-create-group-machine-dependencies.md)
- [Obtenga más información](concepts-assessment-calculation.md) sobre cómo se calculan las evaluaciones.
- [Aprenda](how-to-scale-assessment.md) a detectar y evaluar un entorno grande de VMware.
- [Más información](resources-faq.md) sobre las preguntas más frecuentes sobre Azure Migrate
