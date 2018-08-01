---
title: Reglas de pertenencia a grupos dinámicos basada en atributos en Azure Active Directory | Microsoft Docs
description: Procedimientos para crear reglas avanzadas para la pertenencia dinámica a grupos, con operadores y parámetros de reglas de expresión admitidos.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 07/24/2018
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.openlocfilehash: e49da237584a48c01e72552abae01da2514da3c1
ms.sourcegitcommit: 156364c3363f651509a17d1d61cf8480aaf72d1a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/25/2018
ms.locfileid: "39248896"
---
# <a name="create-dynamic-groups-with-attribute-based-membership-in-azure-active-directory"></a>Creación de grupos dinámicos con pertenencia basada en atributos en Azure Active Directory

En Azure Active Directory (Azure AD), puede crear reglas basadas en atributos complejos para habilitar la pertenencia dinámica a grupos. En este artículo se detallan los atributos y la sintaxis para crear reglas de pertenencia dinámica para usuarios o dispositivos. Puede configurar una regla de pertenencia dinámica a grupos de seguridad o en grupos de Office 365.

Cuando cambia cualquier atributo de un usuario, el sistema evalúa todas las reglas de grupos dinámicos de un directorio para ver si la modificación desencadenaría adiciones o retiradas en el grupo. Si un usuario o dispositivo cumple una regla de un grupo, se agrega a este como miembro. Cuando ya no cumple la regla, se quita.

> [!NOTE]
> Esta característica requiere una licencia de Azure AD Premium P1 para cada usuario único que sea miembro de uno o varios grupos dinámicos. No es necesario asignar licencias a los usuarios para que sean miembros de los grupos dinámicos, pero es preciso tener en el inquilino el número mínimo de licencias para cubrirlos a todos. Por ejemplo, si tiene un total de 1.000 usuarios únicos en todos los grupos dinámicos del inquilino, necesitaría al menos 1.000 licencias de Azure AD Premium P1 para cumplir el requisito de licencia.
>
> Puede crear un grupo dinámico para dispositivos o usuarios, pero no se puede crear una regla que contenga tanto usuarios como dispositivos.
> 
> En este momento, no es posible crear un grupo de dispositivos basado en atributos del usuario propietario. Las reglas de pertenencia de dispositivo solo pueden hacer referencia a atributos inmediatos de objetos de dispositivo del directorio.

## <a name="to-create-an-advanced-rule"></a>Para crear una regla avanzada

