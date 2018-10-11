---
title: Implementación de Azure Blockchain Workbench
description: Implementación de Azure Blockchain Workbench
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 10/1/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 3ee169e4139f5fc9cd4208c53c4997d50521fed7
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/03/2018
ms.locfileid: "48242102"
---
# <a name="deploy-azure-blockchain-workbench"></a>Implementación de Azure Blockchain Workbench

Azure Blockchain Workbench se implementa mediante una plantilla de solución de Azure Marketplace. La plantilla simplifica la implementación de los componentes necesarios para crear aplicaciones de cadena de bloques. Una vez implementado, Blockchain Workbench proporciona acceso a las aplicaciones cliente para crear y administrar usuarios y aplicaciones de cadena de bloques.

Para más información acerca de los componentes de Blockchain Workbench, consulte [Arquitectura de Azure Blockchain Workbench](architecture.md).

## <a name="prepare-for-deployment"></a>Preparación de la implementación

Blockchain Workbench le permite implementar un libro de contabilidad de cadena de bloques junto con un conjunto de los servicios de Azure correspondientes que se usan más frecuentemente para compilar una aplicación basada en cadena de bloques. La implementación de Blockchain Workbench hace que los siguientes servicios de Azure se aprovisionen en un grupo de recursos de la suscripción de Azure.

* 1 tema de Event Grid
* 1 espacio de nombres de Service Bus
* 1 instancia de Application Insights
* 1 instancia de SQL Database (Estándar S0)
* 2 instancias de App Service (Estándar)
* 2 instancias de Azure Key Vault
* 2 cuentas de Azure Storage (Estándar LRS)
* 2 Virtual Machine Scale Sets (para los nodos de validador y de trabajo)
* 2 instancias de Virtual Network (incluidos el equilibrador de carga, el grupo de seguridad de red y la dirección IP pública de cada red virtual)
* Opcional: Azure Monitor

La siguiente es una implementación de ejemplo creada en el grupo de recursos **myblockchain**.

![Implementación de ejemplo](media/deploy/example-deployment.png)

