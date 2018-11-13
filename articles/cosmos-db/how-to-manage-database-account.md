---
title: Aprenda a administrar cuentas de base de datos en Azure Cosmos DB
description: Aprenda a administrar cuentas de base de datos en Azure Cosmos DB
services: cosmos-db
author: christopheranderson
ms.service: cosmos-db
ms.topic: sample
ms.date: 10/17/2018
ms.author: chrande
ms.openlocfilehash: 67cd78d4900b8ce53cf0c50116c02a9c1b967687
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2018
ms.locfileid: "50958770"
---
# <a name="manage-database-accounts-in-azure-cosmos-db"></a>Administración de cuentas de base de datos en Azure Cosmos DB

En este artículo se describe cómo administrar una cuenta de Cosmos DB para configurar el hospedaje múltiple, agregar o quitar una región, configurar varias regiones de escritura y configurar las prioridades de la conmutación por error. 

## <a name="create-a-database-account"></a>Creación de una cuenta de base de datos

### <a id="create-database-account-via-portal"></a>Azure Portal

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

### <a id="create-database-account-via-cli"></a>Azure CLI

```bash
# Create an account
az cosmosdb create --name <Cosmos DB Account name> --resource-group <Resource Group Name>
```

## <a name="configure-clients-for-multi-homing"></a>Configuración de los clientes para el hospedaje múltiple

### <a id="configure-clients-multi-homing-dotnet"></a>SDK para .NET

```csharp
// Create a new Connection Policy
ConnectionPolicy policy = new ConnectionPolicy
    {
        // Note: These aren't required settings for multi-homing,
        // just suggested defaults
        ConnectionMode = ConnectionMode.Direct,
        ConnectionProtocol = Protocol.Tcp,
        UseMultipleWriteLocations = true,
    };
// Add regions to Preferred locations
// The name of the location will match what you see in the portal/etc.
policy.PreferredLocations.Add("East US");
policy.PreferredLocations.Add("North Europe");

// Pass the Connection policy with the preferred locations on it to the client.
DocumentClient client = new DocumentClient(new Uri(this.accountEndpoint), this.accountKey, policy);
```

### <a id="configure-clients-multi-homing-java-async"></a>SDK asincrónico para Java

```java
ConnectionPolicy policy = new ConnectionPolicy();
policy.setPreferredLocations(Collections.singleton("West US"));
AsyncDocumentClient client =
        new AsyncDocumentClient.Builder()
                .withMasterKey(this.accountKey)
                .withServiceEndpoint(this.accountEndpoint)
                .withConnectionPolicy(policy).build();
```

### <a id="configure-clients-multi-homing-java-sync"></a>SDK sincrónico para Java

```java
ConnectionPolicy connectionPolicy = new ConnectionPolicy();
Collection<String> preferredLocations = new ArrayList<String>();
preferredLocations.add("Australia East");
connectionPolicy.setPreferredLocations(preferredLocations);
DocumentClient client = new DocumentClient(accountEndpoint, accountKey, connectionPolicy);
```

### <a id="configure-clients-multi-homing-javascript"></a>SDK para Node.js/JavaScript/TypeScript

```javascript
// Set up the connection policy with your preferred regions
const connectionPolicy: ConnectionPolicy = new ConnectionPolicy();
connectionPolicy.PreferredLocations = ["West US", "Australia East"];

// Pass that connection policy to the client
const client = new CosmosClient({
  endpoint: config.endpoint,
  auth: { masterKey: config.key },
  connectionPolicy
});
```

### <a id="configure-clients-multi-homing-python"></a>SDK para Python

```python
connection_policy = documents.ConnectionPolicy()
connection_policy.PreferredLocations = ['West US', 'Japan West']
client = cosmos_client.CosmosClient(self.account_endpoint, {'masterKey': self.account_key}, connection_policy)

```

## <a name="addremove-regions-from-your-database-account"></a>Incorporación o eliminación de regiones de una cuenta de base de datos

### <a id="add-remove-regions-via-portal"></a>Azure Portal

1. Vaya a la cuenta de Azure Cosmos DB y abra el menú **Replicar datos globalmente**.