1. Inicie sesión en el [Centro de administración de Azure AD](https://aad.portal.azure.com) con una cuenta que sea administrador global o administrador de cuentas de usuario.
2. Seleccione **Usuarios y grupos**.
3. Seleccione **Todos los grupos** y, luego, **Nuevo grupo**.

   ![Add new group](./media/groups-dynamic-membership/new-group-creation.png)

4. En la hoja **Grupo** , escriba un nombre y una descripción para el nuevo grupo. En **Tipo de pertenencia**, seleccione **Usuario dinámico** o **Dispositivo dinámico**, dependiendo de si quiere crear una regla para usuarios o dispositivos y, a continuación, seleccione **Agregar una consulta dinámica**. Puede usar el generador de reglas para crear una regla sencilla o escriba usted mismo una regla avanzada. Este artículo contiene más información sobre los atributos de usuario y dispositivo disponibles, así como ejemplos de las reglas avanzadas.

   ![Adición de una regla de pertenencia dinámica](./media/groups-dynamic-membership/add-dynamic-group-rule.png)

5. Después de crear la regla, seleccione **Agregar consulta** en la parte superior de la hoja.
6. Seleccione **Crear** on the **Grupo** para crear el grupo.

> [!TIP]
> Se produce un error al crear el grupo si la regla que escribió no tiene el formato correcto o no es válida. Se muestra una notificación en la esquina superior derecha del portal, con una explicación de por qué no se pudo procesar la regla. Léala con cuidado para saber cómo debe ajustar la regla para que sea válida.

## <a name="status-of-the-dynamic-rule"></a>Estado de la regla dinámica

Puede ver el estado de procesamiento de la pertenencia y la última fecha actualizada en la página de información general del grupo dinámico.
  
  ![visualización del estado de grupo dinámico](./media/groups-dynamic-membership/group-status.png)


Los mensajes de estado siguientes se pueden mostrar para el estado de **procesamiento de la pertenencia**:

* **Evaluando**: se ha recibido el cambio de grupo y las actualizaciones se están evaluando.
* **Procesando**: las actualizaciones se están procesando.
* **Actualización completada**: se ha completado el procesamiento y se han realizado todas las actualizaciones aplicables.
* **Error de procesamiento**: se detectó un error al evaluar la regla de pertenencia y no se pudo completar el procesamiento.
* **Actualización en pausa**: el administrador han pausado las actualizaciones de la regla de pertenencia dinámica. MembershipRuleProcessingState se establece en "Paused".

Los mensajes de estado siguientes se pueden mostrar para el estado **Última actualización de la pertenencia**:

* &lt;**Fecha y hora**&gt;: la última vez que se actualizó la pertenencia.
* **En curso**: las actualizaciones están actualmente en curso.
* **Desconocido**: no se puede recuperar la hora de la última actualización. Puede deberse a que el grupo sea recién creado.

Si se produce un error al procesar la regla de pertenencia para un grupo específico, se muestra una alerta en la parte superior de la **página de información general** del grupo. Si no se puede procesar ninguna actualización de pertenencia dinámica pendiente para todos los grupos del inquilino durante más de 24 horas, se muestra una alerta en la parte superior de **Todos los grupos**.

![mensaje de error de procesamiento](./media/groups-dynamic-membership/processing-error.png)

## <a name="constructing-the-body-of-an-advanced-rule"></a>Construcción del cuerpo de una regla avanzada

La regla avanzada que se puede crear para las pertenencias dinámicas para grupos es esencialmente una expresión binaria que consta de tres partes y da como resultado true o false. Las tres partes son:

* Parámetro a la izquierda
* Operador binario
* Constante a la derecha

Una regla avanzada completa es similar a esto: (leftParameter binaryOperator "RightConstant"), donde los paréntesis de apertura y cierre son opcionales para toda la expresión binaria, las comillas dobles también son opcionales, pero solo se requieren para la constante de la derecha cuando es una cadena, y la sintaxis para el parámetro izquierdo es user.property. Una regla avanzada puede constar de más de una expresión binaria separadas por los operadores lógicos -and, -or y -not.

Los siguientes son ejemplos de una regla avanzada construida correctamente:
```
(user.department -eq "Sales") -or (user.department -eq "Marketing")
(user.department -eq "Sales") -and -not (user.jobTitle -contains "SDE")
```
Para obtener una lista completa de los parámetros y los operadores de regla de expresión admitidos, vea las secciones siguientes. En el caso de atributos usados para reglas de dispositivo, consulte [Uso de atributos para crear reglas para los objetos de dispositivo](#using-attributes-to-create-rules-for-device-objects).

La longitud total del cuerpo de la regla avanzada no puede superar los 2048 caracteres.

> [!NOTE]
> Las operaciones de cadena y regex no distinguen mayúsculas de minúsculas. También puede realizar comprobaciones Null, usando *null* como constante; por ejemplo, user.department -eq *null*.
> Las cadenas que contienen comillas " deben convertirse en escape con caracteres '; por ejemplo, user.department -eq \`"Sales".

## <a name="supported-expression-rule-operators"></a>Operadores de regla de expresión admitidos

En la tabla siguiente se enumeran todos los operadores de regla de expresión admitidos y su sintaxis para su uso en el cuerpo de la regla avanzada:

| Operador | Sintaxis |
| --- | --- |
| Not Equals |-ne |
| Equals |-eq |
| Not Starts With |-notStartsWith |
| Starts With |-startsWith |
| Not Contains |-notContains |
| Contains |-contains |
| Not Match |-notMatch |
| Match |-match |
| En | -in |
| No en el | -notIn |

## <a name="operator-precedence"></a>Precedencia de operadores

Todos los operadores se enumeran a continuación por orden de precedencia, de menor a mayor. Los operadores en la misma línea tienen la misma prioridad:

````
-any -all
-or
-and
-not
-eq -ne -startsWith -notStartsWith -contains -notContains -match –notMatch -in -notIn
````

Todos los operadores se pueden utilizar con o sin el prefijo de guion. Los paréntesis son necesarios solo si la precedencia no cumple sus requisitos.
Por ejemplo: 

```
   user.department –eq "Marketing" –and user.country –eq "US"
```

equivale a:

```
   (user.department –eq "Marketing") –and (user.country –eq "US")
```

## <a name="using-the--in-and--notin-operators"></a>Uso de los operadores -In y -notIn

Si desea comparar el valor de un atributo de usuario con un número de valores diferentes, puede usar los operadores -In o -notIn. A continuación se muestra un ejemplo del operador -In:
```
   user.department -In ["50001","50002","50003",“50005”,“50006”,“50007”,“50008”,“50016”,“50020”,“50024”,“50038”,“50039”,“51100”]
```
Tenga en cuenta el uso de "[" y "]" al principio y al final de la lista de valores. Esta condición se evalúa como True si el valor de user.department es igual a uno de los valores en la lista.


## <a name="query-error-remediation"></a>Corrección de errores de consulta

En la tabla siguiente se enumeran los errores comunes y se indica cómo corregirlos:

| Error de análisis de consulta | Uso con error | Uso corregido |
| --- | --- | --- |
| Error: no se admite el atributo. |(user.invalidProperty -eq "Value") |(user.department -eq "value")<br/><br/>Asegúrese de que el atributo se encuentra en la [lista de propiedades admitidas](#supported-properties). |
| Error: no se admite el operador en el atributo. |(user.accountEnabled -contains true) |(user.accountEnabled -eq true)<br/><br/>El operador usado no se admite para el tipo de propiedad (en este ejemplo, -contains no se puede usar con el tipo boolean). Utilice los operadores correctos para el tipo de propiedad. |
| Error: error de compilación de consulta. |1. (user.department -eq "Sales") (user.department -eq "Marketing")<br/><br/>2. (user.userPrincipalName -match "*@domain.ext") |1. Falta el operador. Use -and u -or para predicados de combinación<br/><br/>(user.department -eq "Sales") -or (user.department -eq "Marketing")<br/><br/>2. Error en la expresión regular usada con -match<br/><br/>(user.userPrincipalName -match ".*@domain.ext"), o bien: (user.userPrincipalName -match "\@domain.ext$")|

## <a name="supported-properties"></a>Propiedades admitidas

A continuación se muestran todas las propiedades de usuario que puede utilizar en la regla avanzada:

### <a name="properties-of-type-boolean"></a>Propiedades de tipo booleano

Operadores permitidos

* -eq
* -ne

| Properties (Propiedades) | Valores permitidos | Uso |
| --- | --- | --- |
| accountEnabled |true false |user.accountEnabled -eq true |
| dirSyncEnabled |true false |user.dirSyncEnabled -eq true |

### <a name="properties-of-type-string"></a>Propiedades de tipo cadena

Operadores permitidos

* -eq
* -ne
* -notStartsWith
* -startsWith
* -contains
* -notContains
* -match
* -notMatch
* -in
* -notIn

| Properties (Propiedades) | Valores permitidos | Uso |
| --- | --- | --- |
| city |Cualquier valor de cadena o *null* |(user.city -eq "value") |
| country |Cualquier valor de cadena o *null* |(user.country -eq "value") |
| companyName | Cualquier valor de cadena o *null* | (user.companyName -eq "value") |
| department |Cualquier valor de cadena o *null* |(user.department -eq "value") |
| DisplayName |Cualquier valor de cadena |(user.displayName -eq "value") |
| employeeId |Cualquier valor de cadena |(user.employeeId -eq "value")<br>(user.employeeId -ne *null*) |
| facsimileTelephoneNumber |Cualquier valor de cadena o *null* |(user.facsimileTelephoneNumber -eq "value") |
| givenName |Cualquier valor de cadena o *null* |(user.givenName -eq "value") |
| jobTitle |Cualquier valor de cadena o *null* |(user.jobTitle -eq "value") |
| mail |Cualquier valor de cadena o *null* (dirección SMTP del usuario) |(user.mail -eq "value") |
| mailNickName |Cualquier valor de cadena (alias de correo electrónico del usuario) |(user.mailNickName -eq "value") |
| mobile |Cualquier valor de cadena o *null* |(user.mobile -eq "value") |
| objectId |GUID del objeto de usuario |(user.objectId -eq "11111111-1111-1111-1111-111111111111") |
| onPremisesSecurityIdentifier | Identificador de seguridad local (SID) para los usuarios que se han sincronizado desde local a la nube. |(user.onPremisesSecurityIdentifier -eq "S-1-1-11-1111111111-1111111111-1111111111-1111111") |
| passwordPolicies |None DisableStrongPassword DisablePasswordExpiration DisablePasswordExpiration, DisableStrongPassword |(user.passwordPolicies -eq "DisableStrongPassword") |
| physicalDeliveryOfficeName |Cualquier valor de cadena o *null* |(user.physicalDeliveryOfficeName -eq "value") |
| postalCode |Cualquier valor de cadena o *null* |(user.postalCode -eq "value") |
| preferredLanguage |Código ISO 639-1 |(user.preferredLanguage -eq "en-US") |
| sipProxyAddress |Cualquier valor de cadena o *null* |(user.sipProxyAddress -eq "value") |
| state |Cualquier valor de cadena o *null* |(user.state -eq "value") |
| streetAddress |Cualquier valor de cadena o *null* |(user.streetAddress -eq "value") |
| surname |Cualquier valor de cadena o *null* |(user.surname -eq "value") |
| telephoneNumber |Cualquier valor de cadena o *null* |(user.telephoneNumber -eq "value") |
| usageLocation |Código de país con dos letras |(user.usageLocation -eq "US") |
| userPrincipalName |Cualquier valor de cadena |(user.userPrincipalName -eq "alias@domain") |
| userType |miembro invitado *null* |(user.userType -eq "Member") |

### <a name="properties-of-type-string-collection"></a>Propiedades de colección de cadenas de tipo

Operadores permitidos

* -contains
* -notContains

| Properties (Propiedades) | Valores permitidos | Uso |
| --- | --- | --- |
| otherMails |Cualquier valor de cadena |(user.otherMails -contains "alias@domain") |
| proxyAddresses |SMTP: alias@domain smtp: alias@domain |(user.proxyAddresses -contains "SMTP: alias@domain") |

## <a name="multi-value-properties"></a>Propiedades de varios valores

Operadores permitidos

* -any (se satisface cuando al menos un elemento de la colección coincide con la condición)
* -all (se satisface cuando todos los elementos de la colección coinciden con la condición)

| Properties (Propiedades) | Valores | Uso |
| --- | --- | --- |
| assignedPlans |Cada objeto de la colección expone las siguientes propiedades de cadena: capabilityStatus, service, servicePlanId |user.assignedPlans -any (assignedPlan.servicePlanId -eq "efb87545-963c-4e0d-99df-69c6916d9eb0" -and assignedPlan.capabilityStatus -eq "Enabled") |
| proxyAddresses| SMTP: alias@domain smtp: alias@domain | (user.proxyAddresses -any (\_ -contains "contoso")) |

Las propiedades de varios valores son colecciones de objetos del mismo tipo. Puede usar los operadores -any y -all para aplicar una condición a uno o todos los elementos de la colección, respectivamente. Por ejemplo: 

assignedPlans es una propiedad de varios valores que muestra todos los planes de servicio asignados al usuario. En la expresión siguiente se seleccionarán los usuarios que tienen el plan de servicio Exchange Online (Plan 2) que también está en estado Habilitado:

```
user.assignedPlans -any (assignedPlan.servicePlanId -eq "efb87545-963c-4e0d-99df-69c6916d9eb0" -and assignedPlan.capabilityStatus -eq "Enabled")
```

(El identificador GUID identifica el plan de servicio Exchange Online [Plan 2]).

> [!NOTE]
> Esto es útil si desea identificar a todos los usuarios para los que se habilitó una funcionalidad de Office 365 (u otro servicio en línea de Microsoft), por ejemplo, para establecerlos como destino de cierto conjunto de directivas.

En la expresión siguiente se seleccionan todos los usuarios que tengan algún plan de servicio asociado con el servicio de Intune (identificado por el nombre de servicio "SCO"):
```
user.assignedPlans -any (assignedPlan.service -eq "SCO" -and assignedPlan.capabilityStatus -eq "Enabled")
```

### <a name="using-the-underscore--syntax"></a>Con la sintaxis de guión bajo (\_)

La sintaxis de guión bajo (\_) coincide con las apariciones de un valor específico en una de las propiedades de la colección de cadenas multivalor para agregar usuarios o dispositivos a un grupo dinámico. Se usa con los operadores -any u -all.

Este es un ejemplo de uso del guión bajo (\_) en una regla para agregar miembros basados en user.proxyAddress (funciona de la misma forma para user.otherMails). Esta regla agrega cualquier usuario con la dirección de proxy que contiene "contoso" al grupo.

```
(user.proxyAddresses -any (_ -contains "contoso"))
```

## <a name="use-of-null-values"></a>Uso de valores nulos

Para especificar un valor nulo en una regla, puede usar el valor *null*. Tenga cuidado de no usar comillas alrededor de la palabra *null*. Si lo hace, se interpretará como un valor de cadena literal. El operador -not no se puede usar como operador comparativo con valores null. Si lo hace, recibirá un error tanto si usa null como si usa $null. En su lugar, use -eq o -ne. La manera correcta de hacer referencia al valor null es como sigue:
```
   user.mail –ne $null
```

## <a name="extension-attributes-and-custom-attributes"></a>Atributos de extensión y atributos personalizados
Se admiten los atributos de extensión y los atributos personalizados en las reglas de pertenencia dinámica.

Los atributos de extensión se sincronizan desde Windows Server AD local y tienen el formato "ExtensionAttributeX", donde X es igual a un número de 1 a 15.
Un ejemplo de una regla que utiliza un atributo de extensión sería

```
(user.extensionAttribute15 -eq "Marketing")
```

Los atributos personalizados se sincronizan desde Windows Server AD local o desde una aplicación SaaS conectada y el formato de "user.extension_[GUID]\__[Attribute]", donde [GUID] es el identificador único de AAD para la aplicación que creó el atributo en Azure AD y [Atributo] es el nombre del atributo cuando se creó. Un ejemplo de una regla que utiliza un atributo personalizado es

```
user.extension_c272a57b722d4eb29bfe327874ae79cb__OfficeNumber  
```

El nombre de atributo personalizado se puede encontrar en el directorio mediante la consulta de un atributo de usuario a través del explorador de Windows Azure AD Graph y la búsqueda del nombre en cuestión.

## <a name="direct-reports-rule"></a>Regla de "subordinados directos"
Puede crear un grupo con todos los subordinados directos de un administrador. Cuando los subordinados directos de un administrador cambien en el futuro, la pertenencia del grupo se ajustará automáticamente.

> [!NOTE]
> 1. Para que la regla funcione, asegúrese de que la propiedad de **identificador de administrador** esté establecida correctamente en los usuarios del inquilino. Puede comprobar el valor actual de un usuario en la **pestaña Perfil**.
> 2. Esta regla admite solo subordinados **directos**. Actualmente no es posible crear un grupo para una jerarquía anidada, por ejemplo, un grupo que incluya subordinados directos y sus subordinados.
> 3. Esta regla no se puede combinar con ninguna otra regla avanzada.

**Para configurar el grupo**

1. Siga los pasos del 1 al 5 de la sección [Para crear la regla avanzada](#to-create-the-advanced-rule) y seleccione el **tipo de pertenencia** de **Usuario dinámico**.
2. En la hoja **Dynamic membership rules** (Reglas de pertenencia dinámica), escriba la regla con la siguiente sintaxis:

    *Direct Reports for "{objectID_of_manager}"*

    Ejemplo de una regla válida:
```
                    Direct Reports for "62e19b97-8b3d-4d4a-a106-4ce66896a863"
```
    where “62e19b97-8b3d-4d4a-a106-4ce66896a863” is the objectID of the manager. The object ID can be found on manager's **Profile tab**.
3. Después de guardar la regla, todos los usuarios con el valor de identificador de administrador especificado se agregarán al grupo.

## <a name="using-attributes-to-create-rules-for-device-objects"></a>Uso de atributos para crear reglas para los objetos de dispositivo
También puede crear una regla que selecciona objetos de dispositivo para la pertenencia de un grupo. Pueden utilizarse los siguientes atributos del dispositivo.

 Atributo de dispositivo  | Valores | Ejemplo
 ----- | ----- | ----------------
 accountEnabled | true false | (device.accountEnabled -eq true)
 DisplayName | Cualquier valor de cadena |(device.displayName -eq "Rob Iphone”)
 deviceOSType | Cualquier valor de cadena | (device.deviceOSType -eq "iPad") -or (device.deviceOSType -eq "iPhone")
 deviceOSVersion | Cualquier valor de cadena | (device.OSVersion -eq "9.1")
 deviceCategory | un nombre de la categoría de dispositivo válido | (device.deviceCategory -eq "BYOD")
 deviceManufacturer | Cualquier valor de cadena | (device.deviceManufacturer -eq "Samsung")
 deviceModel | Cualquier valor de cadena | (device.deviceModel -eq "iPad Air")
 deviceOwnership | Personal, empresa, desconocido | (device.deviceOwnership -eq "Company")
 domainName | Cualquier valor de cadena | (device.domainName -eq "contoso.com")
 enrollmentProfileName | Perfil de inscripción de dispositivo Apple o nombre de perfil de Windows AutoPilot | (device.enrollmentProfileName -eq "DEP iPhones")
 isRooted | true false | (device.isRooted -eq true)
 managementType | MDM (para dispositivos móviles)<br>PC (para equipos administrados por el agente de PC de Intune) | (device.managementType -eq "MDM")
 organizationalUnit | cualquier valor de cadena que coincida con el nombre de la unidad organizativa establecido por Active Directory local | (device.organizationalUnit -eq "US PCs")
 deviceId | un id. de dispositivo de Azure AD válido | (device.deviceId -eq "d4fe7726-5966-431c-b3b8-cddc8fdb717d")
 objectId | un id. de objeto de Azure AD válido |  (device.objectId -eq 76ad43c9-32c5-45e8-a272-7b58b58f596d")



## <a name="changing-dynamic-membership-to-static-and-vice-versa"></a>Cambio de la pertenencia dinámica a estática, y viceversa
Es posible cambiar cómo se administra la pertenencia a un grupo. Esto es útil cuando desea mantener el mismo nombre de grupo y el identificador en el sistema, por lo que cualquier referencia existente al grupo sigue siendo válida; crear un nuevo grupo requeriría actualizar esas referencias.

Hemos actualizado el centro de administración de Azure AD para incorporar la compatibilidad con esta funcionalidad. Ahora, los clientes pueden convertir los grupos existentes para cambiar de pertenencia dinámica a pertenencia asignada y viceversa mediante el Centro de administración de Azure AD o mediante cmdlets de PowerShell como se indica a continuación.

> [!WARNING]
> Al cambiar un grupo estático existente a uno dinámico, se eliminarán todos sus miembros y, después, se procesará la regla de pertenencia para agregar nuevos miembros. Si el grupo se usa para controlar el acceso a las aplicaciones o los recursos, los miembros originales podrían perder el acceso hasta que se procese por completo la regla de pertenencia.
>
> Es recomendable que pruebe la nueva regla de pertenencia con antelación para asegurarse de que la nueva pertenencia al grupo es la que se preveía.

### <a name="using-azure-ad-admin-center-to-change-membership-management-on-a-group"></a>Uso del Centro de administración de Azure AD para cambiar la administración de la pertenencia a un grupo 

1. Inicie sesión en el [Centro de administración de Azure AD](https://aad.portal.azure.com) con una cuenta que sea administrador global o administrador de cuentas de usuario en el inquilino.
2. Seleccione **Grupos**.
3. En la lista **Todos los grupos**, abra el grupo que desea cambiar.
4. Seleccione **Propiedades**.
5. En la página **Propiedades** del grupo, seleccione un **Tipo de pertenencia**, ya sea Asignada (estática), Usuario dinámico o Dispositivo dinámico, según el tipo de pertenencia que desee. Para la pertenencia dinámica, puede usar el generador de reglas para seleccionar opciones para una regla sencilla o escribir usted mismo una regla avanzada. 

Los pasos siguientes son un ejemplo de cómo cambiar un grupo de usuarios para que pase de una pertenencia estática a una dinámica. 

1. En la página **Propiedades** del grupo seleccionado, elija un **tipo de pertenencia** de **Usuario dinámico** y, a continuación, seleccione Sí en el cuadro de diálogo que explica los cambios en la pertenencia del grupo para continuar. 
  
   ![seleccionar tipo de pertenencia de usuario dinámico](./media/groups-dynamic-membership/select-group-to-convert.png)
  
2. Seleccione **Agregar una consulta dinámica** y, a continuación, proporcione la regla.
  
   ![especificar la regla](./media/groups-dynamic-membership/enter-rule.png)
  
3. Después de crear la regla, seleccione **Agregar consulta** en la parte inferior de la página.
4. Seleccione **Guardar** en la página **Propiedades** del grupo para guardar los cambios. El **tipo de pertenencia** del grupo se actualiza inmediatamente en la lista de grupos.

> [!TIP]
> Es posible que la conversión del grupo presente un error si la regla avanzada que escribió no era correcta. Aparecerá una notificación en la esquina superior derecha del portal, con una explicación de por qué el sistema no pudo aceptar la regla. Léala con cuidado para saber cómo debe ajustar la regla para que sea válida.

### <a name="using-powershell-to-change-membership-management-on-a-group"></a>Uso de PowerShell para cambiar la administración de la pertenencia a un grupo

> [!NOTE]
> Para cambiar las propiedades de un grupo dinámico, es preciso usar los cmdlets de **la versión preliminar de** [Azure AD PowerShell, versión 2](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0). Puede instalar la versión preliminar desde la [Galería de PowerShell](https://www.powershellgallery.com/packages/AzureADPreview).

Este es un ejemplo e las funciones que cambian la administración de la pertenencia a un grupo existente. En este ejemplo, se actúa con precaución para manipular correctamente la propiedad GroupTypes y preservar todos los valores que no tienen relación con la pertenencia a un grupo dinámico.

```
#The moniker for dynamic groups as used in the GroupTypes property of a group object
$dynamicGroupTypeString = "DynamicMembership"

function ConvertDynamicGroupToStatic
{
    Param([string]$groupId)

    #existing group types
    [System.Collections.ArrayList]$groupTypes = (Get-AzureAdMsGroup -Id $groupId).GroupTypes

    if($groupTypes -eq $null -or !$groupTypes.Contains($dynamicGroupTypeString))
    {
        throw "This group is already a static group. Aborting conversion.";
    }


    #remove the type for dynamic groups, but keep the other type values
    $groupTypes.Remove($dynamicGroupTypeString)

    #modify the group properties to make it a static group: i) change GroupTypes to remove the dynamic type, ii) pause execution of the current rule
    Set-AzureAdMsGroup -Id $groupId -GroupTypes $groupTypes.ToArray() -MembershipRuleProcessingState "Paused"
}

function ConvertStaticGroupToDynamic
{
    Param([string]$groupId, [string]$dynamicMembershipRule)

    #existing group types
    [System.Collections.ArrayList]$groupTypes = (Get-AzureAdMsGroup -Id $groupId).GroupTypes

    if($groupTypes -ne $null -and $groupTypes.Contains($dynamicGroupTypeString))
    {
        throw "This group is already a dynamic group. Aborting conversion.";
    }
    #add the dynamic group type to existing types
    $groupTypes.Add($dynamicGroupTypeString)

    #modify the group properties to make it a static group: i) change GroupTypes to add the dynamic type, ii) start execution of the rule, iii) set the rule
    Set-AzureAdMsGroup -Id $groupId -GroupTypes $groupTypes.ToArray() -MembershipRuleProcessingState "On" -MembershipRule $dynamicMembershipRule
}
```
Para convertir un grupo en estático:
```
ConvertDynamicGroupToStatic "a58913b2-eee4-44f9-beb2-e381c375058f"
```
Para convertir un grupo en dinámico:
```
ConvertStaticGroupToDynamic "a58913b2-eee4-44f9-beb2-e381c375058f" "user.displayName -startsWith ""Peter"""
```
## <a name="next-steps"></a>Pasos siguientes
En estos artículos se proporciona información adicional sobre los grupos en Azure Active Directory.

* [Ver los grupos existentes](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Crear un nuevo grupo y agregar miembros](../fundamentals/active-directory-groups-create-azure-portal.md)
* [Administrar la configuración de un grupo](../fundamentals/active-directory-groups-settings-azure-portal.md)
* [Administrar la pertenencia a grupos](../fundamentals/active-directory-groups-membership-azure-portal.md)
* [Administrar reglas dinámicas de los usuarios de un grupo](groups-dynamic-membership.md)
