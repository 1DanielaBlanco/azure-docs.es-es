---
title: Uso de DPM para realizar copias de seguridad de cargas de trabajo en Azure
description: Introducción a la copia de seguridad de datos de DPM en un almacén de Azure Recovery Services.
services: backup
author: adigan
manager: nkolli
keywords: System Center Data Protection Manager, Data Protection Manager, copia de seguridad de DPM
ms.service: backup
ms.topic: conceptual
ms.date: 8/22/2018
ms.author: adigan
ms.openlocfilehash: 873e7066bcf51b32c3a7a54e845ffd5a744f407f
ms.sourcegitcommit: b5ac31eeb7c4f9be584bb0f7d55c5654b74404ff
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/23/2018
ms.locfileid: "42745442"
---
# <a name="preparing-to-back-up-workloads-to-azure-with-dpm"></a>Preparación para la copia de seguridad de cargas de trabajo en Azure con DPM
> [!div class="op_single_selector"]
> * [Azure Backup Server](backup-azure-microsoft-azure-backup.md)
> * [SCDPM](backup-azure-dpm-introduction.md)
>
>

En este artículo se explica cómo realizar copias de seguridad de los datos de System Center Data Protection Manager (DPM) en Azure, donde se incluye:

* Copia de seguridad de los datos del servidor DPM en Azure
* Los requisitos previos para lograr una experiencia fluida de la copia de seguridad
* Los errores típicos y cómo resolverlos
* Escenarios admitidos

> [!NOTE]
> Azure cuenta con dos modelos de implementación para crear recursos y trabajar con ellos: [Resource Manager y el modelo clásico](../azure-resource-manager/resource-manager-deployment-model.md). En este artículo se proporcionan información y procedimientos para restaurar las máquinas virtuales implementadas mediante el modelo de Resource Manager.
>
>

