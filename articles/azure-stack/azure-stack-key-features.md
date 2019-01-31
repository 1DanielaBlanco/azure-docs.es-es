---
title: Características y conceptos clave de Azure Stack | Microsoft Docs
description: Más información sobre las características y conceptos clave de Azure Stack
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: 09ca32b7-0e81-4a27-a6cc-0ba90441d097
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/14/2019
ms.author: jeffgilb
ms.reviewer: unknown
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: b07d8b115b966b9decdfa7379a908da4f9f2ee74
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/30/2019
ms.locfileid: "55243916"
---
# <a name="key-features-and-concepts-in-azure-stack"></a>Características y conceptos clave de Azure Stack
Si nunca ha usado Microsoft Azure Stack, estos términos y las descripciones de las características pueden resultar útiles.

## <a name="personas"></a>Personas
Hay dos tipos de usuarios de Microsoft Azure Stack: el operador y el usuario.

* Un **operador** de Azure Stack puede configurar Azure Stack, para lo cual administra las ofertas, planes, servicios, cuotas y precios a fin de proporcionar recursos a sus usuarios inquilinos. Los operadores también administran la capacidad y responden a las alertas.  
* Un **usuario** de Azure Stack consume los servicios que el operador ofrece. Los inquilinos pueden aprovisionar, supervisar y administrar los servicios a los que se han suscrito, como Web Apps, Storage y Virtual Machines.

## <a name="portal"></a>Portal
Los métodos principales de interacción con Microsoft Azure Stack son el portal de administración, el portal de usuarios y PowerShell.

Cada uno de los portales de Azure Stack cuenta con el respaldo de instancias independientes de Azure Resource Manager. Un operador usa el portal de administración para administrar Azure Stack y para realizar tareas, tales como crear ofertas para inquilinos. El portal de usuarios (conocido también como "portal de inquilinos") proporciona una experiencia de autoservicio para el consumo de recursos de nube, como máquinas virtuales, cuentas de almacenamiento y Web Apps. Para obtener más información, consulte [Using the Azure Stack administrator and user portals](azure-stack-manage-portals.md) (Usar los portales de administración y de usuarios de Azure Stack).

## <a name="identity"></a>Identidad 
Azure Stack usa Azure Active Directory (Azure AD) o los Servicios de federación de Active Directory (AD FS) como proveedor de identidades.  

### <a name="azure-active-directory"></a>Azure Active Directory
Azure AD es el proveedor de identidades multiinquilino basado en la nube de Microsoft. La mayoría de los escenarios híbridos usa Azure AD como almacén de identidades.

### <a name="active-directory-federation-services"></a>Servicios de federación de Active Directory
Puede elegir usar los Servicios de federación de Active Directory (AD FS) para las implementaciones desconectadas de Azure Stack. Los proveedores de recursos de Azure Stack y otras aplicaciones funcionan con AD FS de manera muy similar a como lo hacen con Azure AD. Azure Stack incluye su propia instancia de Active Directory, así como una instancia de Graph API de Active Directory. El Kit de desarrollo de Azure Stack admite los siguientes escenarios de AD FS:

- Inicio de sesión en la implementación mediante AD FS.
- Creación de una máquina virtual con secretos en Key Vault.
- Creación de un almacén para almacenar secretos y acceder a ellos.
- Creación de roles personalizados de RBAC en la suscripción.
- Asignación de usuarios a roles RBAC en recursos.
- Creación de roles RBAC de todo el sistema a través de Azure PowerShell.
- Inicio de sesión como usuario a través de Azure PowerShell.
- Creación de entidades de servicio para usarlas en el inicio de sesión en Azure PowerShell.


## <a name="regions-services-plans-offers-and-subscriptions"></a>Regiones, servicios, planes, ofertas y suscripciones
En Azure Stack, los servicios se prestan a los inquilinos mediante regiones, planes, ofertas y suscripciones. Los inquilinos pueden suscribirse a varias ofertas. Las ofertas pueden tener uno o varios planes, y los planes pueden tener uno o varios servicios.

