---
title: Copia de datos de un servidor SFTP mediante Azure Data Factory | Microsoft Docs
description: Obtenga información sobre el conector MySQL de Azure Data Factory que permite copiar los datos desde un servidor SFTP a un almacén de datos admitido como receptor.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/27/2018
ms.author: jingwang
ms.openlocfilehash: 92f83bb6656578792aea45efe62da75009c1391d
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/20/2018
ms.locfileid: "36284954"
---
# <a name="copy-data-from-sftp-server-using-azure-data-factory"></a>Copia de datos desde un servidor SFTP mediante Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versión 1: Disponibilidad general](v1/data-factory-sftp-connector.md)
> * [Versión 2: versión preliminar](connector-sftp.md)

En este artículo se explica el uso de la actividad de copia de Azure Data Factory para copiar datos con un servidor SFTP como origen o destino. El documento se basa en el artículo de [introducción a la actividad de copia](copy-activity-overview.md) que describe información general de la actividad de copia.

> [!NOTE]
> Este artículo se aplica a la versión 2 de Data Factory, que actualmente se encuentra en versión preliminar. Si usa la versión 1 del servicio Data Factory, que está disponible con carácter general, vea el artículo sobre [la versión 1 del conector SFTP](v1/data-factory-sftp-connector.md).

## <a name="supported-capabilities"></a>Funcionalidades admitidas