2. Para agregar regiones, seleccione una o varias regiones del mapa, haga clic en los hexágonos vacíos con la etiqueta **"+"** correspondiente a la región que desea. También puede agregar una región seleccionando la opción **+ Agregar región** y eligiendo una región en el menú desplegable.

3. Para quitar regiones, anule la selección de una o varias regiones del mapa, para lo que debe hacer clic en los hexágonos azules con una marca de verificación, o bien seleccionar el icono de una "papelera" (🗑) que hay junto a la región, en el lado derecho.

4. Haga clic en Guardar para guardar los cambios.

   ![Agregar o quitar menú de regiones](./media/how-to-manage-database-account/add-region.png)

En el modo de escritura de región individual, no puede quitar la región de escritura. Debe realizar la conmutación por error a otra región antes de eliminar la región de escritura actual.

En el modo de escritura de varias regiones, puede agregar o quitar tantas regiones como desee, siempre que tenga al menos una región.

### <a id="add-remove-regions-via-cli"></a>Azure CLI

```bash
# Given an account created with 1 region like so
az cosmosdb create --name <Cosmos DB Account name> --resource-group <Resource Group name> --locations 'eastus=0'

# Add a new region by adding another region to the list
az cosmosdb update --name <Cosmos DB Account name> --resource-group <Resource Group name> --locations 'eastus=0 westus=1'

# Remove a region by removing a region from the list
az cosmosdb update --name <Cosmos DB Account name> --resource-group <Resource Group name> --locations 'westus=0'
```

## <a name="configure-multiple-write-regions"></a>Configuración de varias regiones de escritura

### <a id="configure-multiple-write-regions-portal"></a>Azure Portal

Cuando cree una cuenta de base de datos, asegúrese de que el valor **Multi-region Writes** (Operaciones de escritura en varias regiones) está habilitado.

![Captura de pantalla de creación de una cuenta de Cosmos DB](./media/how-to-manage-database-account/account-create.png)

### <a id="configure-multiple-write-regions-cli"></a>Azure CLI

```bash
az cosmosdb create --name <Cosmos DB Account name> --resource-group <Resource Group name> --enable-multiple-write-locations true
```

### <a id="configure-multiple-write-regions-arm"></a>plantilla de Resource Manager

