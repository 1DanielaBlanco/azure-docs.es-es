---
title: Tipos de salidas desde trabajos de Azure Stream Analytics
description: Aprenda a seleccionar el destino de las opciones de salida de datos de Stream Analytics, lo que incluye Power BI para los resultados de análisis.
services: stream-analytics
author: jasonwhowell
ms.author: jasonh
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/16/2018
ms.openlocfilehash: 30fa7e081c24339b7fa9f572d9feb25a0f920a86
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/18/2018
---
# <a name="stream-analytics-outputs-options-for-storage-and-analysis"></a>Salidas de Stream Analytics: opciones para almacenamiento y análisis
Al crear un trabajo de Stream Analytics, tenga en cuenta cómo se consumen los datos resultantes. ¿Cómo se ven los resultados del trabajo de Stream Analytics y dónde se almacenan?

Para habilitar diversos patrones de aplicación, Azure Stream Analytics ofrece distintas opciones para el almacenamiento y la visualización de los resultados del trabajo. Esto facilita la visualización de la salida del trabajo y proporciona flexibilidad en el consumo y almacenamiento de la salida del trabajo para el almacenamiento de datos y otros fines. Todos los elementos de salida que se configuren en el trabajo deben existir antes de que se inicie el trabajo y los eventos empiecen a fluir. Por ejemplo, si usa el almacenamiento de blobs como salida, el trabajo no crea una cuenta de almacenamiento automáticamente. Cree una cuenta de almacenamiento antes de iniciar el trabajo de Stream Analytics.

