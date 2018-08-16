---
title: Instalación de Mobility Service (VMware o físico a Azure) | Microsoft Docs
description: Aprenda a instalar el agente de Mobility Service para proteger las máquinas virtuales de VMware locales y los servidores físicos con Azure Site Recovery.
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: ramamill
ms.openlocfilehash: 094c1776c0760c04d85aff6ad3d812a2ad7afa56
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/06/2018
ms.locfileid: "39527004"
---
# <a name="install-the-mobility-service"></a>Instalación de Mobility Service 

Mobility Service de Azure Site Recovery se instala en las máquinas virtuales de VMware y los servidores físicos que quiera replicar en Azure. Este servicio captura escrituras de datos en un equipo y las reenvía al servidor de proceso. Implemente Mobility Service en cualquier equipo (máquina virtual VMware o servidor físico) que quiera replicar en Azure. Puede implementar Mobility Service en los servidores y máquinas virtuales de VMware que desee proteger mediante los métodos siguientes:


* [Instalación mediante herramientas de implementación herramientas de implementación de software como System Center Configuration Manager](vmware-azure-mobility-install-configuration-mgr.md)
* [Instalación con Azure Automation y la configuración de estado deseado (DSC de Automatización)](vmware-azure-mobility-deploy-automation-dsc.md)
* [Instalación manual desde la interfaz de usuario](vmware-azure-install-mobility-service.md#install-mobility-service-manually-by-using-the-gui)
* [Instalación manual desde un símbolo del sistema](vmware-azure-install-mobility-service.md#install-mobility-service-manually-at-a-command-prompt)
* [Instalación mediante la instalación de inserción de Site Recovery](vmware-azure-install-mobility-service.md#install-mobility-service-by-push-installation-from-azure-site-recovery)


>[!IMPORTANT]
> A partir de la versión 9.7.0.0, **en las máquinas virtuales Windows**, el instalador de Mobility Service también instala la versión más reciente disponible del [Agente de máquina virtual de Azure](../virtual-machines/extensions/features-windows.md#azure-vm-agent). Cuando un equipo conmuta por error a Azure, el equipo cumple el requisito previo de instalación del agente para usar cualquier extensión de máquina virtual.
> </br>En **máquinas virtuales Linux**, WALinuxAgent tiene que instalarse manualmente.

## <a name="prerequisites"></a>Requisitos previos
Complete estos pasos de requisitos previos antes de instalar manualmente Mobility Service en el servidor:
1. Inicie sesión en el servidor de configuración y, a continuación, abra una ventana del símbolo del sistema como administrador.
2. Cambie el directorio a la carpeta Bin y, a continuación, cree un archivo de frase de contraseña.

    ```
    cd %ProgramData%\ASR\home\svsystems\bin
    genpassphrase.exe -v > MobSvc.passphrase
    ```
3. Almacene el archivo de frase de contraseña en una ubicación segura. Usará el archivo durante la instalación de Mobility Service.
4. Los instaladores de Mobility Service para todos los sistemas operativos compatibles están en la carpeta %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository.

### <a name="mobility-service-installer-to-operating-system-mapping"></a>Instalador de Mobility Service para la asignación de sistemas operativos

Para ver una lista de las versiones de sistema operativo con un paquete de Mobility Service compatible, consulte la lista de [sistemas operativos admitidos para máquinas virtuales de VMware y servidores físicos](vmware-physical-azure-support-matrix.md#replicated-machines).

| Nombre de plantilla de archivo de instalador| Sistema operativo |
|---|--|
|Microsoft-ASR\_UA\*Windows\*release.exe | Windows Server 2008 R2 SP1 (64 bits) </br> Windows Server 2012 (64 bits) </br> Windows Server 2012 R2 (64 bits) </br> Windows Server 2016 (64 bits) |
|Microsoft-ASR\_UA\*RHEL6-64\*release.tar.gz | Red Hat Enterprise Linux (RHEL) 6.* (solo 64 bits) </br> CentOS 6.* (solo 64 bits) |
|Microsoft-ASR\_UA\*RHEL7-64\*release.tar.gz | Red Hat Enterprise Linux (RHEL) 7.* (solo 64 bits) </br> CentOS 7.* (solo 64 bits) |
|Microsoft-ASR\_UA\*SLES12-64\*release.tar.gz | SUSE Linux Enterprise Server 12 SP1, SP2, SP3 (solo 64 bits)|
|Microsoft-ASR\_UA\*SLES11-SP3-64\*release.tar.gz| SUSE Linux Enterprise Server 11 SP3 (solo 64 bits)|
|Microsoft-ASR\_UA\*SLES11-SP4-64\*release.tar.gz| SUSE Linux Enterprise Server 11 SP4 (solo 64-bit)|
|Microsoft-ASR\_UA\*OL6-64\*release.tar.gz | Oracle Enterprise Linux 6.4, 6.5 (solo 64 bits)|
|Microsoft-ASR\_UA\*UBUNTU-14.04-64\*release.tar.gz | Ubuntu Linux 14.04 (solo 64-bit)|
|Microsoft-ASR\_UA\*UBUNTU-16.04-64\*release.tar.gz | Ubuntu Linux 16.04 LTS Server (solo 64 bits)|
|Microsoft-ASR_UA\*DEBIAN7-64\*release.tar.gz | Debian 7 (solo 64 bits)|
|Microsoft-ASR_UA\*DEBIAN8-64\*release.tar.gz | Debian 8 (solo 64 bits)|

## <a name="install-mobility-service-manually-by-using-the-gui"></a>Instalación manual de Mobility Service mediante la GUI

>[!IMPORTANT]
> Si usa un servidor de configuración para replicar máquinas virtuales de IaaS de Azure de una suscripción o región de Azure a otra, use el método de instalación basado en línea de comandos.

[!INCLUDE [site-recovery-install-mob-svc-gui](../../includes/site-recovery-install-mob-svc-gui.md)]

## <a name="install-mobility-service-manually-at-a-command-prompt"></a>Instalación manual de Mobility Service mediante la línea de comandos

### <a name="command-line-installation-on-a-windows-computer"></a>Instalación de la línea de comandos en un equipo Windows
[!INCLUDE [site-recovery-install-mob-svc-win-cmd](../../includes/site-recovery-install-mob-svc-win-cmd.md)]

### <a name="command-line-installation-on-a-linux-computer"></a>Instalación de la línea de comandos en un equipo Linux
[!INCLUDE [site-recovery-install-mob-svc-lin-cmd](../../includes/site-recovery-install-mob-svc-lin-cmd.md)]


## <a name="install-mobility-service-by-push-installation-from-azure-site-recovery"></a>Instalación de Mobility Service mediante la instalación de inserción de Azure Site Recovery
Para realizar una instalación de inserción de Mobility Service, use Site Recovery. Todos los equipos de destino deben cumplir los siguientes requisitos previos.

[!INCLUDE [site-recovery-prepare-push-install-mob-svc-win](../../includes/site-recovery-prepare-push-install-mob-svc-win.md)]

[!INCLUDE [site-recovery-prepare-push-install-mob-svc-lin](../../includes/site-recovery-prepare-push-install-mob-svc-lin.md)]


> [!NOTE]
Después de instalar Mobility Service, en Azure Portal, seleccione **+Replicar** para empezar a proteger estas máquinas virtuales.

## <a name="update-mobility-service"></a>Actualiza Mobility Service

> [!WARNING]
> Asegúrese de que el servidor de configuración, los servidores de procesos de escalado horizontal y los servidores de destino principales que forman parte de la implementación estén actualizados antes de empezar a actualizar Mobility Service en los servidores protegidos.

1. En Azure Portal, busque la vista *nombre de su almacén* > **Elementos replicados**.
2. Si el servidor de configuración ya se ha actualizado a la versión más reciente, verá una notificación que dice "Hay una nueva actualización del agente de replicación de Site Recovery disponible. Haga clic para instalar".

     ![Ventana Elementos replicados](.\media\vmware-azure-install-mobility-service\replicated-item-notif.png)
3. Seleccione la notificación para abrir la página de selección de la máquina virtual.
4. Seleccione las máquinas virtuales en las que quiere actualizar Mobility Service y seleccione **Aceptar**.

     ![Lista de máquinas virtuales de elementos replicados](.\media\vmware-azure-install-mobility-service\update-okpng.png)

Se iniciar el trabajo de actualización de Mobility Service para cada una de las máquinas virtuales seleccionadas.

> [!NOTE]
> [Lea más](vmware-azure-manage-configuration-server.md) sobre cómo actualizar la contraseña de la cuenta usada para instalar Mobility Service.

## <a name="uninstall-mobility-service-on-a-windows-server-computer"></a>Desinstalación de Mobility Service en un equipo de Windows Server
Utilice uno de los métodos siguientes para desinstalar Mobility Service en un equipo con Windows Server.

### <a name="uninstall-by-using-the-gui"></a>Desinstalación mediante la GUI
1. En el Panel de Control, seleccione **Programas**.
2. Seleccione **Microsoft Azure Site Recovery Mobility Service/Servidor de destino maestro** y, a continuación, haga clic en**Desinstalar**.

### <a name="uninstall-at-a-command-prompt"></a>Desinstalación en un símbolo del sistema
1. Abra una ventana del símbolo del sistema como administrador.
2. Para desinstalar Mobility Service, ejecute el siguiente comando:

    ```
    MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
    ```

## <a name="uninstall-mobility-service-on-a-linux-computer"></a>Desinstalación de Mobility Service en equipos Linux
1. En el servidor Linux, inicie sesión como un usuario **raíz**.
2. En un terminal, vaya a /user/local/ASR.
3. Para desinstalar Mobility Service, ejecute el siguiente comando:

    ```
    uninstall.sh -Y
    ```
