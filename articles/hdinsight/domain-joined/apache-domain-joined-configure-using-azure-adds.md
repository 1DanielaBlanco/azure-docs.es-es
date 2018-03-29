---
title: 'Configuración de clústeres de HDInsight unidos a un dominio con Azure Active Directory Domain Services: Azure | Microsoft Docs'
description: Aprender a configurar y definir clústeres de HDInsight unidos a un dominio con Azure Active Directory Domain Services
services: hdinsight
documentationcenter: ''
author: bprakash
manager: jhubbard
editor: cgronlun
tags: ''
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/10/2017
ms.author: bhanupr
ms.openlocfilehash: a0156915c329dfad1424cfd1f10a6ebb27c56acc
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/17/2018
---
# <a name="configure-domain-joined-hdinsight-clusters-using-azure-active-directory-domain-services"></a>Configurar clústeres de HDInsight unidos a un dominio con Azure Active Directory Domain Services

Los clústeres unidos a un dominio proporcionan funcionalidades de seguridad empresarial multiusuario en HDInsight. Los clústeres de HDInsight unidos a un dominio se conectan a dominios de Active Directory, lo que permite a los usuarios del dominio usar sus credenciales de dominio para autenticarse en los clústeres y ejecutar trabajos de macrodatos. 

Hay dos formas de configurar un controlador de dominio para que un clúster de HDInsight unido a un dominio pueda conectarse a:

- Azure Active Directory Domain Services (Azure AD DS)
- Controlador de dominio de Active Directory en máquinas virtuales IaaS de Azure

En este artículo, aprenderá a configurar un clúster de HDInsight unido a un dominio con Azure Active Directory Domain Services.

## <a name="create-azure-adds"></a>Creación de una instancia de Azure AD DS

Es necesario crear una instancia de Azure AD DS para poder crear un clúster de HDInsight. Para crear una instancia de Azure AD DS, consulte [Habilitación de Azure Active Directory Domain Services mediante Azure Portal](../../active-directory-domain-services/active-directory-ds-getting-started.md). 

> [!NOTE]
> Solo los administradores de inquilinos tienen privilegios para crear servicios de dominio. Si usa Azure Data Lake Storage (ADLS) como almacenamiento predeterminado para HDInsight, asegúrese de que el inquilino de Azure AD predeterminado de ADLS es el mismo que el del dominio del clúster de HDInsight. Para que esta configuración funcione con Azure Data Lake Store, se debe deshabilitar la autenticación multifactor para los usuarios que tengan acceso al clúster.

Después de aprovisionar el servicio de dominio, debe crear una cuenta de servicio en el grupo **Administradores de controladores de dominio de AAD** para crear el clúster de HDInsight. La cuenta de servicio debe ser un administrador global en Azure AD.

Debe habilitar LDAP seguro para un dominio administrado con Azure AD Domain Services. Para habilitar LDAP seguro, consulte [Configuración de LDAP seguro (LDAPS) para un dominio administrado con Azure AD Domain Services](../../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md).

## <a name="create-a-domain-joined-hdinsight-cluster"></a>Creación de un clúster de HDInsight unido a un dominio

El siguiente paso es crear el clúster de HDInsight con AAD DS y la cuenta de servicio que creó en la sección anterior.

Es más fácil colocar el servicio de dominio de Azure AD y el clúster de HDInsight en la misma red virtual (VNET) de Azure. En caso de que se encuentran en diferentes redes virtuales, estas deben emparejarse. Para más información, consulte [Emparejamiento de redes virtuales](../../virtual-network/virtual-network-peering-overview.md).

Al crear un clúster de HDInsight unidos a un dominio, debe proporcionar los parámetros siguientes:

- **Nombre de dominio**: nombre de dominio asociado con Azure AD DS. Por ejemplo, contoso.onmicrosoft.com.
- **Nombre de usuario de dominio**: cuenta de servicio del grupo Administradores de controladores de dominio de AAD creada en la sección anterior. Por ejemplo, hdiadmin@contoso.onmicrosoft.com. Este usuario de dominio es el administrador de este clúster de HDInsight unido a un dominio.
- **Contraseña de dominio**: contraseña de la cuenta de servicio.
- **Unidad organizativa**: nombre distintivo de la unidad organizativa (UO) que quiere utilizar con el clúster de HDInsight. Por ejemplo: OU=HDInsightOU,DC=contoso,DC=onmicrosohift,DC=com. Si no existe esta unidad organizativa, el clúster de HDInsight intenta crearla. 
- **Dirección URL de LDAPS**: ldaps://contoso.onmicrosoft.com:636.
- **Grupo de usuarios de acceso**: grupos de seguridad cuyos usuarios quiere que se sincronicen con el clúster. Por ejemplo, HiveUsers. Si desea especificar varios grupos de usuarios, sepárelos con una coma ','.
 
> [!NOTE]
> Dado que Apache Zeppeling usa el nombre de dominio para autenticar la cuenta de servicio administrativa, dicha cuenta DEBE tener el mismo nombre de dominio que su sufijo de UPN para que Apache Zeppeling funcione correctamente.
 
En la siguiente captura de pantalla se muestran las configuraciones en Azure Portal:

![Configuración de Active Directory Domain Services unido a un dominio de Azure HDInsight](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-domain-joined-configuration-azure-aads-portal.png).


## <a name="next-steps"></a>Pasos siguientes
* Para configurar directivas de Hive y ejecución de consultas de Hive, consulte [Configure Hive policies for Domain-joined HDInsight clusters](apache-domain-joined-run-hive.md) (Configuración de directivas de los clústeres de HDInsight unidos a dominio).
* Para utilizar SSH para conectarse a clústeres de HDInsight unidos a dominio, consulte [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Linux, Unix u OS X](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).

