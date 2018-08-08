---
title: Aplicación del recopilador de Azure Migrate | Microsoft Docs
description: Proporciona información general sobre la aplicación del recopilador y cómo configurarlo.
author: ruturaj
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/27/2018
ms.author: ruturajd
services: azure-migrate
ms.openlocfilehash: c99d0f74dbb8cc28cabebae60fe10645f4bdb3b6
ms.sourcegitcommit: cfff72e240193b5a802532de12651162c31778b6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/27/2018
ms.locfileid: "39308466"
---
# <a name="collector-appliance"></a>Aplicación del recopilador

[Azure Migrate](migrate-overview.md) evalúa las cargas de trabajo locales para su migración a Azure. En este artículo se proporciona información sobre cómo usar la aplicación del recopilador.



## <a name="overview"></a>Información general

Azure Migrate Collector es una aplicación ligera que se puede utilizar para detectar el entorno de vCenter local. Esta aplicación detecta máquinas de VMware locales y envía los metadatos sobre ellas al servicio Azure Migrate.

La aplicación del recopilador es un OVF que se puede descargar desde el proyecto de Azure Migrate. Crea una instancia de una máquina virtual de VMware con 4 núcleos, 8 GB de RAM y un disco de 80 GB. El sistema operativo de la aplicación es Windows Server 2012 R2 (64 bits).