![](media/azure-stack-key-features/image4.png)

Ejemplo de jerarquía de suscripciones de un inquilino a las ofertas, cada una de ellas con distintos planes y servicios.

### <a name="regions"></a>Regiones
Las regiones de Azure Stack son un elemento básico de escala y administración. Una organización puede tener varias regiones con recursos disponibles en cada región. Las regiones también pueden tener distintas ofertas de servicio disponibles. En el Kit de desarrollo de Azure Stack, solo se admite una única región, a la que se asigna el nombre *local* automáticamente.

### <a name="services"></a>Services
Microsoft Azure Stack permite a los proveedores ofrecer una amplia variedad de servicios y aplicaciones, como máquinas virtuales, bases de datos SQL Server, SharePoint, Exchange, etc.

### <a name="plans"></a>Planes
Los planes son agrupaciones de uno o varios servicios. Los proveedores crean planes y los ofrecen a los inquilinos. A su vez, los inquilinos se suscriben a las ofertas para usar los planes y servicios que incluyen.

Todos los servicios que se agregan a un plan pueden configurarse con valores de cuota para ayudarle a administrar la capacidad de la nube. Las cuotas pueden incluir restricciones, como límites de máquinas virtuales, CPU y RAM, y se aplican por suscripción de usuario. Las cuotas se pueden diferenciar por su ubicación. Por ejemplo, un plan que contiene servicios Compute Services de la Región A puede tener una cuota de dos máquinas virtuales, 4 GB de RAM y 10 núcleos de CPU.

Al crear una oferta, el administrador de servicios puede incluir un **plan base**. Estos planes se incluyen de forma predeterminada cuando un inquilino se suscribe a la oferta. Cuando un usuario se suscribe (y se crea la suscripción), tiene acceso a todos los proveedores de recursos especificados en dichos planes base (con las cuotas correspondientes).

El administrador de servicios también puede incluir **planes complementarios** en las ofertas. Dichos planes no se incluyen de forma predeterminada en la suscripción. Los planes complementarios son planes adicionales (cuotas) disponibles en una oferta que los propietarios de suscripciones pueden agregar a sus suscripciones.

### <a name="offers"></a>Ofertas
Las ofertas son grupos de uno o varios planes que los proveedores presentan a los inquilinos para que estos los compren (se suscriban a ellos). Por ejemplo, la Oferta Alfa puede incluir el Plan A (que contiene un conjunto de servicios Compute Services) y el Plan B (que contiene un conjunto de servicios de almacenamiento y Network Services).

Las ofertas incluyen un conjunto de planes de bases y los administradores de servicios pueden crear planes complementarios que los inquilinos pueden agregar a su suscripción.

### <a name="subscriptions"></a>Suscripciones
Una suscripción es la forma en que los inquilinos compran las ofertas. Una suscripción es una combinación de un inquilino con una oferta. Un inquilino puede tener suscripciones a varias ofertas. Cada suscripción se aplica a una sola oferta. Las suscripciones de un inquilino determinan los planes y servicios a los que pueden acceder.

Las suscripciones ayudan a los proveedores a organizar los recursos y servicios de la nube, y a acceder a ellos.

Para el administrador, se crea una suscripción de proveedor predeterminado durante la implementación. Esta suscripción puede utilizarse para administrar Azure Stack, implementar más proveedores de recursos, y crear ofertas y planes para inquilinos. No debe usarse para ejecutar aplicaciones y cargas de trabajo de cliente. A partir de la versión 1804, dos suscripciones adicionales complementan la suscripción del proveedor predeterminado: una suscripción de medición y una suscripción de consumo. Estas adiciones facilitan la separación de la administración de la infraestructura básica, proveedores de recursos adicionales y cargas de trabajo.  

## <a name="azure-resource-manager"></a>Azure Resource Manager
Con Azure Resource Manager, puede trabajar con los recursos de la infraestructura en un modelo declarativo basado en plantillas.   Proporciona una única interfaz que puede usar para implementar y administrar los componentes de la solución. Para obtener información e instrucciones completas, consulte [Información general de Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).