Datos de aplicación y archivo de copia de seguridad de [System Center DPM](https://docs.microsoft.com/system-center/dpm/dpm-overview). Se puede encontrar más información sobre las cargas de trabajo admitidas [aquí](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix). Los datos con copia de seguridad en DPM se pueden almacenar en una cinta o en un disco, o se puede crear una copia de seguridad de ellos en Azure con Microsoft Azure Backup. DPM interactúa con Azure Backup de la manera siguiente:

* **DPM implementado como un servidor físico o una máquina virtual local**: DPM implementado como un servidor físico o una máquina virtual de Hyper-V local, además de en disco y cinta, crea una copia de seguridad de los datos en un almacén de Recovery Services.
* **DPM implementado como una máquina virtual de Azure**: a partir de System Center 2012 R2 con Update 3, puede implementar DPM en una máquina virtual de Azure. Si DPM se implementa como una máquina virtual de Azure, puede realizar una copia de seguridad de los datos en discos de Azure asociados a la máquina virtual, o descargar el almacenamiento de datos mediante una copia de seguridad en un almacén de Recovery Services.

## <a name="why-back-up-dpm-to-azure"></a>¿Por qué realizar copias de seguridad de DPM en Azure?
Las ventajas empresariales de realizar una copia de seguridad de servidores DPM en Azure son las siguientes:

* En implementaciones de DPM locales, use Azure como alternativa a la implementación a largo plazo en una cinta.
* En implementaciones de DPM en una máquina virtual en Azure, descargue el almacenamiento del disco de Azure. Almacenar los datos más antiguos en el almacén de Recovery Services le permite escalar verticalmente su negocio gracias al almacenamiento de nuevos datos en el disco.

## <a name="prerequisites"></a>Requisitos previos
Prepare Azure Backup para crear copias de seguridad de los datos de DPM de la manera siguiente:

1. **Crear un almacén de Recovery Services**: cree un almacén en Azure Portal.
2. **Descargar credenciales de almacén**: descargue las credenciales que usa para registrar el servidor DPM en el almacén de Recovery Services.
3. **Instalar el agente de Azure Backup**: instale el agente en cada servidor DPM.
4. **Registrar el servidor**: registre el servidor DPM en el almacén de Recovery Services.

[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="key-definitions"></a>Definiciones clave
Estas son algunas definiciones clave para copia de seguridad de Azure para DPM:

1. **Credenciales de almacén**: las credenciales de almacén son necesarias para autenticar la máquina para enviar datos de copia de seguridad a un almacén identificado en el servicio Azure Backup. Se pueden descargar desde el almacén y son válidas durante 48 horas.
2. **Frase de contraseña**: la frase de contraseña se utiliza para cifrar las copias de seguridad en la nube. Guarde el archivo en una ubicación segura, ya que puede necesitarlo durante una operación de recuperación.
3. **PIN de seguridad**: si ha habilitado la [configuración de seguridad](https://docs.microsoft.com/azure/backup/backup-azure-security-feature) del almacén, el PIN de seguridad es necesario para realizar operaciones de copia de seguridad críticas. Esta Multi-Factor Authentication agrega otra capa de seguridad. 
4. **Carpeta de recuperación**: es la frase en la que se descargan temporalmente las copias de seguridad de la nube durante las recuperaciones de la nube. Su tamaño debe ser aproximadamente igual al tamaño de los elementos de copia de seguridad que desea recuperar en paralelo.

[!INCLUDE [backup-create-rs-vault.md](../../includes/backup-create-rs-vault.md)]

## <a name="edit-storage-replication"></a>Edición de la replicación de almacenamiento

La replicación de almacenamiento permite elegir entre almacenamiento con redundancia geográfica y almacenamiento con redundancia local. De forma predeterminada, el almacén tiene almacenamiento con redundancia geográfica. Si el almacén es su copia de seguridad principal, deje la opción establecida como almacenamiento con redundancia geográfica. Si desea una opción más económica que no sea tan duradera, use el siguiente procedimiento para configurar el almacenamiento con redundancia local. Para más información sobre las opciones de almacenamiento [con redundancia geográfica](../storage/common/storage-redundancy-grs.md) y [con redundancia local](../storage/common/storage-redundancy-lrs.md), consulte [Replicación de Azure Storage](../storage/common/storage-redundancy.md).

Para editar la configuración de replicación de almacenamiento:

> [!NOTE] 
> Configure la replicación de almacenamiento antes de desencadenar la copia de seguridad inicial. Si decide cambiar la configuración de replicación de almacenamiento después de la copia de seguridad del elemento protegido, debe detener la protección del almacén antes de cambiar la configuración de almacenamiento.
>  

1. Seleccione el almacén y abra su panel.

2. En la sección **Administrar**, haga clic en **Infraestructura de copia de seguridad**.

3. En el menú **Configuración de copia de seguridad**, elija la opción de replicación del almacenamiento para su almacén.

    ![Lista de copias de seguridad](./media/backup-azure-dpm-introduction/choose-storage-configuration-rs-vault.png)

    Tras elegir la opción de almacenamiento del almacén, está listo para asociar la máquina virtual con el almacén. Para comenzar la asociación, es preciso detectar y registrar las máquinas virtuales de Azure.

## <a name="download-vault-credentials"></a>Descarga de las credenciales de almacén
El archivo de credenciales de almacén es un certificado generado por el portal para cada almacén de copia de seguridad. Luego el portal carga la clave pública en Access Control Service (ACS). Durante el flujo de trabajo del registro de la máquina, la clave privada del certificado se pone a disposición del usuario, que autentica la máquina. En función de la autenticación, el servicio Azure Backup envía los datos al almacén identificado.

El archivo de credenciales de almacén se usa solo durante el flujo de trabajo de registro. Es responsabilidad del usuario asegurarse de que el archivo de credenciales de almacén no se ve comprometido. Si se pierde el control de las credenciales, se pueden usar las credenciales de almacén para registrar otras máquinas en el almacén. Sin embargo, los datos de copia de seguridad se cifran mediante una frase de contraseña que pertenece al cliente, por lo que los datos de copia de seguridad existentes no pueden estar en peligro. Para resolver este problema, las credenciales de almacén expiran después de 48 horas. Descargue las credenciales de almacén nuevas tantas veces como sea necesario. Sin embargo, se puede usar solo el último archivo de credenciales de almacén durante el flujo de trabajo de registro.

El archivo de credenciales de almacén se descarga a través de un canal seguro desde el Portal de Azure. El servicio Azure Backup no conoce la clave privada del certificado, y la clave privada no está disponible en el portal ni en el servicio. Siga estos pasos para descargar el archivo de credenciales de almacén a una máquina local.

1. Inicie sesión en el [Azure Portal](https://portal.azure.com/).

2. Abra el almacén de Recovery Services en el que quiere registrar un servidor DPM.

3. El menú de configuración se abre de forma predeterminada. Si está cerrado, haga clic en **Configuración** en el panel del almacén para abrir el menú. En el menú **Configuración**, haga clic en **Propiedades**.

    ![Abrir el menú Almacén](./media/backup-azure-dpm-introduction/vault-settings-dpm.png)

4. En la página Propiedades, en **Credenciales de copia de seguridad**, haga clic en **Descargar**. El portal genera el archivo de credenciales de almacén, que puede descargarse.

    ![Descargar](./media/backup-azure-dpm-introduction/vault-credentials.png)

El portal genera una credencial de almacén mediante una combinación del nombre del almacén y la fecha actual. Haga clic en **Guardar** para descargar las credenciales de almacén en la carpeta de descargas de la cuenta local, o seleccione Guardar como desde el menú Guardar para especificar una ubicación para las credenciales de almacén. El archivo tarda un minuto en generarse.

### <a name="note"></a>Nota:
* Asegúrese de que el archivo de credenciales de almacén se guarde en una ubicación a la que se pueda acceder desde la máquina. Si se almacenan en un recurso compartido/SMB de archivo, compruebe los permisos de acceso.
* El archivo de credenciales de almacén se utiliza solo durante el flujo de trabajo de registro.
* El archivo de credenciales de almacén caduca después de 48 horas y puede descargarse desde el portal.

## <a name="install-backup-agent"></a>Instalación del agente de copia de seguridad
Después de crear el almacén de Azure Backup, se debe instalar un agente en cada una de las máquinas de Windows (servidor de Windows Server, cliente de Windows, servidor de System Center Data Protection Manager o la máquina de Azure Backup Server) que habilite la copia de seguridad de los datos y las aplicaciones en Azure.

1. Abra el almacén de Recovery Services en el que quiere registrar la máquina DPM.
2. El menú de configuración se abre de forma predeterminada. Si está cerrado, haga clic en **Configuración** para abrirlo. En este menú, haga clic en **Propiedades**.

    ![Abrir el menú Almacén](./media/backup-azure-dpm-introduction/vault-settings-dpm.png)
3. En la página Configuración, en **Agente de Azure Backup**, haga clic en **Descargar**.

    ![Descargar](./media/backup-azure-dpm-introduction/azure-backup-agent.png)

   Después de instalar el agente, ejecute MARSAgentInstaller.exe para iniciar la instalación del agente de Azure Backup. Elija la carpeta de instalación y la carpeta temporal requerido para el agente. La ubicación de caché especificada debe tener un espacio libre de al menos el 5% de los datos de copia de seguridad.

4. Si usa un servidor proxy para conectarse a Internet, en la pantalla **Configuración de proxy** , especifique los detalles del servidor proxy. Si utiliza a un servidor proxy autenticado, escriba los detalles de nombre y la contraseña del usuario en esta pantalla.

5. El agente de Azure Backup instala .NET Framework 4.5 y Windows PowerShell (si aún no está disponible) para completar la instalación.

6. Una vez instalado el agente, **cierre** la ventana.

   ![cierre](../../includes/media/backup-install-agent/dpm_FinishInstallation.png)

7. Para **registrar el servidor DPM** en el almacén, en la pestaña **Administración**, haga clic en **En línea**. Después, seleccione **Registrar**. Se abrirá al Asistente para registrar el programa de instalación.

8. Si usa un servidor proxy para conectarse a Internet, en la pantalla **Configuración de proxy** , especifique los detalles del servidor proxy. Si utiliza a un servidor proxy autenticado, escriba los detalles de nombre y la contraseña del usuario en esta pantalla.

    ![Configuración de proxy](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Proxy.png)
9. En la pantalla de credenciales del almacén, busque y seleccione el archivo de credenciales del almacén que se descargó anteriormente.

    ![Credenciales de almacén](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Credentials.jpg)

    El archivo de almacén de credenciales solo es válido durante 48 horas (después de descargarlo desde el portal). Si encuentra algún error en esta pantalla (por ejemplo "El archivo de credenciales de almacén especificado expiró"), inicie sesión en el Portal de Azure y vuelva a descargar el archivo de credenciales de almacén.

    Asegúrese de que el archivo de almacén de credenciales está disponible en una ubicación a la que puede tener acceso la aplicación de instalación. Si encuentra errores relacionados con el acceso, copie el archivo de almacén de credenciales en una ubicación temporal en esta máquina y vuelva a intentar la operación.

    Si se produce un error de credenciales de almacén no válidas (por ejemplo, "Las credenciales del almacén no son válidas"), el archivo está dañado o no tiene asociadas las credenciales más recientes con el servicio de recuperación. Vuelva a intentar la operación después de descargar un nuevo archivo de credenciales de almacén desde el portal. Este error suele aparecer si el usuario hace clic en la opción **Descargar credenciales de almacén** en el Portal de Azure, en sucesión rápida. En este caso, solo es válido el segundo archivo de credenciales de almacén.

10. Para controlar el uso de ancho de banda de red durante el trabajo y las horas no laborables, en la pantalla **Configuración de límite** , puede establecer los límites de uso de ancho de banda y definir las horas de trabajo y no laborables.

    ![Configuración de límite](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Throttling.png)

11. En la pantalla **Recovery Folder Setting** (Configuración de la carpeta de recuperación), examine la carpeta donde los archivos descargados de Azure se almacenarán temporalmente.

    ![Recovery Folder Setting](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_RecoveryFolder.png)

12. En la pantalla **Configuración de cifrado** , puede generar una frase de contraseña o proporcionarla (mínimo de 16 caracteres). Recuerde guardar la frase de contraseña en una ubicación segura.

    ![Cifrado](../../includes/media/backup-install-agent/DPM_SetupOnlineBackup_Encryption.png)

    > [!WARNING]
    > Si la frase de contraseña se pierde u olvida; Microsoft no puede ayudar a recuperar los datos de copia de seguridad. El usuario final posee la frase de contraseña de cifrado y Microsoft no puede ver la frase de contraseña que usa el usuario final. Guarde el archivo en una ubicación segura, ya que puede ser necesario durante una operación de recuperación.
    >
    >

13. Al hacer clic en el botón **Registrar** , la máquina se ha registrado correctamente en el almacén y ahora está lista para iniciar la copia de seguridad en Microsoft Azure.

14. Cuando use Data Protection Manager, puede modificar la configuración especificada durante el flujo de trabajo de registro haciendo clic en la opción **Configurar** y seleccionando **En línea** en la pestaña **Administración**.

## <a name="requirements-and-limitations"></a>Requisitos y limitaciones
* DPM se puede ejecutar como un servidor físico o como una máquina virtual de Hyper-V, instalado en System Center 2012 SP1 o System Center 2012 R2. También se puede ejecutar como una máquina virtual de Azure que ejecuta System Center 2012 R2 con al menos el paquete acumulativo de actualizaciones 3 para DPM 2012 R2, o como una máquina virtual de Windows en VMWare que se ejecuta en System Center 2012 R2 con al menos el paquete acumulativo de actualizaciones 5.
* Si ejecuta DPM con System Center 2012 SP1, debe instalar el paquete acumulativo de actualizaciones 2 para System Center Data Protection Manager SP1. Esto es necesario para poder instalar Azure Backup Agent.
* El servidor DPM debe tener instalado Windows PowerShell y .Net Framework 4.5.
* DPM puede realizar una copia de seguridad de la mayoría de las cargas de trabajo en Azure Backup. Para obtener una lista completa de compatibilidad, vea a continuación los elementos admitidos en Azure Backup.
* Los datos almacenados en Azure Backup no se pueden recuperar con la opción "copiar en cinta".
* Necesitará una cuenta de Azure con la característica Azure Backup habilitada. En caso de no tener cuenta, puede crear una de evaluación gratuita en tan solo unos minutos. Lea acerca de los [Precios de Backup](https://azure.microsoft.com/pricing/details/backup/).
* El uso de Copia de seguridad de Azure requiere la instalación de Azure Backup Agent en los servidores de los que desee realizar una copia de seguridad. Cada servidor debe tener disponible como espacio de almacenamiento local al menos un 5 % del tamaño de los datos de los que se va a realizar la copia de seguridad. Por ejemplo, realizar una copia de seguridad de 100 GB de datos requiere un mínimo de 5 GB de espacio libre en la ubicación temporal.
* Los datos se almacenarán en el almacenamiento de almacén de Azure. No hay ningún límite en la cantidad de datos de los que puede realizar una copia de seguridad en el almacén de Azure Backup; sin embargo, el tamaño de un origen de datos (por ejemplo, una máquina virtual o una base de datos) no debe superar los 54.400 GB.

Los siguientes tipos de archivo se admiten para la copia de seguridad en Azure:

* Cifrados (solo copias de seguridad completas)
* Comprimidos (copias de seguridad incrementales compatibles)
* Dispersos (copias de seguridad incrementales compatibles)
* Comprimidos y dispersos (tratados como dispersos)

No se admiten los siguientes:

* Servidores de sistemas de archivos que distinguen entre mayúsculas y minúsculas.
* Vínculos físicos (omitidos)
* Puntos de reanálisis (omitidos)
* Cifrados y comprimidos (omitidos)
* Cifrados y dispersos (omitidos)
* Flujo comprimido
* Flujo disperso

> [!NOTE]
> A partir de System Center 2012 DPM con SP1, puede realizar una copia de seguridad de las cargas de trabajo protegidas en Azure.
>
>
