---
title: 'Administración de metadatos de artefactos de la cuenta de integración: Azure Logic Apps | Microsoft Docs'
description: Agregue o recupere metadatos de artefactos de cuentas de integración de Azure Logic Apps con Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: bb7d9432-b697-44db-aa88-bd16ddfad23f
ms.date: 02/23/2018
ms.openlocfilehash: 537014c2780fe94cfb35806759f8bcbd974c4c95
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/28/2018
ms.locfileid: "43128810"
---
# <a name="manage-artifact-metadata-from-integration-accounts-in-azure-logic-apps-with-enterprise-integration-pack"></a>Administración de metadatos de artefactos de cuentas de integración de Azure Logic Apps con Enterprise Integration Pack

Puede definir metadatos personalizados para artefactos en cuentas de integración y recuperar metadatos durante el tiempo de ejecución para su aplicación lógica. Por ejemplo, puede especificar metadatos para artefactos como asociados, acuerdos, esquemas y asignaciones: todos almacenan metadatos usando pares de clave-valor. 

## <a name="add-metadata-to-artifacts-in-integration-accounts"></a>Adición de metadatos a artefactos en cuentas de integración

1. En Azure Portal, cree una [cuenta de integración](logic-apps-enterprise-integration-create-integration-account.md).

2. Agregue un artefacto a su cuenta de integración, por ejemplo, un [asociado](logic-apps-enterprise-integration-partners.md), un [acuerdo](logic-apps-enterprise-integration-agreements.md) o un [esquema](logic-apps-enterprise-integration-schemas.md).

3. Seleccione el artefacto, elija **Editar** y escriba los detalles de los metadatos.

   ![Introducción de los metadatos](media/logic-apps-enterprise-integration-metadata/image1.png)

## <a name="retrieve-metadata-from-artifacts-for-logic-apps"></a>Recuperación de metadatos de artefactos para aplicaciones lógicas

1. En Azure Portal, cree una [aplicación lógica](quickstart-create-first-logic-app-workflow.md).

2. Cree un [vínculo desde su aplicación lógica a su cuenta de integración](logic-apps-enterprise-integration-create-integration-account.md#link-account). 

3. En Diseñador de aplicación lógica, agregue un desencadenador como **Solicitud** o **HTTP** a su aplicación lógica.

4. En el desencadenador, elija **Nuevo paso** > **Agregar una acción**. Busque una cuenta de integración para que pueda buscar y seleccionar esta acción: **Cuenta de integración: Búsqueda de artefactos de la cuenta de integración**.

   ![Seleccione Búsqueda de artefactos de la cuenta de integración.](media/logic-apps-enterprise-integration-metadata/image2.png)

5. Seleccione **Tipo de artefacto** y proporcione el **nombre del artefacto**. Por ejemplo: 

   ![Seleccione el tipo de artefacto y proporcione el nombre del artefacto.](media/logic-apps-enterprise-integration-metadata/image3.png)

## <a name="example-retrieve-partner-metadata"></a>Ejemplo: recuperación de metadatos de asociados

Supongamos que este asociado tiene estos metadatos con los detalles de `routingUrl`:

![Búsqueda de metadatos de "routingURL" de asociado](media/logic-apps-enterprise-integration-metadata/image6.png)

1. En la aplicación lógica, agregue su desencadenador, una acción **Cuenta de integración: Búsqueda de artefactos de la cuenta de integración** para su asociado y una acción **HTTP**, por ejemplo:

   ![Adición de un desencadenador, una búsqueda de artefactos y una acción HTTP a una aplicación lógica](media/logic-apps-enterprise-integration-metadata/image4.png)

2. Para recuperar el URI, en la barra de herramientas del Diseñador de aplicación lógica, elija **Vista de código** para la aplicación lógica. La definición de su aplicación lógica debería ser similar a la de este ejemplo:

   ![Buscar lookup](media/logic-apps-enterprise-integration-metadata/image5.png)

## <a name="next-steps"></a>Pasos siguientes

* [Más información sobre acuerdos](logic-apps-enterprise-integration-agreements.md)
