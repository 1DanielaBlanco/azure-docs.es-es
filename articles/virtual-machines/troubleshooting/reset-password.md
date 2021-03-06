---
title: Restablecimiento de la contraseña local de Linux en máquinas virtuales de Azure | Microsoft Docs
description: Introducción de los pasos para restablecer la contraseña local de Linux en la máquina virtual de Azure
services: virtual-machines-linux
documentationcenter: ''
author: Deland-Han
manager: cshepard
editor: ''
tags: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: delhan
ms.openlocfilehash: d96d75f4f2623476f7af4e6eea930c1f2c503e3a
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/07/2018
ms.locfileid: "51226920"
---
# <a name="how-to-reset-local-linux-password-on-azure-vms"></a>Restablecimiento de las contraseñas locales de Linux en máquinas virtuales de Azure

En este artículo se presentan varios métodos para restablecer las contraseñas locales de Linux en una máquina virtual. Si la cuenta de usuario ha expirado o simplemente desea crear una cuenta, puede usar los métodos siguientes para crear una cuenta de administrador local y recuperar el acceso a la máquina virtual.

## <a name="symptoms"></a>Síntomas

No puede iniciar sesión en la máquina virtual y recibe un mensaje que indica que la contraseña que usó es incorrecta. Además, no puede usar VMAgent para restablecer la contraseña en Azure Portal.

## <a name="manual-password-reset-procedure"></a>Procedimiento de restablecimiento de contraseñas manual

1.  Elimine la máquina virtual y mantenga los discos conectados.

2.  Asocie la unidad de sistema operativo como un disco de datos a otra máquina virtual temporal en la misma ubicación.

3.  Ejecute el siguiente comando SSH en la máquina virtual temporal para convertirse en superusuario.

    ```bash
    sudo su
    ```

4.  Ejecute **fdisk -l** o examine los registros del sistema para encontrar el disco recién conectado. Busque el nombre de unidad que va a montar. A continuación, en la máquina virtual temporal, busque en el archivo de registro correspondiente.

    ```bash
    grep SCSI /var/log/kern.log (ubuntu)
    grep SCSI /var/log/messages (centos, suse, oracle)
    ```

    Ejemplo de salida del comando grep:

    ```bash
    kernel: [ 9707.100572] sd 3:0:0:0: [sdc] Attached SCSI disk
    ```

5.  Cree un punto de montaje denominado **tempmount**.

    ```bash
    mkdir /tempmount
    ```

6.  Monte el disco del sistema operativo en el punto de montaje. Generalmente, es necesario montar *sdc1* o *sdc2*. En función de la partición de hospedaje en el directorio */etc* del disco de la máquina dañada.

    ```bash
    mount /dev/sdc1 /tempmount
    ```

7.  Cree copias de los principales archivos de credenciales antes de realizar cambios:

    ```bash
    cp /etc/passwd /etc/passwd_orig    
    cp /etc/shadow /etc/shadow_orig    
    cp /tempmount/etc/passwd /etc/passwd
    cp /tempmount/etc/shadow /etc/shadow 
    cp /tempmount/etc/passwd /tempmount/etc/passwd_orig
    cp /tempmount/etc/shadow /tempmount/etc/shadow_orig
    ```

8.  Restablezca la contraseña del usuario que necesite:

    ```bash
    passwd <<USER>> 
    ```

9.  Mueva los archivos modificados a la ubicación correcta del disco dañado de la máquina.

    ```bash
    cp /etc/passwd /tempmount/etc/passwd
    cp /etc/shadow /tempmount/etc/shadow
    cp /etc/passwd_orig /etc/passwd
    cp /etc/shadow_orig /etc/shadow
    ```

10. Vuelva a la raíz y desmonte el disco.

    ```bash
    cd /
    umount /tempmount
    ```

11. Desconecte el disco del portal de administración.

12. Vuelva a crear la máquina virtual.

## <a name="next-steps"></a>Pasos siguientes

* [Solución de problemas de máquinas virtuales de Azure mediante la conexión de un disco de SO a otra máquina virtual de Azure](https://social.technet.microsoft.com/wiki/contents/articles/18710.troubleshoot-azure-vm-by-attaching-os-disk-to-another-azure-vm.aspx)

* [CLI de Azure: cómo eliminar y volver a implementar una máquina virtual desde VHD](https://blogs.msdn.microsoft.com/linuxonazure/2016/07/21/azure-cli-how-to-delete-and-re-deploy-a-vm-from-vhd/)
