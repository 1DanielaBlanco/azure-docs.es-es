# [Documentación de Azure Resource Manager](index.md)

# Información general
## [¿Qué es Resource Manager?](resource-group-overview.md)
## [Implementación de Resource Manager y clásica](resource-manager-deployment-model.md)

# Guías de inicio rápido
## [Creación de plantillas: portal](resource-manager-quickstart-create-templates-use-the-portal.md)
## [Creación de plantillas: VS Code](resource-manager-quickstart-create-templates-use-visual-studio-code.md)
## [Creación de plantillas: Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)

# Conceptos
## [Tipos y proveedores de recursos](resource-manager-supported-services.md)
## [Grupos de administración](management-groups-overview.md)
## [Gobierno de suscripción](/azure/architecture/cloud-adoption-guide/subscription-governance?toc=%2fazure%2fazure-resource-manager%2ftoc.json)
## [Plantillas para mantener la coherencia en la nube](templates-cloud-consistency.md)

# Procedimientos
## Crear plantillas
### [Secciones de plantilla](resource-group-authoring-templates.md)
#### [Parámetros](resource-manager-templates-parameters.md)
#### [Variables](resource-manager-templates-variables.md)
#### [Funciones](resource-group-authoring-templates.md#functions)
#### [Recursos](resource-manager-templates-resources.md)
#### [Outputs](resource-manager-templates-outputs.md)
### [Plantillas vinculadas y anidadas](resource-group-linked-templates.md)
### [Definición de las dependencias entre recursos](resource-group-define-dependencies.md)
### [Creación de varias instancias](resource-group-create-multiple.md)
### [Actualización de recursos](/azure/architecture/building-blocks/extending-templates/update-resource)

## Implementación
### Azure PowerShell
#### [Implementación de la plantilla](resource-group-template-deploy.md)
#### [Implementación de una plantilla privada con el token de SAS](resource-manager-powershell-sas-token.md)
#### [Exportación de la plantilla y reimplementación](resource-manager-export-template-powershell.md)
### Azure CLI
#### [Implementación de la plantilla](resource-group-template-deploy-cli.md)
#### [Implementación de una plantilla privada con el token de SAS](resource-manager-cli-sas-token.md)
#### [Exportación de la plantilla y reimplementación](resource-manager-export-template-cli.md)
### Azure Portal
#### [Implementación de recursos](resource-group-template-deploy-portal.md)
#### [Exportación de la plantilla](resource-manager-export-template.md)
### [API DE REST](resource-group-template-deploy-rest.md)
### [Varios grupos de recursos o suscripciones](resource-manager-cross-resource-group-deployment.md)
### [Integración continua con Visual Studio Team Services](../vs-azure-tools-resource-groups-ci-in-vsts.md?toc=%2fazure%2fazure-resource-manager%2ftoc.json)
### [Paso de valores seguros durante la implementación](resource-manager-keyvault-parameter.md)

## administración
### [Azure PowerShell](powershell-azure-resource-manager.md)
### [CLI de Azure](xplat-cli-azure-resource-manager.md)
### [Portal de Azure](resource-group-portal.md)
### [Uso de etiquetas para organizar los recursos](resource-group-using-tags.md)
### [Traslado de recursos a una suscripción o grupo nuevo](resource-group-move-resources.md)
### [Creación de suscripciones de EA](programmatically-create-subscription.md)
### [Concesión de acceso para crear suscripciones de EA](grant-access-to-create-subscription.md)
### [Creación de grupos de administración](management-groups-create.md)
### [Administración de grupos de administración](management-groups-manage.md)

## Control de acceso
### Creación de una entidad de servicio
#### [Azure PowerShell](resource-group-authenticate-service-principal.md)
#### [CLI de Azure](/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager%2ftoc.json)
#### [Portal de Azure](resource-group-create-service-principal-portal.md)
### [API de autenticación para acceder a las suscripciones](resource-manager-api-authentication.md)
### [Bloqueo de recursos](resource-group-lock-resources.md)

## Auditoría
### [Visualización de registros de actividad](resource-group-audit.md)
### [Ver operaciones de implementación](resource-manager-deployment-operations.md)

## Solución de problemas
### [Solución de errores de implementación](resource-manager-common-deployment-errors.md)
### [AccountNameInvalid](resource-manager-storage-account-name-errors.md)
### [InvalidTemplate](resource-manager-invalid-template-errors.md)
### [Problemas de implementación de Linux](../virtual-machines/linux/troubleshoot-deploy-vm.md)
### [NoRegisteredProviderFound](resource-manager-register-provider-errors.md)
### [NotFound](resource-manager-not-found-errors.md)
### [ParentResourceNotFound](resource-manager-parent-resource-errors.md)
### [Problemas de aprovisionamiento y asignación de Linux](../virtual-machines/linux/troubleshoot-deployment-new-vm.md)
### [Problemas de aprovisionamiento y asignación de Windows](../virtual-machines/windows/troubleshoot-deployment-new-vm.md)
### [RequestDisallowedByPolicy](resource-manager-policy-requestdisallowedbypolicy-error.md)
### [ReservedResourceName](resource-manager-reserved-resource-name.md)
### [ResourceQuotaExceeded](resource-manager-quota-errors.md)
### [SkuNotAvailable](resource-manager-sku-not-available-errors.md)
### [Problemas de implementación de Windows](../virtual-machines/windows/troubleshoot-deploy-vm.md)

# Referencia
## [Formato de plantilla](/azure/templates/)
## [Funciones de plantillas](resource-group-template-functions.md)
### [Funciones de matriz y objeto](resource-group-template-functions-array.md)
### [Funciones de comparación](resource-group-template-functions-comparison.md)
### [Funciones de implementación](resource-group-template-functions-deployment.md)
### [Funciones lógicas](resource-group-template-functions-logical.md)
### [Funciones numéricas](resource-group-template-functions-numeric.md)
### [Funciones de recursos](resource-group-template-functions-resource.md)
### [Funciones de cadena](resource-group-template-functions-string.md)
## [PowerShell](/powershell/module/azurerm.resources)
## [CLI de Azure](/cli/azure/resource)
## [.NET](/dotnet/api/microsoft.azure.management.resourcemanager)
## [Java](/java/api/com.microsoft.azure.management.resources)
## [Python](http://azure-sdk-for-python.readthedocs.io/en/latest/resourcemanagement.html)
## [REST](/rest/api/resources/)

# Recursos
## [Azure Roadmap](https://azure.microsoft.com/roadmap/?category=monitoring-management)
## [Calculadora de precios](https://azure.microsoft.com/pricing/calculator/)
## [Actualizaciones del servicio](https://azure.microsoft.com/updates/?product=azure-resource-manager)
## [Desbordamiento de la pila](http://stackoverflow.com/questions/tagged/azure-resource-manager)
## [Administración de datos personales](resource-manager-personal-data.md)
## [Solicitudes de limitación](resource-manager-request-limits.md)
## [Seguimiento de operaciones asincrónicas](resource-manager-async-operations.md)
## [Vídeos](https://azure.microsoft.com/documentation/videos/index/?services=azure-resource-manager)