### <a name="resource-groups"></a>Grupos de recursos
Los grupos de recursos son colecciones de recursos, servicios y aplicaciones (y cada recurso tiene un tipo, como máquinas virtuales, redes virtuales, IP públicas, cuentas de almacenamiento y sitios web). Cada recurso debe estar en un grupo de recursos, por lo que los grupos de recursos ayudan a organizar los recursos de manera lógica, por ejemplo, por carga de trabajo o ubicación. En Azure Stack, los recursos, como los planes y las ofertas, también se administran en grupos de recursos.

A diferencia de [Azure](../azure-resource-manager/resource-group-move-resources.md), no se pueden mover recursos de Azure Stack entre grupos de recursos. Al ver las propiedades de un recurso o de un grupo de recursos en el portal de administración de Azure Stack, el botón *Mover* está atenuado y no está disponible. Además, tampoco se permite el uso de las acciones **Cambiar grupo de recursos** o **Cambiar suscripción** desde las propiedades de un elemento de un grupo de recursos o de un grupo de recursos. Todas las operaciones de movimiento que se intenten generarán un error.
 
### <a name="azure-resource-manager-templates"></a>Plantillas del Administrador de recursos de Azure
Con Azure Resource Manager, puede crear una plantilla (en formato JSON) que defina la implementación y configuración de la aplicación. Esta plantilla se conoce como plantilla del Administrador de recursos de Azure y proporciona una manera declarativa de definir la implementación. El uso de una plantilla permite implementar la aplicación repetidamente a lo largo del ciclo de vida de esta y tener la seguridad de que los recursos se implementan de forma coherente.

## <a name="resource-providers-rps"></a>Proveedores de recursos (RP)
Los proveedores de recursos son servicios web que forman la base de todos los servicios IaaS y PaaS basados en Azure. Azure Resource Manager utiliza distintos RP para proporcionar acceso a los servicios.

Hay cuatro proveedores de recursos fundamentales: red, almacenamiento, proceso y almacén de claves. Cada uno de estos RP le ayuda a configurar y controlar sus respectivos recursos. Los administradores de servicios también pueden agregar nuevos proveedores de recursos personalizados.

### <a name="compute-rp"></a>RP de proceso
El proveedor de recursos de proceso (CRP) permite a los inquilinos de Azure Stack crear sus propias máquinas virtuales. El CRP incluye la capacidad de crear tanto máquinas virtuales como extensiones de Máquina virtual. El servicio de extensión de Máquina virtual ayuda a proporcionar capacidades de IaaS a las máquinas virtuales Windows y Linux.  Por ejemplo, puede usar el CRP para aprovisionar una máquina virtual Linux y ejecutar scripts de Bash durante la implementación para configurar la máquina virtual.

### <a name="network-rp"></a>RP de red
El proveedor de recursos de red (NRP) ofrece una serie de características de Redes definidas por software (SDN) y Virtualización de función de red (NFV) a la nube privada.  El NRP puede usarse para crear recursos, como equilibradores de carga de software, IP públicas, grupos de seguridad de red y redes virtuales.

### <a name="storage-rp"></a>RP de almacenamiento
El RP Storage ofrece cuatro servicios de almacenamiento coherentes con Azure: blob, tabla cola y administración de cuentas. También ofrece un servicio de administración del almacenamiento en la nube para facilitar la administración del proveedor de los servicios de almacenamiento coherentes con Azure. Azure Storage proporciona la flexibilidad necesaria para almacenar y recuperar grandes cantidades de datos no estructurados, como documentos y archivos multimedia con Blobs de Azure y datos estructurados basados en NoSQL con Tablas de Azure. Para obtener más información sobre Azure Storage, consulte [Introducción a Microsoft Azure Storage](../storage/common/storage-introduction.md).