Puede crear el recopilador siguiendo los pasos descritos aquí: [Creación de la VM de recopilador](tutorial-assessment-vmware.md#create-the-collector-vm).

## <a name="collector-communication-diagram"></a>Diagrama de comunicación de recopilador

![Diagrama de comunicación de recopilador](./media/tutorial-assessment-vmware/portdiagram.PNG)


| Componente      | Para comunicarse con   | Puerto requerido                            | Motivo                                   |
| -------------- | --------------------- | ---------------------------------------- | ---------------------------------------- |
| Recopilador      | Servicio Azure Migrate | TCP 443                                  | El recopilador debe ser capaz de comunicarse con el servicio a través del puerto SSL 443 |
| Recopilador      | vCenter Server        | 443 predeterminado                             | El recopilador debe ser capaz de comunicarse con el servidor vCenter. Se conecta a vCenter en el puerto 443, de forma predeterminada. Si vCenter escucha en otro puerto, debe estar disponible como puerto de salida en el recopilador. |
| Recopilador      | RDP|   | TCP 3389 | Para que pueda ejecutar RDP en la máquina del recopilador |





## <a name="collector-pre-requisites"></a>Requisitos previos del recopilador

El recopilador debe pasar algunas comprobaciones de requisitos previos para asegurarse de que puede conectarse con el servicio Azure Migrate y cargar los datos detectados. En este artículo se examinan los requisitos previos y se explica por qué son necesarios.

### <a name="internet-connectivity"></a>Conectividad de Internet

La aplicación del recopilador debe estar conectada a Internet para enviar la información de máquinas detectadas. Puede conectar la máquina a Internet de una de estas dos maneras.

1. Puede configurar el recopilador para tener conectividad directa a Internet.
2. Puede configurar el recopilador para que se conecte a través de un servidor proxy.
    * Si el servidor proxy requiere autenticación, puede especificar el nombre de usuario y la contraseña en la configuración de conexión.
    * La dirección IP o el FQDN del servidor proxy deben tener el formato http://IPaddress o http://FQDN. Solo se admiten servidores proxy http.

> [!NOTE]
> El recopilador no admite servidores proxy basados en https.

#### <a name="whitelisting-urls-for-internet-connection"></a>Lista de direcciones URL permitidas para conexión a Internet

La comprobación de requisitos previos es correcta si el recopilador puede conectarse a Internet a través de la configuración proporcionada. La comprobación de conectividad se valida mediante la conexión a una lista de direcciones URL tal como se indica en la tabla siguiente. Si va a usar un proxy de firewall basado en direcciones URL para controlar la conectividad saliente, asegúrese de incluir en la lista de permitidos estas direcciones URL necesarias:

**URL** | **Propósito**  
--- | ---
*.portal.azure.com | Se requiere comprobar la conectividad con el servicio de Azure y validar los problemas de sincronización horaria.

Además, la comprobación también intenta validar la conectividad con las siguientes direcciones URL pero la comprobación se supera aunque no estén accesibles. La configuración de la lista de permitidos para las siguientes direcciones URL es opcional pero debe seguir unos pasos manuales para mitigar la comprobación de requisitos previos.

**URL** | **Propósito**  | **Qué ocurre si no se crean listas de permitidos**
--- | --- | ---
*.oneget.org:443 | Se requiere para descargar el módulo PowerCLI de vCenter basado en PowerShell. | Error en la instalación de PowerCLI. Instalar manualmente el módulo.
*.windows.net:443 | Se requiere para descargar el módulo PowerCLI de vCenter basado en PowerShell. | Error en la instalación de PowerCLI. Instalar manualmente el módulo.
*.windowsazure.com:443 | Se requiere para descargar el módulo PowerCLI de vCenter basado en PowerShell. | Error en la instalación de PowerCLI. Instalar manualmente el módulo.
*.powershellgallery.com:443 | Se requiere para descargar el módulo PowerCLI de vCenter basado en PowerShell. | Error en la instalación de PowerCLI. Instalar manualmente el módulo.
*.msecnd.net:443 | Se requiere para descargar el módulo PowerCLI de vCenter basado en PowerShell. | Error en la instalación de PowerCLI. Instalar manualmente el módulo.
*.visualstudio.com:443 | Se requiere para descargar el módulo PowerCLI de vCenter basado en PowerShell. | Error en la instalación de PowerCLI. Instalar manualmente el módulo.

### <a name="time-is-in-sync-with-the-internet-server"></a>Hora sincronizada con la del servidor de Internet

El recopilador debe estar sincronizado con el servidor horario de Internet para asegurarse de que se autentiquen las solicitudes al servicio. La dirección URL portal.azure.com debe ser accesible desde el recopilador para que se pueda validar la hora. Si la máquina no está sincronizada, debe cambiar la hora del reloj de la máquina virtual del recopilador para que coincida con la hora actual, del modo siguiente:

1. Abra un símbolo del sistema de administrador en la máquina virtual.
1. Para comprobar la zona horaria, ejecute w32tm /tz.
1. Para sincronizar la hora, ejecute w32tm /resync.

### <a name="collector-service-should-be-running"></a>Servicio de recopilador en ejecución

El servicio Azure Migrate Collector debe estar ejecutándose en la máquina. Este servicio se inicia automáticamente cuando se inicia la máquina. Si el servicio no se está ejecutando, puede iniciar el servicio *Azure Migrate Collector* mediante el panel de control. El servicio de recopilador es responsable de conectarse al servidor vCenter, recopilar los metadatos y los datos de rendimiento de la máquina, y enviarlos al servicio.

### <a name="vmware-powercli-65"></a>VMware PowerCLI 6.5

El módulo de Powershell VMware PowerCLI debe estar instalado para que el recopilador pueda comunicarse con el servidor vCenter, y consultar los detalles de la máquina y sus datos de rendimiento. El módulo de Powershell se descarga y se instala automáticamente como parte de la comprobación de requisitos previos. La descarga automática requiere que algunas direcciones URL estén en la lista de permitidos; si no lo están, debe proporcionar acceso incluyéndolas en la lista de permitidos o instalando manualmente el módulo.

Instale el módulo manualmente siguiendo estos pasos:

1. Para instalar el recopilador de PowerCli sin conexión a Internet, siga los pasos indicados en [este vínculo](https://blogs.vmware.com/PowerCLI/2017/04/powercli-install-process-powershell-gallery.html).
2. Una vez instalado el módulo de PowerShell en un equipo diferente, que tenga acceso a Internet, copie los archivos VMware.* desde esa máquina a la del recopilador.
3. Reinicie las comprobaciones de requisitos previos y confirme que está instalado PowerCLI.

## <a name="connecting-to-vcenter-server"></a>Conexión a vCenter Server

El recopilador debe conectarse a vCenter Server y poder consultar sobre las máquinas virtuales, sus metadados y contadores de rendimiento. El proyecto utiliza estos datos para calcular una valoración.

1. Para conectarse a vCenter Server, se puede usar una cuenta de solo lectura con permisos tal como se indica en la tabla siguiente para ejecutar la detección.

    |Task  |Rol/cuenta necesarios  |Permisos  |
    |---------|---------|---------|
    |Detección basada en la aplicación del recopilador    | Necesita al menos un usuario de solo lectura.        |Objeto de centro de datos  –> Propagar al objeto secundario, rol = solo lectura         |

2. Solo se puede acceder a los centros de datos que sean accesibles para la cuenta de vCenter especificada para la detección.
3. Debe especificar la dirección IP/FQDN de vCenter para conectarse al servidor vCenter. De forma predeterminada, se conectará a través del puerto 443. Si ha configurado vCenter para que escuche en un número de puerto diferente, puede especificarlo como parte de la dirección del servidor con el formato IPAddress:Número_de_puerto o FQDN:Número_de_puerto.
4. La configuración de las estadísticas de vCenter Server se debe establecer en el nivel 3 antes de empezar la implementación. Si el nivel es inferior al 3, la detección se completará pero no se recopilarán los datos de rendimiento del almacenamiento y la red. Las recomendaciones de tamaño para la valoración en este caso se basarán en los datos de rendimiento de los datos de la CPU y memoria, y solo los datos de configuración de los adaptadores de red y disco. [Más información](./concepts-collector.md) sobre qué datos se recopilan y cómo influyen en la valoración.
5. El recopilador debe tener una línea de visión de la red con el servidor vCenter.

> [!NOTE]
> Solo se admiten oficialmente las versiones 5.5, 6.0 y 6.5 de vCenter Server.

> [!IMPORTANT]
> Se recomienda establecer el nivel común más alto (3) para el nivel de estadísticas, para que todos los contadores se recopilen correctamente. Si tiene vCenter establecido en un nivel inferior, solo algunos contadores pueden recopilarse por completo y los demás estarán establecidos en 0. Por lo tanto, puede que la evaluación muestre datos incompletos.

### <a name="selecting-the-scope-for-discovery"></a>Selección del ámbito de detección

Una vez conectado a vCenter, puede seleccionar un ámbito de detección. Al seleccionar un ámbito, se detectan todas las máquinas virtuales de la ruta de acceso de inventario de vCenter especificada.

1. El ámbito puede ser un centro de datos, una carpeta o un host ESXi.
2. Solo se puede seleccionar un ámbito a la vez. Para seleccionar más máquinas virtuales, puede completar una detección y reiniciar el proceso de detección con un nuevo ámbito.
3. Solo puede seleccionar un ámbito que tenga *menos de 1500 máquinas virtuales*.

## <a name="specify-migration-project"></a>Especificación del proyecto de migración

Una vez que esté conectado al servidor vCenter local y haya especificado un ámbito, ya puede especificar los detalles del proyecto de migración que se tienen que usar para la detección y la valoración. Especifique el identificador y la clave del proyecto, y conéctese.

## <a name="start-discovery-and-view-collection-progress"></a>Inicio de la detección y visualización del progreso de la recopilación

Una vez iniciada la detección, se detectan las máquinas virtuales de vCenter, y sus metadatos y datos de rendimiento se envían al servidor. El estado del progreso también le informa de los identificadores siguientes:

1. Identificador de recopilador: identificador único que se asigna a la máquina del recopilador. Este identificador no cambia para una máquina determinada en las distintas detecciones. Puede usar este identificador en caso de error al notificar el problema al Soporte técnico de Microsoft.
2. Identificador de sesión: identificador único para el trabajo de recopilación en ejecución. Puede hacer referencia al mismo identificador de sesión del portal cuando finaliza el trabajo de detección. Este identificador cambia para cada trabajo de la recopilación. En caso de error, puede mostrar este identificador al Soporte técnico de Microsoft.

### <a name="what-data-is-collected"></a>¿Qué datos se recopilan?

El trabajo de recopilación detecta los siguientes metadatos estáticos sobre las máquinas virtuales seleccionadas.

1. Nombre para mostrar de la máquina virtual (en vCenter)
2. Ruta de acceso de inventario de la máquina virtual (host o carpeta en vCenter)
3. Dirección IP
4. Dirección MAC
5. Sistema operativo
5. Número de núcleos, discos, NIC
6. Tamaño de memoria, tamaños de disco
7. Y los contadores de rendimiento de la máquina virtual, el disco y la red se muestran en la tabla siguiente.

En la tabla siguiente se enumeran los contadores de rendimiento que se recopilan y también se muestran los resultados de la valoración que se ven afectados si no se recopila un contador determinado.

|Contador                                  |Nivel    |Nivel por dispositivo  |Impacto en la evaluación                               |
|-----------------------------------------|---------|------------------|------------------------------------------------|
|cpu.usage.average                        | 1       |N/D                |Costo y tamaño de VM recomendados                    |
|mem.usage.average                        | 1       |N/D                |Costo y tamaño de VM recomendados                    |
|virtualDisk.read.average                 | 2       |2                 |Tamaño del disco, costo de almacenamiento y tamaño de VM         |
|virtualDisk.write.average                | 2       |2                 |Tamaño del disco, costo de almacenamiento y tamaño de VM         |
|virtualDisk.numberReadAveraged.average   | 1       |3                 |Tamaño del disco, costo de almacenamiento y tamaño de VM         |
|virtualDisk.numberWriteAveraged.average  | 1       |3                 |Tamaño del disco, costo de almacenamiento y tamaño de VM         |
|net.received.average                     | 2       |3                 |Tamaño de VM y costo de la red                        |
|net.transmitted.average                  | 2       |3                 |Tamaño de VM y costo de la red                        |

> [!WARNING]
> Si configuró un nivel de estadística más alto, los contadores de rendimiento pueden tardar hasta un día en generarse. Por lo tanto, se recomienda ejecutar la detección después de un día.

### <a name="time-required-to-complete-the-collection"></a>Tiempo necesario para completar la recopilación

El recopilador solo detecta los datos de la máquina y los envía al proyecto. El proyecto puede tardar más tiempo antes de que se muestren los datos detectados en el portal y pueda empezar a crear una valoración.

En función del número de máquinas virtuales en el ámbito seleccionado, se tarda hasta 15 minutos en enviar los metadatos estáticos al proyecto. Una vez que los metadatos estáticos estén disponibles en el portal, podrá ver la lista de máquinas en el portal y comenzar a crear grupos. No se puede crear una valoración hasta que finalice el trabajo de recopilación y el proyecto haya procesado los datos. Una vez completado el trabajo de recopilación en el recopilador, puede tardar hasta una hora para que los datos de rendimiento estén disponibles en el portal, en función del número de máquinas virtuales en el ámbito seleccionado.

## <a name="locking-down-the-collector-appliance"></a>Bloqueo de la aplicación del recopilador
Se recomienda ejecutar actualizaciones continuas de Windows en la aplicación del recopilador. Si no se actualiza un recopilador durante 60 días, el recopilador se iniciará apagando automáticamente la máquina. Si se está ejecutando una detección, la máquina no se desactivará, incluso si ya transcurrió su plazo de 60 días. Cuando el trabajo de detección se haya completado, se desactivará la máquina. Si usa el recopilador durante más de 45 días, se recomienda mantener la máquina actualizada en todo momento mediante la ejecución de Windows Update.

También se recomienda realizar los siguientes pasos para proteger su aplicación:
1. No pierda ni comparta las contraseñas del administrador con partes no autorizadas.
2. Apagar la aplicación cuando no se esté usando.
3. Colocar la aplicación en una red protegida.
4. Una vez completado el trabajo de migración, elimine la instancia de la aplicación. Asegúrese de eliminar también los archivos de copia de seguridad del disco (VMDK), ya que es posible que dichos discos tenga las credenciales de vCenter en caché.

## <a name="how-to-upgrade-collector"></a>Procedimiento para actualizar el recopilador

Puede actualizar el recopilador con la versión más reciente sin tener que descargar OVA una vez más.

1. Descargue el [paquete de actualización ](https://aka.ms/migrate/col/upgrade_9_13) más reciente (versión 1.0.9.13).
2. Para asegurarse de que la revisión descargada es segura, abra la ventana de comandos del administrador y ejecute el siguiente comando para generar el valor hash para el archivo ZIP. El código hash generado debe coincidir con el hash que se ha mencionado en la versión específica:

    ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```

    (ejemplo de uso C:\>CertUtil - HashFile C\AzureMigrate\CollectorUpdate_release_1.0.9.7.zip SHA256)
3. Copie el archivo zip en la máquina virtual del recopilador de Azure Migrate (aplicación del recopilador).
4. Haga clic con el botón derecho en el archivo ZIP y seleccione Extraer todo.
5. Haga clic con el botón derecho en Setup.ps1, seleccione Ejecutar con PowerShell y siga las instrucciones en pantalla para instalar la actualización.

### <a name="list-of-updates"></a>Lista de actualizaciones

#### <a name="upgrade-to-version-10913"></a>Actualización a la versión 1.0.9.13

Valores de código hash para actualizar el [paquete 1.0.9.13](https://aka.ms/migrate/col/upgrade_9_13)

**Algoritmo** | **Valor del código hash**
--- | ---
MD5 | 739f588fe7fb95ce2a9b6b4d0bf9917e
SHA1 | 9b3365acad038eb1c62ca2b2de1467cb8eed37f6
SHA256 | 7a49fb8286595f39a29085534f29a623ec2edb12a3d76f90c9654b2f69eef87e

#### <a name="upgrade-to-version-10911"></a>Actualización a la versión 1.0.9.11

Hash de valores para actualizar el [paquete 1.0.9.11](https://aka.ms/migrate/col/upgrade_9_11)

**Algoritmo** | **Valor del código hash**
--- | ---
MD5 | 0e36129ac5383b204720df7a56b95a60
SHA1 | aa422ef6aa6b6f8bc88f27727e80272241de1bdf
SHA256 | 5f76dbbe40c5ccab3502cc1c5f074e4b4bcbf356d3721fd52fb7ff583ff2b68f

#### <a name="upgrade-to-version-1097"></a>Actualización a la versión 1.0.9.7

Hash de valores para actualizar el [paquete 1.0.9.7](https://aka.ms/migrate/col/upgrade_9_7)

**Algoritmo** | **Valor del código hash**
--- | ---
MD5 | 01ccd6bc0281f63f2a672952a2a25363
SHA1 | 3e6c57523a30d5610acdaa14b833c070bffddbff
SHA256 | e3ee031fb2d47b7881cc5b13750fc7df541028e0a1cc038c796789139aa8e1e6

## <a name="next-steps"></a>Pasos siguientes

[Configuración de una evaluación para máquinas virtuales VMware locales](tutorial-assessment-vmware.md)