El costo de Blockchain Workbench se agrega al costo de los servicios de Azure subyacentes. La información de precios de los servicios de Azure se pueden calcular mediante la [calculadora de precios](https://azure.microsoft.com/pricing/calculator/).

Azure Blockchain Workbench necesita varios requisitos previos antes de la implementación. Entre estos requisitos previos se incluye la configuración de Azure AD y los registros de aplicaciones.

### <a name="blockchain-workbench-api-app-registration"></a>Registro de aplicación de API de Blockchain Workbench

La implementación de Blockchain Workbench requiere el registro de una aplicación de Azure AD. Necesita un inquilino de Azure Active Directory (Azure AD) para registrar la aplicación. Puede usar un inquilino existente o crear uno nuevo. Si va a usar un inquilino de Azure AD ya existente, necesitará suficientes permisos para registrar aplicaciones y otorgar permisos de Graph API dentro de un inquilino de Azure AD. Si no tiene permisos suficientes en un inquilino de Azure AD existente, cree un inquilino. 

> [!IMPORTANT]
> El área de trabajo no tiene que implementarse en el mismo inquilino que el que se usa para registrar una aplicación de Azure AD. Se debe implementar en un inquilino donde tenga permisos suficientes para implementar recursos. Para más información sobre los inquilinos de Azure AD, consulte [Obtención de un inquilino de Azure Active Directory](../../active-directory/develop/quickstart-create-new-tenant.md) e [Integración de aplicaciones con Azure Active Directory](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md).

1. Inicie sesión en el [Azure Portal](https://portal.azure.com).
2. Seleccione su cuenta en la esquina superior derecha y cambie al inquilino de Azure AD que desee. El inquilino debe ser el inquilino del administrador de la suscripción en la que está implementado Workbench y tener permisos suficientes para registrar aplicaciones.
3. En el panel de navegación izquierdo, seleccione el servicio **Azure Active Directory**. Seleccione **Registros de aplicaciones** > **Nuevo registro de aplicaciones**.

    ![Registro de aplicación](media/deploy/app-registration.png)

4. Proporcione un **nombre** y una **dirección URL de inicio de sesión** para la aplicación. Puede usar valores de marcador de posición ya que los valores se cambian durante la implementación. 

    ![Crear el registro de aplicaciones](media/deploy/app-registration-create.png)

    |Configuración  | Valor  |
    |---------|---------|
    |NOMBRE | `Blockchain API` |
    |Tipo de aplicación |Aplicación web/API|
    |URL de inicio de sesión | `https://blockchainapi` |

5. Seleccione **Crear** para registrar la aplicación de Azure AD.

### <a name="modify-application-manifest"></a>Modificación del manifiesto de aplicación

A continuación, debe modificar el manifiesto de aplicación para que use los roles de aplicación en Azure AD para especificar los administradores de Blockchain Workbench.  Para más información acerca de los manifiestos de aplicación, consulte [Manifiesto de aplicación de Azure Active Directory](../../active-directory/develop/reference-app-manifest.md).

1. Para la aplicación que registró, seleccione **Manifiesto** en el panel de detalles de la aplicación registrada.
2. Genere un identificador único global. Puede generar un GUID mediante el comando de PowerShell [guid] :: NewGuid () o el cmdlet New-GUID. Otra opción es usar un sitio web generador de GUID.
3. Va a actualizar la sección **appRoles** del manifiesto. En el panel Editar manifiesto, seleccione **Editar** y sustituya `"appRoles": []` por el código JSON que se proporciona. Asegúrese de reemplazar el valor del campo **ID** por el identificador único global que generó. 

    ``` json
    "appRoles": [
         {
           "allowedMemberTypes": [
             "User",
             "Application"
           ],
           "displayName": "Administrator",
           "id": "<A unique GUID>",
           "isEnabled": true,
           "description": "Blockchain Workbench administrator role allows creation of applications, user to role assignments, etc.",
           "value": "Administrator"
         }
       ],
    ```

    ![Editar manifiesto](media/deploy/edit-manifest.png)

    > [!IMPORTANT]
    > El valor **Administrador** es necesario para identificar los administradores de Blockchain Workbench.

4.  Haga clic en **Guardar** para guardar los cambios del manifiesto de aplicación.

### <a name="add-graph-api-required-permissions"></a>Incorporación de los permisos necesarios de Graph API

La aplicación de API necesita solicitar permiso del usuario para acceder al directorio. Establezca el siguiente permiso necesario para la aplicación de API:

1. En el registro de la aplicación de API de Blockchain, seleccione **Configuración > Permisos necesarios > Seleccionar una API > Microsoft Graph**.

    ![Selección de una API](media/deploy/client-app-select-api.png)

    Haga clic en **Seleccionar**.

2. En **Habilitar acceso** en **Permisos de la aplicación**, elija **Leer los perfiles completos de todos los usuarios**.

    ![Habilitar acceso](media/deploy/client-app-read-perms.png)

    Haga clic en **Seleccionar** y, a continuación, en **Listo**.

3. En **Permisos necesarios**, seleccione **Conceder permisos** y, a continuación, seleccione **Sí** para el mensaje de comprobación.

   ![Concesión de permisos](media/deploy/client-app-grant-permissions.png)

   La concesión de permisos permite a Blockchain Workbench acceder a los usuarios del directorio. Se requiere el permiso de lectura para buscar y agregar miembros a Blockchain Workbench.

### <a name="add-graph-api-key-to-application"></a>Incorporación de la clave de Graph API a la aplicación

Blockchain Workbench usa Azure AD como sistema principal de administración de identidades de los usuarios que interactúan con aplicaciones de cadena de bloques. Para que Blockchain Workbench acceda a Azure AD y recupere información sobre el usuario como nombres y correos electrónicos, debe agregar una clave de acceso. Blockchain Workbench utiliza la clave para autenticarse en Azure AD.

1. Para la aplicación que registró, seleccione **Configuración** en el panel de detalles de la aplicación registrada.
2. Seleccione **Claves**.
3. Agregue una nueva clave especificando una **descripción** de clave y seleccionando un valor de tiempo en **Expira**. 

    ![Crear clave](media/deploy/app-key-create.png)

    |Configuración  | Valor  |
    |---------|---------|
    | DESCRIPCIÓN | `Service` |
    | Expira | Seleccione el momento de expiración |

4. Seleccione **Guardar**. 
5. Copie el valor de la clave y almacénelo para más tarde. Lo necesitará para la implementación.

    > [!IMPORTANT]
    >  Si no guarda la clave para la implementación, deberá generar una nueva. Más adelante, no se puede recuperar el valor de la clave desde el portal.

### <a name="get-application-id"></a>Obtención del identificador de la aplicación

Se necesita el identificador de la aplicación y la información del inquilino para la implementación. Recopile y almacene la información para su uso durante la implementación.

1. Para la aplicación que registró, seleccione **Configuración** > **Propiedades**.
2.  En el panel **Propiedades**, copie y almacene los siguientes valores para su uso durante la implementación.

    ![Propiedades de la aplicación de API](media/deploy/app-properties.png)

    | Configuración para almacenar  | Uso en la implementación |
    |------------------|-------------------|
    | Identificador de aplicación | Configuración de Azure Active Directory > Identificador de aplicación |

### <a name="get-tenant-domain-name"></a>Obtención del nombre de dominio del inquilino

Recopile y almacene el nombre de dominio del inquilino de Active Directory donde se registran las aplicaciones. 

En el panel de navegación izquierdo, seleccione el servicio **Azure Active Directory**. Seleccione **Nombres de dominio personalizados**. Copie y almacene el nombre de dominio.

![Nombre de dominio](media/deploy/domain-name.png)

## <a name="deploy-blockchain-workbench"></a>Implementación de Blockchain Workbench

Una vez que se han completado los pasos descritos en los requisitos previos, estará listo para implementar Blockchain Workbench. En las secciones siguientes se describe cómo implementar la plataforma.

1.  Inicie sesión en el [Azure Portal](https://portal.azure.com).
2.  Seleccione su cuenta en la esquina superior derecha y cambie al inquilino de Azure AD en el que desee implementar Azure Blockchain Workbench.
3.  En el panel izquierdo, seleccione **Crear un recurso**. Busque `Azure Blockchain Workbench` en la barra de búsqueda de **Buscar en el Marketplace**. 

    ![Barra de búsqueda en Marketplace](media/deploy/marketplace-search-bar.png)

4.  Seleccione **Azure Blockchain Workbench**.

    ![Resultados de la búsqueda en Marketplace](media/deploy/marketplace-search-results.png)

4.  Seleccione **Crear**.
5.  Complete la configuración básica.

    ![Creación de Azure Blockchain Workbench](media/deploy/blockchain-workbench-settings-basic.png)

    | Configuración | DESCRIPCIÓN  |
    |---------|--------------|
    | Prefijo de recurso. | Identificador único corto para la implementación. Este valor se utiliza como base para asignar nombres a los recursos. |
    | Nombre de usuario de máquina virtual | El nombre de usuario se utiliza como administrador de todas las máquinas virtuales (VM). |
    | Tipo de autenticación | Seleccione si desea utilizar una contraseña o clave para conectarse a las máquinas virtuales. |
    | Contraseña | La contraseña se usa para conectarse a las máquinas virtuales. |
    | SSH | Use una clave pública RSA en formato de una sola línea con **ssh-rsa** o utilice el formato PEM de varias líneas. Puede generar claves SSH mediante `ssh-keygen` en Linux y OS X o PuTTYGen en Windows. Para más información sobre las claves SSH, consulte [Uso de claves SSH con Windows en Azure](../../virtual-machines/linux/ssh-from-windows.md). |
    | Contraseña de base de datos / Confirmar contraseña de la base de datos | Especifique la contraseña que se utilizará para acceder a la base de datos creada como parte de la implementación. |
    | Región de la implementación | Especifique dónde se van a implementar los recursos de Blockchain Workbench. Para una mejor disponibilidad, el valor debe ser el mismo que el de **Ubicación**. |
    | Subscription | Especifique la suscripción de Azure que desea usar para la implementación. |
    | Grupos de recursos | Cree un nuevo grupo de recursos seleccionando **Crear nuevo** y especifique un nombre de grupo de recursos único. |
    | Ubicación | Especifique la región en la que desea implementar la plataforma. |

6.  Seleccione **Aceptar** para finalizar la sección de configuración básica.

7.  Complete la **configuración de Azure Active Directory**.

    ![Configuración de Azure AD](media/deploy/blockchain-workbench-settings-aad.png)

    | Configuración | DESCRIPCIÓN  |
    |---------|--------------|
    | Nombre de dominio | Use el inquilino de Azure AD que recopiló en la sección [Obtención del nombre de dominio del inquilino](#get-tenant-domain-name) de los requisitos previos. |
    | Identificador de aplicación | Use el identificador de aplicación del registro de aplicación cliente de Blockchain que recopiló en la sección [Obtención del identificador de la aplicación](#get-application-id) de los requisitos previos. |
    | Clave de aplicación | Use la clave de aplicación del registro de aplicación cliente de Blockchain que recopiló en la sección [Incorporación de la clave de Graph API a la aplicación](#add-graph-api-key-to-application) de los requisitos previos. |


8.  Haga clic en **Aceptar** para finalizar la sección de configuración de los parámetros de Azure AD.

9.  En **Network Settings and Performance** (Configuración de red y rendimiento), elija si desea crear una nueva red de cadena de bloques o usar una red de cadena de bloques prueba de autoridad existente.

    Para **Crear nueva**:

    La opción *crear nueva* opción crea un conjunto de prueba de autoridad de (PoA) Ethereum dentro de la suscripción de un miembro único. 

    ![Configuración de red y rendimiento](media/deploy/blockchain-workbench-settings-network-new.png)

    | Configuración | DESCRIPCIÓN  |
    |---------|--------------|
    | Número de nodos de la cadena de bloques | Elija el número de nodos del control de servidor de validación Ethereum PoA que se van a implementar en la red. |
    | Rendimiento del almacenamiento | Elija el rendimiento de almacenamiento preferido de la máquina virtual para la red de cadena de bloques. |
    | Tamaño de la máquina virtual | Elija el tamaño preferido de la máquina virtual para la red de cadena de bloques. |

    Para **Usar existente**:

    La opción *usar existente* opción le permite especificar una red de cadena de bloques de prueba de autoridad (PoA) de Ethereum. Los puntos de conexión tienen los siguientes requisitos.

    * El punto de conexión debe ser una red de cadena de bloques de prueba de autoridad (PoA) de Ethereum.
    * El punto de conexión debe ser públicamente accesible a través de la red.
    * La red de cadena de bloques PoA debe configurarse para que el precio del gas se establezca en cero (Nota: Las cuentas de Blockchain Workbench no están financiadas. Si se requieren fondos, habrá un error de transacción).

    ![Configuración de red y rendimiento](media/deploy/blockchain-workbench-settings-network-existing.png)

    | Configuración | DESCRIPCIÓN  |
    |---------|--------------|
    | Punto de conexión RPC de Ethereum | Proporcione el punto de conexión RPC de una red de cadena de bloques PoA existente. El punto de conexión comienza con http:// y termina con un número de puerto. Por ejemplo: `http://contoso-chain.onmicrosoft.com:8545` |
    | Rendimiento del almacenamiento | Elija el rendimiento de almacenamiento preferido de la máquina virtual para la red de cadena de bloques. |
    | Tamaño de la máquina virtual | Elija el tamaño preferido de la máquina virtual para la red de cadena de bloques. |

10. Seleccione **Aceptar** para finalizar la configuración de red y el rendimiento.

11. Complete la configuración de **Azure Monitor**.

    ![Azure Monitor](media/deploy/blockchain-workbench-settings-oms.png)

    | Configuración | DESCRIPCIÓN  |
    |---------|--------------|
    | Supervisión | Elija si desea habilitar Azure Monitor para supervisar la red de la cadena de bloques |
    | Conectarse a instancia de Log Analytics existente | Elija si desea usar una instancia de Log Analytics ya existente o crear una nueva. Si opta por la primera opción, escriba su identificador del área de trabajo y su clave principal. |

12. Haga clic en **Aceptar** para completar la sección de Azure Monitor.

13. Revise el resumen para comprobar que los parámetros son precisos.

    ![Resumen](media/deploy/blockchain-workbench-summary.png)

14. Seleccione **Crear** para aceptar los términos e implementar Azure Blockchain Workbench.

La implementación puede tardar hasta 90 minutos. Puede usar Azure Portal para supervisar el progreso. En el grupo de recursos recién creado, seleccione **Implementaciones > Información general** para ver el estado de los artefactos implementados.

## <a name="blockchain-workbench-web-url"></a>Dirección URL web de Blockchain Workbench

Cuando se haya completado la implementación de Blockchain Workbench, un nuevo grupo de recursos contendrá los recursos de este. Se puede acceder a los servicios de Blockchain Workbench a través de una dirección URL web. Los pasos siguientes muestran cómo recuperar la dirección URL web de la plataforma implementada.

1. Inicie sesión en el [Azure Portal](https://portal.azure.com).
2. En el panel de navegación de la izquierda, seleccione **Grupos de recursos**.
3. Elija el nombre del grupo de recursos que especificó al implementar Blockchain Workbench.
4. Haga clic en el encabezado de columna **TIPO** para ordenar la lista alfabéticamente por tipo.
5. Hay dos recursos del tipo **App Service**. Seleccione el recurso del tipo **App Service** *sin* el sufijo "-api".

    ![Lista de App Service](media/deploy/resource-group-list.png)

6.  En la sección **Información esencial** de App Service, copie el valor **URL** que representa la dirección URL web a la instancia de Blockchain Workbench implementada.

    ![Información esencial de App Service](media/deploy/app-service.png)

Para asociar un nombre de dominio personalizado a Blockchain Workbench, consulte [Configuración de un nombre de dominio personalizado para una aplicación web en Azure App Service mediante Traffic Manager](../../app-service/web-sites-traffic-manager-custom-domain-name.md).

## <a name="configuring-the-reply-url"></a>Configuración de la dirección URL de respuesta

Una vez que se ha implementado Azure Blockchain Workbench, el siguiente paso es asegurarse de que la aplicación cliente de Azure Active Directory (Azure AD) se ha registrado en la **dirección URL de respuesta** correcta de la dirección URL web de la instancia de Blockchain Workbench implementada.

1. Inicie sesión en el [Azure Portal](https://portal.azure.com).
2. Compruebe que se encuentra en el inquilino en el que se ha registrado la aplicación cliente de Azure AD.
3. En el panel de navegación izquierdo, seleccione el servicio **Azure Active Directory**. Seleccione **App registrations** (Registros de aplicaciones).
4. Seleccione la aplicación cliente de Azure AD que registró en la sección de requisitos previos.
5. Seleccione **Configuración > URL de respuesta**.
6. Especifique la dirección URL web principal de la implementación de Azure Blockchain Workbench que recuperó en la sección **Obtención de la dirección URL web de Azure Blockchain Workbench**. La dirección URL de respuesta tiene como prefijo `https://`. Por ejemplo: `https://myblockchain2-7v75.azurewebsites.net`

    ![URL de respuesta](media/deploy/configure-reply-url.png)

7. Seleccione **Guardar** para actualizar el registro del cliente.

## <a name="remove-a-deployment"></a>Eliminación de una implementación

Cuando ya no se necesita una implementación, puede quitarla eliminando el grupo de recursos de Blockchain Workbench.

1. En Azure Portal, vaya a **Grupo de recursos** en el panel de navegación izquierdo y seleccione el grupo de recursos que desea eliminar. 
2. Seleccione **Eliminar grupo de recursos**. Compruebe la eliminación escribiendo el nombre del grupo de recursos y seleccionando **Eliminar**.

    ![Eliminación de un grupo de recursos](media/deploy/delete-resource-group.png)

## <a name="next-steps"></a>Pasos siguientes

En este artículo, ha implementado Azure Blockchain Workbench. Para aprender a crear una aplicación de cadena de bloques, continúe con el siguiente artículo de procedimientos.

> [!div class="nextstepaction"]
> [Creación de una aplicación de cadena de bloques en Azure Blockchain Workbench](create-app.md)
