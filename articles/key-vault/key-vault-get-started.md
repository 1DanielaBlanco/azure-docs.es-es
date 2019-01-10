---
title: 'Introducción a Azure Key Vault: Azure Key Vault | Microsoft Docs'
description: Use este tutorial para empezar a trabajar con Azure Key Vault para crear un contenedor reforzado en Azure en el que almacenar y administrar las claves criptográficas y los secretos en Azure.
services: key-vault
documentationcenter: ''
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 36721e1d-38b8-4a15-ba6f-14ed5be4de79
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/02/2019
ms.author: barclayn
ms.openlocfilehash: 72e17d5628be307d6c73cd2bba7576d0e734af15
ms.sourcegitcommit: da69285e86d23c471838b5242d4bdca512e73853
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/03/2019
ms.locfileid: "53999074"
---
# <a name="get-started-with-azure-key-vault"></a>Introducción a Azure Key Vault

Este artículo sirve de ayuda para empezar a trabajar con Azure Key Vault mediante PowerShell y guía a través de las actividades siguientes:

- Procedimiento para crear un contenedor protegido (un almacén) en Azure.
- Procedimiento para usar KeyVault para almacenar y administrar las claves criptográficas y los secretos en Azure.
- Procedimiento para que una aplicación use esa clave o contraseña.

