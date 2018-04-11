---
title: Desarrollo local con el Emulador de Azure Cosmos DB | Microsoft Docs
description: Con el Emulador de Azure Cosmos DB, puede desarrollar y probar su aplicación localmente de forma gratuita, sin necesidad de crear una suscripción a Azure.
services: cosmos-db
documentationcenter: ''
keywords: Emulador de Azure Cosmos DB
author: David-Noble-at-work
manager: jhubbard
editor: ''
ms.assetid: 90b379a6-426b-4915-9635-822f1a138656
ms.service: cosmos-db
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2018
ms.author: danoble
ms.openlocfilehash: e0d23a163f16763dd4764eb7857dec8076f4754c
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/03/2018
---
# <a name="use-the-azure-cosmos-db-emulator-for-local-development-and-testing"></a>Uso del Emulador de Azure Cosmos DB para desarrollo y pruebas de forma local

<table>
<tr>
  <td><strong>Archivos binarios</strong></td>
  <td>[Descargar MSI](https://aka.ms/cosmosdb-emulator)</td>
</tr>
<tr>
  <td><strong>Docker</strong></td>
  <td>[Docker Hub](https://hub.docker.com/r/microsoft/azure-cosmosdb-emulator/)</td>
</tr>
<tr>
  <td><strong>Origen de Docker</strong></td>
  <td>[Github](https://github.com/Azure/azure-cosmos-db-emulator-docker)</td>
</tr>
</table>
  
El Emulador de Azure Cosmos DB proporciona un entorno local que emula el servicio de Azure Cosmos DB con fines de desarrollo. Mediante el Emulador de Azure Cosmos DB, puede desarrollar y probar su aplicación localmente, sin crear una suscripción de Azure o realizar algún gasto. Cuando esté satisfecho con el funcionamiento de la aplicación en el Emulador, puede cambiar a una cuenta de Azure Cosmos DB en la nube.

> [!NOTE]
> En este momento, el Explorador de datos del emulador solo admite colecciones de la API de SQL y colecciones de MongoDB. Los contenedores de Table, Graph y Cassandra no son totalmente compatibles. 

En este artículo se tratan las tareas siguientes: 

> [!div class="checklist"]
> * Instalación del Emulador
> * Autenticación de solicitudes
> * Uso del Explorador de datos en el Emulador
> * Exportación de certificados SSL
> * Llamada al Emulador desde la línea de comandos
> * Ejecución del Emulador en Docker para Windows
> * Recopilación de archivos de seguimiento
> * solución de problemas

Antes que nada, se recomienda ver el siguiente vídeo, donde Kirill Gavrylyuk describe las funciones básicas del Emulador de Azure Cosmos DB. Tenga en cuenta que en el vídeo se hace referencia al emulador como el "Emulador de DocumentDB", pero después de que se grabara el vídeo se ha cambiado el nombre de la herramienta a "Emulador de Azure Cosmos DB". Toda la información contenida en el vídeo sigue siendo correcta para el Emulador de Azure Cosmos DB. 

> [!VIDEO https://channel9.msdn.com/Events/Connect/2016/192/player]
> 
> 

## <a name="how-the-emulator-works"></a>Funcionamiento del emulador
El Emulador de Azure Cosmos DB proporciona una emulación de gran fidelidad del servicio Azure Cosmos DB. Admite una funcionalidad idéntica a la de Azure Cosmos DB, incluida la compatibilidad con la creación y la consulta de documentos JSON, el aprovisionamiento y el escalado de colecciones y la ejecución de procedimientos y desencadenadores almacenados. Puede desarrollar y probar aplicaciones mediante el Emulador de Azure Cosmos DB e implementarlas en Azure a escala global realizando simplemente un solo cambio de configuración en el punto de conexión de la conexión de Azure Cosmos DB.

Aunque se crea una emulación local de alta fidelidad del propio servicio Azure Cosmos DB, la implementación del Emulador de Azure Cosmos DB es diferente de la del servicio. Por ejemplo, el Emulador de Azure Cosmos DB usa componentes del sistema operativo estándar, como el sistema de archivos local para la persistencia y la pila del protocolo HTTPS para la conectividad. Esto significa que ciertas funcionalidades que se basan en la infraestructura de Azure, como la replicación global, la latencia de milisegundos de un solo dígito para lecturas/escrituras y los niveles de coherencia ajustable, no están disponibles a través del Emulador de Azure Cosmos DB.

## <a name="differences-between-the-emulator-and-the-service"></a>Diferencias entre el emulador y el servicio 
Dado que el Emulador de Azure Cosmos DB proporciona un entorno emulado que se ejecuta en una estación de trabajo de desarrollador local, hay algunas diferencias de funcionalidad entre el emulador y una cuenta de Azure Cosmos DB en la nube:

* El Emulador de Azure Cosmos DB es compatible con una sola cuenta fija y una clave maestra ya conocida.  La regeneración de claves no es posible en el Emulador de Azure Cosmos DB.
* El Emulador de Azure Cosmos DB no es un servicio escalable y no será compatible con un gran número de colecciones.
* El Emulador de Azure Cosmos DB no simula diferentes [niveles de coherencia de Azure Cosmos DB](consistency-levels.md).
* El Emulador de Azure Cosmos DB no simula [la replicación en varias regiones](distribute-data-globally.md).
* El Emulador de Azure Cosmos DB no es compatible con las invalidaciones de cuotas de servicio que están disponibles en el servicio Azure Cosmos DB (por ejemplo, los límites de tamaño del documento, el mayor almacenamiento de colección con particiones).
* Como la copia del emulador de Azure Cosmos DB puede no contener los cambios más recientes del servicio Azure Cosmos DB, inicie el [planeador de capacidad de Azure Cosmos DB](https://www.documentdb.com/capacityplanner) para calcular con precisión las necesidades de rendimiento de producción (RU) de la aplicación.

## <a name="system-requirements"></a>Requisitos del sistema
El Emulador de Azure Cosmos DB presenta los siguientes requisitos de hardware y software:

* Requisitos de software
  * Windows Server 2012 R2, Windows Server 2016 o Windows 10
*   Requisitos mínimos de hardware
  * 2 GB DE RAM
  * 10 GB de espacio disponible en disco duro

## <a name="installation"></a>Instalación
Puede descargar e instalar el emulador de Azure Cosmos DB desde el [Centro de descarga de Microsoft](https://aka.ms/cosmosdb-emulator) o puede ejecutarlo en Docker para Windows. Para más instrucciones sobre cómo utilizar el emulador en Docker para Windows, consulte [Ejecución en Docker para Windows](#running-on-docker). 

> [!NOTE]
> Para instalar, configurar y ejecutar el Emulador de Azure Cosmos DB, debe tener privilegios administrativos en el equipo.

## <a name="running-on-windows"></a>Ejecución en Windows

Para iniciar el Emulador de Azure Cosmos DB, seleccione el botón Inicio o presione la tecla Windows. Comience a escribir **Emulador de Azure Cosmos DB** y seleccione el emulador de la lista de aplicaciones. 

![Seleccione el botón Inicio o presione la tecla Windows, comience a escribir **Emulador de Azure Cosmos DB** y seleccione el emulador en la lista de aplicaciones.](./media/local-emulator/database-local-emulator-start.png)

Cuando se ejecuta el emulador, verá un icono en el área de notificación de la barra de tareas de Windows. ![Notificación en la barra de tareas del emulador local de Azure Cosmos DB](./media/local-emulator/database-local-emulator-taskbar.png)

El Emulador de Azure Cosmos DB se ejecuta de forma predeterminada en la máquina local ("localhost") que escucha en el puerto 8081.

El Emulador de Azure Cosmos DB se instala de forma predeterminada en el directorio `C:\Program Files\Azure Cosmos DB Emulator`. También puede iniciar y detener el emulador desde la línea de comandos. Vea la [referencia de la herramienta de la línea de comandos](#command-line) para obtener más información.

## <a name="start-data-explorer"></a>Inicio del Explorador de datos

Cuando se inicia el Emulador de Azure Cosmos DB, se abre automáticamente el Explorador de datos de Azure Cosmos DB en su explorador. La dirección aparece como [https://localhost:8081/_explorer/index.html](https://localhost:8081/_explorer/index.html). Si cierra el explorador y desea volver a abrirlo más adelante, puede abrir la dirección URL en el explorador o iniciarla desde el icono de la bandeja de Windows del Emulador de Azure Cosmos DB, tal y como se muestra a continuación.

![Iniciador del Explorador de datos del emulador local de Azure Cosmos DB](./media/local-emulator/database-local-emulator-data-explorer-launcher.png)

## <a name="checking-for-updates"></a>Búsqueda de actualizaciones
En el Explorador de datos se indica si hay una nueva actualización disponible para descarga. 

> [!NOTE]
> No se garantiza que los datos que se crean en una versión del Emulador de Azure Cosmos DB estén disponibles cuando se utilice una versión diferente. Si necesita conservar los datos a largo plazo, es recomendable que almacene esos datos en una cuenta de Azure Cosmos DB y no en el Emulador de Azure Cosmos DB. 

## <a name="authenticating-requests"></a>Autenticación de solicitudes
Al igual que con Azure Cosmos DB en la nube, se deben autenticar todas las solicitudes que se realicen en el Emulador de Azure Cosmos DB. El Emulador de Azure Cosmos DB es compatible con una sola cuenta fija y una clave de autenticación ya conocida para la autenticación de clave maestra. Esta cuenta y clave son las únicas credenciales que se admiten para su uso con el Emulador de Azure Cosmos DB. Son las siguientes:

    Account name: localhost:<port>
    Account key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==

> [!NOTE]
> La clave maestra admitida por el Emulador de Azure Cosmos DB está destinada a su uso exclusivo con el emulador. No puede usar su clave y cuenta de producción de Azure Cosmos DB con el Emulador de Azure Cosmos DB. 

> [!NOTE] 
> Si ha iniciado el emulador con la opción /Key, use la clave generada en lugar de "C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw=="

Además, lo mismo que el servicio Azure Cosmos DB, el Emulador de Azure Cosmos DB solo admite la comunicación segura a través de SSL.

## <a name="running-on-a-local-network"></a>Ejecución en una red local

Puede ejecutar el emulador en una red local. Para habilitar el acceso de red, especifique la opción /AllowNetworkAccess en la [línea de comandos](#command-line-syntax), que también requiere que especifique /Key=key_string o /KeyFile=file_name. Puede usar /GenKeyFile=file_name para generar un archivo con una clave aleatoria por adelantado.  Después, puede pasar /KeyFile=file_name o /Key=contents_of_file.

Para habilitar el acceso de red por primera vez, el usuario debe cerrar el emulador y eliminar el directorio de datos del emulador (C:\Users\user_name\AppData\Local\CosmosDBEmulator).

## <a name="developing-with-the-emulator"></a>Desarrollo con el emulador
Cuando tenga el Emulador de Azure Cosmos DB funcionando en su escritorio, puede usar cualquier [SDK de Azure Cosmos DB](sql-api-sdk-dotnet.md) admitido o la [API de REST de Azure Cosmos DB](/rest/api/cosmos-db/) para interactuar con el Emulador. El emulador de Azure Cosmos DB también incluye un Explorador de datos integrado que le permite crear colecciones de las API de SQL y MongoDB, así como ver y editar documentos sin escribir ningún código.   

    // Connect to the Azure Cosmos DB Emulator running locally
    DocumentClient client = new DocumentClient(
        new Uri("https://localhost:8081"), 
        "C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==");

Si va a utilizar la [compatibilidad del protocolo de Azure Cosmos DB con MongoDB](mongodb-introduction.md), use la siguiente cadena de conexión:

    mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10255/admin?ssl=true

También puede usar herramientas existentes como [Azure DocumentDB Studio](https://github.com/mingaliu/DocumentDBStudio) para conectar con el Emulador de Azure Cosmos DB. También puede migrar datos entre el Emulador de Azure Cosmos DB y el servicio Azure Cosmos DB con la [Herramienta de migración de datos de Azure Cosmos DB](https://github.com/azure/azure-documentdb-datamigrationtool).

> [!NOTE] 
> Si ha iniciado el emulador con la opción /Key, use la clave generada en lugar de "C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw=="

Con el Emulador de Azure Cosmos DB, puede crear de forma predeterminada hasta 25 colecciones de una única partición o una colección con particiones. Para más información acerca de cómo cambiar este valor, consulte la sección sobre la [configuración del valor de PartitionCount](#set-partitioncount).

## <a name="export-the-ssl-certificate"></a>Exportación del certificado SSL

El sistema en tiempo de ejecución y los lenguajes .NET utilizan el almacén de certificados de Windows para conectarse de forma segura al emulador local de Azure Cosmos DB. Otros lenguajes tienen sus propios métodos de administración y uso de certificados. Java utiliza su propio [almacén de certificados](https://docs.oracle.com/cd/E19830-01/819-4712/ablqw/index.html), mientras que Python utiliza [contenedores de sockets](https://docs.python.org/2/library/ssl.html).

Si quiere obtener un certificado que se pueda utilizar con los lenguajes y los tiempos de ejecución sin que se integre con el almacén de certificados de Windows, tendrá que realizar la exportación por medio del administrador de certificados de Windows. Para iniciarlo, ejecute certlm.msc o siga las instrucciones paso a paso de [Exportación de los certificados del Emulador de Azure Cosmos DB](./local-emulator-export-ssl-certificates.md). Una vez que el Administrador de certificados se esté ejecutando, abra los certificados personales, tal y como se muestra a continuación, y exporte el certificado con el nombre descriptivo "DocumentDBEmulatorCertificate" como archivo X.509 codificado en BASE-64 (.cer).

![Certificado SSL del emulador local de Azure Cosmos DB](./media/local-emulator/database-local-emulator-ssl_certificate.png)

El certificado X.509 puede importarse en el almacén de certificados de Java siguiendo las instrucciones de [Incorporación de un certificado al almacén de certificados CA de Java](https://docs.microsoft.com/azure/java-add-certificate-ca-store). Cuando el certificado se haya importado en el almacén de certificados, las aplicaciones de Java y MongoDB podrán conectarse al Emulador de Azure Cosmos DB.

Al conectarse al emulador desde los SDK de Node.js y Python, se deshabilita la verificación de SSL.

## <a id="command-line"></a>Referencia de la herramienta de la línea de comandos
Desde la ubicación de instalación, puede usar la línea de comandos para iniciar y detener el emulador, configurar las opciones y realizar otras operaciones.

### <a name="command-line-syntax"></a>Sintaxis de línea de comandos

    CosmosDB.Emulator.exe [/Shutdown] [/DataPath] [/Port] [/MongoPort] [/DirectPorts] [/Key] [/EnableRateLimiting] [/DisableRateLimiting] [/NoUI] [/NoExplorer] [/?]

Para ver la lista de opciones, escriba `CosmosDB.Emulator.exe /?` en el símbolo del sistema.

<table>
<tr>
  <td><strong>Opción</strong></td>
  <td><strong>Descripción</strong></td>
  <td><strong>Comando</strong></td>
  <td><strong>Argumentos</strong></td>
</tr>
<tr>
  <td>[Sin argumentos]</td>
  <td>Inicia el Emulador de Azure Cosmos DB con la configuración predeterminada.</td>
  <td>CosmosDB.Emulator.exe</td>
  <td></td>
</tr>
<tr>
  <td>[Ayuda]</td>
  <td>Muestra la lista de argumentos de la línea de comandos admitidos.</td>
  <td>CosmosDB.Emulator.exe /?</td>
  <td></td>
</tr>
<tr>
  <td>GetStatus</td>
  <td>Obtiene el estado del emulador de Azure Cosmos DB. El estado se indica mediante el código de salida: 1 = iniciado, 2 = en ejecución, 3 = detenido. Un código de salida negativo indica que se ha producido un error. Se produce ninguna otra salida.</td>
  <td>CosmosDB.Emulator.exe /GetStatus</td>
  <td></td>
<tr>
  <td>Shutdown</td>
  <td>Cierra el Emulador de Azure Cosmos DB.</td>
  <td>CosmosDB.Emulator.exe /Shutdown</td>
  <td></td>
</tr>
<tr>
  <td>DataPath</td>
  <td>Especifica la ruta de acceso en la que se almacenarán los archivos de datos. El valor predeterminado es %LocalAppdata%\CosmosDBEmulator.</td>
  <td>CosmosDB.Emulator.exe /DataPath=&lt;datapath&gt;</td>
  <td>&lt;datapath&gt;: una ruta accesible</td>
</tr>
<tr>
  <td>Port</td>
  <td>Especifica el número de puerto que se utilizará para el emulador.  El valor predeterminado es 8081.</td>
  <td>CosmosDB.Emulator.exe /Port=&lt;port&gt;</td>
  <td>&lt;port&gt;: número de puerto sencillo</td>
</tr>
<tr>
  <td>MongoPort</td>
  <td>Especifica el número de puerto que se utilizará para la API de compatibilidad de MongoDB. El valor predeterminado es 10255.</td>
  <td>CosmosDB.Emulator.exe /MongoPort=&lt;mongoport&gt;</td>
  <td>&lt;mongoport&gt;: número de puerto sencillo</td>
</tr>
<tr>
  <td>DirectPorts</td>
  <td>Especifica los puertos que se usarán para la conectividad directa. Los valores predeterminados son 10251,10252,10253,10254.</td>
  <td>CosmosDB.Emulator.exe /DirectPorts:&lt;directports&gt;</td>
  <td>&lt;directports&gt;: una lista delimitada por comas de 4 puertos</td>
</tr>
<tr>
  <td>Clave</td>
  <td>Clave de autorización para el emulador. La clave debe ser la codificación en base 64 de un vector de 64 bytes.</td>
  <td>CosmosDB.Emulator.exe /Key:&lt;key&gt;</td>
  <td>&lt;key&gt;: la clave debe ser la codificación en base 64 de un vector de 64 bytes</td>
</tr>
<tr>
  <td>EnableRateLimiting</td>
  <td>Especifica que el comportamiento de limitación de velocidad de solicitudes está habilitado.</td>
  <td>CosmosDB.Emulator.exe /EnableRateLimiting</td>
  <td></td>
</tr>
<tr>
  <td>DisableRateLimiting</td>
  <td>Especifica que el comportamiento de limitación de velocidad de solicitudes está deshabilitado.</td>
  <td>CosmosDB.Emulator.exe /DisableRateLimiting</td>
  <td></td>
</tr>
<tr>
  <td>NoUI</td>
  <td>No muestra la interfaz de usuario del emulador.</td>
  <td>CosmosDB.Emulator.exe /NoUI</td>
  <td></td>
</tr>
<tr>
  <td>NoExplorer</td>
  <td>No muestra el Explorador de datos en el inicio.</td>
  <td>CosmosDB.Emulator.exe /NoExplorer</td>
  <td></td>
</tr>
<tr>
  <td>PartitionCount</td>
  <td>Especifica el número máximo de las colecciones particionadas. Consulte [Cambio del número de colecciones](#set-partitioncount) para más información.</td>
  <td>CosmosDB.Emulator.exe /PartitionCount=&lt;partitioncount&gt;</td>
  <td>&lt;partitioncount&gt;: número máximo de colecciones de una sola partición permitidas. El valor predeterminado es 25. El máximo permitido es 250.</td>
</tr>
<tr>
  <td>DefaultPartitionCount</td>
  <td>Especifica el número predeterminado de particiones para una colección con particiones.</td>
  <td>CosmosDB.Emulator.exe /DefaultPartitionCount=&lt;defaultpartitioncount&gt;</td>
  <td>El valor predeterminado de &lt;defaultpartitioncount&gt; es 25.</td>
</tr>
<tr>
  <td>AllowNetworkAccess</td>
  <td>Permite acceder al emulador a través de una red. También debe pasar /Key=&lt;key_string&gt; o /KeyFile=&lt;file_name&gt; para habilitar el acceso de red.</td>
  <td>CosmosDB.Emulator.exe /AllowNetworkAccess /Key=&lt;key_string&gt;<br><br>o<br><br>CosmosDB.Emulator.exe /AllowNetworkAccess /KeyFile=&lt;file_name&gt;</td>
  <td></td>
</tr>
<tr>
  <td>NoFirewall</td>
  <td>No ajuste las reglas de firewall cuando se utiliza /AllowNetworkAccess.</td>
  <td>CosmosDB.Emulator.exe /NoFirewall</td>
  <td></td>
</tr>
<tr>
  <td>GenKeyFile</td>
  <td>Genere una nueva clave de autorización y guárdela en el archivo especificado. La clave generada se puede utilizar con las opciones /Key o /KeyFile.</td>
  <td>CosmosDB.Emulator.exe /GenKeyFile=&lt;ruta de acceso al archivo de clave&gt;</td>
  <td></td>
</tr>
<tr>
  <td>Coherencia</td>
  <td>Defina el nivel de coherencia predeterminado de la cuenta.</td>
  <td>CosmosDB.Emulator.exe /Consistency=&lt;consistency&gt;</td>
  <td>&lt;consistency&gt;: el valor debe ser uno de los siguientes [niveles de coherencia](consistency-levels.md): Session, Strong, Eventual o BoundedStaleness.  El valor predeterminado es Session.</td>
</tr>
<tr>
  <td>?</td>
  <td>Se muestra el mensaje de ayuda.</td>
  <td></td>
  <td></td>
</tr>
</table>

## <a id="set-partitioncount"></a>Cambio del número de colecciones

Con el Emulador de Azure Cosmos DB, puede crear de forma predeterminada hasta 25 colecciones de partición única o una colección con particiones. Al modificar el valor de **PartitionCount**, puede crear hasta 250 colecciones de partición única o 10 colecciones con particiones o cualquier combinación de las dos que no superen las 250 particiones de partición única (donde 1 colección con particiones = 25 colecciones de partición única).

Si intenta crear una colección después de que se haya excedido el recuento de particiones actual, el emulador generará una excepción ServiceUnavailable, con el siguiente mensaje.

    Sorry, we are currently experiencing high demand in this region, 
    and cannot fulfill your request at this time. We work continuously 
    to bring more and more capacity online, and encourage you to try again. 
    Please do not hesitate to email askcosmosdb@microsoft.com at any time or
    for any reason. ActivityId: 29da65cc-fba1-45f9-b82c-bf01d78a1f91

Para cambiar el número de colecciones disponibles en el Emulador de Azure Cosmos DB, haga lo siguiente:

1. Elimine todos los datos del emulador local de Azure Cosmos DB; para ello, haga clic con el botón derecho en el icono del **Emulador de Azure Cosmos DB** de la bandeja del sistema y, luego, haga clic en **Reset Data…** (Restablecer datos...).
2. Elimine todos los datos del emulador en la carpeta C:\Users\user_name\AppData\Local\CosmosDBEmulator.
3. Cierre todas las instancias abiertas; para ello, haga clic con el botón derecho en el icono del **Emulador de Azure Cosmos DB** de la bandeja del sistema y, luego, haga clic en **Salir**. Todas las instancias pueden tardar un minuto en salir.
4. Instale la versión más reciente del [Emulador de Azure Cosmos DB](https://aka.ms/cosmosdb-emulator).
5. Para iniciar el emulador con la marca PartitionCount, establezca un valor <= 250. Por ejemplo: `C:\Program Files\Azure CosmosDB Emulator>CosmosDB.Emulator.exe /PartitionCount=100`.

## <a name="controlling-the-emulator"></a>Control del emulador

El emulador incluye un módulo de PowerShell para iniciar, detener, desinstalar y recuperar el estado del servicio. Para usarla:

```powershell
Import-Module "$env:ProgramFiles\Azure Cosmos DB Emulator\PSModules\Microsoft.Azure.CosmosDB.Emulator"
```

o coloque el directorio `PSModules` en `PSModulesPath` e impórtelo como sigue:

```powershell
$env:PSModulesPath += "$env:ProgramFiles\Azure Cosmos DB Emulator\PSModules"
Import-Module Microsoft.Azure.CosmosDB.Emulator
```

Este es un resumen de los comandos de control del emulador desde PowerShell:

### `Get-CosmosDbEmulatorStatus`

#### <a name="syntax"></a>Sintaxis

`Get-CosmosDbEmulatorStatus`

#### <a name="remarks"></a>Comentarios

Devuelve uno de estos valores de ServiceControllerStatus: ServiceControllerStatus.StartPending, ServiceControllerStatus.Running o ServiceControllerStatus.Stopped.

### `Start-CosmosDbEmulator`

#### <a name="syntax"></a>Sintaxis

`Start-CosmosDbEmulator [-DataPath <string>] [-DefaultPartitionCount <uint16>] [-DirectPort <uint16[]>] [-MongoPort <uint16>] [-NoUI] [-NoWait] [-PartitionCount <uint16>] [-Port <uint16>]  [<CommonParameters>]`

#### <a name="remarks"></a>Comentarios

Inicia el emulador. De forma predeterminada, el comando espera hasta que el emulador está listo para aceptar solicitudes. Utilice la opción -NoWait, si desea que el cmdlet realice la devolución en cuanto inicie el emulador.

### `Stop-CosmosDbEmulator`

#### <a name="syntax"></a>Sintaxis

 `Stop-CosmosDbEmulator [-NoWait]`

#### <a name="remarks"></a>Comentarios

Detiene el emulador. De forma predeterminada, este comando espera hasta que el emulador esté completamente apagado. Utilice la opción -NoWait, si desea que el cmdlet realice la devolución en cuanto el emulador comience el apagado.

### `Uninstall-CosmosDbEmulator`

#### <a name="syntax"></a>Sintaxis

`Uninstall-CosmosDbEmulator [-RemoveData]`

#### <a name="remarks"></a>Comentarios

Desinstala el emulador y, opcionalmente, quita todo el contenido de $env: LOCALAPPDATA\CosmosDbEmulator.
El cmdlet garantiza que el emulador se detiene antes de desinstalarlo.

## <a name="running-on-docker"></a>Ejecución en Docker

El Emulador de Azure Cosmos DB se puede ejecutar en Docker para Windows. El emulador no funciona en Docker para Oracle Linux.

Una vez que haya instalado [Docker para Windows](https://www.docker.com/docker-windows), cambie a contenedores de Windows haciendo clic con el botón derecho en el icono de Docker en la barra de herramientas, y seleccione **Switch to Windows containers** (Cambiar a contenedores de Windows).

A continuación, extraiga la imagen del emulador de Docker Hub ejecutando el siguiente comando desde su shell favorito.

```     
docker pull microsoft/azure-cosmosdb-emulator 
```
Para iniciar la imagen, ejecute los siguientes comandos.

Desde la línea de comandos:
```cmd 
md %LOCALAPPDATA%\CosmosDBEmulatorCert 2>null
docker run -v %LOCALAPPDATA%\CosmosDBEmulatorCert:c:\CosmosDBEmulator\CosmosDBEmulatorCert -P -t -i -m 2GB microsoft/azure-cosmosdb-emulator 
```

Desde PowerShell:
```powershell
md $env:LOCALAPPDATA\CosmosDBEmulatorCert 2>null
docker run -v $env:LOCALAPPDATA\CosmosDBEmulatorCert:c:\CosmosDBEmulator\CosmosDBEmulatorCert -P -t -i -m 2GB microsoft/azure-cosmosdb-emulator 
```

La respuesta será similar a la siguiente:

```
Starting Emulator
Emulator Endpoint: https://172.20.229.193:8081/
Master Key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
Exporting SSL Certificate
You can import the SSL certificate from an administrator command prompt on the host by running:
cd /d %LOCALAPPDATA%\CosmosDBEmulatorCert
powershell .\importcert.ps1
--------------------------------------------------------------------------------------------------
Starting interactive shell
``` 

Ahora, use el punto de conexión y la clave maestra de la respuesta en el cliente e importe el certificado SSL en el host. Para importar el certificado SSL, haga lo siguiente desde un símbolo del sistema de administración:

Desde la línea de comandos:
```cmd 
cd %LOCALAPPDATA%\CosmosDBEmulatorCert
powershell .\importcert.ps1
```

Desde PowerShell:
```powershell
cd $env:LOCALAPPDATA\CosmosDBEmulatorCert
.\importcert.ps1
```

Si cierra el shell interactivo una vez iniciado el emulador, se cerrará el contenedor de este.

Para abrir el Explorador de datos vaya a la siguiente dirección URL en el explorador. El punto de conexión del emulador se proporciona en el mensaje de respuesta mostrado anteriormente.

    https://<emulator endpoint provided in response>/_explorer/index.html


## <a name="troubleshooting"></a>solución de problemas

Use las siguientes sugerencias para solucionar los problemas que puedan surgir con el Emulador de Azure Cosmos DB:

- Si se instala una nueva versión del emulador y se experimentan errores, asegúrese de restablecer los datos. Para restablecer los datos, haga clic con el botón derecho en el icono del Emulador de Azure Cosmos DB de la bandeja del sistema y, a continuación, haga clic en Restablecer datos.... Si no se solucionan los errores, puede desinstalar y volver a instalar la aplicación. Consulte [Desinstalación del emulador local](#uninstall) para obtener instrucciones.

- Si el Emulador de Azure Cosmos DB se bloquea, recopile los archivos de volcado de memoria de la carpeta c:\Users\user_name\AppData\Local\CrashDumps, comprímalos y adjúntelos a un correo electrónico para [askcosmosdb@microsoft.com ](mailto:askcosmosdb@microsoft.com).

- Si experimenta bloqueos en CosmosDB.StartupEntryPoint.exe, ejecute el siguiente comando desde un símbolo del sistema de administración: `lodctr /R` 

- Si se produce un problema de conectividad, [recopile los archivos de seguimiento](#trace-files), comprímalos y adjúntelos a un correo electrónico y envíelo a [ askcosmosdb@microsoft.com ](mailto:askcosmosdb@microsoft.com).

- Si recibe un mensaje de **Servicio no disponible**, es posible que el emulador no pueda inicializar la pila de red. Compruebe si tiene instalado el cliente de Pulse Secure o el cliente de Juniper Networks, ya que sus controladores de filtro de red podrían ser la causa del problema. Normalmente el problema se corrige si se desinstalan los controladores de filtro de red de otros fabricantes.

### <a id="trace-files"></a>Recopilación de archivos de seguimiento

Para recopilar los seguimientos de depuración, ejecute los siguientes comandos desde un símbolo del sistema administrativo:

1. `cd /d "%ProgramFiles%\Azure Cosmos DB Emulator"`
2. `CosmosDB.Emulator.exe /shutdown`. Vea la bandeja del sistema para asegurarse de que el programa se ha cerrado; esto podría tardar un minuto. Puede también hacer clic en **Salir** en la interfaz de usuario del Emulador de Azure Cosmos DB.
3. `CosmosDB.Emulator.exe /starttraces`
4. `CosmosDB.Emulator.exe`
5. Reproduzca el problema. Si el Explorador de datos no funciona, solo necesitará esperar a que el explorador se abra durante unos segundos para detectar el error.
5. `CosmosDB.Emulator.exe /stoptraces`
6. Vaya a `%ProgramFiles%\Azure Cosmos DB Emulator` y localice el archivo docdbemulator_000001.etl.
7. Envíe el archivo .etl junto con los pasos para reproducirlo a [ askcosmosdb@microsoft.com ](mailto:askcosmosdb@microsoft.com) para la depuración.

### <a id="uninstall"></a>Desinstalación del emulador local

1. Cierre todas las instancias abiertas del emulador local; para ello, haga clic con el botón derecho en el icono del Emulador de Azure Cosmos DB de la bandeja del sistema y, luego, haga clic en Salir. Todas las instancias pueden tardar un minuto en salir.
2. En el cuadro de búsqueda de Windows, escriba **Aplicaciones y características** y haga clic en el resultado **Aplicaciones y características (configuración del sistema)**.
3. En la lista de aplicaciones, desplácese a **Emulador de Azure Cosmos DB**, selecciónela, haga clic en **Desinstalar**, confirme y haga clic en **Desinstalar** de nuevo.
4. Cuando se desinstale la aplicación, vaya a C:\Users\<usuario> \AppData\Local\CosmosDBEmulator y elimine la carpeta. 

## <a name="change-list"></a>Lista de cambios

Para comprobar el número de versión haga clic con el botón derecho en el icono del emulador local de la barra de tareas y seleccione el elemento de menú Acerca de.

### <a name="12106-released-on-march-27-2018"></a>1.21.0.6, publicada el 27 de marzo de 2018

Además de actualizar los servicios del emulador para igualar con los servicios en la nube de Cosmos DB, hemos incluido una característica nueva y dos correcciones de errores en esta versión.

#### <a name="features"></a>Características

1. El comando Start-CosmosDbEmulator ahora incluye opciones de inicio.

#### <a name="bug-fixes"></a>Corrección de errores

1. El módulo de PowerShell Microsoft.Azure.CosmosDB.Emulator ahora garantiza que se cargue la enumeración de `ServiceControllerStatus`.

2. El módulo de PowerShell Microsoft.Azure.CosmosDB.Emulator incluye ahora un manifiesto; omisión de la primera versión.

### <a name="1201084-released-on-february-14-2018"></a>1.20.108.4 publicada el 14 de febrero de 2018

Hay una nueva característica y dos correcciones de errores en esta versión. Gracias a los clientes que nos ayudaron a encontrar y corregir estos problemas.

#### <a name="bug-fixes"></a>Corrección de errores

1. El emulador ahora funciona en equipos con 1 o 2 núcleos (o CPU virtuales)

   Cosmos DB asigna tareas para llevar a cabo varios servicios. El número de tareas asignado es un múltiplo del número de núcleos de un host. El múltiplo predeterminado funciona correctamente en entornos de producción donde el número de núcleos es grande. Sin embargo, en máquinas con uno o dos procesadores, no se asignan tareas que lleven a cabo estos servicios cuando se aplica el múltiplo.

   Lo hemos corregido al incorporar una invalidación de la configuración en el emulador. Ahora se aplica un múltiplo de 1. El número de tareas asignado para llevar a cabo distintos servicios ahora es igual al número de núcleos de un host.

   Para poder abordar este problema, en esta versión no hemos hecho nada más. Creemos que muchos entornos de desarrollo/pruebas que hospedan el emulador tienen 1 o 2 núcleos.

2. El emulador ya no necesita la instalación de Microsoft Visual C++ 2015 Redistributable.

   Se descubrió que las instalaciones nuevas de Windows (versiones de escritorio y servidor) no incluye este paquete. Por lo tanto, ahora se agrupan los archivos binarios redistribuibles del paquete con el emulador.

#### <a name="features"></a>Características

Muchos de los clientes con los que hemos hablado han dicho que sería estupendo si el emulador permitiera ejecutar scripts. Por lo tanto, en esta versión hemos agregado cierta capacidad de ejecución de scripts. El emulador ahora incluye un módulo de PowerShell para el inicio, la detención, la obtención del estado y la desinstalación de sí mismo: `Microsoft.Azure.CosmosDB.Emulator`. 

### <a name="120911-released-on-january-26-2018"></a>1.20.91.1 publicada el 26 de enero de 2018

* Se ha habilitado la canalización de agregación de MongoDB de forma predeterminada.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha hecho lo siguiente:

> [!div class="checklist"]
> * Ha instalado el emulador local
> * Ha ejecutado el Emulador en Docker para Windows
> * Ha autenticado solicitudes
> * Ha usado el Explorador de datos en el Emulador
> * Ha exportado certificados SSL
> * Ha llamado al Emulador desde la línea de comandos
> * Ha recopilado archivos de seguimiento

En este tutorial, ha aprendido a usar el emulador local para el desarrollo local gratuito. Ahora puede pasar al siguiente tutorial y aprender a exportar certificados SSL del Emulador. 

> [!div class="nextstepaction"]
> [Exportación de los certificados del Emulador de Azure Cosmos DB](local-emulator-export-ssl-certificates.md)
