---
title: Notas de la versión de Explorador de Microsoft Azure Storage
description: Notas de la versión de Explorador de Microsoft Azure Storage
services: storage
documentationcenter: na
author: cawa
manager: paulyuk
editor: ''
ms.assetid: ''
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/12/2018
ms.author: cawa
ms.openlocfilehash: 94ade24f1761700b93ab79d497e273c64c51bddf
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/12/2018
ms.locfileid: "38990904"
---
# <a name="microsoft-azure-storage-explorer-release-notes"></a>Notas de la versión de Explorador de Microsoft Azure Storage

Este artículo contiene las notas de la versión del Explorador de Azure Storage 1.2.0, así como las de versiones anteriores.

[Explorador de Microsoft Azure Storage](./vs-azure-tools-storage-manage-with-storage-explorer.md) es una aplicación independiente que permite trabajar fácilmente con los datos de Azure Storage en Windows, macOS y Linux.

## <a name="version-130"></a>Versión 1.3.0
09/07/2018

### <a name="download-azure-storage-explorer-130"></a>Descargar Explorador de Azure Storage 1.3.0
- [Explorador de Azure Storage 1.3.0 para Windows](https://go.microsoft.com/fwlink/?LinkId=708343)
- [Explorador de Azure Storage 1.3.0 para Mac](https://go.microsoft.com/fwlink/?LinkId=708342)
- [Explorador de Azure Storage 1.3.0 para Linux](https://go.microsoft.com/fwlink/?LinkId=722418)

### <a name="new"></a>Nuevo
* Ahora se admite el acceso a los contenedores de $web que usan los sitios web estáticos. Esto le permite cargar y administrar fácilmente los archivos y las carpetas que usa el sitio web. [#223](https://github.com/Microsoft/AzureStorageExplorer/issues/223)
* Se ha reorganizado la barra de aplicaciones en macOS. Los cambios incluyen un menú Archivo, algunos cambios de las teclas de método abreviado y varios comandos nuevos en el menú de la aplicación. [#99](https://github.com/Microsoft/AzureStorageExplorer/issues/99)
* El punto de conexión de la autoridad para iniciar sesión en Azure US Government ha cambiado a https://login.microsoftonline.us/
* Accesibilidad: cuando un lector de pantalla está activo, la navegación mediante teclado ahora funciona con las tablas que se usan para mostrar los elementos en el lado derecho. Puede usar las teclas de dirección para navegar por las filas y columnas, ENTRAR para invocar acciones predeterminadas, la tecla de menú contextual para abrir el menú contextual de un elemento y Mayús o Control para realizar una selección múltiple. [#103](https://github.com/Microsoft/AzureStorageExplorer/issues/103)

### <a name="fixes"></a>Correcciones
*  En algunos equipos, los procesos secundarios tardaban mucho tiempo en iniciarse. Cuando esto sucedía, aparecería un error "el proceso secundario no se pudo iniciar de manera oportuna". El tiempo asignado para que se inicie un proceso secundario ahora se ha aumentado de 20 a 90 segundos. Si todavía se ve afectado por este problema, comente en el problema de GitHub vinculado. [#281](https://github.com/Microsoft/AzureStorageExplorer/issues/281)
* Al usar una SAS que no tenía permisos de lectura, no era posible cargar un blob grande. La lógica para la carga se ha modificado para trabajar en este escenario. [#305](https://github.com/Microsoft/AzureStorageExplorer/issues/305)
* Establecer el nivel de acceso público para un contenedor quitaba todas las directivas de acceso y viceversa. Ahora, las directivas de acceso y el nivel de acceso público se conservan al establecer cualquiera de los dos. [#197](https://github.com/Microsoft/AzureStorageExplorer/issues/197)
* "AccessTierChangeTime" se ha truncado en el cuadro de diálogo Propiedades. Esto se ha solucionado. [#145](https://github.com/Microsoft/AzureStorageExplorer/issues/145)
* El prefijo "Explorador de Microsoft Azure Storage - " no estaba presente en el cuadro de diálogo Crear nuevo directorio. Esto se ha solucionado. [#299](https://github.com/Microsoft/AzureStorageExplorer/issues/299)
* Accesibilidad: era difícil desplazarse por el cuadro de diálogo Agregar cuando se usaba VoiceOver. Se han realizado mejoras. [#206](https://github.com/Microsoft/AzureStorageExplorer/issues/206)
* Accesibilidad: el color de fondo del botón de expandir y contraer para el panel Acciones y propiedades era incoherente con controles de interfaz de usuario similares en el tema negro en alto contraste. Se ha cambiado el color. [#123](https://github.com/Microsoft/AzureStorageExplorer/issues/123)
* Accesibilidad: en el tema negro en alto contraste, el estilo de foco para el botón "X" en el cuadro de diálogo Propiedades no estaba visible. Esto se ha solucionado. [#243](https://github.com/Microsoft/AzureStorageExplorer/issues/243)
* Accesibilidad: las fichas Propiedades y Acciones no tenían varios valores de aria que dieron lugar a una mala experiencia al usar lectores de pantalla. Ahora se han agregado los valores de aria que faltaban. [#316](https://github.com/Microsoft/AzureStorageExplorer/issues/316)
* Accesibilidad: no se les dio un valor de aria expandido false a los nodos de árbol contraídos en el lado izquierdo. Esto se ha solucionado. [#352](https://github.com/Microsoft/AzureStorageExplorer/issues/352)

### <a name="known-issues"></a>Problemas conocidos
* Al usar emuladores, como el emulador de Azure Storage o Azurite, necesitará hacer que escuchen conexiones en sus puertos predeterminados. De otro modo, el Explorador de Storage no podrá conectarlos.
* Si usa VS para Mac y nunca ha creado una configuración de AAD personalizada, es posible que no pueda iniciar sesión. Para solucionar el problema, elimine el contenido de ~/.IdentityService/AadConfigurations. Si al hacerlo no se desbloquea, incluya un comentario sobre [este problema](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* Azurite todavía no ha implementado por completo todas las API de Azure Storage. Por este motivo, pueden haber errores o comportamientos inesperados cuando se usa Azurite para el almacenamiento de desarrollo.
* En raras ocasiones, el foco de árbol puede quedarse bloqueado en un acceso rápido. Para desbloquear el foco, puede seleccionar Actualizar todo.
* Cargar desde la carpeta de OneDrive no funciona debido a un error en NodeJS. El error se ha corregido, pero aún no se ha integrado en Electron.
* Cuando el destino es Azure Stack, es posible que la carga de determinados archivos como blobs en anexos pueda producir errores.
* Después de hacer clic en “Cancelar” en una tarea, puede que esta tarde un tiempo en cancelarse. Esto es porque se usa la solución de filtro de cancelación que se describe [aquí](https://github.com/Azure/azure-storage-node/issues/317).
* Si no elige el certificado de tarjeta inteligente o PIN adecuados, tendrá que reiniciar para que el Explorador de Storage olvide esa decisión.
* Al cambiar de nombre los blobs (individualmente o dentro de un contenedor de blobs cuyo nombre ha cambiado), no se conservan las instantáneas. Todas las otras propiedades y metadatos de blobs, archivos y entidades se conservan al cambiar de nombre.
* Aunque Azure Stack actualmente no admite recursos compartidos de archivos, todavía aparece un nodo de recurso compartido de archivos en la cuenta de almacenamiento de Azure Stack conectada.
* El shell de Electron que usa el Explorador de Storage tiene problemas con la aceleración de hardware de GPU (unidad de procesamiento gráfico). Si el Explorador de Storage muestra una ventana principal en blanco (vacía), puede intentar iniciar el Explorador de Storage desde la línea de comandos y deshabilitar la aceleración de GPU al agregar el conmutador `--disable-gpu`:

```
./StorageExplorer.exe --disable-gpu
```

* Para los usuarios de Linux, debe instalar [.NET Core 2.0](https://docs.microsoft.com/en-us/dotnet/core/linux-prerequisites?tabs=netcore2x).
* Si es usuario de Ubuntu 14.04, deberá asegurarse de que GCC está actualizado. Para ello, se pueden ejecutar los siguientes comandos y, luego, es necesario reiniciar la máquina:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Los usuarios de Ubuntu 17.04 tendrán que instalar GConf. Esto se puede hacer mediante la ejecución de los siguientes comandos. Después de esto, es necesario reiniciar la máquina.

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="previous-releases"></a>Versiones anteriores

* [Versión 1.2.0](#version-120)
* [Versión 1.1.0](#version-110)
* [Versión 1.0.0](#version-100)
* [Versión 0.9.6](#version-096)
* [Versión 0.9.5](#version-095)
* [Versión 0.9.4 y 0.9.3](#version-094-and-093)
* [Versión 0.9.2](#version-092)
* [Versión 0.9.1 y 0.9.0](#version-091-and-090)
* [Versión 0.8.16](#version-0816)
* [Versión 0.8.14](#version-0814)
* [Versión 0.8.13](#version-0813)
* [Versión 0.8.12, 0.8.11 y 0.8.10](#version-0812-and-0811-and-0810)
* [Versión 0.8.9 y 0.8.8](#version-089-and-088)
* [Versión 0.8.7](#version-087)
* [Versión 0.8.6](#version-086)
* [Versión 0.8.5](#version-085)
* [Versión 0.8.4](#version-084)
* [Versión 0.8.3](#version-083)
* [Versión 0.8.2](#version-082)
* [Versión 0.8.0](#version-080)
* [Versión 0.7.20160509.0](#version-07201605090)
* [Versión 0.7.20160325.0](#version-07201603250)
* [Versión 0.7.20160129.1](#version-07201601291)
* [Versión 0.7.20160105.0](#version-07201601050)
* [Versión 0.7.20151116.0](#version-07201511160)

## <a name="version-120"></a>Versión 1.2.0
12/06/2018

### <a name="new"></a>Nuevo
* Si el Explorador de Azure Storage no puede cargar suscripciones de solo un subconjunto de los inquilinos, se mostrará cualquier suscripción cargada correctamente junto con un mensaje de error específicamente para los inquilinos que dieron error. [#159](https://github.com/Microsoft/AzureStorageExplorer/issues/159)
* En Windows, cuando hay una actualización disponible, se puede elegir la opción "Update on Close" ("Actualizar al cerrar"). Cuando se selecciona esta opción, se ejecuta el instalador de la actualización después de cerrar el Explorador de Storage. [#21](https://github.com/Microsoft/AzureStorageExplorer/issues/21)
* Se ha agregado la instantánea de restauración al menú emergente del editor del recurso compartido de archivos al ver una instantánea del recurso compartido de archivos.[#131](https://github.com/Microsoft/AzureStorageExplorer/issues/131)
* Ahora, el botón Borrar cola está siempre habilitado.[#135](https://github.com/Microsoft/AzureStorageExplorer/issues/135)
* Se ha vuelto a habilitar la compatibilidad para iniciar sesión en Azure Stack de ADFS. Se requiere la versión 1804 o superior de Azure Stack. [#150](https://github.com/Microsoft/AzureStorageExplorer/issues/150)

### <a name="fixes"></a>Correcciones
* Si ha visto instantáneas para un recurso compartido de archivos cuyo nombre era un prefijo de otro recurso compartido de archivos en la misma cuenta de almacenamiento, entonces las instantáneas también se mostrarán para el otro recurso compartido de archivos. Ahora se ha corregido. [#255](https://github.com/Microsoft/AzureStorageExplorer/issues/255)
* Cuando se adjunta por medio de SAS, la restauración de un archivo a partir de una instantánea de recurso compartido de archivos provocará un error. Ahora se ha corregido. [#211](https://github.com/Microsoft/AzureStorageExplorer/issues/211)
* Al ver instantáneas para un blob, la acción Promover la instantánea se habilitó cuando se seleccionaron el blob de base y una sola instantánea. Ahora la acción solo se habilita si se selecciona una sola instantánea. [#230](https://github.com/Microsoft/AzureStorageExplorer/issues/230)
* Si se inicia un único trabajo (como descargar un blob) y, luego, se produce un error, no se reintentará automáticamente hasta que inicie otro trabajo del mismo tipo. Ahora, se deben reintentar todos los trabajos automáticamente, independientemente de cuántos estén en cola.
* Los editores abiertos para contenedores de blob recién creados en GPV2 y las cuentas de Blob Storage no tenían ninguna columna de nivel de acceso. Ahora se ha corregido. [#109](https://github.com/Microsoft/AzureStorageExplorer/issues/109)
* Una columna de nivel de acceso a veces no aparecería cuando se adjuntaba una cuenta de almacenamiento o contenedor de blob a través de SAS. Ahora, la columna se mostrará siempre, pero con un valor vacío si no hay ningún conjunto de nivel de acceso. [#160](https://github.com/Microsoft/AzureStorageExplorer/issues/160)
* Se ha deshabilitado la configuración de nivel de acceso de un blob en bloques recientemente cargado. Ahora se ha corregido. [#171](https://github.com/Microsoft/AzureStorageExplorer/issues/171)
* Si se invoca el botón "Mantener pestaña abierta" mediante el teclado, entonces se pierde el enfoque del teclado. Ahora, el enfoque se moverá a la pestaña que se mantuvo abierta. [#163](https://github.com/Microsoft/AzureStorageExplorer/issues/163)
* Para una consulta en el Generador de consultas, VoiceOver no proporcionaba ninguna descripción utilizable del operador actual. Ahora es más descriptivo. [#207](https://github.com/Microsoft/AzureStorageExplorer/issues/207)
* Los vínculos de paginación para los diferentes editores no eran descriptivos. Se cambiaron para ser más descriptivos. [#205](https://github.com/Microsoft/AzureStorageExplorer/issues/205)
* En el cuadro de diálogo Agregar entidad, VoiceOver no anunciaba de qué columna formaba parte un elemento de entrada. El nombre de la columna actual ahora se incluye en la descripción del elemento. [#206](https://github.com/Microsoft/AzureStorageExplorer/issues/206)
* Las casillas de verificación y botones de radio no tenían ningún borde visible al estar enfocados. Ahora se ha corregido. [#237](https://github.com/Microsoft/AzureStorageExplorer/issues/237)

### <a name="known-issues"></a>Problemas conocidos
* Al usar emuladores, como el emulador de Azure Storage o Azurite, necesitará hacer que escuchen conexiones en sus puertos predeterminados. De otro modo, el Explorador de Storage no podrá conectarlos.
* Si usa VS para Mac y nunca ha creado una configuración de AAD personalizada, es posible que no pueda iniciar sesión. Para solucionar el problema, elimine el contenido de ~/.IdentityService/AadConfigurations. Si al hacerlo no se desbloquea, incluya un comentario sobre [este problema](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* Azurite todavía no ha implementado por completo todas las API de Azure Storage. Por este motivo, pueden haber errores o comportamientos inesperados cuando se usa Azurite para el almacenamiento de desarrollo.
* En raras ocasiones, el foco de árbol puede quedarse bloqueado en un acceso rápido. Para desbloquear el foco, puede seleccionar Actualizar todo.
* Cargar desde la carpeta de OneDrive no funciona debido a un error en NodeJS. El error se ha corregido, pero aún no se ha integrado en Electron.
* Cuando el destino es Azure Stack, es posible que la carga de determinados archivos como blobs en anexos pueda producir errores.
* Después de hacer clic en “Cancelar” en una tarea, puede que esta tarde un tiempo en cancelarse. Esto es porque se usa la solución de filtro de cancelación que se describe [aquí](https://github.com/Azure/azure-storage-node/issues/317).
* Si no elige el certificado de tarjeta inteligente o PIN adecuados, tendrá que reiniciar para que el Explorador de Storage olvide esa decisión.
* Al cambiar de nombre los blobs (individualmente o dentro de un contenedor de blobs cuyo nombre ha cambiado), no se conservan las instantáneas. Todas las otras propiedades y metadatos de blobs, archivos y entidades se conservan al cambiar de nombre.
* Aunque Azure Stack actualmente no admite recursos compartidos de archivos, todavía aparece un nodo de recurso compartido de archivos en la cuenta de almacenamiento de Azure Stack conectada.
* El shell de Electron que usa el Explorador de Storage tiene problemas con la aceleración de hardware de GPU (unidad de procesamiento gráfico). Si el Explorador de Storage muestra una ventana principal en blanco (vacía), puede intentar iniciar el Explorador de Storage desde la línea de comandos y deshabilitar la aceleración de GPU al agregar el conmutador `--disable-gpu`:

```
./StorageExplorer.exe --disable-gpu
```

* Para los usuarios de Linux, debe instalar [.NET Core 2.0](https://docs.microsoft.com/en-us/dotnet/core/linux-prerequisites?tabs=netcore2x).
* Si es usuario de Ubuntu 14.04, deberá asegurarse de que GCC está actualizado. Para ello, se pueden ejecutar los siguientes comandos y, luego, es necesario reiniciar la máquina:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Los usuarios de Ubuntu 17.04 tendrán que instalar GConf. Esto se puede hacer mediante la ejecución de los siguientes comandos. Después de esto, es necesario reiniciar la máquina.

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-110"></a>Versión 1.1.0
09/05/2018

### <a name="new"></a>Nuevo
* El Explorador de Azure Storage admite ahora el uso de Azurite. Nota: La conexión a Azurite está codificada en los puntos de conexión de desarrollo predeterminados.
* El Explorador de Azure Storage ahora admite los niveles de acceso para las cuentas de almacenamiento Solo blob y GPV2. Obtenga [más información](https://docs.microsoft.com/en-us/azure/storage/blobs/storage-blob-storage-tiers) sobre los niveles de acceso.
* Ya no se requiere una hora de inicio al generar una SAS.

### <a name="fixes"></a>Correcciones
* Se interrumpió la recuperación de suscripciones para las cuentas del gobierno de Estados Unidos. Ahora se ha corregido. [#61](https://github.com/Microsoft/AzureStorageExplorer/issues/61)
* La hora de expiración de las directivas de acceso no se ha guardado correctamente. Ahora se ha corregido. [#50](https://github.com/Microsoft/AzureStorageExplorer/issues/50)
* Al generar una dirección URL de SAS para un elemento en un contenedor, el nombre del elemento no se ha anexado a la dirección URL. Ahora se ha corregido. [#44](https://github.com/Microsoft/AzureStorageExplorer/issues/44)
* Al crear una SAS, las horas de expiración que se encuentran en el pasado a veces eran el valor predeterminado. Esto se debía a que el Explorador de Azure Storage usaba la última hora de inicio y expiración usadas como valores predeterminados. Ahora, cada vez que abre el cuadro de diálogo de SAS, se genera un nuevo conjunto de valores predeterminados. [#35](https://github.com/Microsoft/AzureStorageExplorer/issues/35)
* Al copiar datos entre las cuentas de almacenamiento, se genera una SAS de 24 horas. Si la copia dura más de 24 horas, se producirá un error en la copia. Hemos aumentado la SAS hasta una duración de una semana para reducir la posibilidad de que una copia dé un error debido a una SAS que expiró. [#62](https://github.com/Microsoft/AzureStorageExplorer/issues/62)
* Para determinadas actividades, hacer clic en "Cancelar" no siempre funcionaba. Ahora se ha corregido. [#125](https://github.com/Microsoft/AzureStorageExplorer/issues/125)
* Para determinadas actividades, la velocidad de transferencia no era correcta. Ahora se ha corregido. [#124](https://github.com/Microsoft/AzureStorageExplorer/issues/124)
* La ortografía de "Anterior" en el menú Ver no era correcta. Ahora está correctamente escrita. [#71](https://github.com/Microsoft/AzureStorageExplorer/issues/71)
* La página final del instalador de Windows tenía el botón "Siguiente". Se ha cambiado por el botón "Finalizar". [#70](https://github.com/Microsoft/AzureStorageExplorer/issues/70)
* El foco de la pestaña no era visible para los botones en los cuadros de diálogo al usar el tema negro de HC. Ahora es visible.[#64](https://github.com/Microsoft/AzureStorageExplorer/issues/64)
* Las mayúsculas y minúsculas de "Resolución automática" para las acciones en el registro de actividad no eran correctas. Ahora son correctas. [#51](https://github.com/Microsoft/AzureStorageExplorer/issues/51)
* Al eliminar una entidad de una tabla, en el cuadro de diálogo que le pide que confirme la acción se muestra un icono de error. Ahora, en el cuadro de diálogo se usa un icono de advertencia. [#148](https://github.com/Microsoft/AzureStorageExplorer/issues/148)

### <a name="known-issues"></a>Problemas conocidos
* Si usa VS para Mac y nunca ha creado una configuración de AAD personalizada, es posible que no pueda iniciar sesión. Para solucionar el problema, elimine el contenido de ~/.IdentityService/AadConfigurations. Si al hacerlo no se desbloquea, incluya un comentario sobre [este problema](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* Azurite todavía no ha implementado por completo todas las API de Azure Storage. Por este motivo, pueden haber errores o comportamientos inesperados cuando se usa Azurite para el almacenamiento de desarrollo.
* En raras ocasiones, el foco de árbol puede quedarse bloqueado en un acceso rápido. Para desbloquear el foco, puede seleccionar Actualizar todo.
* Cargar desde la carpeta de OneDrive no funciona debido a un error en NodeJS. El error se ha corregido, pero aún no se ha integrado en Electron.
* Cuando el destino es Azure Stack, es posible que la carga de determinados archivos como blobs en anexos pueda producir errores.
* Después de hacer clic en “Cancelar” en una tarea, puede que esta tarde un tiempo en cancelarse. Esto es porque se usa la solución de filtro de cancelación que se describe [aquí](https://github.com/Azure/azure-storage-node/issues/317).
* Si no elige el certificado de tarjeta inteligente o PIN adecuados, tendrá que reiniciar para que el Explorador de Storage olvide esa decisión.
* Al cambiar de nombre los blobs (individualmente o dentro de un contenedor de blobs cuyo nombre ha cambiado), no se conservan las instantáneas. Todas las otras propiedades y metadatos de blobs, archivos y entidades se conservan al cambiar de nombre.
* Aunque Azure Stack actualmente no admite recursos compartidos de archivos, todavía aparece un nodo de recurso compartido de archivos en la cuenta de almacenamiento de Azure Stack conectada.
* El shell de Electron que usa el Explorador de Storage tiene problemas con la aceleración de hardware de GPU (unidad de procesamiento gráfico). Si el Explorador de Storage muestra una ventana principal en blanco (vacía), puede intentar iniciar el Explorador de Storage desde la línea de comandos y deshabilitar la aceleración de GPU al agregar el conmutador `--disable-gpu`:

```
./StorageExplorer.exe --disable-gpu
```

* Para los usuarios de Linux, debe instalar [.NET Core 2.0](https://docs.microsoft.com/en-us/dotnet/core/linux-prerequisites?tabs=netcore2x).
* Si es usuario de Ubuntu 14.04, deberá asegurarse de que GCC está actualizado. Para ello, se pueden ejecutar los siguientes comandos y, luego, es necesario reiniciar la máquina:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Los usuarios de Ubuntu 17.04 tendrán que instalar GConf. Esto se puede hacer mediante la ejecución de los siguientes comandos. Después de esto, es necesario reiniciar la máquina.

    ```
    sudo apt-get install libgconf-2-4
    ```


## <a name="version-100"></a>Versión 1.0.0
16/04/2018

### <a name="new"></a>Nuevo
* Autenticación mejorada que permite que el Explorador de Storage use el mismo almacén de cuentas que Visual Studio 2017. Para usar esta característica, debe volver a iniciar sesión en sus cuentas y volver a establecer las suscripciones filtradas.
* En cuanto a las cuentas de Azure Stack que respalda AAD, el Explorador de Storage recuperará las suscripciones de Azure Stack cuando esté habilitada la opción "Target Azure Stack" ("Usar Azure Stack como destino"). Ya no es necesario crear un entorno de inicio de sesión personalizado.
* Se agregaron varios accesos directos para que la exploración sea más larga. Entre ellos, se incluyen las opciones de alternar entre varios paneles y moverse entre los editores. Consulte el menú Vista para obtener más detalles.
* Los comentarios del Explorador de Storage ahora se guardan en GitHub. Puede acceder a nuestra página de problemas haciendo clic en el botón Comentarios en la esquina inferior izquierda, o visitando [https://github.com/Microsoft/AzureStorageExplorer/issues](https://github.com/Microsoft/AzureStorageExplorer/issues). No dude en hacer sugerencias, informar sobre problemas, hacer preguntas o dejar cualquier otro comentario.
* Si tiene problemas relacionados con certificados SSL y no puede encontrar el certificado en cuestión, puede iniciar el Explorador de Storage desde la línea de comandos con la marca `--ignore-certificate-errors`. Cuando se inicie con esta marca, el Explorador de Storage pasará por alto los errores del certificado SSL.
* Hemos incluido la opción "Descargar" en el menú emergente para elementos de blobs y archivos.
* Se ha mejorado la accesibilidad y la compatibilidad del lector de pantalla. Si confía en las características de accesibilidad, consulte nuestra [documentación de accesibilidad](https://docs.microsoft.com/en-us/azure/vs-azure-tools-storage-explorer-accessibility) para obtener más información.
* El Explorador de Store ahora usa Electron 1.8.3.

### <a name="breaking-changes"></a>Cambios importantes
* El Explorador de Storage tiene una nueva biblioteca de autenticación. Como parte del cambio a esta biblioteca, debe volver a iniciar sesión en sus cuentas y volver a establecer las suscripciones filtradas.
* Se ha cambiado el método que se usa para cifrar datos confidenciales. Esto puede ocasionar que algunos de los elementos de acceso rápido o recursos adjuntos deban volverse agregar de nuevo.

### <a name="fixes"></a>Correcciones
* Varias de las descargas o descargas de blobs de grupos de algunos usuarios con servidores proxy se veían interrumpidas debido al mensaje de error "No se puede resolver". Ahora se ha corregido.
* Si era necesario iniciar sesión cuando se usaba un vínculo directo, al hacer clic en el aviso "Iniciar sesión" aparecía un cuadro de diálogo en blanco. Ahora se ha corregido.
* En Linux, si el Explorador de Storage no se puede iniciar debido a un bloqueo del proceso de GPU, se le informará del bloqueo y se le indicará que use el modificador "--disable-gpu", y el Explorador de Storage se reiniciará automáticamente con el modificador ya habilitado.
* Las directivas de acceso no válidas eran difíciles de identificar en el cuadro de diálogo Directivas de acceso. Los id. de las directivas de acceso no válidas ahora aparecen destacados en rojo para ofrecer más visibilidad.
* El registro de actividad en ocasiones tenía grandes áreas en blanco entre las distintas partes de una actividad. Ahora se ha corregido.
* En el editor de consultas de tabla, si dejaba una cláusula de marca de tiempo en un estado no válido y luego se intentaba modificar otra, el editor se bloqueaba. Ahora, el editor restaurará la cláusula de marca de tiempo a su último estado válido cuando se detecte un cambio en otra cláusula.
* Si paraba de escribir la consulta de búsqueda en la vista de árbol, la búsqueda comenzaba y el foco se obtenía del cuadro de texto. Ahora, es preciso iniciar expresamente la búsqueda presionando la tecla "Entrar" o haciendo clic en el botón de inicio de la búsqueda.
* El comando "Obtener firma de acceso compartido" se deshabilitaba a veces cuando se hacía clic con el botón derecho en un archivo del recurso compartido de archivos. Ahora se ha corregido.
* Si el nodo del árbol de recursos enfocado se filtró durante la búsqueda, no es posible tabular al árbol de recursos y usar las teclas de dirección para moverse por el mismo. Ahora, si se oculta el nodo del árbol de recursos enfocado, el primer nodo del árbol de recursos se enfocará automáticamente.
* A veces, se ve un separador adicional en la barra de herramientas del editor. Ahora se ha corregido.
* El cuadro de texto de la ruta de navegación a veces se desborda. Ahora se ha corregido.
* A veces, los editores de recursos compartidos de blobs y archivos se actualizan constantemente al cargar varios archivos a la vez. Ahora se ha corregido.
* La característica "Estadísticas de carpetas" no tenía ningún propósito en la vista de Administración de instantáneas de archivos compartidos. Ahora está deshabilitada.
* En Linux, el menú Archivo no aparecía. Ahora se ha corregido.
* Al cargar una carpeta a un recurso compartido de archivos, solo se cargaba el contenido de la carpeta de forma predeterminada. Ahora, el comportamiento predeterminado se basa en cargar el contenido de la carpeta en una carpeta coincidente del recurso compartido de archivos.
* El orden de los botones de varios cuadros de diálogo estaba invertido. Ahora se ha corregido.
* Varias correcciones relacionadas con la seguridad.

### <a name="known-issues"></a>Problemas conocidos
* En raras ocasiones, el foco de árbol puede quedarse bloqueado en un acceso rápido. Para desbloquear el foco, puede seleccionar Actualizar todo.
* Cuando el destino es Azure Stack, es posible que la carga de determinados archivos como blobs en anexos pueda producir errores.
* Después de hacer clic en “Cancelar” en una tarea, puede que esta tarde un tiempo en cancelarse. Esto es porque se usa la solución de filtro de cancelación que se describe aquí.
* Si no elige el certificado de tarjeta inteligente o PIN adecuados, tendrá que reiniciar para que el Explorador de Storage olvide esa decisión.
* Al cambiar de nombre los blobs (individualmente o dentro de un contenedor de blobs cuyo nombre ha cambiado), no se conservan las instantáneas. Todas las otras propiedades y metadatos de blobs, archivos y entidades se conservan al cambiar de nombre.
* Aunque Azure Stack actualmente no admite recursos compartidos de archivos, todavía aparece un nodo de recurso compartido de archivos en la cuenta de almacenamiento de Azure Stack conectada.
* El shell de Electron que usa el Explorador de Storage tiene problemas con la aceleración de hardware de GPU (unidad de procesamiento gráfico). Si el Explorador de Storage muestra una ventana principal en blanco (vacía), puede intentar iniciar el Explorador de Storage desde la línea de comandos y deshabilitar la aceleración de GPU al agregar el conmutador `--disable-gpu`:

```
./StorageExplorer.exe --disable-gpu
```

* Para los usuarios de Linux, debe instalar [.NET Core 2.0](https://docs.microsoft.com/en-us/dotnet/core/linux-prerequisites?tabs=netcore2x).
* Si es usuario de Ubuntu 14.04, deberá asegurarse de que GCC está actualizado. Para ello, se pueden ejecutar los siguientes comandos y, luego, es necesario reiniciar la máquina:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Los usuarios de Ubuntu 17.04 tendrán que instalar GConf. Esto se puede hacer mediante la ejecución de los siguientes comandos. Después de esto, es necesario reiniciar la máquina.

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-096"></a>Versión 0.9.6
28/02/2018

### <a name="fixes"></a>Correcciones
* Un problema impedía que los blobs o archivos que esperaba se mostraran en el editor. Ahora se ha corregido.
* Un problema provocaba que los elementos se mostrarán de manera incorrecta al cambiar entre vistas de instantánea. Ahora se ha corregido.

### <a name="known-issues"></a>Problemas conocidos
* El Explorador de Storage no admite cuentas de AD FS.
* Cuando el destino es Azure Stack, es posible que la carga de determinados archivos como blobs en anexos pueda producir errores.
* Después de hacer clic en “Cancelar” en una tarea, puede que esta tarde un tiempo en cancelarse. Esto es porque se usa la solución de filtro de cancelación que se describe [aquí](https://github.com/Azure/azure-storage-node/issues/317).
* Si no elige el certificado de tarjeta inteligente o PIN adecuados, tendrá que reiniciar para que el Explorador de Storage olvide esa decisión.
* El panel de configuración de la cuenta puede indicar que necesita especificar de nuevo las credenciales para filtrar las suscripciones.
* Al cambiar de nombre los blobs (individualmente o dentro de un contenedor de blobs cuyo nombre ha cambiado), no se conservan las instantáneas. Todas las otras propiedades y metadatos de blobs, archivos y entidades se conservan al cambiar de nombre.
* Aunque Azure Stack actualmente no admite recursos compartidos de archivos, todavía aparece un nodo de recurso compartido de archivos en la cuenta de almacenamiento de Azure Stack conectada.
* El shell de Electron que usa el Explorador de Storage tiene problemas con la aceleración de hardware de GPU (unidad de procesamiento gráfico). Si el Explorador de Storage muestra una ventana principal en blanco (vacía), puede intentar iniciar el Explorador de Storage desde la línea de comandos y deshabilitar la aceleración de GPU al agregar el conmutador `--disable-gpu`:

```
./StorageExplorer.exe --disable-gpu
```

* Si es usuario de Ubuntu 14.04, deberá asegurarse de que GCC está actualizado. Para ello, se pueden ejecutar los siguientes comandos y, luego, es necesario reiniciar la máquina:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Los usuarios de Ubuntu 17.04 tendrán que instalar GConf. Esto se puede hacer mediante la ejecución de los siguientes comandos. Después de esto, es necesario reiniciar la máquina.

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-095"></a>Versión 0.9.5
06/02/2018

### <a name="new"></a>Nuevo

* Compatibilidad con las instantáneas de recursos compartidos de archivos:
    * Cree y administre las instantáneas para los recursos compartidos de archivos.
    * Cambie de vista fácilmente entre las instantáneas de los recursos compartidos de archivos a medida que los explora.
    * Restaure las versiones anteriores de los archivos.
* Compatibilidad de versión preliminar con Azure Data Lake Store:
    * Conéctese a los recursos de ADLS entre varias cuentas.
    * Conéctese a los recursos de ADLS y compártalos mediante URI de ADL.
    * Realice operaciones básicas de archivo o carpeta de manera recursiva.
    * Ancle carpetas individuales al acceso rápido.
    * Muestre las estadísticas de carpeta.

### <a name="fixes"></a>Correcciones
* Mejoras de rendimiento del inicio.
* Varias correcciones de errores.

### <a name="known-issues"></a>Problemas conocidos
* El Explorador de Storage no admite cuentas de AD FS.
* Cuando el destino es Azure Stack, es posible que la carga de determinados archivos como blobs en anexos pueda producir errores.
* Después de hacer clic en “Cancelar” en una tarea, puede que esta tarde un tiempo en cancelarse. Esto es porque se usa la solución de filtro de cancelación que se describe aquí.
* Si no elige el certificado de tarjeta inteligente o PIN adecuados, tendrá que reiniciar para que el Explorador de Storage olvide esa decisión.
* El panel de configuración de la cuenta puede indicar que necesita especificar de nuevo las credenciales para filtrar las suscripciones.
* Al cambiar de nombre los blobs (individualmente o dentro de un contenedor de blobs cuyo nombre ha cambiado), no se conservan las instantáneas. Todas las otras propiedades y metadatos de blobs, archivos y entidades se conservan al cambiar de nombre.
* Aunque Azure Stack actualmente no admite recursos compartidos de archivos, todavía aparece un nodo de recurso compartido de archivos en la cuenta de almacenamiento de Azure Stack conectada.
* El shell de Electron que usa el Explorador de Storage tiene problemas con la aceleración de hardware de GPU (unidad de procesamiento gráfico). Si el Explorador de Storage muestra una ventana principal en blanco (vacía), puede intentar iniciar el Explorador de Storage desde la línea de comandos y deshabilitar la aceleración de GPU al agregar el conmutador `--disable-gpu`:

```
./StorageExplorer.exe --disable-gpu
```

* Si es usuario de Ubuntu 14.04, deberá asegurarse de que GCC está actualizado. Para ello, se pueden ejecutar los siguientes comandos y, luego, es necesario reiniciar la máquina:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Los usuarios de Ubuntu 17.04 tendrán que instalar GConf. Esto se puede hacer mediante la ejecución de los siguientes comandos. Después de esto, es necesario reiniciar la máquina.

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-094-and-093"></a>Versión 0.9.4 y 0.9.3
01/21/2018

### <a name="new"></a>Nuevo
* La ventana del Explorador de Storage existente se volverá a utilizar al:
    * Abrir vínculos directos generados en el Explorador de Storage.
    * Abrir el Explorador de Storage desde el portal.
    * Abrir el Explorador de Storage desde la extensión de código de VS de Azure Storage (próximamente).
* Se ha añadido la posibilidad de abrir una nueva ventana del Explorador de Storage desde el propio Explorador de Storage.
    * En Windows, puede usar la opción "Nueva ventana" en el menú Archivo y en el menú contextual de la barra de tareas.
    * En Mac, puse usar la opción "Nueva ventana" en el menú de la aplicación.

### <a name="fixes"></a>Correcciones
* Se ha corregido un problema de seguridad. Actualice a la versión 0.9.4 lo antes posible.
* Las actividades antiguas no se borraban correctamente. Esto afectaba al rendimiento de trabajos de larga ejecución. Ahora sí se borran correctamente.
* En ocasiones, algunas acciones que implican una gran cantidad de archivos y directorios provocarán que el Explorador de Storage se bloquee. Las solicitudes de recursos compartidos de archivos a Azure ahora se aceleran para limitar el uso de recursos.

### <a name="known-issues"></a>Problemas conocidos
* El Explorador de Storage no admite cuentas de AD FS.
* Las teclas de método abreviado de "Ver explorador" y "Ver administración de cuentas" deberían ser respectivamente CTRL/CMD+MAIÚS+E y CTRL/CMD+MAYÚS+A.
* Cuando el destino es Azure Stack, es posible que la carga de determinados archivos como blobs en anexos pueda producir errores.
* Después de hacer clic en “Cancelar” en una tarea, puede que esta tarde un tiempo en cancelarse. Esto es porque se usa la solución de filtro de cancelación que se describe aquí.
* Si no elige el certificado de tarjeta inteligente o PIN adecuados, tendrá que reiniciar para que el Explorador de Storage olvide esa decisión.
* El panel de configuración de la cuenta puede indicar que necesita especificar de nuevo las credenciales para filtrar las suscripciones.
* Al cambiar de nombre los blobs (individualmente o dentro de un contenedor de blobs cuyo nombre ha cambiado), no se conservan las instantáneas. Todas las otras propiedades y metadatos de blobs, archivos y entidades se conservan al cambiar de nombre.
* Aunque Azure Stack actualmente no admite recursos compartidos de archivos, todavía aparece un nodo de recurso compartido de archivos en la cuenta de almacenamiento de Azure Stack conectada.
* El shell de Electron que usa el Explorador de Storage tiene problemas con la aceleración de hardware de GPU (unidad de procesamiento gráfico). Si el Explorador de Storage muestra una ventana principal en blanco (vacía), puede intentar iniciar el Explorador de Storage desde la línea de comandos y deshabilitar la aceleración de GPU al agregar el conmutador `--disable-gpu`:
```
./StorageExplorer --disable-gpu
```
* Si es usuario de Ubuntu 14.04, deberá asegurarse de que GCC está actualizado. Para ello, se pueden ejecutar los siguientes comandos y, luego, es necesario reiniciar la máquina:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Los usuarios de Ubuntu 17.04 tendrán que instalar GConf. Esto se puede hacer mediante la ejecución de los siguientes comandos. Después de esto, es necesario reiniciar la máquina.

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-092"></a>Versión 0.9.2
01/11/2017

### <a name="hotfixes"></a>Revisiones
* Se podían producir cambios de datos inesperados al editar los valores de Edm.DateTime para entidades de tabla según la zona horaria local. El editor ahora utiliza un cuadro de texto sin formato, lo que proporciona un control preciso y coherente de los valores de Edm.DateTime.
* La carga o descarga de un grupo de blobs cuando se asocia con un nombre y una clave no se iniciarían. Ahora se ha corregido.
* El Explorador de Storage solo le preguntaría si desea volver a autenticar una cuenta obsoleta si una o varias de las suscripciones de la cuenta se seleccionaron. Ahora el Explorador de Storage le pedirá incluso si la cuenta se filtra completamente.
* El dominio de los puntos de conexión para Azure Gobierno de EE.UU. era incorrecto. Esto se ha solucionado.
* A veces resultaba difícil hacer clic en el botón Aplicar del panel Administrar cuentas. Esto ya no debería ocurrir.

### <a name="new"></a>Nuevo
* Compatibilidad de versión preliminar para Azure Cosmos DB:
    * [Documentación en línea](./cosmos-db/storage-explorer.md)
    * Crear bases de datos y colecciones
    * Manipular datos
    * Consultar, crear o eliminar documentos
    * Actualizar procedimientos almacenados, desencadenadores y funciones definidas por el usuario
    * Usar las cadenas de conexión para conectarse y administrar las bases de datos
* Se mejoró el rendimiento de la carga y descarga de muchos blobs pequeños.
* Se agregó una acción "Reintentar todo" si hay errores en un grupo de carga de blobs o en un grupo de descarga de blobs.
* El Explorador de Storage ahora pausará la iteración durante la carga o descarga de blobs si detecta que se perdió la conexión de red. A continuación, puede reanudar la iteración una vez que se haya restablecido la conexión de red.
* Se agregó la posibilidad de "Cerrar todo", "Cerrar otros" y "Cerrar" pestañas a través del menú contextual.
* El Explorador de Storage ahora usa los cuadros de diálogo nativos y los menús contextuales nativos.
* El Explorador de Storage ahora es más accesible. Estas mejoras incluyen:
    * Compatibilidad mejorada con el lector de pantalla, para NVDA en Windows y para VoiceOver en Mac
    * Temas mejorados de contraste alto
    * Correcciones del foco del teclado y del tabulador del teclado

### <a name="fixes"></a>Correcciones
* Si intenta abrir o descargar un blob con un nombre de archivo no válido de Windows, se podría producir un error en la operación. El Explorador de Storage detectará si el nombre de un blob no es válido y le solicitará si quiere codificarlo u omitir el blob. El Explorador de Storage también detectará si un nombre de archivo parece estar codificado y le solicitará si quiere descodificarlo antes de cargarlo.
* Durante la carga del blob, el editor del contenedor del blob de destino podría no actualizarse correctamente. Ahora se ha corregido.
* La compatibilidad con varios formatos de cadenas de conexión y URI de SAS se ha revertido. Se han resuelto todos los problemas conocidos, pero agradecemos que nos envíe sus comentarios si encuentra algún otro.
* La notificación de actualización se interrumpió para algunos usuarios en 0.9.0. Este problema se ha corregido y, si este error le afectó, se puede descargar manualmente la última versión del Explorador de Storage [aquí](https://azure.microsoft.com/features/storage-explorer/).

### <a name="known-issues"></a>Problemas conocidos
* El Explorador de Storage no admite cuentas de AD FS.
* Las teclas de método abreviado de "Ver explorador" y "Ver administración de cuentas" deberían ser respectivamente CTRL/CMD+MAIÚS+E y CTRL/CMD+MAYÚS+A.
* Cuando el destino es Azure Stack, es posible que la carga de determinados archivos como blobs en anexos pueda producir errores.
* Después de hacer clic en “Cancelar” en una tarea, puede que esta tarde un tiempo en cancelarse. Esto es porque se usa la solución de filtro de cancelación que se describe aquí.
* Si no elige el certificado de tarjeta inteligente o PIN adecuados, tendrá que reiniciar para que el Explorador de Storage olvide esa decisión.
* El panel de configuración de la cuenta puede indicar que necesita especificar de nuevo las credenciales para filtrar las suscripciones.
* Al cambiar de nombre los blobs (individualmente o dentro de un contenedor de blobs cuyo nombre ha cambiado), no se conservan las instantáneas. Todas las otras propiedades y metadatos de blobs, archivos y entidades se conservan al cambiar de nombre.
* Aunque Azure Stack actualmente no admite recursos compartidos de archivos, todavía aparece un nodo de recurso compartido de archivos en la cuenta de almacenamiento de Azure Stack conectada.
* El shell de Electron que usa el Explorador de Storage tiene problemas con la aceleración de hardware de GPU (unidad de procesamiento gráfico). Si el Explorador de Storage muestra una ventana principal en blanco (vacía), puede intentar iniciar el Explorador de Storage desde la línea de comandos y deshabilitar la aceleración de GPU al agregar el conmutador `--disable-gpu`:
```
./StorageExplorer --disable-gpu
```
* Si es usuario de Ubuntu 14.04, deberá asegurarse de que GCC está actualizado. Para ello, se pueden ejecutar los siguientes comandos y, luego, es necesario reiniciar la máquina:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Los usuarios de Ubuntu 17.04 tendrán que instalar GConf. Esto se puede hacer mediante la ejecución de los siguientes comandos. Después de esto, es necesario reiniciar la máquina.

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-091-and-090"></a>Versión 0.9.1 y 0.9.0
20/10/2017
### <a name="new"></a>Nuevo
* Compatibilidad de versión preliminar para Azure Cosmos DB:
    * [Documentación en línea](./cosmos-db/storage-explorer.md)
    * Crear bases de datos y colecciones
    * Manipular datos
    * Consultar, crear o eliminar documentos
    * Actualizar procedimientos almacenados, desencadenadores y funciones definidas por el usuario
    * Usar las cadenas de conexión para conectarse y administrar las bases de datos
* Se mejoró el rendimiento de la carga y descarga de muchos blobs pequeños.
* Se agregó una acción "Reintentar todo" si hay errores en un grupo de carga de blobs o en un grupo de descarga de blobs.
* El Explorador de Storage ahora pausará la iteración durante la carga o descarga de blobs si detecta que se perdió la conexión de red. A continuación, puede reanudar la iteración una vez que se haya restablecido la conexión de red.
* Se agregó la posibilidad de "Cerrar todo", "Cerrar otros" y "Cerrar" pestañas a través del menú contextual.
* El Explorador de Storage ahora usa los cuadros de diálogo nativos y los menús contextuales nativos.
* El Explorador de Storage ahora es más accesible. Estas mejoras incluyen:
    * Compatibilidad mejorada con el lector de pantalla, para NVDA en Windows y para VoiceOver en Mac
    * Temas mejorados de contraste alto
    * Correcciones del foco del teclado y del tabulador del teclado

### <a name="fixes"></a>Correcciones
* Si intenta abrir o descargar un blob con un nombre de archivo no válido de Windows, se podría producir un error en la operación. El Explorador de Storage detectará si el nombre de un blob no es válido y le solicitará si quiere codificarlo u omitir el blob. El Explorador de Storage también detectará si un nombre de archivo parece estar codificado y le solicitará si quiere descodificarlo antes de cargarlo.
* Durante la carga del blob, el editor del contenedor del blob de destino podría no actualizarse correctamente. Ahora se ha corregido.
* La compatibilidad con varios formatos de cadenas de conexión y URI de SAS se ha revertido. Se han resuelto todos los problemas conocidos, pero agradecemos que nos envíe sus comentarios si encuentra algún otro.
* La notificación de actualización se interrumpió para algunos usuarios en 0.9.0. Este problema se ha corregido y, si este error le afectó, se puede descargar manualmente la versión más reciente del Explorador de Storage [aquí](https://azure.microsoft.com/features/storage-explorer/).

### <a name="known-issues"></a>Problemas conocidos
* El Explorador de Storage no admite cuentas de AD FS.
* Las teclas de método abreviado de "Ver explorador" y "Ver administración de cuentas" deberían ser respectivamente CTRL/CMD+MAIÚS+E y CTRL/CMD+MAYÚS+A.
* Cuando el destino es Azure Stack, es posible que la carga de determinados archivos como blobs en anexos pueda producir errores.
* Después de hacer clic en “Cancelar” en una tarea, puede que esta tarde un tiempo en cancelarse. Esto es porque se usa la solución de filtro de cancelación que se describe aquí.
* Si no elige el certificado de tarjeta inteligente o PIN adecuados, tendrá que reiniciar para que el Explorador de Storage olvide esa decisión.
* El panel de configuración de la cuenta puede indicar que necesita especificar de nuevo las credenciales para filtrar las suscripciones.
* Al cambiar de nombre los blobs (individualmente o dentro de un contenedor de blobs cuyo nombre ha cambiado), no se conservan las instantáneas. Todas las otras propiedades y metadatos de blobs, archivos y entidades se conservan al cambiar de nombre.
* Aunque Azure Stack actualmente no admite recursos compartidos de archivos, todavía aparece un nodo de recurso compartido de archivos en la cuenta de almacenamiento de Azure Stack conectada.
* El shell de Electron que usa el Explorador de Storage tiene problemas con la aceleración de hardware de GPU (unidad de procesamiento gráfico). Si el Explorador de Storage muestra una ventana principal en blanco (vacía), puede intentar iniciar el Explorador de Storage desde la línea de comandos y deshabilitar la aceleración de GPU al agregar el conmutador `--disable-gpu`:
```
./StorageExplorer --disable-gpu
```
* Si es usuario de Ubuntu 14.04, deberá asegurarse de que GCC está actualizado. Para ello, se pueden ejecutar los siguientes comandos y, luego, es necesario reiniciar la máquina:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Los usuarios de Ubuntu 17.04 tendrán que instalar GConf. Esto se puede hacer mediante la ejecución de los siguientes comandos. Después de esto, es necesario reiniciar la máquina.

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-0816"></a>Versión 0.8.16
8/21/2017

### <a name="new"></a>Nuevo
* Al abrir un blob, el Explorador de Storage le pedirá que cargue el archivo descargado si se detectó un cambio
* Experiencia mejorada de inicio de sesión de Azure Stack
* Mejorado el rendimiento de la carga y descarga de muchos archivos pequeños al mismo tiempo


### <a name="fixes"></a>Correcciones
* En algunos tipos de blob, al elegir la opción "reemplazar" durante un conflicto de carga, a veces provocaría que la carga se volviese a reiniciar.
* En la versión 0.8.15, las cargas se detendrían a veces en un 99 %.
* Al cargar archivos en un recurso compartido de archivos, si decide cargar en un directorio que todavía no existe, la carga produciría un error.
* El Explorador de Storage estaba generando marcas de tiempo de forma incorrecta para las firmas de acceso compartido y las consultas de tabla.


### <a name="known-issues"></a>Problemas conocidos
* El uso de un nombre y una cadena de conexión de clave no funciona actualmente. Funcionará en la próxima versión. Hasta ese momento, puede usar la asociación con el nombre y la clave.
* Si intenta abrir un archivo con un nombre de archivo de Windows no válido, la descarga provocará un error de archivo no encontrado.
* Después de hacer clic en “Cancelar” en una tarea, puede que esta tarde un tiempo en cancelarse. Se trata de una limitación de la biblioteca Azure Storage Node.
* Después de completar una carga de blobs, se actualiza la pestaña en la que se inició la carga. Se trata de un cambio con respecto al comportamiento anterior y también provocará que se le devuelva a la raíz del contenedor en el que se halle.
* Si no elige el certificado de tarjeta inteligente o PIN adecuados, tendrá que reiniciar para que el Explorador de Storage olvide esa decisión.
* El panel de configuración de la cuenta puede indicar que necesita especificar de nuevo las credenciales para filtrar las suscripciones.
* Al cambiar de nombre los blobs (individualmente o dentro de un contenedor de blobs cuyo nombre ha cambiado), no se conservan las instantáneas. Todas las otras propiedades y metadatos de blobs, archivos y entidades se conservan al cambiar de nombre.
* Aunque Azure Stack actualmente no admite recursos compartidos de archivos, todavía aparece un nodo de recurso compartido de archivos en la cuenta de almacenamiento de Azure Stack conectada.
* Si es usuario de Ubuntu 14.04, deberá asegurarse de que GCC está actualizado. Para ello, se pueden ejecutar los siguientes comandos y, luego, es necesario reiniciar la máquina:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Los usuarios de Ubuntu 17.04 tendrán que instalar GConf. Esto se puede hacer mediante la ejecución de los siguientes comandos. Después de esto, es necesario reiniciar la máquina.

    ```
    sudo apt-get install libgconf-2-4
    ```

### <a name="version-0814"></a>Versión 0.8.14
22/06/2017

### <a name="new"></a>Nuevo

* Versión de Electron actualizada a la 1.7.2 para aprovechar las múltiples actualizaciones de seguridad críticas
* Ahora se puede acceder rápidamente a la guía de solución de problemas en línea desde el menú de ayuda
* [Guía][2] de solución de problemas del Explorador de Storage
* [Instrucciones][3] sobre cómo conectarse a una suscripción de Azure Stack

### <a name="known-issues"></a>Problemas conocidos

* Los botones del cuadro de diálogo de confirmación de eliminación de carpeta no registran los clics del mouse en Linux. La solución alternativa consiste en usar la tecla Entrar.
* Si no elige el certificado de tarjeta inteligente o el PIN adecuados, tendrá que reiniciar para que el Explorador de Storage olvide la decisión.
* Si hay más de tres grupos de blobs o archivos cargándose al mismo tiempo, se pueden producir errores.
* El panel de configuración de la cuenta puede indicar que necesita especificar de nuevo las credenciales para filtrar las suscripciones.
* Al cambiar de nombre los blobs (individualmente o dentro de un contenedor de blobs cuyo nombre ha cambiado), no se conservan las instantáneas. Todas las otras propiedades y metadatos de blobs, archivos y entidades se conservan al cambiar de nombre.
* Aunque Azure Stack actualmente no admite recursos compartidos de archivos, todavía aparece un nodo de recurso compartido de archivos en la cuenta de almacenamiento de Azure Stack conectada.
* La instalación de Ubuntu 14.04 requiere que se actualice la versión de gcc. A continuación puede ver los pasos para actualizarla:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

### <a name="version-0813"></a>Versión 0.8.13
05/12/2017

#### <a name="new"></a>Nuevo

* [Guía][2] de solución de problemas del Explorador de Storage
* [Instrucciones][3] sobre cómo conectarse a una suscripción de Azure Stack

#### <a name="fixes"></a>Correcciones

* Problema corregido: Había una alta probabilidad de que, al cargar un archivo, se produjese un error de memoria agotada.
* Problema corregido: ahora puede iniciar sesión con una tarjeta inteligente o un PIN.
* Problema corregido: Abrir en Portal ahora funciona con Azure China, Azure Germany, Azure US Government y Azure Stack.
* Problema corregido: Al cargar una carpeta en un contenedor de blobs, a veces se produce un error de “operación ilegal”.
* Problema corregido: Seleccionar todo se deshabilita durante la administración de instantáneas.
* Problema corregido: Los metadatos del blob base puede que se sobrescriban después de ver las propiedades de las instantáneas.

#### <a name="known-issues"></a>Problemas conocidos

* Si no elige el certificado de tarjeta inteligente o el PIN adecuados, tendrá que reiniciar para que el Explorador de Storage olvide la decisión.
* Cuando se amplía o reduce, el nivel de zoom puede restablecerse momentáneamente al nivel predeterminado.
* Si hay más de tres grupos de blobs o archivos cargándose al mismo tiempo, se pueden producir errores.
* El panel de configuración de la cuenta puede indicar que necesita especificar de nuevo las credenciales para filtrar las suscripciones.
* Al cambiar de nombre los blobs (individualmente o dentro de un contenedor de blobs cuyo nombre ha cambiado), no se conservan las instantáneas. Todas las otras propiedades y metadatos de blobs, archivos y entidades se conservan al cambiar de nombre.
* Aunque Azure Stack actualmente no admite recursos compartidos de archivos, todavía aparece un nodo de recurso compartido de archivos en la cuenta de almacenamiento de Azure Stack conectada.
* La instalación de Ubuntu 14.04 requiere que se actualice la versión de gcc. A continuación puede ver los pasos para actualizarla:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```


### <a name="version-0812-and-0811-and-0810"></a>Versión 0.8.12, 0.8.11 y 0.8.10
07/04/2017

#### <a name="new"></a>Nuevo

* Explorador de Storage ahora se cerrará automáticamente al instalar una actualización desde la notificación de actualización.
* El acceso rápido local proporciona una experiencia mejorada a la hora de trabajar con los recursos a los que accede con más frecuencia.
* En el editor de contenedores de blobs, ahora puede ver a qué máquina virtual pertenece un blob concedido.
* Ahora puede contraer el panel izquierdo.
* Ahora la detección se ejecuta al mismo tiempo que la descarga.
* Use Estadísticas del contenedor de blobs, el recurso compartido de archivos y los editores de tablas para ver el tamaño del recurso o de la selección.
* Ahora puede iniciar sesión en Azure Active Directory (AAD) basándose en cuentas de Azure Stack.
* Ahora puede cargar archivos de archivo de más de 32 MB en cuentas de almacenamiento Premium.
* Compatibilidad de accesibilidad mejorada.
* Ahora puede agregar certificados SSL X.509 cifrados en Base-64 de confianza en Editar -&gt; Certificados SSL -&gt; Importar certificados.

#### <a name="fixes"></a>Correcciones

* Problema corregido: Después de actualizar las credenciales de una cuenta, a veces no se actualizaba la vista de árbol.
* Problema corregido: Al generar un SAS para tablas y colas de emulador, se producía una URL no válida.
* Problema corregido: Las cuentas de almacenamiento premium ahora se pueden ampliar mientras haya un proxy habilitado.
* Problema corregido: El botón Aplicar de la página de administración de cuentas no funcionaba si había una o cero cuentas seleccionadas.
* Problema corregido: Se puede producir un error al cargar blobs que requieran resoluciones de conflictos. Esto se ha corregido en la versión 0.8.11.
* Problema corregido: No se podían enviar comentarios en la versión 0.8.11. Corregido en la versión 0.8.12.

#### <a name="known-issues"></a>Problemas conocidos

* Después de actualizar a la versión 0.8.10, tendrá que actualizar todas las credenciales.
* Cuando se amplía o reduce, el nivel de zoom puede restablecerse momentáneamente al nivel predeterminado.
* Si hay más de tres grupos de blobs o archivos cargándose al mismo tiempo, se pueden producir errores.
* El panel de configuración de la cuenta puede indicar que necesita especificar de nuevo las credenciales para filtrar las suscripciones.
* Al cambiar de nombre los blobs (individualmente o dentro de un contenedor de blobs cuyo nombre ha cambiado), no se conservan las instantáneas. Todas las otras propiedades y metadatos de blobs, archivos y entidades se conservan al cambiar de nombre.
* Aunque Azure Stack actualmente no admite recursos compartidos de archivos, todavía aparece un nodo de recurso compartido de archivos en la cuenta de almacenamiento de Azure Stack conectada.
* La instalación de Ubuntu 14.04 requiere que se actualice la versión de gcc. A continuación puede ver los pasos para actualizarla:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```


### <a name="version-089-and-088"></a>Versión 0.8.9 y 0.8.8
23/02/2017

>[!VIDEO https://www.youtube.com/embed/R6gonK3cYAc?ecver=1]

>[!VIDEO https://www.youtube.com/embed/SrRPCm94mfE?ecver=1]


#### <a name="new"></a>Nuevo

* Explorador de Storage 0.8.9 descargará automáticamente la última versión de las actualizaciones.
* Revisión: Al usar un URI de SAS generado en el portal para conectarse a una cuenta de almacenamiento, se producía un error.
* Ahora puede crear, administrar y promover instantáneas de blobs.
* Ahora puede iniciar sesión en cuentas de Azure China, Azure Alemania y Azure US Government.
* Ahora puede cambiar el nivel de zoom. Use las opciones del menú Ver para acercar, alejar y restablecer el zoom.
* Ahora se admiten caracteres Unicode en los metadatos de usuarios para blobs y archivos.
* Mejoras de accesibilidad.
* Las notas de la versión de la siguiente versión se pueden ver desde la notificación de actualización. También puede ver las notas de la versión actual desde el menú Ayuda.

#### <a name="fixes"></a>Correcciones

* Problema corregido: El número de versión ahora se muestra correctamente en el Panel de control de Windows.
* Problema corregido: La búsqueda ya no está limitada a 50 000 nodos.
* Problema corregido: La carga a un recurso compartido de archivos se alargaba infinitamente si el directorio de destino no existía anteriormente.
* Problema corregido: Estabilidad mejorada para cargas y descargas largas.

#### <a name="known-issues"></a>Problemas conocidos

* Cuando se amplía o reduce, el nivel de zoom puede restablecerse momentáneamente al nivel predeterminado.
* Acceso rápido solo funciona con elementos basados en la suscripción. En esta versión no se admiten recursos locales o recursos adjuntados a través de la clave o token de SAS.
* Es posible que Acceso rápido tarde unos segundos en desplazarse hasta el recurso de destino, en función del número de recursos que tenga.
* Si hay más de tres grupos de blobs o archivos cargándose al mismo tiempo, se pueden producir errores.

16/12/2016
### <a name="version-087"></a>Versión 0.8.7

>[!VIDEO https://www.youtube.com/embed/Me4Y4jxoer8?ecver=1]

#### <a name="new"></a>Nuevo

* Puede elegir cómo resolver conflictos al principio de una actualización, descarga o sesión de copia en la ventana Actividades.
* Mantenga el puntero sobre una pestaña para ver la ruta de acceso completa del recurso de almacenamiento.
* Al hacer clic en una pestaña, se sincroniza con su ubicación en el panel de navegación izquierdo.

#### <a name="fixes"></a>Correcciones

* Problema corregido: El Explorador de Storage es ahora una aplicación de confianza en Mac.
* Problema corregido: Ubuntu 14.04 se admite de nuevo.
* Problema corregido: En ocasiones, la interfaz de usuario para agregar una cuenta parpadea al cargar las suscripciones.
* Problema corregido: En ocasiones, no todos los recursos de almacenamiento se muestran en el panel de navegación izquierdo.
* Problema corregido: El panel de acciones a veces muestra acciones vacías.
* Problema corregido: Ahora se conserva el tamaño de la ventana de la última sesión que se cerró.
* Problema corregido: Se pueden abrir varias pestañas para el mismo recurso mediante el menú contextual.

#### <a name="known-issues"></a>Problemas conocidos

* Acceso rápido solo funciona con elementos basados en la suscripción. En esta versión no se admiten recursos locales o recursos adjuntados a través de la clave o token de SAS.
* Es posible que Acceso rápido tarde unos segundos en desplazarse hasta el recurso de destino, en función del número de recursos que tenga.
* Si hay más de tres grupos de blobs o archivos cargándose al mismo tiempo, se pueden producir errores.
* Identificadores de búsqueda que buscan en unos 50.000 nodos: después de esto, el rendimiento puede verse afectado o se pueden producir excepciones no controladas.
* Por primera vez con el Explorador de Storage en macOS, es posible que aparezcan varias solicitudes que piden permiso del usuario para acceder a las llaves. Se recomienda seleccionar Permitir siempre para que la solicitud no vuelva a aparecer más.

18/11/2016
### <a name="version-086"></a>Versión 0.8.6

#### <a name="new"></a>Nuevo

* Ya puede anclar los servicios más utilizados en Acceso rápido para facilitar la navegación.
* Ya puede abrir varios editores en diferentes pestañas. Haga un solo clic para abrir una pestaña temporal y doble clic para abrir una pestaña permanente. También puede hacer clic en la pestaña temporal para que se convierta en una pestaña permanente.
* Hemos realizado mejoras notables de rendimiento y estabilidad para cargas y descargas, especialmente para archivos de gran tamaño en máquinas rápidas
* Las carpetas vacías "virtuales" ya se pueden crear en contenedores de blobs.
* Hemos vuelto a incorporar la búsqueda en ámbito con la nueva y mejorada búsqueda de subcadenas, por lo que ahora tiene dos opciones para realizar búsquedas:
    * Búsqueda global: solo tiene que indicar un término de búsqueda en el cuadro de texto de búsqueda.
    * Búsqueda de ámbito: haga clic en el icono de lupa al lado de un nodo; después, agregue un término de búsqueda al final de la ruta o haga clic con el botón derecho y seleccione “Buscar desde aquí”.
* Hemos agregado varios temas: claro (valor predeterminado), oscuro, negro en alto contraste y blanco en alto contraste. Vaya a Editar -&gt; Temas para cambiar las preferencias de temas.
* Puede modificar las propiedades de blobs y archivos.
* Ahora se admiten mensajes de cola cifrados (Base64) y sin cifrar.
* En Linux, ahora es necesario usar un sistema operativo de 64 bits. En esta versión solo se admite Ubuntu 16.04.1 LTS de 64 bits.
* ¡Hemos actualizado nuestro logotipo!

#### <a name="fixes"></a>Correcciones

* Problema corregido: Problemas de inmovilización de pantalla
* Problema corregido: Mayor seguridad
* Problema corregido: A veces, podrían aparecer cuentas conectadas duplicadas.
* Problema corregido: Un blob con un tipo de contenido sin definir podría generar una excepción.
* Problema corregido: No se podía abrir el Panel de consulta en una tabla vacía.
* Problema corregido: Varios errores en la búsqueda.
* Problema corregido: Aumento del número de recursos que se cargan de 50 a 100 al hacer clic en “Cargar más”.
* Problema corregido: En la primera ejecución, si ha iniciado sesión con una cuenta, ahora se seleccionan todas las suscripciones para esa cuenta de manera predeterminada.

#### <a name="known-issues"></a>Problemas conocidos

* Esta versión del Explorador de Storage no se ejecuta en Ubuntu 14.04.
* Para abrir varias pestañas para el mismo recurso, no haga clic varias veces en el mismo recurso. Haga clic en otro recurso y, después, vuelva y haga clic en el recurso original para volver a abrirlo en otra pestaña.
* Acceso rápido solo funciona con elementos basados en la suscripción. En esta versión no se admiten recursos locales o recursos adjuntados a través de la clave o token de SAS.
* Es posible que Acceso rápido tarde unos segundos en desplazarse hasta el recurso de destino, en función del número de recursos que tenga.
* Si hay más de tres grupos de blobs o archivos cargándose al mismo tiempo, se pueden producir errores.
* Identificadores de búsqueda que buscan en unos 50.000 nodos: después de esto, el rendimiento puede verse afectado o se pueden producir excepciones no controladas.

03/10/2016
### <a name="version-085"></a>Versión 0.8.5

#### <a name="new"></a>Nuevo

* Ahora puede usar claves SAS generadas por el Portal para conectarse a cuentas de almacenamiento y recursos

#### <a name="fixes"></a>Correcciones

* Problema corregido: A veces, la condición de carrera durante las búsquedas provocaba que los nodos no se pudiesen expandir.
* Problema corregido: “Usar HTTP” no funciona al conectarse a cuentas de almacenamiento con un nombre de cuenta y una clave.
* Problema corregido: Las claves SAS (especialmente las generadas en el Portal) devuelven un error de “barra oblicua final”.
* Problema corregido: Problemas al importar tablas.
    * A veces se revertían la clave de fila y la clave de partición
    * No se puede leer las claves de partición “null”.

#### <a name="known-issues"></a>Problemas conocidos

* Identificadores de búsqueda que buscan en unos 50.000 nodos: después de esto, el rendimiento puede verse afectado.
* Azure Stack no admite Files actualmente por lo que, al intentar expandir Files, se produce un error.

12/09/2016
### <a name="version-084"></a>Versión 0.8.4

>[!VIDEO https://www.youtube.com/embed/cr5tOGyGrIQ?ecver=1]

#### <a name="new"></a>Nuevo

* Genere vínculos directos a cuentas de almacenamiento, contenedores, colas, tablas o recursos compartidos de archivos para compartir y acceder fácilmente a sus recursos. Es compatible con Windows y Mac OS.
* Busque contenedores de blobs, tablas, colas, recursos compartidos de archivos o cuentas de almacenamiento en el cuadro de búsqueda.
* Ahora puede agrupar las cláusulas en el generador de consultas de tablas.
* Cambie el nombre y copie y pegue contenedores de blobs, recursos compartidos de archivos, tablas, blobs, carpetas de blobs, archivos y directorios desde cuentas conectadas mediante SAS y contenedores.
* Al cambiar el nombre y copiar contenedores de blobs y recursos compartidos de archivos, ahora se conservan las propiedades y los metadatos.

#### <a name="fixes"></a>Correcciones

* Problema corregido: No se pueden editar las entidades de tablas si contienen propiedades binarias o booleanas.

#### <a name="known-issues"></a>Problemas conocidos

* Identificadores de búsqueda que buscan en unos 50.000 nodos: después de esto, el rendimiento puede verse afectado.

03/08/2016
### <a name="version-083"></a>Versión 0.8.3

>[!VIDEO https://www.youtube.com/embed/HeGW-jkSd9Y?ecver=1]

#### <a name="new"></a>Nuevo

* Cambie el nombre de contenedores, tablas y recursos compartidos de archivos.
* Experiencia mejorada con el generador de consultas.
* Posibilidad de guardar y cargar consultas.
* Vínculos directos a cuentas de almacenamiento o contenedores, colas, tablas o recursos compartidos de archivos para compartir y acceder fácilmente a los recursos (solo disponible para Windows, macOS se admitirá pronto).
* Capacidad para administrar y configurar reglas de CORS

#### <a name="fixes"></a>Correcciones

* Problema corregido: Las cuentas de Microsoft requieren que vuelva a autenticarse en períodos de 8 a 12 horas.

#### <a name="known-issues"></a>Problemas conocidos

* A veces, puede que parezca que la IU se bloquea. Este problema se resuelve maximizando la ventana.
* Puede que la instalación en macOS requiera permisos elevados.
* El panel de configuración de la cuenta puede indicar que necesita especificar de nuevo las credenciales para filtrar las suscripciones.
* Al cambiar de nombre los recursos compartidos, contenedores de blobs y tablas, no se conservan los metadatos ni otras propiedades del contenedor, como la cuota de uso compartido de archivos, el nivel de acceso público ni las directivas de acceso.
* Al cambiar de nombre los blobs (individualmente o dentro de un contenedor de blobs cuyo nombre ha cambiado), no se conservan las instantáneas. Todas las otras propiedades y metadatos de blobs, archivos y entidades se conservan al cambiar de nombre.
* No se puede copiar o cambiar de nombre recursos en cuentas conectadas mediante SAS.

07/07/2016
### <a name="version-082"></a>Versión 0.8.2

>[!VIDEO https://www.youtube.com/embed/nYgKbRUNYZA?ecver=1]

#### <a name="new"></a>Nuevo

* Las cuentas de almacenamiento se agrupan por suscripciones; el almacenamiento de desarrollo y los recursos conectados mediante clave o SAS se muestran en el nodo (Local y Adjuntos).
* Cierre la sesión de las cuentas en el panel “Configuración de la cuenta de Azure”.
* Configure el proxy para habilitar y administrar el inicio de sesión.
* Cree y rompa concesiones de blobs.
* Abra contenedores de blobs, colas, tablas y archivos con un solo clic.

#### <a name="fixes"></a>Correcciones

* Problema corregido: Los mensajes de cola insertados con bibliotecas de Java o .NET no se descodifican correctamente desde Base64.
* Problema corregido: Las tablas de $metrics no se muestran en las cuentas de Blob Storage.
* Problema corregido: El nodo de tablas no funciona para el almacenamiento local (desarrollo).

#### <a name="known-issues"></a>Problemas conocidos

* Puede que la instalación en macOS requiera permisos elevados.

15/06/2016
### <a name="version-080"></a>Versión 0.8.0

>[!VIDEO https://www.youtube.com/embed/ycfQhKztSIY?ecver=1]

>[!VIDEO https://www.youtube.com/embed/k4_kOUCZ0WA?ecver=1]

>[!VIDEO https://www.youtube.com/embed/3zEXJcGdl_k?ecver=1]

#### <a name="new"></a>Nuevo

* Compatibilidad con recursos compartidos de archivos: visualización, carga, descarga, copia de archivos y directorios, URI de SAS (creación y conexión).
* Mejoras de la experiencia del usuario para conectarse a Storage con URI de SAS o claves de cuentas.
* Exportación de resultados de consulta de tabla.
* Reordenación y personalización de columnas de tabla.
* Consulta de contenedores de blob $logs y tablas de $metrics para cuentas de almacenamiento con las métricas habilitadas.
* Comportamiento de importación y exportación mejorado. Ahora incluye el tipo de valor de propiedad.

#### <a name="fixes"></a>Correcciones

* Problema corregido: Al cargar o descargar blobs grandes, se puede producir una carga o descarga incompleta.
* Problema corregido: Al editar, agregar o importar una entidad con un valor de cadena numérico (“1”) se convertirá en doble.
* Problema corregido: No se puede expandir el nodo de tabla en el entorno de desarrollo local.

#### <a name="known-issues"></a>Problemas conocidos

* Las tablas de $metrics no son visibles para cuentas de Blob Storage.
* Los mensajes de cola que se agregan mediante programación pueden no mostrarse correctamente si los mensajes se codifican con Base 64.

17/05/2016
### <a name="version-07201605090"></a>Versión 0.7.20160509.0

#### <a name="new"></a>Nuevo

* Mejor control de errores para bloqueos de la aplicación.

#### <a name="fixes"></a>Correcciones

* Se ha corregido un error en el que los mensajes de la barra de información a veces no se mostraban cuando se requerían credenciales de inicio de sesión.

#### <a name="known-issues"></a>Problemas conocidos

* Tablas: Al agregar, editar o importar una entidad con una propiedad con un valor numérico ambiguo, como “1” o “1.0” y cuando el usuario intenta enviarla como `Edm.String`, el valor volverá a través de la API del cliente como un Edm.Double.

31/03/2016

### <a name="version-07201603250"></a>Versión 0.7.20160325.0

>[!VIDEO https://www.youtube.com/embed/imbgBRHX65A?ecver=1]

>[!VIDEO https://www.youtube.com/embed/ceX-P8XZ-s8?ecver=1]

#### <a name="new"></a>Nuevo

* Compatibilidad con tablas: visualización, realización de consultas, exportación, importación y operaciones CRUD para entidades.
* Compatibilidad con colas: visualización, adición y eliminación de mensajes de la cola.
* Generación de URI de SAS para cuentas de almacenamiento.
* Conexión a cuentas de almacenamiento con URI de SAS.
* Notificaciones de actualizaciones para actualizaciones futuras a Explorador de Storage.
* Apariencia actualizada.

#### <a name="fixes"></a>Correcciones

* Mejoras de rendimiento y confiabilidad.

### <a name="known-issues-amp-mitigations"></a>Problemas conocidos y mitigaciones

* La descarga de archivos de blob grandes no funciona correctamente. Le recomendamos que use AzCopy mientras no se soluciona este problema.
* Las credenciales de cuentas no se recuperan ni se almacenan en caché si la carpeta de inicio no se encuentra o no se puede escribir en ella.
* Al agregar, editar o importar una entidad con una propiedad con un valor numérico ambiguo, como “1” o “1.0” y cuando el usuario intenta enviarla como `Edm.String`, el valor volverá a través de la API del cliente como un Edm.Double.
* Al importar archivos CSV con registros de varias líneas, es posible que los datos se corten o se desordenen.

03/02/2016

### <a name="version-07201601291"></a>Versión 0.7.20160129.1

#### <a name="fixes"></a>Correcciones

* Rendimiento general mejorado al cargar, descargar y copiar blobs.

14/01/2016

### <a name="version-07201601050"></a>Versión 0.7.20160105.0

#### <a name="new"></a>Nuevo

* Compatibilidad con Linux (características de paridad para OSX)
* Agregue contenedores de blobs con clave de Firmas de acceso compartido (SAS).
* Agregue cuentas de almacenamiento para Azure China.
* Agregue cuentas de almacenamiento con puntos de conexión personalizados.
* Abra y vea blobs de imagen y texto de contenido.
* Vea y edite propiedades y metadatos de blob.

#### <a name="fixes"></a>Correcciones

* Problema corregido: La carga o descarga de un gran número de blobs (más de 500) puede provocar en ocasiones que la aplicación muestre una pantalla en blanco.
* Problema corregido: Al establecer el nivel de acceso público en un contenedor de blobs, el valor nuevo no se actualiza hasta que vuelva a establecer el foco en el contenedor. Además, el diálogo siempre tiene como valor predeterminado “Sin acceso público” y no el valor real actual.
* Mejor accesibilidad de teclado general y compatibilidad con IU.
* El historial de enlaces se ajusta cuando es demasiado largo con espacios en blanco.
* El cuadro de diálogo de SAS admite la validación de entradas.
* El almacenamiento local sigue estando disponible incluso aunque las credenciales de usuario hayan expirado.
* Cuando se elimina un contenedor de blobs abierto, el explorador de blobs de la derecha se cierra.

#### <a name="known-issues"></a>Problemas conocidos

* La instalación de Linux requiere que se actualice la versión de gcc. A continuación puede ver los pasos para actualizarla:
    * `sudo add-apt-repository ppa:ubuntu-toolchain-r/test`
    * `sudo apt-get update`
    * `sudo apt-get upgrade`
    * `sudo apt-get dist-upgrade`

18/11/2015
### <a name="version-07201511160"></a>Versión 0.7.20151116.0

#### <a name="new"></a>Nuevo

* Versiones de Windows y macOS.
* Inicie sesión para ver las cuentas de almacenamiento: use su cuenta de organización, cuenta Microsoft, 2FA, etc.
* Almacenamiento de desarrollo local (use un emulador de almacenamiento, solo para Windows).
* Compatibilidad de Azure Resource Manager y recursos clásicos.
* Cree y elimine blobs, colas o tablas.
* Busque blobs, colas o tablas específicos.
* Explore el contenido de los contenedores de blobs.
* Vea y navegue por directorios.
* Cargue, descargue y elimine blobs y carpetas.
* Vea y edite propiedades y metadatos de blob.
* Genere claves SAS.
* Administre y cree directivas de acceso a almacenamiento (SAP).
* Busque blobs por prefijo.
* Arrastre y coloque archivos para cargarlos o descargarlos.

#### <a name="known-issues"></a>Problemas conocidos

* Al establecer el nivel de acceso público en un contenedor de blobs, el valor nuevo no se actualiza hasta que vuelva a establecer el foco en el contenedor.
* Al abrir el cuadro de diálogo para establecer el nivel de acceso público, siempre se muestra “Sin acceso público” como valor predeterminado y no el valor real actual.
* No se puede cambiar el nombre de los blobs descargados.
* Las entradas del registro de actividades a veces se bloquean en un estado de progreso cuando se produce un error y el error no se muestra.
* A veces se bloquea o se pone en blanco al intentar cargar o descargar un gran número de blobs.
* A veces no se puede cancelar una operación de copia.
* Durante la creación de un contenedor (blob, cola o tabla), si indica un nombre no válido y continúa creando otro en otro tipo de contenedor, no podrá establecer el foco en el tipo nuevo.
* No se puede crear una carpeta nueva ni cambiarle el nombre.




[2]: https://support.microsoft.com/en-us/help/4021389/storage-explorer-troubleshooting-guide
[3]: vs-azure-tools-storage-manage-with-storage-explorer.md