Azure Key Vault está disponible en la mayoría de las regiones. Para obtener más información, consulte la [página de precios de Key Vault](https://azure.microsoft.com/pricing/details/key-vault/).

Para obtener instrucciones acerca de la interfaz de la línea de comandos para todas las plataformas, consulte [este tutorial equivalente](key-vault-manage-with-cli2.md).

## <a name="requirements"></a>Requisitos

Antes de continuar, compruebe que tiene lo siguiente:

- **Una suscripción de Azure**. Si no tiene, puede registrarse para obtener una [cuenta gratuita](https://azure.microsoft.com/free/).
- **Azure PowerShell**, **versión mínima de 1.1.0**. Para instalar Azure PowerShell y asociarlo con una suscripción de Azure, consulte [Instalación y configuración de Azure PowerShell](/powershell/azure/overview). Si ya instaló Azure PowerShell y no sabe la versión, en la consola de Azure PowerShell, escriba `(Get-Module azure -ListAvailable).Version`. Si tiene Azure PowerShell versiones 0.9.1 a 0.9.8 instalado, también puede usar este tutorial con algunos cambios menores. Por ejemplo, debe usar el comando `Switch-AzureMode AzureResourceManager` y algunos de los comandos de Azure Key Vault que cambiaron. Para ver una lista de los cmdlets de Key Vault para las versiones 0.9.1 a 0.9.8, consulte [Azure Key Vault Cmdlets](/powershell/module/azurerm.keyvault/#key_vault) (Cmdlets de Azure Key Vault).
- **Una aplicación que se pueda configurar para usar Key Vault**. Hay una aplicación de ejemplo disponible en el [Centro de descarga de Microsoft](https://www.microsoft.com/download/details.aspx?id=45343). Para obtener instrucciones, consulte el archivo **Léame** adjunto.

>[!NOTE]
En este artículo se supone que el usuario tiene un conocimiento básico de Azure PowerShell. Para más información acerca de PowerShell, consulte [Introducción a Windows PowerShell](https://technet.microsoft.com/library/hh857337.aspx).

Para obtener ayuda detallada con cualquier cmdlet que aparezca en este tutorial, use el cmdlet **Get-Help** .

```powershell-interactive
Get-Help <cmdlet-name> -Detailed
```
    
Por ejemplo, para obtener ayuda para el cmdlet **Connect-AzureRmAccount**, escriba:

```PowerShell
Get-Help Connect-AzureRmAccount -Detailed
```

También puede leer los siguientes artículos para familiarizarse con el modelo de implementación de Azure Resource Manager en Azure PowerShell:

* [Instalación y configuración de Azure PowerShell](/powershell/azure/overview)
* [Uso de Azure PowerShell con el Administrador de recursos](../powershell-azure-resource-manager.md)

## <a id="connect"></a>Conexión a las suscripciones

Inicie una sesión de PowerShell de Azure e inicie sesión en su cuenta de Azure con el siguiente comando:  

```PowerShell
Connect-AzureRmAccount
```

>[!NOTE]
 Si utiliza una instancia específica de Azure, use el parámetro -Environment. Por ejemplo:  
 ```powershell
 Connect-AzureRmAccount –Environment (Get-AzureRmEnvironment –Name AzureUSGovernment)
 ```

En la ventana emergente del explorador, escriba el nombre de usuario y la contraseña de su cuenta de Azure. Azure PowerShell obtiene todas las suscripciones asociadas a esta cuenta y, de forma predeterminada, usa la primera.

Si hay varias suscripciones y desea especificar que una en concreto se use para Azure Key Vault, escriba lo siguiente para ver las suscripciones de la cuenta:

```powershell
Get-AzureRmSubscription
```

A continuación, para especificar la suscripción que se va a usar, escriba:

```powershell
Set-AzureRmContext -SubscriptionId <subscription ID>
```

Para obtener más información sobre cómo configurar PowerShell de Azure, consulte [Instalación y configuración de PowerShell de Azure](/powershell/azure/overview).

## <a id="resource"></a>Creación de un nuevo grupo de recursos

Cuando se utiliza el Administrador de recursos de Azure, todos los recursos relacionados se crean dentro de un grupo de recursos. Crearemos un nuevo grupo de recursos denominado **ContosoResourceGroup** para este tutorial:

```powershell
New-AzureRmResourceGroup –Name 'ContosoResourceGroup' –Location 'East US'
```

## <a id="vault"></a>Creación de un Almacén de claves

Use el cmdlet [New-AzureRmKeyVault](/powershell/module/azurerm.keyvault/new-azurermkeyvault) para crear una instancia de Key Vault. Este cmdlet tiene tres parámetros obligatorios: el **nombre del grupo de recursos**, el **nombre del Almacén de claves** y la **ubicación geográfica**.

Por ejemplo, si utiliza:
- El nombre de almacén **ContosoKeyVault**.
- El nombre de grupo de recursos **ContosoResourceGroup**.
- La ubicación **Este de EE.UU**.

Escribiría:

```powershell
New-AzureRmKeyVault -Name 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East US'
```
![Salida tras completarse el comando de creación de Key Vault](./media/key-vault-get-started/output-after-creating-keyvault.png)

El resultado de este cmdlet muestra las propiedades del almacén de claves que creó. Las dos propiedades más importantes son:

* **Nombre del almacén**: en este ejemplo es **ContosoKeyVault**. Utilizará este nombre para otros cmdlets de Key Vault.
* **URI de almacén**: en el ejemplo, es https://contosokeyvault.vault.azure.net/. Las aplicaciones que utilizan el almacén a través de su API de REST deben usar este identificador URI.

Su cuenta de Azure ahora está autorizada para realizar operaciones en este Almacén de claves. Hasta el momento, nadie más lo está.

> [!NOTE]
> Al intentar crear un nuevo almacén de claves, puede ver el error **La suscripción no está registrada para usar el espacio de nombres 'Microsoft.KeyVault'**. Si aparece el mensaje, ejecute `Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.KeyVault"`. Después de que el registro se completa correctamente, puede volver a ejecutar el comando New-AzureRmKeyVault. Para más información, consulte [Register-AzureRmResourceProvider](/powershell/module/azurerm.resources/register-azurermresourceprovider).
>
>

## <a id="add"></a>Adición de una clave o un secreto al Almacén de claves

Hay un par de maneras distintas en que puede que necesite interactuar con el almacén de claves y las claves o secretos.

### <a name="azure-key-vault-generates-a-software-protected-key"></a>Azure Key Vault genera una clave protegida por software

Si desea que Azure Key Vault cree una clave protegida mediante software, utilice el cmdlet [Add-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/add-azurekeyvaultkey) y escriba lo siguiente:

```powershell
$key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVault' -Name 'ContosoFirstKey' -Destination 'Software'
```
Para mostrar el URI para esta clave, escriba:
```powershell
$key.id
```

Ahora puede hacer referencia a una clave que creó o cargó en Azure Key Vault mediante su URI. Para obtener la versión actual, puede usar **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey**, pero para obtener esta versión concreta debe usar **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey/cgacf4f763ar42ffb0a1gca546aygd87**.  

### <a name="importing-an-existing-pfx-file-into-azure-key-vault"></a>Importación de un archivo PFX existente en Azure Key Vault

Si las claves existentes están almacenadas en un archivo PFX que quiere cargar en Azure Key Vault, los pasos son diferentes. Por ejemplo: 
- Si tiene una clave protegida por software en un archivo PFX
- El archivo PFX se denomina softkey.pfx 
- Se almacena en la unidad C.

Puede escribir:

```powershell
$securepfxpwd = ConvertTo-SecureString –String '123' –AsPlainText –Force  // This stores the password 123 in the variable $securepfxpwd
```

A continuación, escriba lo siguiente para importar la clave desde el archivo .PFX, que protege la clave mediante software en el servicio Key Vault:

```powershell
$key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVault' -Name 'ContosoImportedPFX' -KeyFilePath 'c:\softkey.pfx' -KeyFilePassword $securepfxpwd
```

Para mostrar el URI para esta clave, escriba:

```powershell
$Key.id
```
Para ver la clave, escriba: 

```powershell
Get-AzureKeyVaultKey –VaultName 'ContosoKeyVault'
```
Si quiere ver las propiedades del archivo PFX en el portal, verá algo parecido a la imagen que se muestra a continuación.

![Apariencia de un certificado en el portal](./media/key-vault-get-started/imported-pfx.png)

### <a name="to-add-a-secret-to-azure-key-vault"></a>Para agregar un secreto a Azure Key Vault

Para agregar un secreto al almacén, que es una contraseña denominada SQLPassword con el valor Pa$$w0rd, a Azure Key Vault, convierta primero el valor Pa$$w0rd en una cadena segura escribiendo:

```powershell    
$secretvalue = ConvertTo-SecureString 'Pa$$w0rd' -AsPlainText -Force
```

A continuación, escriba:

```powershell
$secret = Set-AzureKeyVaultSecret -VaultName 'ContosoKeyVault' -Name 'SQLPassword' -SecretValue $secretvalue
```


Ahora puede hacer referencia a esta clave que agregó a Azure Key Vault utilizando su URI. Use **https://ContosoVault.vault.azure.net/secrets/SQLPassword** para obtener siempre la versión actual y use **https://ContosoVault.vault.azure.net/secrets/SQLPassword/90018dbb96a84117a0d2847ef8e7189d** para obtener esta versión concreta.

Para mostrar el URI para este secreto, escriba:

```powershell
$secret.Id
```
Para ver el secreto, escriba: `Get-AzureKeyVaultSecret –VaultName 'ContosoKeyVault'` o también puede ver el secreto en el portal.

![secret](./media/key-vault-get-started/secret-value.png)

Para ver el valor contenido en el secreto como texto sin formato:
```powershell
(get-azurekeyvaultsecret -vaultName "Contosokeyvault" -name "SQLPassword").SecretValueText
```
Ahora, el almacén de claves y la clave o el secreto están listos para que los usen las aplicaciones. Debe conceder autorización a las aplicaciones para que puedan usarlos.  

## <a id="register"></a>Registro de una aplicación con Azure Active Directory

Este paso lo haría normalmente un programador en un equipo independiente. No es específico para Azure Key Vault. Para obtener instrucciones detalladas sobre cómo registrar una aplicación con Azure Active Directory, revise el artículo titulado [Integración de aplicaciones con Azure Active Directory](../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md) o [Uso del portal para crear una aplicación de Azure Active Directory y la entidad de servicio que puede acceder a los recursos](../active-directory/develop/howto-create-service-principal-portal.md).

> [!IMPORTANT]
> Para finalizar el tutorial, la cuenta, el almacén y la aplicación que vaya a registrar en este paso deben estar en el mismo directorio de Azure.


Las aplicaciones que utilizan un Almacén de claves deben autenticarse utilizando un token de Azure Active Directory. El propietario de la aplicación debe registrarla primero en su Azure Active Directory. Al final del registro, el propietario de la aplicación obtiene los valores siguientes:

- **Id. de aplicación** 
- **Clave de autenticación** (también conocida como "secreto compartido") 

Para obtener un token, la aplicación debe presentar estos dos valores a Azure Active Directory. La configuración de la aplicación depende de la aplicación. Para la [aplicación de ejemplo de Key Vault](https://www.microsoft.com/download/details.aspx?id=45343), el propietario de la aplicación establece estos valores en el archivo app.config.


Para registrar la aplicación en Azure Active Directory:

1. Inicie sesión en el [Azure Portal](https://portal.azure.com).
2. Haga clic en **Registros de aplicaciones** en la parte izquierda. Si no ve los registros de aplicaciones, haga clic en **Más servicios**.  

> [!NOTE]
> Debe seleccionar el mismo directorio que contiene la suscripción de Azure con la que creó la instancia de Key Vault.

3. Haga clic en **Nuevo registro de aplicaciones**.
4. En la hoja **Crear**, proporcione un nombre para la aplicación y, después, seleccione **APLICACIÓN WEB Y/O API WEB** (valor predeterminado) y especifique la **DIRECCION URL DE INICIO DE SESIÓN** para su aplicación web. Si no dispone de esta información, puede inventársela para este paso (por ejemplo, podría especificar http://test1.contoso.com). No importa si los sitios existen. 

    ![Nuevo registro de aplicaciones](./media/key-vault-get-started/new-application-registration.png)
    > [!WARNING]
    > Asegúrese de elegir **APLICACIÓN WEB Y/O API WEB**; si no lo hizo, no verá la opción **Claves** en la configuración.

5. Haga clic en el botón **Crear**.
6. Una vez que haya completado el registro de la aplicación, verá la lista de aplicaciones registradas. Busque la aplicación que ha registrado y haga clic en ella.
7. Haga clic en la hoja **Aplicación registrada** y copie el **id. de aplicación**.
8. Haga clic en **Toda la configuración**.
9. En la hoja **Configuración**, haga clic en **claves**.
9. Escriba una descripción en el cuadro **Descripción de la clave** y seleccione una duración; a continuación, haga clic en **GUARDAR**. La página se actualiza y muestra ahora un valor de clave. 
10. En el paso siguiente, usará la información del **id. de aplicación** y la **clave** para establecer permisos en el almacén.

## <a id="authorize"></a>Autorización de la aplicación para que use la clave o el secreto

Hay dos formas de autorizar el acceso de la aplicación a la clave o al secreto del almacén.

### <a name="using-powershell"></a>Con PowerShell

Para utilizar PowerShell, use el cmdlet [Set-AzureRmKeyVaultAccessPolicy](/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy).

Por ejemplo, si el nombre del almacén es **ContosoKeyVault** y la aplicación que quiere autorizar tiene el identificador de cliente 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed y desea que la aplicación tenga autorización para descifrar y firmar con claves en el almacén, ejecute el cmdlet siguiente:

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed -PermissionsToKeys decrypt,sign
```

Si desea autorizar a esa misma aplicación para leer los secretos en el almacén, ejecute lo siguiente:

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed -PermissionsToSecrets Get
```

### <a name="using-the-azure-portal"></a>Uso de Azure Portal

Para cambiar la autorización de una aplicación para usar las claves o los secretos:
1. Seleccione **Directivas de acceso** en la hoja de recursos de Key Vault
2. Haga clic en el botón [+ Agregar nueva] en la parte superior de la hoja
3. Haga clic en **Seleccionar la entidad de seguridad** para seleccionar la aplicación que creó anteriormente
4. En la lista desplegable **Permisos de clave**, seleccione "Descifrar" y "Firmar" para autorizar que la aplicación descifre y firme con claves de su almacén
5. En la lista desplegable **Permisos de secretos**, seleccione "Obtener" para permitir que la aplicación lea los secretos del almacén

## <a id="HSM"></a>Trabajo con un módulo de seguridad de hardware (HSM)

Para obtener seguridad adicional, puede importar o generar claves en módulos de seguridad de hardware (HSM) que no se salen nunca del límite de los HSM. Los HSM tienen la validación FIPS 140-2 de nivel 2. Si este requisito no es relevante para usted, omita esta sección y vaya al paso [Eliminación del Almacén de claves junto con las claves y secretos asociados](#delete).

Para crear estas claves protegidas con HSM, debe utilizar el [nivel de servicio Premium de Azure Key Vault para admitir claves protegidas con HSM](https://azure.microsoft.com/pricing/details/key-vault/). Además, tenga en cuenta que esta funcionalidad no está disponible para Azure China.

Cuando cree Key Vault, agregue el parámetro **-SKU**:

```powershell
New-AzureRmKeyVault -Name 'ContosoKeyVaultHSM' -ResourceGroupName 'ContosoResourceGroup' -Location 'East US' -SKU 'Premium'
```

Puede agregar claves protegidas mediante software (como se ha mostrado anteriormente) y claves protegidas con HSM a Key Vault. Para crear una clave protegida con HSM, establezca el parámetro **-Destination** en 'HSM':

```powershell
$key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMKey' -Destination 'HSM'
```

Puede utilizar el siguiente comando para importar una clave desde un archivo .PFX a su equipo. Este comando importa la clave a HSM en el servicio Key Vault:

```powershell
$key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMKey' -KeyFilePath 'c:\softkey.pfx' -KeyFilePassword $securepfxpwd -Destination 'HSM'
```

El comando siguiente importa un paquete BYOK ("traiga su propia clave"). Este escenario le permite generar la clave en el HSM local y transferirla a los HSM del servicio Key Vault, sin que la clave salga del límite del HSM:

```powershell
$key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMKey' -KeyFilePath 'c:\ITByok.byok' -Destination 'HSM'
```

Para obtener instrucciones detalladas sobre cómo generar el paquete BYOK, consulte [Generación y transferencia de claves protegidas con HSM para Azure Key Vault](key-vault-hsm-protected-keys.md).

## <a id="delete"></a>Eliminación del Almacén de claves junto con las claves y secretos asociados

Si ya no necesita la instancia de Key Vault, ni la clave y el secreto que contiene, puede eliminarlo mediante el cmdlet [Remove-AzureRmKeyVault](/powershell/module/azurerm.keyvault/remove-azurermkeyvault):

```powershell
Remove-AzureRmKeyVault -VaultName 'ContosoKeyVault'
```

O bien puede eliminar un grupo de recursos de Azure completo, que incluye el Almacén de claves y otros recursos incluidos en dicho grupo:

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName 'ContosoResourceGroup'
```

## <a id="other"></a>Otros cmdlets de Azure PowerShell

Estos son otros comandos que pueden resultar útiles para administrar Azure Key Vault.

- `$Keys = Get-AzureKeyVaultKey -VaultName 'ContosoKeyVault'`: este comando ofrece una presentación tabular de todas las claves y las propiedades seleccionadas.
- `$Keys[0]`: este comando muestra una lista completa de propiedades para la clave especificada.
- `Get-AzureKeyVaultSecret`: este comando muestra una presentación tabular de todos nombres de secretos y las propiedades que se elijan.
- `Remove-AzureKeyVaultKey -VaultName 'ContosoKeyVault' -Name 'ContosoFirstKey'`: ejemplo de cómo eliminar una clave específica.
- `Remove-AzureKeyVaultSecret -VaultName 'ContosoKeyVault' -Name 'SQLPassword'`: ejemplo de cómo eliminar un secreto específico.

## <a name="next-steps"></a>Pasos siguientes

- Para obtener información general sobre Azure Key Vault, consulte [¿Qué es Azure Key Vault?](key-vault-whatis.md)
- Para ver cómo se usa el almacén de claves, consulte [Registro de Azure Key Vault](key-vault-logging.md).
- Para obtener un tutorial de seguimiento que usa Azure Key Vault en una aplicación web, consulte [Uso de Azure Key Vault desde una aplicación web](key-vault-use-from-web-application.md).
- Para conocer las referencias de programación, consulte la [Guía del desarrollador de Azure Key Vault](key-vault-developers-guide.md).
- Para obtener una lista de los cmdlets de Azure PowerShell más recientes para Azure Key Vault, consulte [Azure Key Vault Cmdlets](/powershell/module/azurerm.keyvault/#key_vault) (Cmdlets de Azure Key Vault).
