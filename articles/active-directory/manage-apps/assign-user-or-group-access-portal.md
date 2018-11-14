---
title: Asignación de un usuario o un grupo a una aplicación empresarial en Azure Active Directory | Microsoft Docs
description: Procedimiento para seleccionar una aplicación empresarial para asignarla a un usuario o un grupo en la versión preliminar de Azure Active Directory
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/05/2018
ms.author: barbkess
ms.reviewer: luleon
ms.openlocfilehash: ee0b14123e193f219e403d2608368c27f953013d
ms.sourcegitcommit: f0c2758fb8ccfaba76ce0b17833ca019a8a09d46
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/06/2018
ms.locfileid: "51037981"
---
# <a name="assign-a-user-or-group-to-an-enterprise-app-in-azure-active-directory"></a>Asignación de un usuario o un grupo a una aplicación empresarial en Azure Active Directory
Para asignar un usuario o un grupo a una aplicación empresarial, debe contar con los permisos adecuados para administrar dicha aplicación, y debe ser administrador global del directorio.

> [!NOTE]
> Para los requisitos de licencia de las características tratadas en este artículo, vea la [página de precios de Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory).

> [!NOTE]
> Con aplicaciones de Microsoft (por ejemplo, aplicaciones de Office 365), use PowerShell para asignar usuarios a una aplicación empresarial.


## <a name="how-do-i-assign-user-access-to-an-enterprise-app-in-the-azure-portal"></a>¿Cómo se asigna el acceso de usuario a una aplicación empresarial en Azure Portal?
1. Inicie sesión en [Azure Portal](https://portal.azure.com) con una cuenta que tenga el rol de administrador global en el directorio.
2. Seleccione **Todos los servicios**, escriba Azure Active Directory en el cuadro de texto y seleccione **Entrar**.
3. Seleccione **Aplicaciones empresariales**.

    ![Apertura de Enterprise apps (Aplicaciones empresariales)](./media/assign-user-or-group-access-portal/open-enterprise-apps.png)
4. En la hoja **Aplicaciones empresariales**, seleccione **Todas las aplicaciones**. A continuación se muestran las aplicaciones que se pueden administrar.
5. En la hoja **Enterprise applications (Aplicaciones empresariales) - Todas las aplicaciones** , seleccione una aplicación.
6. En la hoja ***nombre de la aplicación*** (es decir, la hoja con el nombre de la aplicación seleccionada en el título), seleccione **Usuarios y grupos**.

    ![Selección del comando Todas las aplicaciones](./media/assign-user-or-group-access-portal/select-app-users.png)
7. En la hoja ***nombreDeAplicación*** **- User & Group Assignment** (Asignación de usuario y de grupo), seleccione el comando **Agregar**.
8. En la hoja **Agregar asignación**, seleccione **Usuarios y grupos**.

    ![Asignación de un usuario o un grupo a la aplicación](./media/assign-user-or-group-access-portal/assign-users.png)
9. En la hoja **Usuarios y grupos**, seleccione uno o varios usuarios o grupos de la lista y luego haga clic en el botón **Seleccionar** de la parte inferior de la hoja.
10. En la hoja **Agregar asignación**, seleccione **Rol**. Después, en la hoja **Seleccionar rol**, seleccione el rol que desea aplicar a los usuarios o grupos seleccionados, y luego haga clic en el botón **Aceptar** de la parte inferior de la hoja.
11. En la hoja **Agregar asignación**, haga clic en el botón **Asignar** de la parte inferior de la hoja. Los usuarios o grupos asignados tienen definidos los permisos por el rol seleccionado para esta aplicación empresarial.

## <a name="how-do-i-assign-a-user-to-an-enterprise-app-using-powershell"></a>¿Cómo se asigna un usuario a una aplicación empresarial mediante PowerShell?

1. Abra un símbolo del sistema de Windows PowerShell con privilegios elevados.

    >[!NOTE] 
    > Debe instalar el módulo de AzureAD (use el comando `Install-Module -Name AzureAD`). Si se le pide que instale un módulo de NuGet o el nuevo módulo de PowerShell para Azure Active Directory V2, escriba S y presione ENTRAR.

2. Ejecute `Connect-AzureAD` e inicie sesión con una cuenta de usuario administrador global.
3. Use el siguiente script para asignar un usuario y un rol a una aplicación:

    ```powershell
    # Assign the values to the variables
    $username = "<You user's UPN>"
    $app_name = "<Your App's display name>"
    $app_role_name = "<App role display name>"
    
    # Get the user to assign, and the service principal for the app to assign to
    $user = Get-AzureADUser -ObjectId "$username"
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    $appRole = $sp.AppRoles | Where-Object { $_.DisplayName -eq $app_role_name }
    
    # Assign the user to the app role
    New-AzureADUserAppRoleAssignment -ObjectId $user.ObjectId -PrincipalId $user.ObjectId -ResourceId $sp.ObjectId -Id $appRole.Id
    ```     

Para más información sobre cómo asignar un usuario a un rol de aplicación, consulte la documentación de [AzureADUserAppRoleAssignment de nuevo](https://docs.microsoft.com/powershell/module/azuread/new-azureaduserapproleassignment?view=azureadps-2.0).

Para asignar un grupo a una aplicación empresarial, es necesario reemplazar `Get-AzureADUser` por `Get-AzureADGroup`.

### <a name="example"></a>Ejemplo

En este ejemplo se asigna el usuario Britta Simon a la aplicación [Microsoft Workplace Analytics](https://products.office.com/business/workplace-analytics) mediante PowerShell.

1. En PowerShell, asigne los valores correspondientes a las variables $username, app_name $ y $app_role_name. 

    ```powershell
    # Assign the values to the variables
    $username = "britta.simon@contoso.com"
    $app_name = "Workplace Analytics"
    ```

2. En este ejemplo, no se sabe cuál es el nombre exacto del rol de aplicación que se quiere asignar a Britta Simon. Ejecute los comandos siguientes para obtener el usuario ($user) y la entidad de servicio ($sp) mediante el UPN de usuario y los nombres para mostrar de la entidad de servicio.

    ```powershell
    # Get the user to assign, and the service principal for the app to assign to
    $user = Get-AzureADUser -ObjectId "$username"
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    ```
        
3. Ejecute el comando `$sp.AppRoles` para mostrar los roles disponibles para la aplicación Workplace Analytics. En este ejemplo, queremos asignar a Britta Simon el rol de Analyst (acceso limitado).
    
    ![Rol de Workplace Analytics](./media/assign-user-or-group-access-portal/workplace-analytics-role.png)

4. Asigne el nombre de rol a la variable `$app_role_name`.
        
    ```powershell
    # Assign the values to the variables
    $app_role_name = "Analyst (Limited access)"
    $appRole = $sp.AppRoles | Where-Object { $_.DisplayName -eq $app_role_name }
    ```

5. Ejecute el comando siguiente para asignar el rol de aplicación al usuario:

    ```powershell
    # Assign the user to the app role
    New-AzureADUserAppRoleAssignment -ObjectId $user.ObjectId -PrincipalId $user.ObjectId -ResourceId $sp.ObjectId -Id $appRole.Id
    ```

## <a name="next-steps"></a>Pasos siguientes
* [Ver todos mis grupos](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Eliminación de asignaciones de usuario o grupo de una aplicación empresarial](remove-user-or-group-access-portal.md)
* [Deshabilitar los inicios de sesión de los usuarios de una aplicación empresarial](disable-user-sign-in-portal.md)
* [Cambiar el nombre o el logotipo de una aplicación empresarial](change-name-or-logo-portal.md)
