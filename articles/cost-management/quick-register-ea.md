---
title: Registrar el Contrato Enterprise de Azure con Administración de costos de Azure | Microsoft Docs
description: Use el Contrato Enterprise para registrarse en Azure Cost Management.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 06/07/2018
ms.topic: quickstart
ms.custom: ''
ms.service: cost-management
manager: dougeby
ms.openlocfilehash: 0a687d9f48a068260044a6ba91d5af20e61014b6
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2018
ms.locfileid: "38237634"
---
# <a name="register-an-azure-enterprise-agreement-and-view-cost-data"></a>Registrar un Contrato Enterprise de Azure y ver datos de costo

El Contrato Enterprise se usa para registrarse en Azure Cost Management. Este registro le proporciona acceso al portal de Cloudyn. En esta guía de inicio rápido se detalla el proceso de registro necesario para crear una suscripción de evaluación de Cloudyn e iniciar sesión en el portal de Cloudyn. También se muestra cómo empezar a ver inmediatamente los datos de costo.

## <a name="sign-in-to-azure"></a>Inicio de sesión en Azure

- Inicie sesión en Azure Portal en http://portal.azure.com.

## <a name="register-with-azure-cost-management"></a>Regístrese en Azure Cost Management

1. En Azure Portal, haga clic en **Administración de costos + facturación** en la lista de servicios.
2. En **Overview** (Información general), haga clic en **Administración de costos**.  
    ![Página Administración de costos](./media/quick-register-ea/cost-mgt-billing-service.png)
3. En la página **Administración de costos**, haga clic en **Go to Cost Management** (Ir a Administración de costos) para abrir la página de registro de Cloudyn en una ventana nueva.
4. En la página de registro de evaluación del portal de Cloudyn, escriba el nombre de la compañía y, después, seleccione **Azure Enterprise Enrollment Administrator** (Administrador de inscripciones Enterprise de Azure).  
    ![registro de evaluación](./media/quick-register-ea/trial-reg.png)
5. Escriba la clave de API de inscripción a Enterprise Portal. Si no tiene la clave a mano, haga clic en el vínculo [Enterprise Portal](https://ea.azure.com) y siga los pasos que se describen a continuación:
  1. Inicie sesión en el sitio web de Azure Enterprise y haga clic en **Informes**, **API Access Key** (Clave de acceso de API) y copie la clave principal.  
    ![Clave de API de EA](./media/quick-register-ea/ea-key.png)
  3. Vuelva a la página de registro y pegue la clave de API.
6. Acepte los términos de uso y, después, valide la clave. Haga clic en **Siguiente** para permitir que Cloudyn recopile datos de recursos de Azure. Los datos recopilados incluyen datos de uso, de rendimiento, de facturación y de etiquetas de las suscripciones.  
    ![validación de claves](./media/quick-register-ea/ea-key-validated.png)
7. En **Invite other stakeholders** (Invitar a otras partes interesadas), puede agregar usuarios escribiendo sus direcciones de correo electrónico. Cuando haya terminado, haga clic en **Siguiente**. Según el tamaño de la inscripción de Azure, puede tardar hasta 24 horas para que todos sus datos de facturación se agreguen a Cloudyn.
8. Haga clic en **Go to Cloudyn** (Ir a Cloudyn) para abrir el portal de Cloudyn y, después, en la página **Cloud Accounts Management**, (Administración de cuentas en la nube) debería ver la información de la cuenta de EA registrada.

Para ver un tutorial de vídeo sobre cómo registrar su contrato Enterprise, consulte [How to Find Your EA Enrollment ID and API Key for use in Azure Cost Management by Cloudyn](https://youtu.be/u_phLs_udig) (Búsqueda del identificador de registro de EA y de la clave de API para su uso en Azure Cost Management).

[!INCLUDE [cost-management-create-account-view-data](../../includes/cost-management-create-account-view-data.md)]

## <a name="next-steps"></a>Pasos siguientes

En esta guía de inicio rápido, se usa la información de su Contrato Enterprise de Azure para registrarse en Administración de costos. También se inicia sesión en el portal de Cloudyn para que pueda empezar a visualizar los datos de costo. Para más información sobre Azure Cost Management, continúe con el tutorial de Cost Management.

> [!div class="nextstepaction"]
> [Revisión del uso y los costos](./tutorial-review-usage.md)
