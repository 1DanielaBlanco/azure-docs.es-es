---
title: Uso de Data Lake Store con Hadoop en Azure HDInsight
description: Aprenda a consultar datos desde Azure Data Lake Store y a almacenar los resultados del análisis.
services: hdinsight,storage
author: jasonwhowell
ms.author: jasonh
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 07/23/2018
ms.openlocfilehash: 1f2863ac13a6b94a226cdc01a57f6554f75625ae
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2018
ms.locfileid: "43103684"
---
# <a name="use-data-lake-store-with-azure-hdinsight-clusters"></a>Uso de Data Lake Store con clústeres de Azure HDInsight

Para analizar datos del clúster de HDInsight, puede almacenar los datos en [Azure Storage](../storage/common/storage-introduction.md), [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md), o en ambos lugares. Ambas opciones de almacenamiento le permiten eliminar de forma segura clústeres de HDInsight que se usan para el cálculo sin perder datos del usuario.

En este artículo, aprenderá cómo funciona Data Lake Store con clústeres de HDInsight. Para más información sobre cómo funciona Azure Storage con clústeres de HDInsight, consulte [Uso de Azure Storage con clústeres de Azure HDInsight](hdinsight-hadoop-use-blob-storage.md). Consulte [Creación de clústeres de Hadoop en HDInsight](hdinsight-hadoop-provision-linux-clusters.md) para obtener información sobre la creación de un clúster de HDInsight.

> [!NOTE]
> Para acceder a Azure Data Lake Store siempre se usa un canal seguro, así que no hay ningún nombre de esquema de sistema de archivos `adls`. Usa siempre `adl`.
> 


## <a name="availability-for-hdinsight-clusters"></a>Disponibilidad de los clústeres de HDInsight

Hadoop admite una noción del sistema de archivos predeterminado. El sistema de archivos predeterminado implica una autoridad y un esquema predeterminados. También se puede usar para resolver rutas de acceso relativas. Durante el proceso de creación del clúster de HDInsight, puede especificar un contenedor de blobs en Azure Blob Storage como el sistema de archivos predeterminado; también, con HDInsight 3.5 y versiones más recientes, puede seleccionar Azure Storage o Azure Data Lake Store como el sistema de archivos predeterminado con algunas excepciones. 

Los clústeres de HDInsight pueden usar Data Lake Store de dos maneras:

* Como almacenamiento predeterminado
* Como almacenamiento adicional, con Azure Storage Blob como almacenamiento predeterminado.

A partir de ahora, solo algunos tipos o versiones de clústeres de HDInsight admiten el uso de Data Lake Store como cuentas de almacenamiento predeterminado y almacenamiento adicional:

| Tipo de clúster de HDInsight | Data Lake Store como almacenamiento predeterminado | Data Lake Store como almacenamiento adicional| Notas |
|------------------------|------------------------------------|---------------------------------------|------|
| HDInsight versión 3.6 | Sí | Sí | |
| Versión de HDInsight 3.5 | Sí | SÍ | Con la excepción de HBase|
| Versión de HDInsight 3.4 | No | SÍ | |
| HDInsight versión 3.3 | Sin  | Sin  | |
| HDInsight versión 3.2 | Sin  | Sí | |
| Storm | | |Data Lake Store se puede usar para escribir datos de una topología de Storm. Puede usar Data Lake Store para datos de referencia que luego puede leer una topología de Storm.|

El uso de Data Lake Store como una cuenta de almacenamiento adicional no afecta al rendimiento o la capacidad de lectura o escritura en Azure Storage desde el clúster.


## <a name="use-data-lake-store-as-default-storage"></a>Uso de Data Lake Store como almacenamiento predeterminado

Cuando se implementa HDInsight con Data Lake Store como almacenamiento predeterminado, los archivos relacionados con el clúster se almacenan en Data Lake Store en la siguiente ubicación:

    adl://mydatalakestore/<cluster_root_path>/

donde `<cluster_root_path>` es el nombre de una carpeta que crea en Data Lake Store. Al especificar una ruta de acceso raíz para cada clúster, puede usar la misma cuenta de Data Lake Store con más de un clúster. Por lo tanto, puede tener una configuración donde:

* Cluster1 puede usar la ruta de acceso `adl://mydatalakestore/cluster1storage`
* Cluster2 puede usar la ruta de acceso `adl://mydatalakestore/cluster2storage`

Observe que ambos clústeres usan la misma cuenta de Data Lake Store **mydatalakestore**. Cada clúster tiene acceso a su propio sistema de archivos raíz en Data Lake Store. La experiencia de implementación de Azure Portal en particular le pide que use un nombre de carpeta como **/clusters/\<clustername >** para la ruta de acceso raíz.