#### <a name="blob-storage"></a>Almacenamiento de blobs
Almacenamiento de blobs almacena cualquier conjunto de datos. Un blob puede ser un tipo cualquiera de datos binarios o texto, como un documento, un archivo multimedia o un instalador de aplicación. El almacenamiento de tablas almacena conjuntos de datos estructurados. Se trata de un almacén de datos de clave-atributo NoSQL, que permite el desarrollo rápido de grandes cantidades de datos y el acceso inmediato a los mismos. El almacenamiento de colas ofrece una solución de mensajería confiable para el procesamiento de flujos de trabajo y para la comunicación entre los componentes de los servicios en la nube.

Cada blob se organiza en un contenedor. Los contenedores también ofrecen una forma útil de asignar directivas de seguridad a grupos de objetos. Una cuenta de almacenamiento puede incluir un número cualquiera de contenedores y, a su vez, un contenedor puede incluir un número cualquiera de blobs, hasta alcanzar el límite de capacidad de 500 TB de la cuenta de almacenamiento. Blob Storage ofrece tres tipos de blobs: blobs en bloques, blobs en anexos y blobs en páginas (discos). Los blobs en bloques están optimizados para el streaming y para el almacenamiento de objetos en la nube, y constituyen una opción idónea para almacenar documentos, archivos multimedia y copias de seguridad, entre otros. Los blobs en anexos son similares a los blobs en bloques, pero están optimizados para anexar las operaciones. Un blob de anexos puede actualizarse solo al agregar un nuevo bloque al final. Los blob en anexos son una buena opción para escenarios como el registro, donde es necesario escribir solo al final del blob nuevos datos. Los blobs en páginas están optimizados para representar discos IaaS y admitir la escritura aleatoria. Pueden tener un tamaño máximo de 1 TB. Un disco IaaS asociado a una red de máquinas virtuales de Azure es un VHD almacenado como blob en páginas.

#### <a name="table-storage"></a>Almacenamiento de tablas
Este tipo de almacenamiento se basa en un almacén de claves/atributos NoSQL de Microsoft con un diseño sin esquema que lo diferencia de las bases de datos relacionales tradicionales. Dado que los almacenes de datos carecen de esquemas, es fácil adaptar los datos a medida que evolucionan las necesidades de la aplicación. El almacenamiento de tablas es fácil de usar, por lo que los desarrolladores pueden crear aplicaciones de forma rápida. Este tipo de almacenamiento se basa en un almacén de clave-atributo, lo que significa que cada valor de una tabla se almacena con un nombre de propiedad tipado. El nombre de propiedad se puede usar para filtrar y especificar criterios de selección. Una colección de propiedades y sus valores, componen una entidad. Puesto que este tipo de almacenamiento carece de esquemas, dos entidades de una misma tabla pueden contener diferentes colecciones de propiedades y dichas propiedades pueden ser de distintos tipos. El almacenamiento de tablas se puede usar para almacenar conjuntos de datos flexibles, como datos de usuarios para aplicaciones web, libretas de direcciones, información de dispositivos y cualquier otro tipo de metadatos requerido por el servicio. Una tabla puede almacenar un número cualquiera de entidades y una cuenta de almacenamiento puede incluir un número cualquiera de tablas, hasta alcanzar el límite de capacidad de este tipo de cuenta.

#### <a name="queue-storage"></a>Queue Storage
El almacenamiento en cola de Azure proporciona mensajería en la nube entre componentes de aplicaciones. A la hora de diseñar aplicaciones para escala, los componentes de las mismas suelen desacoplarse para poder escalarlos de forma independiente. El almacenamiento en cola ofrece mensajería asincrónica para la comunicación entre los componentes de las aplicaciones, independientemente de si se ejecutan en la nube, en el escritorio, en un servidor local o en un dispositivo móvil. Además, este tipo de almacenamiento admite la administración de tareas asincrónicas y la creación de flujos de trabajo de procesos.

### <a name="keyvault"></a>KeyVault
El RP KeyVault proporciona administración y auditoría de secretos, como contraseñas y certificados. Por ejemplo, un inquilino puede utilizar el RP KeyVault para proporcionar contraseñas o claves de administrador durante la implementación de máquinas virtuales.

