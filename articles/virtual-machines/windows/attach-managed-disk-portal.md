---
title: 'Conexión de un disco de datos administrado a una VM con Windows: Azure | Microsoft Docs'
description: Se muestra cómo conectar un nuevo disco de datos administrado a una máquina virtual con Windows en Azure Portal con el modelo de implementación de Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 12/13/2017
ms.author: cynthn
ms.openlocfilehash: 14721b2f2bc7913c2b7eadfc5ee801a223201ea9
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/06/2018
---
# <a name="how-to-attach-a-managed-data-disk-to-a-windows-vm-in-the-azure-portal"></a>Cómo conectar un disco de datos administrado a una VM con Windows en Azure Portal

En este artículo se muestra cómo conectar un nuevo disco de datos administrado a una máquina virtual con Windows en Azure Portal. Antes de hacerlo, revise estas sugerencias:

* El tamaño de la máquina virtual controla cuántos discos de datos puede conectar. Para obtener más información, consulte [Tamaños de máquinas virtuales](sizes.md).
* Para un disco nuevo, no es necesario crearlo en primer lugar porque Azure lo crea cuando lo conecta.

También puede [conectar un disco de datos mediante Powershell](attach-disk-ps.md).



## <a name="add-a-data-disk"></a>Agregar un disco de datos
1. En el menú izquierdo, haga clic en **Virtual Machines**.
2. Seleccione la máquina virtual en la lista.
3. En la página de la máquina virtual, haga clic en **Discos**.
4. En la página **Discos**, haga clic en **+ Agregar disco de datos**.
5. En la lista desplegable para el nuevo disco, seleccione **Crear disco**.
6. En la página **Crear disco administrado**, escriba un nombre para el disco y ajuste las demás opciones de configuración según sea necesario. Cuando haya terminado, haga clic en **Crear**.
7. En la página **Discos**, haga clic en **Guardar** para guardar la configuración del nuevo disco de la máquina virtual.
6. Una vez que Azure crea el disco y lo asocia a la máquina virtual, el nuevo disco aparece en la configuración de disco de la máquina virtual en el apartado **Discos de datos**.


## <a name="initialize-a-new-data-disk"></a>Inicio de un nuevo disco de datos

1. Conectar a la máquina virtual.
1. Haga clic en el menú Inicio dentro de la máquina virtual, escriba **diskmgmt.msc** y presione **Entrar**. Se abrirá el complemento Administración de discos.
2. Administración de discos reconocerá que hay un nuevo disco sin inicializar y se abrirá la ventana **Inicializar disco**.
3. Asegúrese de que el nuevo disco está seleccionado y haga clic en **Aceptar** para inicializarlo.
4. El nuevo disco aparecerá como **sin asignar**. Haga clic con el botón derecho en cualquier lugar del disco y seleccione **Nuevo volumen simple**. Se abrirá el **Asistente para nuevo volumen simple**.
5. Navegue por el asistente, conserve todos los valores predeterminados y, cuando haya terminado, seleccione **Finalizar**.
6. Cierre Administración de discos.
7. Se abrirá una ventana emergente necesaria para formatear el nuevo disco para poder usarlo. Haga clic en **Dar formato al disco**.
8. En el cuadro de diálogo **Formatear disco nuevo**, active las opciones y haga clic en **Iniciar**.
9. Recibirá una advertencia sobre que al formatear los discos se borrarán todos los datos, haga clic en **Aceptar**.
10. Una vez completado el formato, haga clic en **Aceptar**.

## <a name="use-trim-with-standard-storage"></a>Uso de TRIM con el almacenamiento estándar

Si utiliza almacenamiento estándar (HDD), debe habilitar TRIM. TRIM descarta los bloques sin utilizar del disco, por lo que solo se le facturará el almacenamiento que utiliza realmente. Esto puede suponerle un ahorro si crea archivos grandes y, luego, los elimina. 

Puede ejecutar este comando para comprobar la configuración de TRIM. Abra un símbolo del sistema en su máquina virtual de Windows y escriba:

```
fsutil behavior query DisableDeleteNotify
```

Si el comando devuelve 0, significa que TRIM está habilitada correctamente. Si devuelve 1, ejecute el siguiente comando para habilitar TRIM:
```
fsutil behavior set DisableDeleteNotify 0
```

Después de eliminar los datos del disco, puede garantizar que las operaciones de TRIM se vacían correctamente mediante la ejecución de desfragmentación con TRIM:

```
defrag.exe <volume:> -l
```

También puede formatear el volumen para asegurarse de que quede recortado por completo.

## <a name="next-steps"></a>Pasos siguientes
Si la aplicación debe usar la unidad D: para almacenar datos, puede [cambiar la letra de la unidad del disco temporal de Windows](change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
