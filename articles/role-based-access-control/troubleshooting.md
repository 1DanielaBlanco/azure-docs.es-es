---
title: Solución de problemas de RBAC en Azure | Microsoft Docs
description: Solución de problemas con el control de acceso basado en rol (RBAC) de Azure.
services: azure-portal
documentationcenter: na
author: rolyon
manager: mtillman
ms.assetid: df42cca2-02d6-4f3c-9d56-260e1eb7dc44
ms.service: role-based-access-control
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: seohack1
ms.openlocfilehash: 557d3330ef155181c050a18b14d31b65ba1f2dcf
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/20/2018
ms.locfileid: "36295410"
---
# <a name="troubleshooting-rbac-in-azure"></a>Solución de problemas de RBAC en Azure

En este artículo se responden preguntas comunes acerca del control de acceso basado en rol (RBAC), con el fin de que sepa qué esperar cuando usa los roles de Azure Portal y de que pueda solucionar los problemas de acceso. Estos tres roles abarcan todos los tipos de recursos:

* Propietario  
* Colaborador  
* Lector  

Tanto los propietarios como los colaboradores tienen acceso total a la experiencia de administración, pero un colaborador no puede conceder acceso a otros usuarios o grupos. Nos centraremos en el rol de lector, ya que tiene más aspectos que comentar. Para obtener información acerca de cómo conceder acceso, consulte [Administración del acceso mediante RBAC y Azure Portal](role-assignments-portal.md).

## <a name="app-service"></a>App Service
### <a name="write-access-capabilities"></a>Funcionalidades de acceso de escritura
Si concede a un usuario acceso de solo lectura a una única aplicación web, se deshabilitan algunas características que no cabría esperar. Las siguientes funcionalidades de administración requieren acceso de **escritura** a una aplicación web (bien como colaborador, bien como propietario) y no están disponibles en un escenario de solo lectura.

* Comandos (p. ej., iniciar, parar, etc.)
* Cambiar opciones, como la configuración general, opciones de escala, opciones de copia de seguridad y opciones de supervisión
* Acceder a las credenciales de publicación y otros secretos, como opciones de aplicaciones y cadenas de conexión
* Registros de streaming
* Configuración de registros de diagnóstico
* Consola (símbolo del sistema)
* Implementaciones activas y recientes (para implementaciones git continuas locales)
* Gasto estimado
* Pruebas web
* Red virtual (solo visible para un lector si un usuario con acceso de escritura ha configurado previamente una red virtual)

Si no puede acceder a ninguno de estos iconos, debe pedirle al administrador el acceso de colaborador a la aplicación web.

### <a name="dealing-with-related-resources"></a>Tratar con recursos relacionados
Las aplicaciones web pueden resultar complicadas si entran en juego distintos recursos. Este es un grupo de recursos típico con un par de sitios web:

![Grupo de recursos de aplicación web](./media/troubleshooting/website-resource-model.png)

Como consecuencia, si le concede a alguien acceso solo a la aplicación web, muchas de las funcionalidades de la hoja del sitio web de Azure Portal están deshabilitadas.

Estos elementos requieren acceso de **escritura** al **plan de App Service** que corresponde a su sitio web:  

* Visualización del plan de tarifa de la aplicación web (gratis o estándar).  
* Configuración de escala (número de instancias, tamaño de la máquina virtual y configuración de escala automática).  
* Cuotas (almacenamiento, ancho de banda y CPU).  

Estos elementos requieren acceso de **escritura** a todo el **grupo de recursos** que contiene su sitio web:  

* Enlaces y certificados SSL (los certificados SSL se pueden compartir entre sitios en el mismo grupo de recursos y la misma ubicación geográfica)  
* Reglas de alertas  
* Opciones de escala automática  
* Componentes de Application Insights  
* Pruebas web  

## <a name="azure-functions"></a>Azure Functions
Algunas características de [Azure Functions](../azure-functions/functions-overview.md) requieren acceso de escritura. Por ejemplo, si se asigna a un usuario el rol de lector, este no podrá ver las funciones dentro de una aplicación de función. El portal mostrará **(Sin acceso)**.

![Sin acceso a aplicaciones de función](./media/troubleshooting/functionapps-noaccess.png)

Un lector puede hacer clic en la pestaña **Características de la plataforma** y, a continuación, hacer clic en **Toda las opciones de configuración** para ver algunas opciones de configuración relacionadas con una aplicación de función (similar a una aplicación web), pero no puede modificar ninguna de estas opciones de configuración.

## <a name="virtual-machine"></a>Máquina virtual
Al igual que con las aplicaciones web, algunas funciones de la hoja de máquina virtual requieren acceso de escritura a la máquina virtual o a otros recursos del grupo de recursos.

Las máquinas virtuales están relacionadas con los nombres de dominio, las redes virtuales, las cuentas de almacenamiento y las reglas de alerta.

Estos elementos requieren acceso de **escritura** a la **máquina virtual**:

* Puntos de conexión  
* Direcciones IP  
* Discos  
* Extensiones  

Estos requieren acceso de **escritura** a la **máquina virtual** y al **grupo de recursos** (junto con el nombre de dominio) donde se encuentran:  

* Conjunto de disponibilidad  
* El conjunto de carga equilibrada  
* Reglas de alertas  

Si no puede acceder a ninguno de estos iconos, debe pedirle al administrador el acceso de colaborador al grupo de recursos.

## <a name="next-steps"></a>Pasos siguientes
* [Administración del acceso mediante RBAC y Azure Portal](role-assignments-portal.md)
* [Visualización de los registros de actividad de cambios de RBAC](change-history-report.md)