## <a name="high-availability-for-azure-stack"></a>Alta disponibilidad para Azure Stack
Para conseguir la alta disponibilidad de un sistema de producción con varias VM en Azure, las VM se colocan en un [conjunto de disponibilidad](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) que las distribuye a varios dominios de error y dominios de actualización. En la escala más pequeña de Azure Stack, un dominio de error en un conjunto de disponibilidad se define como un único nodo en la unidad de escalado.  

Si bien la infraestructura de Azure Stack ya es resistente ante errores, la tecnología subyacente (clústeres de conmutación por error) de todos modos tiene cierto tiempo de inactividad de las máquinas virtuales en un servidor físico si se produce un error de hardware. Azure Stack admite un conjunto de disponibilidad con un máximo de tres dominios de error para coherencia con Azure.

- **Dominios de error**. Las máquinas virtuales colocadas en conjuntos de disponibilidad se aislarán físicamente entre sí al distribuirlas de la manera más uniforme que sea posible en varios dominios de error (nodos de Azure Stack). Si se produce un error de hardware, las máquinas virtuales del dominio de error que presente el error se reiniciarán en otros dominios de error pero, si es posible, se mantendrán en dominios de error independientes de las otras máquinas virtuales que se encuentran en el mismo conjunto de disponibilidad. Cuando el hardware vuelva a estar en línea, las máquinas virtuales se volverán a equilibrar para mantener la alta disponibilidad. 
 
- **Dominios de actualización**. Los dominios de actualización son otro concepto de Azure que proporciona alta disponibilidad en los conjuntos de disponibilidad. Un dominio de actualización es un grupo lógico de hardware adyacente que puede someterse a mantenimiento al mismo tiempo. Las máquinas virtuales que se encuentran en el mismo dominio de actualización se reiniciarán en conjunto durante el mantenimiento planeado. Cuando los inquilinos crean máquinas virtuales dentro de un conjunto de disponibilidad, la plataforma de Azure las distribuye de manera automática entre estos dominios de actualización. En Azure Stack, las máquinas virtuales se migran en vivo entre los otros hosts en línea del clúster antes de que se actualice su host subyacente. Como no hay tiempo de inactividad para el inquilino durante una actualización del host, la característica de dominio de actualización de Azure Stack solo existe para compatibilidad de plantilla con Azure. 

## <a name="role-based-access-control-rbac"></a>Control de acceso basado en roles (RBAC)
RBAC se puede usar para conceder acceso al sistema a usuarios, grupos y servicios autorizados asignándoles roles a nivel de suscripción, grupo de recursos o recurso individual. Cada rol define el nivel de acceso que un usuario, grupo o servicio tiene sobre los recursos de Microsoft Azure Stack.

RBAC de Azure cuenta con tres roles básicos que se aplican a todos los tipos de recurso: propietario, colaborador y lector. El propietario tiene acceso completo a todos los recursos y cuenta con el derecho a delegar acceso a otros. El colaborador puede crear y administrar todos los tipos de recursos de Azure pero no puede conceder acceso a otros. El lector solo puede ver los recursos existentes de Azure. El resto de los roles RBAC de Azure permiten la administración de recursos específicos de Azure. Por ejemplo, el rol de colaborador de máquina virtual permite crear y administrar máquinas virtuales, pero no permite administrar la red virtual ni la subred a las que se conecta la máquina virtual.

## <a name="usage-data"></a>Datos de uso
Microsoft Azure Stack recopila y agrega los datos de uso de todos los proveedores de recursos y los transmite a Azure para su procesamiento por parte de Comercio de Azure. Los datos de uso recopilados en Azure Stack pueden verse a través de una API de REST. Hay una API de inquilino coherente con Azure coherente, así como API de proveedor y de proveedor delegado para obtener datos de uso de todas las suscripciones del inquilino. Estos datos se pueden utilizar para realizar la integración con una herramienta o servicio externos con fines de facturación o contracargo. Una vez que Comercio de Azure ha procesado el uso, puede verse en el portal de facturación de Azure.

## <a name="next-steps"></a>Pasos siguientes
[Conceptos básicos de administración](azure-stack-manage-basics.md)