El siguiente código JSON es una plantilla de Resource Manager de ejemplo. Se puede usar para implementar una cuenta de Azure Cosmos DB con una directiva de coherencia de obsolescencia limitada, un intervalo de obsolescencia máximo de 5 segundos y el número máximo de solicitudes obsoletas toleradas fijado en 100. Para obtener información sobre el formato y la sintaxis de una plantilla de Resource Manager, consulte la documentación de [Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "name": {
            "type": "String"
        },
        "location": {
            "type": "String"
        },
        "locationName": {
            "type": "String"
        },
        "defaultExperience": {
            "type": "String"
        }
    },
    "resources": [
        {
            "type": "Microsoft.DocumentDb/databaseAccounts",
            "kind": "GlobalDocumentDB",
            "name": "[parameters('name')]",
            "apiVersion": "2015-04-08",
            "location": "[parameters('location')]",
            "tags": {
                "defaultExperience": "[parameters('defaultExperience')]"
            },
            "properties": {
                "databaseAccountOfferType": "Standard",
                "consistencyPolicy": {
                    "defaultConsistencyLevel": "BoundedStaleness",
                    "maxIntervalInSeconds": 5,
                    "maxStalenessPrefix": 100
                },
                "locations": [
                    {
                        "id": "[concat(parameters('name'), '-', parameters('location'))]",
                        "failoverPriority": 0,
                        "locationName": "[parameters('locationName')]"
                    }
                ],
                "isVirtualNetworkFilterEnabled": false,
                "enableMultipleWriteLocations": true,
                "virtualNetworkRules": [],
                "dependsOn": []
            }
        }
    ]
}
```


## <a name="enable-manual-failover-for-your-cosmos-account"></a>Habilitación de la conmutación por error manual en una cuenta de Cosmos

### <a id="enable-manual-failover-via-portal"></a>Azure Portal

1. Vaya a la cuenta de Azure Cosmos DB y abra el menú **"Replicar datos globalmente"**.

2. Haga clic en el botón **"Conmutación por error manual"** de la parte superior del menú.

   ![Menú Replicar datos globalmente](./media/how-to-manage-database-account/replicate-data-globally.png)

3. En el menú **Conmutación por error manual**, seleccione la nueva región de escritura y active la casilla para indicar que entiende que esta opción cambiará la región de escritura.

4. Haga clic en "Aceptar" para desencadenar la conmutación por error.

   ![Menú Conmutación por error del portal](./media/how-to-manage-database-account/manual-failover.png)

### <a id="enable-manual-failover-via-cli"></a>Azure CLI

```bash
# Given your account currently has regions with priority like so: 'eastus=0 westus=1'
# Change the priority order to trigger a failover of the write region
az cosmosdb update --name <Cosmos DB Account name> --resource-group <Resource Group name> --locations 'eastus=1 westus=0'
```

## <a name="enable-automatic-failover-for-your-cosmos-account"></a>Habilitación de la conmutación por error automática en una cuenta de Cosmos

### <a id="enable-automatic-failover-via-portal"></a>Azure Portal

1. En la cuenta de Azure Cosmos DB, abra el panel **"Replicar datos globalmente"**. 

2. Haga clic en el botón **"Conmutación por error automática"** de la parte superior del panel.

   ![Menú Replicar datos globalmente](./media/how-to-manage-database-account/replicate-data-globally.png)

3. En el panel **"Conmutación por error automática"**, asegúrese de que **Habilitar conmutación por error automática** está establecido en **Activado**. 

4. Haga clic en Guardar en la parte inferior de la página.

   ![Menú Conmutación por error automática del portal](./media/how-to-manage-database-account/automatic-failover.png)

En este menú también se pueden establecer las prioridades de conmutación por error.

### <a id="enable-automatic-failover-via-cli"></a>Azure CLI

```bash
# Enable automatic failover on account creation
az cosmosdb create --name <Cosmos DB Account name> --resource-group <Resource Group name> --enable-automatic-failover true

# Enable automatic failover on an existing account
az cosmosdb update --name <Cosmos DB Account name> --resource-group <Resource Group name> --enable-automatic-failover true

# Disable automatic failover on an existing account
az cosmosdb update --name <Cosmos DB Account name> --resource-group <Resource Group name> --enable-automatic-failover false
```

## <a name="set-failover-priorities-for-your-cosmos-account"></a>Establecimiento de las prioridades de conmutación por error en una cuenta de Cosmos

### <a id="set-failover-priorities-via-portal"></a>Azure Portal

1. En la cuenta de Azure Cosmos DB, abra el panel **"Replicar datos globalmente"**. 

2. Haga clic en el botón **"Conmutación por error automática"** de la parte superior del panel.

   ![Menú Replicar datos globalmente](./media/how-to-manage-database-account/replicate-data-globally.png)

3. En el panel **"Conmutación por error automática"**, asegúrese de que **Habilitar conmutación por error automática** está establecido en **Activado**. 

4. Para modificar la prioridad de conmutación por error, haga clic y arrastre las regiones de lectura mediante los tres puntos del lado izquierdo de la fila que aparecen al mantener el mouse sobre ellos. 

5. Haga clic en Guardar en la parte inferior de la página.

   ![Menú Conmutación por error automática del portal](./media/how-to-manage-database-account/automatic-failover.png)

En este menú no se puede modificar la región de escritura. Para cambiar la región de escritura de forma manual, es preciso realizar una conmutación por error manual.

### <a id="set-failover-priorities-via-cli"></a>Azure CLI

```bash
az cosmosdb failover-priority-change --name <Cosmos DB Account name> --resource-group <Resource Group name> --failover-policies 'eastus=0 westus=2 southcentralus=1'
```

## <a name="next-steps"></a>Pasos siguientes

Para aprender a administrar conflictos de datos y los niveles de coherencia en Cosmos DB. use los siguientes documentos:

* [How to manage consistency](how-to-manage-consistency.md) (Administración de la coherencia)
* [How to manage conflicts between regions](how-to-manage-conflicts.md) (Administración de conflictos entre regiones)