Para poder usar Data Lake Store como almacenamiento predeterminado, debe conceder a la entidad de servicio acceso a las siguientes rutas:

- La raíz de la cuenta de Data Lake Store.  Por ejemplo: adl://mydatalakestore/.
- La carpeta para todas las carpetas del clúster.  Por ejemplo: adl://mydatalakestore/clusters.
- La carpeta para el clúster.  Por ejemplo: adl://mydatalakestore/clusters/cluster1storage.

Para más información sobre cómo crear la entidad de servicio y concederle acceso, consulte [Configuración del acceso a Data Lake Store](#configure-data-lake-store-access).


## <a name="use-data-lake-store-as-additional-storage"></a>Uso de Data Lake Store como almacenamiento adicional

También puede usar Data Lake Store como almacenamiento adicional para el clúster. En tales casos, el almacenamiento predeterminado del clúster puede ser una cuenta de Azure Storage Blob o de Data Lake Store. Si va a ejecutar trabajos de HDInsight con los datos almacenados en Data Lake Store como almacenamiento adicional, debe usar la ruta de acceso completa a los archivos. Por ejemplo: 

    adl://mydatalakestore.azuredatalakestore.net/<file_path>

Tenga en cuenta que ahora no hay ningún elemento **cluster_root_path** en la dirección URL. Esto se debe a que, en este caso, Data Lake Store no es un almacenamiento predeterminado, así que todo lo que debe hacer es proporcionar la ruta de acceso a los archivos.

Para poder usar Data Lake Store como almacenamiento adicional solo debe conceder a la entidad de servicio acceso a las rutas en las que se almacenan los archivos.  Por ejemplo: 

    adl://mydatalakestore.azuredatalakestore.net/<file_path>

Para más información sobre cómo crear la entidad de servicio y concederle acceso, consulte [Configuración del acceso a Data Lake Store](#configure-data-lake-store-access).


## <a name="use-more-than-one-data-lake-store-accounts"></a>Uso de más de una cuenta de Data Lake Store

La incorporación de una cuenta de Data Lake Store como adicional y la incorporación de más de una cuenta de Data Lake Store, se logra mediante la concesión de permisos al clúster de HDInsight sobre los datos de una o varias cuentas de Data Lake Store. Consulte [Configuración del acceso a Data Lake Store](#configure-data-lake-store-access).

## <a name="configure-data-lake-store-access"></a>Configuración del acceso a Data Lake Store

Para configurar el acceso a Data Lake Store desde el clúster de HDInsight, debe tener una entidad de servicio de Azure Active Directory (Azure AD). Solo un administrador de Azure AD puede crear una entidad de servicio. La entidad de servicio debe crearse con un certificado. Para más información, consulte [Inicio rápido: Configuración de clústeres en HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md) y [Creación de una entidad de servicio con un certificado autofirmado](../azure-resource-manager/resource-group-authenticate-service-principal.md#create-service-principal-with-self-signed-certificate).

> [!NOTE]
> Si va a usar Azure Data Lake Store como almacenamiento adicional para un clúster de HDInsight, se recomienda encarecidamente hacer esto al crear el clúster como se describe en este artículo. La incorporación de Azure Data Lake Store como almacenamiento adicional a un clúster de HDInsight existente es un escenario admitido.
>

## <a name="access-files-from-the-cluster"></a>Acceso a los archivos desde el clúster

Existen varias maneras de acceder a los archivos de Data Lake Store desde un clúster de HDInsight.

* **Con el nombre completo**. Con este enfoque, proporciona la ruta de acceso completa al archivo al que quiere acceder.

        adl://mydatalakestore.azuredatalakestore.net/<cluster_root_path>/<file_path>

* **Con el formato abreviado de la ruta de acceso**. Con este enfoque, reemplaza la ruta de acceso hasta la raíz del clúster con adl:///. Por lo tanto, en el ejemplo anterior, puede reemplazar `adl://mydatalakestore.azuredatalakestore.net/<cluster_root_path>/` por `adl:///`.

        adl:///<file path>

* **Con la ruta de acceso relativa**. Con este enfoque, solo proporciona la ruta de acceso relativa al archivo al que quiere acceder. Por ejemplo, si la ruta de acceso completa al archivo es:

        adl://mydatalakestore.azuredatalakestore.net/<cluster_root_path>/example/data/sample.log

    Puede acceder al mismo archivo sample.log mediante esta ruta de acceso relativa.

        /example/data/sample.log

## <a name="create-hdinsight-clusters-with-access-to-data-lake-store"></a>Creación de clústeres de HDInsight con acceso a Data Lake Store

Use los vínculos siguientes para obtener instrucciones detalladas sobre cómo crear clústeres de HDInsight con acceso a Data Lake Store.

* [Uso del portal](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)
* [Uso de PowerShell (con Data Lake Store como almacenamiento predeterminado)](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
* [Uso de PowerShell (con Data Lake Store como almacenamiento adicional)](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md)
* [Uso de plantillas de Azure](../data-lake-store/data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)

## <a name="refresh-the-hdinsight-certificate-for-data-lake-store-access"></a>Actualización del certificado de HDInsight para el acceso a Data Lake Store

El siguiente código de PowerShell de ejemplo lee un archivo de certificado local y actualiza el clúster de HDInsight con el nuevo certificado para acceder a Azure Data Lake Store. Especifique su propio nombre de clúster de HDInsight, el nombre del grupo de recursos, el identificador de la suscripción, el identificador de la aplicación y la ruta de acceso local al certificado. Escriba la contraseña cuando se le solicite.

```powershell-interactive
$clusterName = '<clustername>'
$resourceGroupName = '<resourcegroupname>'
$subscriptionId = '01234567-8a6c-43bc-83d3-6b318c6c7305'
$appId = '01234567-e100-4118-8ba6-c25834f4e938'
$generateSelfSignedCert = $false
$addNewCertKeyCredential = $true
$certFilePath = 'C:\localfolder\adls.pfx'
$certPassword = Read-Host "Enter Certificate Password"

if($generateSelfSignedCert)
{
    Write-Host "Generating new SelfSigned certificate"
    
    $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=hdinsightAdlsCert" -KeySpec KeyExchange
    $certBytes = $cert.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12, $certPassword);
    $certString = [System.Convert]::ToBase64String($certBytes)
}
else
{

    Write-Host "Reading the cert file from path $certFilePath"

    $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($certFilePath, $certPassword)
    $certString = [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes($certFilePath))
}

Login-AzureRmAccount

if($addNewCertKeyCredential)
{
    Write-Host "Creating new KeyCredential for the app"
    $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())
    New-AzureRmADAppCredential -ApplicationId $appId -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore
    Write-Host "Waiting for 30 seconds for the permissions to get propagated"
    Start-Sleep -s 30
}

Select-AzureRmSubscription -SubscriptionId $subscriptionId
Write-Host "Updating the certificate on HDInsight cluster..."

Invoke-AzureRmResourceAction `
    -ResourceGroupName $resourceGroupName `
    -ResourceType 'Microsoft.HDInsight/clusters' `
    -ResourceName $clusterName `
    -ApiVersion '2015-03-01-preview' `
    -Action 'updateclusteridentitycertificate' `
    -Parameters @{ ApplicationId = $appId; Certificate = $certString; CertificatePassword = $certPassword.ToString() } `
    -Force
```

## <a name="next-steps"></a>Pasos siguientes
En este artículo, aprendió a usar Azure Data Lake Store compatible con HDFS con HDInsight. Esto le permite crear soluciones de adquisición de datos de archivado escalables y a largo plazo y usar HDInsight para desbloquear la información que hay dentro de los datos estructurados y no estructurados almacenados.

Para más información, consulte:

* [Introducción a Azure HDInsight][hdinsight-get-started]
* [Guía de inicio rápido: Configuración de clústeres en HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)
* [Uso de Azure PowerShell para crear clústeres de HDInsight con Data Lake Store (como almacenamiento adicional)](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md)
* [Carga de datos en HDInsight][hdinsight-upload-data]
* [Uso de Hive con HDInsight][hdinsight-use-hive]
* [Uso de Pig con HDInsight][hdinsight-use-pig]
* [Uso de firmas de acceso compartido de Azure Storage para restringir el acceso a datos con HDInsight][hdinsight-use-sas]

[hdinsight-use-sas]: hdinsight-storage-sharedaccesssignature-permissions.md
[powershell-install]: /powershell/azureps-cmdlets-docs
[hdinsight-creation]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-use-pig]:hadoop/hdinsight-use-pig.md

[blob-storage-restAPI]: http://msdn.microsoft.com/library/windowsazure/dd135733.aspx
[azure-storage-create]:../storage/common/storage-create-storage-account.md

[img-hdi-powershell-blobcommands]: ./media/hdinsight-hadoop-use-blob-storage/HDI.PowerShell.BlobCommands.png
[img-hdi-quick-create]: ./media/hdinsight-hadoop-use-blob-storage/HDI.QuickCreateCluster.png
[img-hdi-custom-create-storage-account]: ./media/hdinsight-hadoop-use-blob-storage/HDI.CustomCreateStorageAccount.png  