Puede copiar datos desde un servidor SFTP a cualquier almacén de datos receptor admitido. Consulte la tabla de [almacenes de datos compatibles](copy-activity-overview.md#supported-data-stores-and-formats) para ver una lista de almacenes de datos que la actividad de copia admite como orígenes o receptores.

En concreto, este conector SFTP admite las siguientes funcionalidades:

- Copiar datos mediante la autenticación **Básica** o **SshPublicKey**.
- Copiar los archivos tal cual, o bien analizarlos con los [códecs de compresión y los formatos de archivo compatibles](supported-file-formats-and-compression-codecs.md).

## <a name="get-started"></a>Introducción

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

En las secciones siguientes se proporcionan detalles sobre las propiedades que se usan para definir entidades de Data Factory específicas de SFTP.

## <a name="linked-service-properties"></a>Propiedades del servicio vinculado

Las siguientes propiedades son compatibles con el servicio vinculado SFTP:

| Propiedad | DESCRIPCIÓN | Obligatorio |
|:--- |:--- |:--- |
| Tipo | La propiedad type debe establecerse en: **Sftp**. |Sí |
| host | Nombre o dirección IP del servidor SFTP. |Sí |
| puerto | Puerto en el que escucha el servidor SFTP.<br/>Los valores permitidos son: enteros; el valor predeterminado es **22**. |Sin  |
| skipHostKeyValidation | Especifique si desea omitir la validación de claves de host.<br/>Los valores válidos son **true** y **false** (valor predeterminado).  | Sin  |
| hostKeyFingerprint | Especifique la huella dactilar de la clave de host. | Sí, si "skipHostKeyValidation" está establecido en false.  |
| authenticationType | Especifique el tipo de autenticación.<br/>Los valores permitidos son: **Básica**, **SshPublicKey**. Consulte las secciones [Uso de autenticación básica](#using-basic-authentication) y [Uso de autenticación de clave pública SSH](#using-ssh-public-key-authentication) sobre más propiedades y ejemplos JSON respectivamente. |Sí |
| connectVia | El entorno [Integration Runtime](concepts-integration-runtime.md) que se usará para conectarse al almacén de datos. Puede usar los entornos Integration Runtime (autohospedado) (si el almacén de datos se encuentra en una red privada) o Azure Integration Runtime. Si no se especifica, se usará Azure Integration Runtime. |Sin  |

### <a name="using-basic-authentication"></a>Uso de autenticación básica

Para usar la autenticación básica, establezca la propiedad "authenticationType" en **Básica** y especifique las siguientes propiedades además de las genéricas del conector SFTP descritas en la última sección:

| Propiedad | DESCRIPCIÓN | Obligatorio |
|:--- |:--- |:--- |
| userName | Usuario que tiene acceso al servidor SFTP. |Sí |
| contraseña | Contraseña para el usuario (nombre de usuario). Marque este campo como SecureString para almacenarlo de forma segura en Data Factory o [para hacer referencia a un secreto almacenado en Azure Key Vault](store-credentials-in-key-vault.md). | Sí |

**Ejemplo:**

```json
{
    "apiVersion": "2017-09-01-preview",
    "name": "SftpLinkedService",
    "type": "linkedservices",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<sftp server>",
            "port": 22,
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
            "authenticationType": "Basic",
            "userName": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="using-ssh-public-key-authentication"></a>Uso de autenticación de clave pública SSH

Para usar la autenticación de clave pública SSH, establezca la propiedad "authenticationType" en **SshPublicKey** y especifique las siguientes propiedades además de las genéricas del conector SFTP descritas en la última sección:

| Propiedad | DESCRIPCIÓN | Obligatorio |
|:--- |:--- |:--- |
| userName | Usuario que tiene acceso al servidor SFTP. |Sí |
| privateKeyPath | Especifique una ruta de acceso absoluta al archivo de clave privada al que pueda acceder Integration Runtime. Solo se aplica cuando se especifica un tipo autohospedado de un entorno de ejecución de integración en "connectVia". | Especifique `privateKeyPath` o `privateKeyContent`.  |
| privateKeyContent | Contenido de clave privada SSH codificada en Base64. La clave privada SSH debe tener el formato OpenSSH. Marque este campo como SecureString para almacenarlo de forma segura en Data Factory o [para hacer referencia a un secreto almacenado en Azure Key Vault](store-credentials-in-key-vault.md). | Especifique `privateKeyPath` o `privateKeyContent`. |
| passPhrase | Especifique la contraseña o la frase de contraseña para descifrar la clave privada si el archivo de clave está protegido por una frase de contraseña. Marque este campo como SecureString para almacenarlo de forma segura en Data Factory o [para hacer referencia a un secreto almacenado en Azure Key Vault](store-credentials-in-key-vault.md). | Sí, si el archivo de clave privada está protegido por una frase de contraseña. |

> [!NOTE]
> El conector SFTP solo admite la clave OpenSSH de RSA/DSA. Asegúrese de que el contenido del archivo de claves comienza con "-----BEGIN [RSA/DSA] PRIVATE KEY-----". Si el archivo de clave privada es un archivo con formato ppk, use la herramienta Putty para convertir del formato .ppk a OpenSSH. 

**Ejemplo 1: Autenticación de SshPublicKey mediante el valor de filePath de clave privada**

```json
{
    "apiVersion": "2017-09-01-preview",
    "name": "SftpLinkedService",
    "type": "Linkedservices",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<sftp server>",
            "port": 22,
            "skipHostKeyValidation": true,
            "authenticationType": "SshPublicKey",
            "userName": "xxx",
            "privateKeyPath": "D:\\privatekey_openssh",
            "passPhrase": {
                "type": "SecureString",
                "value": "<pass phrase>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Ejemplo 2: Autenticación de SshPublicKey mediante contenido de clave privada**

```json
{
    "apiVersion": "2017-09-01-preview",
    "name": "SftpLinkedService",
    "type": "Linkedservices",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<sftp server>",
            "port": 22,
            "skipHostKeyValidation": true,
            "authenticationType": "SshPublicKey",
            "userName": "<username>",
            "privateKeyContent": {
                "type": "SecureString",
                "value": "<base64 string of the private key content>"
            },
            "passPhrase": {
                "type": "SecureString",
                "value": "<pass phrase>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Propiedades del conjunto de datos

Si desea ver una lista completa de las secciones y propiedades disponibles para definir conjuntos de datos, consulte el artículo sobre conjuntos de datos. En esta sección se proporciona una lista de las propiedades que admite el conjunto de datos de SFTP.

Para copiar datos desde SFTP, establezca la propiedad type del conjunto de datos en **FileShare**. Se admiten las siguientes propiedades:

| Propiedad | DESCRIPCIÓN | Obligatorio |
|:--- |:--- |:--- |
| Tipo | La propiedad type del conjunto de datos debe establecerse en: **FileShare** |Sí |
| folderPath | Ruta de acceso a la carpeta. No se admiten filtros con caracteres comodín. Por ejemplo: carpeta/subcarpeta/ |Sí |
| fileName |  **Filtro de nombre o de comodín** para los archivos de la ruta "folderPath" especificada. Si no especifica ningún valor para esta propiedad, el conjunto de datos apunta a todos los archivos de la carpeta. <br/><br/>Para filtrar, los caracteres comodín permitidos son: `*` (equivale a cero o a varios caracteres) y `?` (equivale a cero o a un único carácter).<br/>- Ejemplo 1: `"fileName": "*.csv"`<br/>- Ejemplo 2: `"fileName": "???20180427.txt"`<br/>Use `^` como escape si el nombre de archivo real contiene un comodín o este carácter de escape. |Sin  |
| formato | Si desea **copiar los archivos tal cual** entre los almacenes basados en archivos (copia binaria), omita la sección de formato en las definiciones de los conjuntos de datos de entrada y salida.<br/><br/>Si desea analizar archivos con un formato concreto, se admiten los siguientes tipos de formato: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** y **ParquetFormat**. Establezca la propiedad **type** de formato en uno de los siguientes valores. Para más información, consulte las secciones [Formato de texto](supported-file-formats-and-compression-codecs.md#text-format), [Formato Json](supported-file-formats-and-compression-codecs.md#json-format), [Formato Avro](supported-file-formats-and-compression-codecs.md#avro-format), [Formato Orc](supported-file-formats-and-compression-codecs.md#orc-format) y [Formato Parquet](supported-file-formats-and-compression-codecs.md#parquet-format). |No (solo para el escenario de copia binaria) |
| compresión | Especifique el tipo y el nivel de compresión de los datos. Para más información, consulte el artículo sobre [códecs de compresión y formatos de archivo compatibles](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Los tipos admitidos son **GZip**, **Deflate**, **BZip2** y **ZipDeflate**.<br/>Los niveles admitidos son **Optimal** y **Fastest**. |Sin  |

>[!TIP]
>Para copiar todos los archivos en una carpeta, especifique solo **folderPath**.<br>Para copiar un único archivo con un nombre determinado, especifique **folderPath** con el elemento de carpeta y **fileName** con el nombre de archivo.<br>Para copiar un subconjunto de archivos en una carpeta, especifique **folderPath** con el elemento de carpeta y **fileName** con el filtro de comodín.

>[!NOTE]
>Si estaba usando la propiedad "fileFilter" para el filtro de archivos, esta todavía se admite como está, aunque se le sugiere que use la nueva funcionalidad de filtro agregada a "fileName" de ahora en adelante.

**Ejemplo:**

```json
{
    "apiVersion": "2017-09-01-preview",
    "name": "SFTPDataset",
    "type": "Datasets",
    "properties": {
        "type": "FileShare",
        "linkedServiceName":{
            "referenceName": "<SFTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "folder/subfolder/",
            "fileName": "myfile.csv.gz",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propiedades de la actividad de copia

Si desea ver una lista completa de las secciones y propiedades disponibles para definir actividades, consulte el artículo sobre [canalizaciones](concepts-pipelines-activities.md). En esta sección se proporciona una lista de las propiedades que admite el origen SFTP.

### <a name="sftp-as-source"></a>SFTP como origen

Para copiar datos desde un servidor SFTP, establezca el tipo de origen de la actividad de copia en **FileSystemSource**. Se admiten las siguientes propiedades en la sección **source** de la actividad de copia:

| Propiedad | DESCRIPCIÓN | Obligatorio |
|:--- |:--- |:--- |
| Tipo | La propiedad type del origen de la actividad de copia debe establecerse en: **FileSystemSource** |Sí |
| recursive | Indica si los datos se leen de forma recursiva de las subcarpetas o solo de la carpeta especificada. Tenga en cuenta que cuando recursive se establezca en true y el receptor sea un almacén basado en archivos, la carpeta o subcarpeta vacías no se copiarán ni crearán en el receptor.<br/>Los valores permitidos son: **True** (valor predeterminado) y **False** | Sin  |

**Ejemplo:**

```json
"activities":[
    {
        "name": "CopyFromSFTP",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SFTP input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "FileSystemSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```


## <a name="next-steps"></a>Pasos siguientes
Consulte los [almacenes de datos compatibles](copy-activity-overview.md##supported-data-stores-and-formats) para ver la lista de almacenes de datos que la actividad de copia de Azure Data Factory admite como orígenes y receptores.