## <a name="azure-data-lake-store"></a>Almacén de Azure Data Lake
Stream Analytics admite el [Almacén de Azure Data Lake](https://azure.microsoft.com/services/data-lake-store/). El Almacén de Azure Data Lake es un repositorio de gran escala en toda la empresa para cargas de trabajo de análisis de macrodatos. El Almacén de Data Lake permite almacenar datos de cualquier tamaño, tipo y velocidad de ingesta para realizar análisis exploratorios y operativos. Además, Stream Analytics debe tener autorización para acceder a Data Lake Store.

### <a name="authorize-an-azure-data-lake-store-account"></a>Autorización de una cuenta de Azure Data Lake Store

1. Al seleccionar Data Lake Storage como salida en Azure Portal, se le va a pedir que autorice una conexión a una instancia existente de Data Lake Store.  

   ![Autorizar el Almacén de Azure Data Lake](./media/stream-analytics-define-outputs/06-stream-analytics-define-outputs.png)  

2. Si ya dispone de acceso a Data Lake Store, haga clic en "Autorizar ahora" y se mostrará una página con el mensaje "Redirigiendo a la autorización...". Cuando la autorización se ha realizado correctamente, se le presenta la página que le permite configurar la salida de Data Lake Store.  

3. Una vez que se haya autenticado la cuenta del Almacén de Data Lake, podrá configurar las propiedades para su salida del Almacén de Data Lake. En la siguiente tabla podrá encontrar una lista de nombres de propiedades y su descripción para configurar la salida del Almacén de Data Lake.

   ![Autorizar el Almacén de Azure Data Lake](./media/stream-analytics-define-outputs/07-stream-analytics-define-outputs.png)  

<table>
<tbody>
<tr>
<td><B>NOMBRE DE LA PROPIEDAD</B></td>
<td><B>DESCRIPCIÓN</B></td>
</tr>
<tr>
<td>Alias de salida</td>
<td>Es un nombre descriptivo utilizado en las consultas para dirigir la salida de la consulta a esta instancia de Data Lake Store.</td>
</tr>
<tr>
<td>Nombre de cuenta</td>
<td>El nombre de la cuenta de Data Lake Storage donde va a enviar la salida. Se le presentará una lista desplegable de cuentas de Data Lake Store que están disponibles en la suscripción.</td>
</tr>
<tr>
<td>Patrón del prefijo de la ruta de acceso</td>
<td>La ruta de acceso utilizada para escribir sus archivos en la cuenta del Almacén de Data Lake especificada. Puede especificar una o más instancias de las variables {date} y {time}.<BR> Ejemplo 1: carpeta1/registros/{date}/{time}<BR>Ejemplo 2: carpeta1/registros/{date}<BR>Además, estas son las situaciones en las que se crea un archivo nuevo:<BR>1. Cambio en el esquema de salida <BR>2. Reinicio externo o interno de un trabajo<BR><BR>Además, si el patrón de la ruta de acceso al archivo no contiene un carácter "/" final, el patrón final de la ruta de acceso al archivo se considera como un prefijo del nombre de archivo.<BR></td>
</tr>
<tr>
<td>Formato de fecha [<I>opcional</I>]</td>
<td>Si el token de fecha se usa en la ruta de acceso de prefijo, puede seleccionar el formato de fecha en el que se organizan los archivos. Ejemplo: AAAA/MM/DD</td>
</tr>
<tr>
<td>Formato de hora [<I>opcional</I>]</td>
<td>Si el token de hora se usa en la ruta de acceso de prefijo, puede seleccionar el formato de hora en el que se organizan los archivos. Actualmente, el único valor admitido es HH.</td>
</tr>
<tr>
<td>Formato de serialización de eventos</td>
<td>Formato de serialización para los datos de salida. Se admiten JSON, CSV y Avro.</td>
</tr>
<tr>
<td>Encoding</td>
<td>Si se usa el formato CSV o JSON, debe especificarse una codificación. Por el momento, UTF-8 es el único formato de codificación compatible.</td>
</tr>
<tr>
<td>Delimitador</td>
<td>Solo se aplica para la serialización de CSV. Stream Analytics admite un número de delimitadores comunes para la serialización de datos CSV. Los valores admitidos son la coma, punto y coma, espacio, tabulador y barra vertical.</td>
</tr>
<tr>
<td>Formato</td>
<td>Solo se aplica para la serialización de JSON. Separado por líneas especifica que la salida se formatea de tal forma que cada objeto JSON esté separado por una línea nueva. La matriz especifica que la salida se formatea como una matriz de objetos JSON. Esta matriz se cierra cuando el trabajo se detenga o Stream Analytics haya pasado a la siguiente ventana de tiempo. En general, es preferible utilizar JSON separado con líneas, ya que no requiere ningún control especial mientras todavía se esté escribiendo en el archivo de salida.</td>
</tr>
</tbody>
</table>

### <a name="renew-data-lake-store-authorization"></a>Renovación de la autorización del Almacén de Data Lake
Tiene que volver a autenticar la cuenta de Data Lake Store si su contraseña ha cambiado desde que se creó o autenticó por última vez su trabajo. Si no se vuelve a autenticar, el trabajo no generará resultados y se registrará un error que indica la necesidad de volver a autorizar en los registros de operaciones. Actualmente, existe una limitación según la que el token de autenticación debe actualizarse manualmente cada 90 días para todos los trabajos con salida del Almacén de Data Lake. 

Para renovar la autorización, **detenga** el trabajo > vaya a la salida de Data Lake Store > haga clic en el vínculo **Renovar autorización** y, durante unos segundos, se mostrará una página con el mensaje “Redirigiendo a la autorización...”. La página se cerrará automáticamente y, si la operación se realiza correctamente, indicará “La autorización se ha renovado correctamente”. Después, debe hacer clic en **Guardar** en la parte inferior de la página y podrá continuar reiniciando el trabajo desde la **hora de la última detención** para evitar la pérdida de datos.

![Autorizar el Almacén de Azure Data Lake](./media/stream-analytics-define-outputs/08-stream-analytics-define-outputs.png)  

## <a name="sql-database"></a>SQL Database
[Azure SQL Database](https://azure.microsoft.com/services/sql-database/) como salida de datos que son relacionales por naturaleza o de aplicaciones que dependen del contenido hospedado en una base de datos relacional. Los trabajos de Stream Analytics se escriben en una tabla existente en Azure SQL Database.  El esquema de tabla debe coincidir exactamente con los campos y los tipos de salida del trabajo. También es posible especificar [Azure SQL Data Warehouse](https://azure.microsoft.com/documentation/services/sql-data-warehouse/) como la salida mediante la opción de salida de SQL Database. En la tabla siguiente se enumeran los nombres de propiedad y su descripción para crear una salida de SQL Database.

| Nombre de propiedad | DESCRIPCIÓN |
| --- | --- |
| Alias de salida |Un nombre descriptivo usado en las consultas para dirigir la salida de la consulta a esta base de datos. |
| Base de datos |El nombre de la base de datos a donde está enviando la salida |
| Nombre del servidor |El nombre del servidor Azure SQL Database |
| Nombre de usuario |El nombre del usuario, con permiso para escribir en la base de datos |
| Password |La contraseña para conectarse a la base de datos |
| Tabla |El nombre de la tabla donde se escribe la salida. El nombre de tabla distingue mayúsculas de minúsculas y el esquema de esta tabla debe coincidir exactamente con el número de campos y tipos que va a generar la salida del trabajo. |

> [!NOTE]
> Actualmente, la oferta de Azure SQL Database se admite para una salida de trabajo de Stream Analytics. Sin embargo, no se admite una máquina virtual de Azure que ejecute SQL Server con una base de datos asociada. Esto está sujeto a cambios en versiones futuras.
> 
> 

## <a name="blob-storage"></a>Almacenamiento de blobs
Almacenamiento de blobs ofrece una solución rentable y escalable para almacenar grandes cantidades de datos no estructurados en la nube.  Para obtener una introducción sobre el almacenamiento de blobs de Azure y su uso, consulte la documentación en [Uso de blobs](../storage/blobs/storage-dotnet-how-to-use-blobs.md).

En la tabla siguiente se enumeran los nombres de propiedad y su descripción para crear una salida de blob.

<table>
<tbody>
<tr>
<td>Nombre de propiedad</td>
<td>Description</td>
</tr>
<tr>
<td>Alias de salida</td>
<td>Un nombre descriptivo usado en las consultas para dirigir la salida de la consulta a este almacenamiento de blobs.</td>
</tr>
<tr>
<td>Cuenta de almacenamiento</td>
<td>El nombre de la cuenta de almacenamiento a donde está enviando la salida</td>
</tr>
<tr>
<td>Clave de cuenta de almacenamiento</td>
<td>La clave secreta asociada con la cuenta de almacenamiento.</td>
</tr>
<tr>
<td>Contenedor de almacenamiento</td>
<td>Los contenedores proporcionan una agrupación lógica de los blobs almacenados en Microsoft Azure Blob service. Cuando carga un blob a Blob service, debe especificar un contenedor para ese blob.</td>
</tr>
<tr>
<td>Patrón del prefijo de la ruta de acceso [opcional]</td>
<td>El patrón de la ruta de acceso al archivo para escribir los blobs dentro del contenedor especificado. <BR> En el patrón de la ruta de acceso, puede elegir usar una o más instancias de las dos variables siguientes para especificar la frecuencia con la que se escriben los blobs: <BR> {date}, {time} <BR> Ejemplo 1: cluster1/logs/{date}/{time} <BR> Ejemplo 2: cluster1/logs/{date} <BR> <BR> La nomenclatura de los archivos sigue la convención siguiente: <BR> {Patrón del prefijo de la ruta de acceso}/schemaHashcode_Guid_Number.extension <BR> <BR> Archivos de salida de ejemplo: <BR> Myoutput/20170901/00/45434_gguid_1.csv <BR> Myoutput/20170901/01/45434_gguid_1.csv <BR> <BR> Además, estas son las situaciones en las que se crea un archivo nuevo: <BR> 1. El archivo actual excede el número de bloques máximo permitido (actualmente, 50 000) <BR> 2. Cambio en el esquema de salida <BR> 3. Reinicio externo o interno de un trabajo  </td>
</tr>
<tr>
<td>Formato de fecha [opcional]</td>
<td>Si el token de fecha se usa en la ruta de acceso de prefijo, puede seleccionar el formato de fecha en el que se organizan los archivos. Ejemplo: AAAA/MM/DD</td>
</tr>
<tr>
<td>Formato de hora [opcional]</td>
<td>Si el token de hora se usa en la ruta de acceso de prefijo, puede seleccionar el formato de hora en el que se organizan los archivos. Actualmente, el único valor admitido es HH.</td>
</tr>
<tr>
<td>Formato de serialización de eventos</td>
<td>Formato de serialización para los datos de salida.  Se admiten JSON, CSV y Avro.</td>
</tr>
<tr>
<td>Encoding</td>
<td>Si se usa el formato CSV o JSON, debe especificarse una codificación. Por el momento, UTF-8 es el único formato de codificación compatible.</td>
</tr>
<tr>
<td>Delimitador</td>
<td>Solo se aplica para la serialización de CSV. Stream Analytics admite un número de delimitadores comunes para la serialización de datos CSV. Los valores admitidos son la coma, punto y coma, espacio, tabulador y barra vertical.</td>
</tr>
<tr>
<td>Formato</td>
<td>Solo se aplica para la serialización de JSON. Separado por líneas especifica que la salida se formatea de tal forma que cada objeto JSON esté separado por una línea nueva. La matriz especifica que la salida se formatea como una matriz de objetos JSON. Esta matriz se cierra cuando el trabajo se detenga o Stream Analytics haya pasado a la siguiente ventana de tiempo. En general, es preferible utilizar JSON separado con líneas, ya que no requiere ningún control especial mientras todavía se esté escribiendo en el archivo de salida.</td>
</tr>
</tbody>
</table>

Cuando se utiliza el almacenamiento de blobs como salida, se crea un nuevo archivo en el blob en los siguientes casos:

* Si el archivo supera el número máximo de bloques permitidos (tenga en cuenta que se puede alcanzar el número máximo permitido de bloques sin alcanzar el tamaño máximo de blobs permitidos. Por ejemplo, si la velocidad de salida es alta, puede ver más bytes por bloque y el tamaño de archivo es mayor. Si la velocidad de salida es baja, cada bloque tendrá menos datos y el tamaño de archivo es menor).  
* Si hay un cambio de esquema en la salida y el formato de salida requiere un esquema fijo (CSV y Avro).  
* Si se reinicia un trabajo de forma externa o interna.  
* Si la consulta está completamente particionada, se crea un nuevo archivo para cada partición de salida.  
* Si el usuario elimina un archivo o un contenedor de la cuenta de almacenamiento.  
* Si la salida está particionada en el tiempo mediante el patrón de prefijo de ruta de acceso, se usa un nuevo blob cuando la consulta pasa a la hora siguiente.

## <a name="event-hub"></a>Centro de eventos
[Event Hubs](https://azure.microsoft.com/services/event-hubs/) es un servicio de introducción de eventos de publicación-suscripción altamente escalable. Puede recopilar millones de eventos por segundo. Un uso del centro de eventos como salida es cuando la salida de un trabajo de Stream Analytics se convierte en la entrada de otro trabajo de streaming.

Hay unos cuantos parámetros que son necesarios para configurar los flujos de datos del Centro de eventos como salida.

| Nombre de propiedad | DESCRIPCIÓN |
| --- | --- |
| Alias de salida |Un nombre descriptivo usado en las consultas para dirigir la salida de la consulta a este Centro de eventos. |
| Espacio de nombres de Service Bus |Un espacio de nombres de Service Bus es un contenedor para un conjunto de entidades de mensajería. Al crear un nuevo centro de eventos, también se crea un espacio de nombres de Service Bus. |
| Centro de eventos |El nombre de la salida del Centro de eventos |
| Nombre de la directiva del centro de eventos |La directiva de acceso compartido, que se puede crear en la pestaña Configuración de Centro de eventos. Cada directiva de acceso compartido tiene un nombre, los permisos establecidos y las claves de acceso |
| Clave de la directiva de centro de eventos |La clave de acceso compartido usada para autenticar el acceso al espacio de nombres de Service Bus. |
| Columna Clave de partición [opcional] |Esta columna contiene la clave de partición para la salida del Centro de eventos. |
| Formato de serialización de eventos |Formato de serialización para los datos de salida.  Se admiten JSON, CSV y Avro. |
| Encoding |Por el momento, UTF-8 es el único formato de codificación compatible para CSV y JSON. |
| Delimitador |Solo se aplica para la serialización de CSV. Stream Analytics admite un número de delimitadores comunes para la serialización de datos en formato CSV. Los valores admitidos son la coma, punto y coma, espacio, tabulador y barra vertical. |
| Formato |Solo se aplica para la serialización de JSON. Separado por líneas especifica que la salida se formatea de tal forma que cada objeto JSON esté separado por una línea nueva. La matriz especifica que la salida se formatea como una matriz de objetos JSON. Esta matriz se cierra cuando el trabajo se detenga o Stream Analytics haya pasado a la siguiente ventana de tiempo. En general, es preferible utilizar JSON separado con líneas, ya que no requiere ningún control especial mientras todavía se esté escribiendo en el archivo de salida. |

## <a name="power-bi"></a>Power BI
[Power BI](https://powerbi.microsoft.com/) como salida para un trabajo de Stream Analytics a fin de ofrecer una amplia experiencia de visualización de los resultados del análisis. Esta funcionalidad puede usarse con paneles operativos, generación de informes e informes basados en métricas.

### <a name="authorize-a-power-bi-account"></a>Autorización de una cuenta de Power BI
1. Cuando se selecciona Power BI como salida en Azure Portal, se le pide que autorice a un usuario de Power BI o que cree una nueva cuenta de Power BI.  
   
   ![Autorización de un usuario de Power BI](./media/stream-analytics-define-outputs/01-stream-analytics-define-outputs.png)  
2. Cree una cuenta nueva si no aún no tiene una y después haga clic en Autorizar ahora.  Aparecerá una pantalla similar a la siguiente.  
   
   ![Cuenta de Power BI de Azure](./media/stream-analytics-define-outputs/02-stream-analytics-define-outputs.png)  
3. En este paso, proporcione la cuenta profesional o educativa para la autorización de la salida de Power BI. Si aún no se ha registrado en Power BI, elija Registrarse ahora. La cuenta profesional o educativa que use para Power BI podría ser diferente de la cuenta de suscripción de Azure en la que ha iniciado sesión.

### <a name="configure-the-power-bi-output-properties"></a>Configuración de las propiedades de salida de Power BI
Cuando la cuenta de Power BI esté autenticada, puede configurar las propiedades de la salida de Power BI. La tabla siguiente muestra la lista de nombres de propiedad y su descripción para configurar la salida de Power BI.

| Nombre de propiedad | DESCRIPCIÓN |
| --- | --- |
| Alias de salida |Un nombre descriptivo usado en las consultas para dirigir la salida de la consulta a esta salida de Power BI. |
| Área de trabajo de grupo |Para habilitar el uso compartido de datos con otros usuarios de Power BI, puede seleccionar grupos dentro de su cuenta de Power BI o elegir "Mi área de trabajo" si no quiere escribir en un grupo.  Actualizar un grupo existente requiere renovar la autenticación de Power BI. |
| Nombre del conjunto de datos |Proporcione un nombre del conjunto de datos que desea que tenga la salida de Power BI. |
| Nombre de la tabla |Proporcione un nombre de tabla en el conjunto de datos de la salida de Power BI. Actualmente, la salida de Power BI de trabajos de Stream Analytics solo puede tener una tabla en un conjunto de datos. |

Para visualizar un tutorial sobre la configuración de una salida de Power BI y un panel, consulte el artículo [Azure Stream Analytics y Power BI](stream-analytics-power-bi-dashboard.md).

> [!NOTE]
> No cree explícitamente el conjunto de datos y la tabla en el panel de Power BI. El conjunto de datos y la tabla se rellena automáticamente cuando se inicie el trabajo y este inicie el bombeo de la salida en Power BI. Observe que si la consulta de trabajo no genera ningún resultado, el conjunto de datos y la tabla no se crea. Tenga en cuenta asimismo que si Power BI ya cuenta con un conjunto de datos y una tabla con el mismo nombre que el proporcionado en este trabajo de Stream Analytics, se sobrescriben los datos existentes.
> 
> 

### <a name="schema-creation"></a>Creación de esquemas
Azure Stream Analytics crea una tabla y un conjunto de datos de Power BI en nombre del usuario si todavía no existe. En todos los demás casos, la tabla se actualiza con los nuevos valores. Actualmente, hay una limitación acerca de que solo puede existir una tabla dentro de un conjunto de datos.

### <a name="data-type-conversion-from-stream-analytics-to-power-bi"></a>Conversión de tipo de datos de Stream Analytics a Power BI
Azure Stream Analytics actualiza el modelo de datos dinámicamente en tiempo de ejecución si cambia el esquema de salida. Se realiza un seguimiento de los cambios de nombre de columna, los cambios de tipo de columna y la adición o eliminación de columnas.

Esta tabla cubre las conversiones de tipos de datos de [tipos de datos de Stream Analytics](https://msdn.microsoft.com/library/azure/dn835065.aspx) a [tipos de Entity Data Model (EDM)](https://powerbi.microsoft.com/documentation/powerbi-developer-walkthrough-push-data/) de Power BI si no existen una tabla y un conjunto de datos de POWER BI.


De Stream Analytics | A Power BI
-----|-----|------------
bigint | Int64
nvarchar(max) | string
Datetime | DateTime
float | Doble
Matriz de registro | Tipo cadena, valor constante “IRecord” o “IArray”

### <a name="schema-update"></a>Actualización de esquema
Stream Analytics deduce el esquema de modelo de datos basándose en el primer conjunto de eventos en la salida. Más adelante, si es necesario, el esquema de modelo de datos se actualiza para dar cabida a los eventos entrantes que pueden encajar en el esquema original.

La consulta `SELECT *` debería evitarse para impedir la actualización de esquema dinámica en las filas. Además de las posibles implicaciones de rendimiento, también podría dar lugar a la incertidumbre de la hora que se toma para los resultados. Se deben seleccionar los campos exactos que necesitan mostrarse en el panel de Power BI. Además, los valores de datos deben ser compatibles con el tipo de datos elegido.


Anterior o actual | Int64 | string | DateTime | Doble
-----------------|-------|--------|----------|-------
Int64 | Int64 | string | string | Doble
Doble | Doble | string | string | Doble
string | string | string | string |  | string | 
DateTime | string | string |  DateTime | string


### <a name="renew-power-bi-authorization"></a>Renovación de la autorización de Power BI
Tendrá que volver a autenticar la cuenta de Power BI si la contraseña ha cambiado desde que se creó el trabajo o desde la última autenticación. Si Multi-Factor Authentication (MFA) se configura en el inquilino de Azure Active Directory (AAD), también debe renovar la autorización de Power BI cada dos semanas. Un síntoma de este problema es la ausencia de salidas de trabajos y un "error de autenticación de usuario" en los registros de operaciones:

  ![Error de token de actualización de Power BI](./media/stream-analytics-define-outputs/03-stream-analytics-define-outputs.png)  

Para resolver este problema, detenga su trabajo en ejecución y vaya a la salida de Power BI.  Haga clic en el vínculo "Renovar autorización" y reinicie el trabajo desde la hora en que se detuvo por última vez para evitar la pérdida de datos.

  ![Renovación de autorización de Power BI](./media/stream-analytics-define-outputs/04-stream-analytics-define-outputs.png)  

## <a name="table-storage"></a>Table Storage
[Azure Table Storage](../storage/common/storage-introduction.md) ofrece un tipo de almacenamiento de alta disponibilidad y escalabilidad masiva, de forma que las aplicaciones pueden escalarse automáticamente para ajustarse a la demanda de los usuarios. Almacenamiento de tablas es un almacén de claves/atributos NoSQL de Microsoft que puede aprovechar para datos estructurados con menos restricciones en el esquema. El almacenamiento de tablas de Azure puede usarse para almacenar datos con de persistencia y recuperación eficaz.

En la tabla siguiente se enumeran los nombres de propiedad y su descripción para crear una salida de tabla.

| Nombre de propiedad | DESCRIPCIÓN |
| --- | --- |
| Alias de salida |Un nombre descriptivo usado en las consultas para dirigir la salida de la consulta a este almacenamiento de tablas. |
| Cuenta de almacenamiento |El nombre de la cuenta de almacenamiento a donde está enviando la salida |
| Clave de cuenta de almacenamiento |La clave de acceso asociada con la cuenta de almacenamiento. |
| Nombre de la tabla |El nombre de la tabla. Se crea la tabla si no existe. |
| Partition Key |El nombre de la columna de salida que contiene la clave de partición. La clave de partición es un identificador único de la partición dentro de una tabla determinada que constituye la primera parte de la clave principal de la entidad. Es un valor de cadena que puede tener un tamaño de hasta 1 KB. |
| Row Key |El nombre de la columna de salida que contiene la clave de la fila. La clave de fila es un identificador único de una identidad dentro de una partición determinada. Forma la segunda parte de la clave principal de la entidad. La clave de fila es un valor de cadena que puede tener un tamaño de hasta 1 KB. |
| Tamaño de lote |El número de registros para una operación por lotes. Normalmente, el valor predeterminado es suficiente para la mayoría de los trabajos. Consulte [Especificaciones de la operación por lotes de tablas](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablebatchoperation.aspx) para más información sobre la modificación de esta configuración. |
 
## <a name="service-bus-queues"></a>Colas de Service Bus
[Colas de Service Bus](https://msdn.microsoft.com/library/azure/hh367516.aspx) ofrecen una entrega de mensajes según el modelo El primero en entrar es el primero en salir (FIFO) a uno o más consumidores de la competencia. Normalmente, se espera que los receptores reciban y procesen los mensajes en el orden temporal en el que se agregaron a la cola y solo un destinatario del mensaje recibe y procesa cada uno de los mensajes.

En la tabla siguiente se enumeran los nombres de propiedad y su descripción para crear una salida de cola.

| Nombre de propiedad | DESCRIPCIÓN |
| --- | --- |
| Alias de salida |Un nombre descriptivo usado en las consultas para dirigir la salida de la consulta a esta cola de Service Bus. |
| Espacio de nombres de Service Bus |Un espacio de nombres de Service Bus es un contenedor para un conjunto de entidades de mensajería. |
| Nombre de la cola |El nombre de la cola de Service Bus |
| Nombre de la directiva de colas |Al crear una cola, también puede crear directivas de acceso compartidas en la pestaña Configurar cola. Cada directiva de acceso compartido tiene un nombre, los permisos establecidos y las claves de acceso. |
| Clave de la directiva de colas |La clave de acceso compartido usada para autenticar el acceso al espacio de nombres de Service Bus. |
| Formato de serialización de eventos |Formato de serialización para los datos de salida.  Se admiten JSON, CSV y Avro. |
| Encoding |Por el momento, UTF-8 es el único formato de codificación compatible para CSV y JSON. |
| Delimitador |Solo se aplica para la serialización de CSV. Stream Analytics admite un número de delimitadores comunes para la serialización de datos en formato CSV. Los valores admitidos son la coma, punto y coma, espacio, tabulador y barra vertical. |
| Formato |Solo se aplica para el tipo JSON. Separado por líneas especifica que la salida se formatea de tal forma que cada objeto JSON esté separado por una línea nueva. La matriz especifica que la salida se formatea como una matriz de objetos JSON. |

El número de particiones se [basa en la SKU y el tamaño de Service Bus](../service-bus-messaging/service-bus-partitioning.md). La clave de partición es un valor entero único para cada partición.

## <a name="service-bus-topics"></a>Temas de Service Bus
Mientras que las colas de Service Bus proporcionan un método de comunicación individual del remitente al receptor, los [temas de Service Bus](https://msdn.microsoft.com/library/azure/hh367516.aspx) proporcionan una forma de comunicación de uno a muchos.

En la tabla siguiente se enumeran los nombres de propiedad y su descripción para crear una salida de tabla.

| Nombre de propiedad | DESCRIPCIÓN |
| --- | --- |
| Alias de salida |Un nombre descriptivo usado en las consultas para dirigir la salida de la consulta a este tema de Service Bus. |
| Espacio de nombres de Service Bus |Un espacio de nombres de Service Bus es un contenedor para un conjunto de entidades de mensajería. Al crear un nuevo centro de eventos, también se crea un espacio de nombres de Service Bus. |
| Nombre del tema |Los temas son entidades de mensajería, similares a los centros de eventos y las colas. Están diseñados para recopilar secuencias de eventos de una serie de diferentes dispositivos y servicios. Cuando se crea un tema, se proporciona también un nombre específico. Los mensajes enviados a un tema no están disponibles a menos que se cree una suscripción, así que asegúrese de que hay una o más suscripciones en el tema. |
| Nombre de la directiva de temas |Al crear un tema, también puede crear directivas de acceso compartido en la pestaña Configurar tema. Cada directiva de acceso compartido tiene un nombre, los permisos establecidos y las claves de acceso. |
| Clave de la directiva de temas |La clave de acceso compartido usada para autenticar el acceso al espacio de nombres de Service Bus. |
| Formato de serialización de eventos |Formato de serialización para los datos de salida.  Se admiten JSON, CSV y Avro. |
 | Encoding |Si se usa el formato CSV o JSON, debe especificarse una codificación. Por el momento, UTF-8 es el único formato de codificación compatible. |
| Delimitador |Solo se aplica para la serialización de CSV. Stream Analytics admite un número de delimitadores comunes para la serialización de datos en formato CSV. Los valores admitidos son la coma, punto y coma, espacio, tabulador y barra vertical. |

El número de particiones se [basa en la SKU y el tamaño de Service Bus](../service-bus-messaging/service-bus-partitioning.md). La clave de partición es un valor entero único para cada partición.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB
[Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) es un servicio de base de datos multimodelo distribuido globalmente que ofrece un escalado ilimitado en todo el mundo, una avanzada funcionalidad de indexación automática y de consultas a partir de modelos de datos independientes del esquema, baja latencia garantizada y completos contratos de nivel de servicio líderes en el sector. Para conocer las opciones de colección de Cosmos DB para Stream Analytics, consulte el artículo [Salida de Azure Stream Analytics a Azure Cosmos DB](stream-analytics-documentdb-output.md).

> [!Note]
> En este momento, Azure Stream Analytics solo admite la conexión a CosmosDB mediante la **API de SQL**.
> Aún no se admiten otras API de Azure Cosmos DB. Si apunta Azure Stream Analytics a las cuentas de Azure Cosmos DB creadas con otras API, puede que los datos no se almacenen correctamente. 

En la tabla siguiente se describen las propiedades para crear una salida de Azure Cosmos DB.
| Nombre de propiedad | DESCRIPCIÓN |
| --- | --- |
| Alias de salida | Un alias para hacer referencia a esta salida en la consulta de Stream Analytics. |
| Receptor | Cosmos DB |
| Opción de importación | Elija "Seleccionar base de datos de Cosmos desde su suscripción", o en "Proporcionar la configuración de Cosmos DB manualmente".
| Identificador de la cuenta | El nombre o el identificador URI del punto de conexión de la cuenta de Cosmos DB. |
| Clave de cuenta | La clave de acceso compartido para la cuenta de Cosmos DB. |
| Base de datos | Nombre de la base de datos de Cosmos DB. |
| Patrón de nombre de colección | Nombre de colección o patrón para las colecciones que se usará. <br/>El formato de nombre de la colección se pueden construir con el token opcional {partition}, donde las particiones comienzan desde 0. Dos ejemplos:  <br/>1.  _MyCollection_: debe existir una colección denominada "MyCollection".  <br/>2. _MyCollection{partition}_: en función de la columna de partición. <br/>Deben existir las colecciones de columna de particiones: "MyCollection0", "MyCollection1", "MyCollection2", etc. |
| Partition Key | Opcional. Esto solo es necesario si usa un token {partition} en el patrón de nombre de la colección.<br/> La clave de partición es el nombre del campo en los eventos de salida que se usa para especificar la clave de la salida de la creación de particiones entre colecciones.<br/> Para una salida de colección sencilla, se puede utilizar cualquier columna de salida arbitraria, por ejemplo, PartitionId. |
| Id. de documento |Opcional. Nombre del campo de los eventos de salida utilizado para especificar la clave principal en la que se basan las operaciones de inserción o actualización.  

## <a name="azure-functions"></a>Azure Functions
Azure Functions es un servicio de proceso sin servidor que le permite ejecutar código a petición sin necesidad de aprovisionar ni administrar explícitamente la infraestructura. Permite implementar el código desencadenado por los eventos que se producen en servicios de Azure o en servicios de terceros.  Esta capacidad que tiene Azure Functions para responder a los desencadenadores hace que sea una salida natural para una instancia de Azure Stream Analytics. Este adaptador de salida permite que los usuarios conecten Stream Analytics con Azure Functions y ejecuten un script o una parte de un código como respuesta a una variedad de eventos.

Azure Stream Analytics invoca a Azure Functions a través de desencadenadores HTTP. El nuevo adaptador de salida de Azure Functions está disponible con las propiedades configurables siguientes:

| Nombre de propiedad | DESCRIPCIÓN |
| --- | --- |
| Function App |Nombre de la instancia de Azure Functions App |
| Función |Nombre de la función en la instancia de Azure Functions App |
| Tamaño máximo de lote |Esta propiedad se puede usar para establecer el tamaño máximo de cada lote de salida que se envía a la instancia de Azure Functions. De manera predeterminada, este valor es 256 KB |
| Número máximo de lotes  |Tal como el nombre lo indica, esta propiedad permite especificar el número máximo de eventos en cada lote que se envía a Azure Functions. El valor predeterminado del número máximo de lotes es 100 |
| Clave |Si desea usar una instancia de Azure Functions desde otra suscripción, puede proporcionar la clave para acceder a la función |

Tenga en cuenta que cuando Azure Stream Analytics recibe una excepción 413 (La entidad de solicitud http es demasiado grande) de una función de Azure, disminuye el tamaño de los lotes que envía a Azure Functions. En el código de función de Azure, use esta excepción para asegurarse de que Azure Stream Analytics no envíe lotes demasiado grandes. Además, asegúrese de que los valores de tamaño y número máximo de lotes que se usan en la función sean coherentes con los valores que se ingresan en el portal de Stream Analytics. 

También tenga en cuenta que no se genera ninguna salida en una situación en la que no hay ningún aterrizaje de evento en una ventana de tiempo. Como resultado, no se llama a la función computeResult. Este comportamiento es coherente con las funciones integradas de agregado en ventanas.

## <a name="partitioning"></a>Creación de particiones

En la tabla siguiente se resume la asistencia de la partición y el número de redactores de salida para cada tipo de salida:

| Tipo de salida | Compatibilidad con particiones | Clave de partición  | Número de redactores de salida | 
| --- | --- | --- | --- |
| Almacén de Azure Data Lake | Sí | Use los tokens {date} y {time} en el patrón de prefijo de ruta de acceso. Elija el formato de fecha, como AAAA/MM/DD, DD/MM/AAAA, MM-DD-AAAA. HH se usa para el formato de hora. | Igual que la entrada. | 
| Azure SQL Database | Sin  | None | No aplicable. | 
| Azure Blob Storage | Sí | Utilice los tokens {date} y {time} en el patrón de ruta de acceso. Elija el formato de fecha, como AAAA/MM/DD, DD/MM/AAAA, MM-DD-AAAA. HH se usa para el formato de hora. | Igual que la entrada. | 
| Centro de eventos de Azure | Sí | Sí | Igual que las particiones de Event Hub de salida. |
| Power BI | Sin  | None | No aplicable. | 
| Almacenamiento de tablas de Azure | Sí | Cualquier columna de resultados.  | Igual que la entrada o paso anterior. | 
| Tema de Azure Service Bus | Sí | Se elige automáticamente. El número de particiones se basa en [la SKU y el tamaño de Service Bus](../service-bus-messaging/service-bus-partitioning.md). La clave de partición es un valor entero único para cada partición.| Igual que la salida.  |
| Cola de Azure Service Bus | Sí | Se elige automáticamente. El número de particiones se basa en [la SKU y el tamaño de Service Bus](../service-bus-messaging/service-bus-partitioning.md). La clave de partición es un valor entero único para cada partición.| Igual que la salida. |
| Azure Cosmos DB | Sí | Utilice el token {partition} en el patrón de nombre de la colección. El valor de {partition} se basa en la cláusula PARTITION BY de la consulta. | Igual que la entrada. |
| Azure Functions | Sin  | None | No aplicable. | 


## <a name="get-help"></a>Obtención de ayuda
Para obtener más ayuda, pruebe nuestro [foro de Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Pasos siguientes
Ya conoce Stream Analytics, un servicio administrado para el análisis del streaming de datos desde Internet de las cosas. Para obtener más información acerca de este servicio, consulte:

* [Introducción al uso de Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Escalación de trabajos de Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referencia del lenguaje de consulta de Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referencia de API de REST de administración de Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
