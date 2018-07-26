---
title: Programación del entorno de ejecución para la integración de SSIS en Azure | Microsoft Docs
description: En este artículo se describe cómo programar el inicio y la detención de una instancia de Integration Runtime (IR) de SSIS en Azure mediante Azure Automation y Data Factory.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 07/16/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: f83715d2a382db271686210d9df285c255c09216
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/18/2018
ms.locfileid: "39114001"
---
# <a name="how-to-start-and-stop-the-azure-ssis-integration-runtime-on-a-schedule"></a>Inicio y detención del entorno de ejecución para la integración de SSIS en Azure según una programación
En este artículo se describe cómo programar el inicio y la detención de una instancia de Integration Runtime (IR) de SSIS en Azure mediante Azure Automation y Azure Data Factory. La ejecución de una instancia de Integration Runtime (IR) para la integración de SSIS (SQL Server Integration Services) en Azure lleva un costo asociado. Por lo tanto, normalmente es preferible ejecutar la instancia de Integration Runtime solo cuando haya que ejecutar paquetes de SSIS en Azure y detenerla cuando ya no se necesite. Puede usar la interfaz de usuario de Data Factory o Azure PowerShell para [iniciar o detener manualmente una instancia de IR de SSIS en Azure](manage-azure-ssis-integration-runtime.md)).

Por ejemplo, puede crear actividades web con webhooks en un runbook de PowerShell de Azure Automation y encadenar una actividad Ejecución de paquetes SSIS entre ellos. Las actividades web pueden iniciar y detener la instancia de Integration Runtime para la integración de SSIS en Azure inmediatamente antes y después de que el paquete se ejecute. Para más información sobre la actividad de Ejecución de paquetes SSIS, vea [Ejecución de un paquete de SSIS mediante una actividad de SSIS de Azure Data Factory](how-to-invoke-ssis-package-ssis-activity.md).

## <a name="overview-of-the-steps"></a>Información general de los pasos

Estos son los pasos de alto nivel que se describen en este artículo:

1. **Creación y prueba de un runbook de Azure Automation**. En este paso, va a crear un runbook de PowerShell con el script que inicia o detiene una instancia de Integration Runtime de SSIS en Azure. Después, pruebe el runbook en ambos escenarios de inicio y detención y confirme que la instancia de IR se inicia o se detiene. 
2. **Creación de dos programaciones para el runbook.** En la primera programación, va a configurar el runbook con la operación START (Iniciar). En la segunda programación, va a configurar el runbook con la operación STOP (Detener). En ambas programaciones, especifique la cadencia en la que se va a ejecutar el runbook. Por ejemplo, puede querer programar la primera de ellas para que se ejecute a las 8 a. m. cada día y la segunda para que se ejecute a las 11 p. m. todos los días. Cuando se ejecuta el primer runbook, inicia la instancia de Integration Runtime de SSIS en Azure. Cuando se ejecuta el segundo runbook, la detiene. 
3. **Cree dos webhooks para el runbook**, uno para la operación de inicio y otro para la operación de detención. Utilice las direcciones URL de estos webhooks al configurar las actividades web en una canalización de Data Factory. 
4. **Creación de una canalización de Data Factory.** La canalización que se va a crear consta de tres actividades. La primera actividad **Web** invoca al primer webhook para que inicie la instancia de Integration Runtime de SSIS en Azure. La actividad **Stored Procedure** ejecuta un script SQL que ejecuta el paquete de SSIS. La segunda actividad **Web** detiene la instancia de IR de SSIS en Azure. Para más información sobre cómo invocar un paquete de SSIS desde una canalización de Data Factory mediante el uso de la actividad Stored Procedure, consulte [Invocación de un paquete de SSIS](how-to-invoke-ssis-package-stored-procedure-activity.md). A continuación, va a crear un desencadenador de programación para programar la canalización para que se ejecute a la cadencia que haya especificado.

## <a name="prerequisites"></a>Requisitos previos
Si todavía no ha aprovisionado una instancia de Integration Runtime de SSIS en Azure, aprovisiónela ahora siguiendo las instrucciones del [tutorial](tutorial-create-azure-ssis-runtime-portal.md). 

## <a name="create-and-test-an-azure-automation-runbook"></a>Creación y prueba de un runbook de Azure Automation
En esta sección se realizarán los pasos siguientes: 

1. Creación de una cuenta de Azure Automation
2. Creación de un runbook de PowerShell en la cuenta de Azure Automation El script de PowerShell asociado al runbook inicia o detiene una instancia de Integration Runtime de SSIS en Azure en función del comando que se haya especificado para el parámetro OPERATION. 
3. Pruebe el runbook en ambos escenarios de inicio y detención para confirmar que funciona. 

### <a name="create-an-azure-automation-account"></a>Creación de una cuenta de Azure Automation
Si no tiene una cuenta de Azure Automation, cree una siguiendo las instrucciones de este paso. Para obtener instrucciones detalladas, consulte [Creación de una cuenta de Azure Automation](../automation/automation-quickstart-create-account.md). Como parte de este paso, va a crear una cuenta **de ejecución de Azure** (una entidad de servicio en su instancia de Azure Active Directory) y la va a agregar al rol **Colaborador** de su suscripción a Azure. Asegúrese de que es igual que la suscripción que contiene la factoría de datos que tiene la instancia de Integration Runtime de SSIS en Azure. Azure Automation utiliza esta cuenta para autenticarse en Azure Resource Manager y trabajar con sus recursos. 

1. Inicie el explorador web **Microsoft Edge** o **Google Chrome**. Actualmente, la interfaz de usuario de Data Factory solo se admite en los exploradores web Microsoft Edge y Google Chrome.
2. Inicie sesión en [Azure Portal](https://portal.azure.com/).    
3. Seleccione **Nuevo** en el menú de la izquierda, seleccione **Supervisión y administración** y seleccione **Automation**. 

    ![Nuevo -> Supervisión y administración -> Automation](./media/how-to-schedule-azure-ssis-integration-runtime/new-automation.png)
2. En la ventana **Agregar una cuenta de Automation**, siga estos pasos: 

    1. Especifique un **nombre** para la cuenta de automatización. 
    2. Seleccione la **suscripción** que tenga la factoría de datos con la instancia de Integration Runtime de SSIS en Azure. 
    3. En **Grupo de recursos**, seleccione **Crear nuevo** para seleccionar un nuevo grupo de recursos o seleccione **Usar existente** para seleccionar un grupo de recursos existente. 
    4. Seleccione una **ubicación** para la cuenta de Automation. 
    5. Confirme que **Crear cuenta de ejecución** está establecido en **Sí**. Se crea una entidad de servicio en la instancia de Azure Active Directory. Se agrega al rol de **colaborador** de la suscripción a Azure.
    6. Seleccione **Anclar al panel** para que se vea en el panel del portal. 
    7. Seleccione **Crear**. 

        ![Nuevo -> Supervisión y administración -> Automation](./media/how-to-schedule-azure-ssis-integration-runtime/add-automation-account-window.png)
3. Verá el **estado de implementación** en el panel y en las notificaciones. 
    
    ![Implementando automatización](./media/how-to-schedule-azure-ssis-integration-runtime/deploying-automation.png) 
4. Verá la página principal de la cuenta de automatización una vez creada correctamente. 

    ![Página principal de Automation](./media/how-to-schedule-azure-ssis-integration-runtime/automation-home-page.png)

### <a name="import-data-factory-modules"></a>Importación de módulos de Data Factory

1. Seleccione **Módulos** en la sección **RECURSOS COMPARTIDOS** del menú de la izquierda y compruebe si tiene **AzureRM.Profile** y **AzureRM.DataFactoryV2** en la lista de módulos.

    ![Comprobación de los módulos necesarios](media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image1.png)

2.  Vaya al módulo [AzureRM.DataFactoryV2](https://www.powershellgallery.com/packages/AzureRM.DataFactoryV2/) en la Galería de PowerShell, seleccione **Implementar en Azure Automation**, seleccione su cuenta de Automation y luego, **Aceptar**. Vuelva a la vista **Módulos** en la sección **RECURSOS COMPARTIDOS** del menú izquierdo y espere hasta que vea que el **ESTADO** del módulo **AzureRM.DataFactoryV2** cambia a **Disponible**.

    ![Comprobación del módulo de factoría de datos](media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image2.png)

3.  Vaya al módulo [AzureRM.Profile](https://www.powershellgallery.com/packages/AzureRM.profile/) en la Galería de PowerShell, haga clic en **Implementar en Azure Automation**, seleccione su cuenta de Automation y luego, **Aceptar**. Vuelva a la vista **Módulos** en la sección **RECURSOS COMPARTIDOS** del menú izquierdo y espere hasta que vea que el **ESTADO** del módulo **AzureRM.Profile** cambia a **Disponible**.

    ![Comprobación del módulo Perfil](media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image3.png)

### <a name="create-a-powershell-runbook"></a>Creación de un runbook de PowerShell
El siguiente procedimiento proporciona los pasos para crear un runbook de PowerShell. El script asociado al runbook inicia o detiene una instancia de Integration Runtime de SSIS en Azure en función del comando que se haya especificado para el parámetro **OPERATION**. En esta sección no se ofrecen todos los detalles para crear un runbook. Para más información, consulte el artículo [Creación de un runbook de Azure Automation](../automation/automation-quickstart-create-runbook.md).

1. Cambie a la pestaña **Runbooks** y seleccione **+ Agregar un runbook** en la barra de herramientas. 

    ![Botón Agregar un runbook](./media/how-to-schedule-azure-ssis-integration-runtime/runbooks-window.png)
2. Seleccione **Crear un runbook nuevo** y realice los pasos siguientes: 

    1. En **Nombre**, escriba **StartStopAzureSsisRuntime**.
    2. En **Tipo de runbook**, seleccione **PowerShell**.
    3. Seleccione **Crear**.
    
        ![Botón Agregar un runbook](./media/how-to-schedule-azure-ssis-integration-runtime/add-runbook-window.png)
3. Copie y pegue el siguiente script en la ventana de script del runbook. Guardar y, después, publique el runbook con los botones **Guardar** y **Publicar** de la barra de herramientas. 

    ```powershell
    Param
    (
          [Parameter (Mandatory= $true)]
          [String] $ResourceGroupName,
    
          [Parameter (Mandatory= $true)]
          [String] $DataFactoryName,
    
          [Parameter (Mandatory= $true)]
          [String] $AzureSSISName,
    
          [Parameter (Mandatory= $true)]
          [String] $Operation
    )
    
    $connectionName = "AzureRunAsConnection"
    try
    {
        # Get the connection "AzureRunAsConnection "
        $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         
    
        "Logging in to Azure..."
        Connect-AzureRmAccount `
            -ServicePrincipal `
            -TenantId $servicePrincipalConnection.TenantId `
            -ApplicationId $servicePrincipalConnection.ApplicationId `
            -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint 
    }
    catch {
        if (!$servicePrincipalConnection)
        {
            $ErrorMessage = "Connection $connectionName not found."
            throw $ErrorMessage
        } else{
            Write-Error -Message $_.Exception
            throw $_.Exception
        }
    }
    
    if($Operation -eq "START" -or $operation -eq "start")
    {
        "##### Starting #####"
        Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $AzureSSISName -Force
    }
    elseif($Operation -eq "STOP" -or $operation -eq "stop")
    {
        "##### Stopping #####"
        Stop-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
    }  
    "##### Completed #####"    
    ```

    ![Edición de un runbook de PowerShell](./media/how-to-schedule-azure-ssis-integration-runtime/edit-powershell-runbook.png)
5. Pruebe el runbook seleccionando el botón **Iniciar** en la barra de herramientas. 

    ![Botón Iniciar runbook](./media/how-to-schedule-azure-ssis-integration-runtime/start-runbook-button.png)
6. En la ventana **Iniciar runbook**, realice los pasos siguientes: 

    1. En **RESOURCE GROUP NAME** (Nombre del grupo de recursos), escriba el nombre del grupo de recursos con la factoría de datos que tenga la instancia de IR de SSIS en Azure. 
    2. En **DATA FACTORY NAME** (Nombre de factoría de datos), escriba el nombre de la factoría de datos que tenga instancia de IR de SSIS en Azure. 
    3. En **AZURESSISNAME** (Nombre de SSIS en Azure), escriba el nombre de la instancia de IR de SSIS en Azure. 
    4. En **OPERATION** (Operación), escriba **START** (Iniciar). 
    5. Seleccione **Aceptar**.  

        ![Ventana Iniciar runbook](./media/how-to-schedule-azure-ssis-integration-runtime/start-runbook-window.png)
7. En la ventana de trabajo, seleccione el icono **Salida**. En la ventana de salida del trabajo, espere hasta ver el mensaje **### Completed ###** (Completado) después de ver **### Starting ###** (Iniciando). Se tardan veinte minutos aproximadamente en iniciar una instancia de Integration Runtime de SSIS en Azure. Cierre la ventana **Trabajo** y vuelva a la ventana **Runbook**.

    ![IR de SSIS en Azure: iniciado](./media/how-to-schedule-azure-ssis-integration-runtime/start-completed.png)
8.  Repita los dos pasos anteriores, pero utilice **STOP** (Detener) como valor de **OPERATION**. Vuelva a iniciar el runbook seleccionando el botón **Iniciar** en la barra de herramientas. Especifique el nombre del grupo de recursos, el nombre de la factoría de datos y el nombre de la instancia de Integration Runtime de SSIS en Azure. En **OPERATION** (Operación), escriba **STOP** (Detener). 

    En la ventana de salida del trabajo, espere hasta ver el mensaje **### Completed ###** (Completado) después de ver **### Stopping ###** (Deteniendo). La detención de una instancia de IR de SSIS en Azure no tarda tanto como iniciarla. Cierre la ventana **Trabajo** y vuelva a la ventana **Runbook**.

## <a name="create-schedules-for-the-runbook-to-startstop-the-azure-ssis-ir"></a>Creación de programaciones para que el runbook inicie o detenga la instancia de IR de SSIS en Azure
En la sección anterior, ha creado un runbook de Azure Automation que puede iniciar o detener una instancia de Integration Runtime de SSIS en Azure. En esta sección, va a crear dos programaciones para el runbook. Al configurar la primera programación, especifique START para el parámetro OPERATION. De igual forma, al configurar la segunda programación, especifique STOP para OPERATION. Para ver los pasos detallados necesarios para crear programaciones, consulte [Creación de una programación](../automation/automation-schedules.md#creating-a-schedule).

1. En la ventana **Runbook**, seleccione **Programaciones** y seleccione **+ Agregar una programación** en la barra de herramientas. 

    ![IR de SSIS en Azure: iniciado](./media/how-to-schedule-azure-ssis-integration-runtime/add-schedules-button.png)
2. En la ventana **Programar Runbook**, realice los pasos siguientes: 

    1. Seleccione **Vincular una programación a su Runbook**. 
    2. Seleccione **Crear una programación nueva**.
    3. En la ventana **Nueva programación**, escriba **Iniciar IR a diario** en **Nombre**. 
    4. En la sección **Se inicia el**, especifique una hora unos minutos después de la hora actual. 
    5. En **Periodicidad**, seleccione **Periódico**. 
    6. En la sección **Repetir cada**, seleccione **Día**. 
    7. Seleccione **Crear**. 

        ![Programación para el inicio de una instancia de IR de SSIS en Azure](./media/how-to-schedule-azure-ssis-integration-runtime/new-schedule-start.png)
3. Cambie a la pestaña **Configuración de ejecución y parámetros**. Especifique el nombre del grupo de recursos, el nombre de la factoría de datos y el nombre de la instancia de Integration Runtime de SSIS en Azure. En **OPERATION** (Operación), escriba **START** (Iniciar). Seleccione **Aceptar**. Vuelva a seleccionar **Aceptar** para ver la programación en la página **Programaciones** del runbook. 

    ![Programación para el inicio de la instancia de IR de SSIS en Azure](./media/how-to-schedule-azure-ssis-integration-runtime/start-schedule.png)
4. Repita los dos pasos anteriores para crear una programación denominada **Detener IR a diario**. Esta vez, especifique la hora en al menos 30 minutos después de la hora que ha especificado en la programación **Iniciar IR a diario**. En **OPERATION**, especifique **STOP**. 
5. En la ventana **Runbook**, seleccione **Trabajos** en el menú de la izquierda. Debería ver los trabajos creados por las programaciones en las horas y estados especificados. Puede ver detalles sobre el trabajo, como una salida similar a la que ha visto al probar el runbook. 

    ![Programación para el inicio de la instancia de IR de SSIS en Azure](./media/how-to-schedule-azure-ssis-integration-runtime/schedule-jobs.png)
6. Cuando haya realizado las pruebas, para deshabilitar las programaciones, edítelas y seleccione **NO** en **Habilitado**. Seleccione **Programaciones** en el menú de la izquierda, seleccione **Iniciar IR a diario o Detener IR a diario** y seleccione **No** para **Habilitado**. 

## <a name="create-webhooks-to-start-and-stop-the-azure-ssis-ir"></a>Creación de webhooks para iniciar y detener la instancia de IR de SSIS en Azure
Siga las instrucciones de [Creación de un webhook](../automation/automation-webhooks.md#creating-a-webhook) para crear dos webhooks para el runbook. En el primero, especifique START en OPERATION y, en el segundo, especifique STOP en OPERATION. Guarde las direcciones URL de ambos webhooks en algún lugar (por ejemplo, un archivo de texto o un cuaderno de OneNote). Utilizará estas direcciones URL al configurar las actividades Web en una canalización de Data Factory. La siguiente imagen muestra un ejemplo de cómo crear un webhook que inicia la instancia de IR de SSIS en Azure:

1. En la ventana **Runbook**, seleccione **Webhooks** en el menú de la izquierda y seleccione **+ Agregar webhook** en la barra de herramientas. 

    ![Webhooks -> Agregar webhook](./media/how-to-schedule-azure-ssis-integration-runtime/add-web-hook-menu.png)
2. En la ventana **Agregar webhook**, seleccione **Crear nuevo webhook** y realice las siguientes acciones: 

    1. En **Nombre**, escriba **StartAzureSsisIR**. 
    2. Confirme que **Habilitado** está establecido en **Sí**. 
    3. Copie la **URL** y guárdela en algún lugar. Este paso es importante. No verá la dirección URL después. 
    4. Seleccione **Aceptar**. 

        ![Ventana Nuevo webhook](./media/how-to-schedule-azure-ssis-integration-runtime/new-web-hook-window.png)
3. Cambie a la pestaña **Configuración de ejecución y parámetros**. Especifique el nombre del grupo de recursos, el nombre de la factoría de datos y el nombre de la instancia de Integration Runtime de SSIS en Azure. En **OPERATION** (Operación), escriba **START** (Iniciar). Haga clic en **OK**. A continuación, haga clic en **Crear**. 

    ![Webhook: configuración de ejecución y parámetros](./media/how-to-schedule-azure-ssis-integration-runtime/webhook-parameters.png)
4. Repita los tres pasos anteriores para crear otro webhook denominado **StopAzureSsisIR**. No se olvide de copiar la dirección URL. Al especificar los parámetros y los parámetros de ejecución, escriba **STOP** en **OPERATION**. 

Debe tener dos direcciones URL, una para el webhook **StartAzureSsisIR** y otra para el webhook **StopAzureSsisIR**. Puede enviar una solicitud HTTP POST a estas direcciones URL para iniciar o detener la instancia de IR de SSIS en Azure. 

## <a name="create-and-schedule-a-data-factory-pipeline-that-startsstops-the-ir"></a>Creación y programación de una canalización de Data Factory que inicia o detiene el entorno de ejecución de integración
En esta sección se muestra cómo utilizar una actividad Web para invocar a los webhooks que se crearon en la sección anterior.

La canalización que se va a crear consta de tres actividades. 

1. La primera actividad **Web** invoca al primer webhook para que inicie la instancia de Integration Runtime de SSIS en Azure. 
2. La actividad **Ejecución de paquetes SSIS** o **Procedimiento almacenado** ejecuta el paquete SSIS.
3. La segunda actividad **Web** invoca al webhook para que detenga la instancia de Integration Runtime de SSIS en Azure. 

Después de crear y probar la canalización, cree un desencadenador de programación y asócielo a la canalización. El desencadenador de programación define una programación para la canalización. Suponga que va a crear un desencadenador que está programado para ejecutarse diariamente a las 11 p. m. El desencadenador ejecuta la canalización a las 11 p. m. todos los días. La canalización inicia la instancia de IR de SSIS en Azure, ejecuta el paquete de SSIS y, finalmente, detiene dicha instancia. 

### <a name="create-a-data-factory"></a>Crear una factoría de datos

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).    
2. En el menú de la izquierda, haga clic en **Nuevo**, **Datos y análisis** y **Factoría de datos**. 
   
   ![New->DataFactory](./media/tutorial-create-azure-ssis-runtime-portal/new-data-factory-menu.png)
3. En la página **Nueva factoría de datos**, escriba **MyAzureSsisDataFactory** en el **nombre**. 
      
     ![Página New data factory (Nueva factoría de datos)](./media/tutorial-create-azure-ssis-runtime-portal/new-azure-data-factory.png)
 
   El nombre de la instancia de Azure Data Factory debe ser **único de forma global**. Si recibe el siguiente error, cambie el nombre de la factoría de datos (por ejemplo, yournameMyAzureSsisDataFactory) e intente crearla de nuevo. Consulte el artículo [Azure Data Factory: reglas de nomenclatura](naming-rules.md) para conocer las reglas de nomenclatura de los artefactos de Data Factory.
  
       `Data factory name �MyAzureSsisDataFactory� is not available`
3. Seleccione la **suscripción** de Azure donde desea crear la factoría de datos. 
4. Para el **grupo de recursos**, realice uno de los siguientes pasos:
     
      - Seleccione en primer lugar **Usar existente**y después un grupo de recursos de la lista desplegable. 
      - Seleccione **Crear nuevo**y escriba el nombre de un grupo de recursos.   
         
      Para obtener más información sobre los grupos de recursos, consulte [Uso de grupos de recursos para administrar los recursos de Azure](../azure-resource-manager/resource-group-overview.md).  
4. Seleccione **V2** para la **versión**.
5. Seleccione la **ubicación** de Data Factory. En la lista solo se muestran las ubicaciones que se admiten para la creación de factorías de datos.
6. Seleccione **Anclar al panel**.     
7. Haga clic en **Create**(Crear).
8. En el panel, verá el icono siguiente con el estado: **Implementando factoría de datos**. 

    ![icono implementando factoría de datos](media/tutorial-create-azure-ssis-runtime-portal/deploying-data-factory.png)
9. Una vez completada la creación, verá la página **Data Factory** tal como se muestra en la imagen.
   
   ![Página principal Factoría de datos](./media/tutorial-create-azure-ssis-runtime-portal/data-factory-home-page.png)
10. Haga clic en **Author & Monitor** (Creación y supervisión) para iniciar la interfaz de usuario de Data Factory en una pestaña independiente.

### <a name="create-a-pipeline"></a>Crear una canalización

1. En la página **Introducción** (Introducción), haga clic en **Create pipeline** (Crear canalización). 

   ![Página de introducción](./media/how-to-schedule-azure-ssis-integration-runtime/get-started-page.png)
2. En el cuadro de herramientas **Activities** (Actividades), expanda **General** (General), arrastre la actividad **web** y colóquela en la superficie del diseñador de canalizaciones. En la pestaña **General** de la ventana **Properties** (Propiedades), cambie el nombre de la actividad a **StartIR**.

   ![Primera actividad Web: pestaña General](./media/how-to-schedule-azure-ssis-integration-runtime/first-web-activity-general-tab.png)
3. Cambie a la pestaña **Settings** (Configuración) de la ventana de **Properties** (Propiedades) y realice las siguientes acciones: 

    1. En **URL**, pegue la dirección URL del webhook que inicia la instancia de IR de SSIS en Azure. 
    2. En **Method** (Método) seleccione **POST**. 
    3. En **Body** (Cuerpo), especifique `{"message":"hello world"}`. 
   
        ![Primera actividad Web: pestaña Configuración](./media/how-to-schedule-azure-ssis-integration-runtime/first-web-activity-settnigs-tab.png)

4. Arrastre y coloque la actividad Procedimiento almacenado de la sección **General** del cuadro de herramientas **Actividades**. Establezca el nombre de la actividad en **RunSSISPackage**. 

5. Si selecciona la actividad Ejecución de paquetes SSIS, siga las instrucciones de [Run an SSIS package using the SSIS activity in Azure Data Factory](how-to-invoke-ssis-package-ssis-activity.md) (Ejecución de un paquete SSIS mediante la actividad SSIS en Azure Data Factory) para completar la creación de la actividad.  Asegúrese de especificar un número suficiente de reintentos que tengan frecuencia bastante como para esperar a que el entorno de ejecución de integración de Azure-SSIS esté disponible, ya que tarda hasta 30 minutos en iniciarse. 

    ![Configuración de los reintentos](media/how-to-schedule-azure-ssis-integration-runtime/retry-settings.png)

6. Si selecciona la actividad Procedimiento almacenado, siga las instrucciones de [Invocación de un paquete de SSIS mediante una actividad de procedimiento almacenado de Azure Data Factory](how-to-invoke-ssis-package-stored-procedure-activity.md) para completar la creación de la actividad. Asegúrese de insertar un script de Transact-SQL que espere hasta que esté disponible el entorno de ejecución de integración de Azure-SSIS, ya que tarda hasta 30 minutos en iniciarse.
    ```sql
    DECLARE @return_value int, @exe_id bigint, @err_msg nvarchar(150)

    -- Wait until Azure-SSIS IR is started
    WHILE NOT EXISTS (SELECT * FROM [SSISDB].[catalog].[worker_agents] WHERE IsEnabled = 1 AND LastOnlineTime > DATEADD(MINUTE, -10, SYSDATETIMEOFFSET()))
    BEGIN
        WAITFOR DELAY '00:00:01';
    END

    EXEC @return_value = [SSISDB].[catalog].[create_execution] @folder_name=N'YourFolder',
        @project_name=N'YourProject', @package_name=N'YourPackage',
        @use32bitruntime=0, @runincluster=1, @useanyworker=1,
        @execution_id=@exe_id OUTPUT 

    EXEC [SSISDB].[catalog].[set_execution_parameter_value] @exe_id, @object_type=50, @parameter_name=N'SYNCHRONIZED', @parameter_value=1

    EXEC [SSISDB].[catalog].[start_execution] @execution_id = @exe_id, @retry_count = 0

    -- Raise an error for unsuccessful package execution, check package execution status = created (1)/running (2)/canceled (3)/
    -- failed (4)/pending (5)/ended unexpectedly (6)/succeeded (7)/stopping (8)/completed (9) 
    IF (SELECT [status] FROM [SSISDB].[catalog].[executions] WHERE execution_id = @exe_id) <> 7 
    BEGIN
        SET @err_msg=N'Your package execution did not succeed for execution ID: '+ CAST(@execution_id as nvarchar(20))
        RAISERROR(@err_msg, 15, 1)
    END
    ```

7. Conecte la actividad **Web** a la actividad **Ejecución de paquetes SSIS** o **Procedimiento almacenado**. 

    ![Conexión de las actividades Web y Stored Procedure](./media/how-to-schedule-azure-ssis-integration-runtime/connect-web-sproc.png)

8. Arrastre y coloque otra actividad **Web** a la derecha de la actividad **Ejecución de paquetes SSIS** o **Procedimiento almacenado**. Establezca el nombre de la actividad en **StopIR**. 
9. Cambie a la pestaña **Settings** (Configuración) de la ventana de **Properties** (Propiedades) y realice las siguientes acciones: 

    1. En **URL**, pegue la dirección URL del webhook que detiene la instancia de IR de SSIS en Azure. 
    2. En **Method** (Método) seleccione **POST**. 
    3. En **Body** (Cuerpo), especifique `{"message":"hello world"}`.  
10. Conecte la actividad **Ejecución de paquetes SSIS** o **Procedimiento almacenado** a la actividad **Web** más reciente.

    ![Canalización completa](./media/how-to-schedule-azure-ssis-integration-runtime/full-pipeline.png)
11. Para comprobar la configuración de canalización, haga clic en **Validate** (Comprobar) en la barra de herramientas. Para cerrar **Pipeline Validation Report** (Informe de comprobación de la canalización), haga clic en el botón **>>**. 

    ![Comprobar la canalización](./media/how-to-schedule-azure-ssis-integration-runtime/validate-pipeline.png)

### <a name="test-run-the-pipeline"></a>Realización de la serie de pruebas de la canalización

1. Seleccione **Test Run** (Serie de pruebas) en la barra de herramientas de la canalización. Verá la salida en la ventana **Salida** del panel inferior. 

    ![Serie de pruebas](./media/how-to-schedule-azure-ssis-integration-runtime/test-run-output.png)
2. En la página **Runbook** de la cuenta de Azure Automation, puede comprobar que los trabajos se ejecutaron para iniciar y detener la instancia de IR de SSIS en Azure. 

    ![Trabajos de runbook](./media/how-to-schedule-azure-ssis-integration-runtime/runbook-jobs.png)
3. Inicie SQL Server Management Studio. En la ventana **Conectar al servidor**, realice las acciones siguientes: 

    1. En **Nombre del servidor**, especifique **&lt;base de datos de SQL Azure&gt;.database.windows.net**.
    2. Seleccione **Opciones >>**.
    3. En **Conectar una base de datos**, seleccione **SSISDB**.
    4. Seleccione **Conectar**. 
    5. Expanda **Catálogos de Integration Services** -> **SSISDB** -> Su carpeta -> **Proyectos** -> Su proyecto de SSIS -> **Paquetes** . 
    6. Haga clic con el botón derecho en el paquete de SSIS y seleccione **Informes** -> **Informes estándar** -> **Todas las ejecuciones**. 
    7. Compruebe que se ejecuta el paquete de SSIS. 

        ![Comprobación de la ejecución del paquete de SSIS](./media/how-to-schedule-azure-ssis-integration-runtime/verfiy-ssis-package-run.png)

### <a name="schedule-the-pipeline"></a>Programación de la canalización 
Ahora que la canalización funciona de la manera prevista, puede crear un desencadenador para ejecutar esta canalización en una cadencia especificada. Para más información acerca de cómo asociar un desencadenador de programación a una canalización, consulte [Desencadenamiento de la canalización de forma programada](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule).

1. En la barra de herramientas de la canalización, seleccione **Desencadenador** y seleccione **Nuevo/Editar**. 

    ![Desencadenador -> Nuevo/Editar](./media/how-to-schedule-azure-ssis-integration-runtime/trigger-new-menu.png)
2. En la ventana **Agregar desencadenadores**, seleccione **+ Nuevo**.

    ![Agregar desencadenadores: nuevo](./media/how-to-schedule-azure-ssis-integration-runtime/add-triggers-new.png)
3. En **Nuevo desencadenador**, lleve a cabo las siguientes acciones: 

    1. En **Nombre**, especifique un nombre para el desencadenador. En el ejemplo siguiente, **Ejecutar diariamente** es el nombre del desencadenador. 
    2. En **Tipo**, seleccione **Programación**. 
    3. En **Fecha de inicio**, seleccione una fecha y hora de inicio. 
    4. En **Periodicidad**, especifique la cadencia para el desencadenador. En el ejemplo siguiente, es una vez a diario. 
    5. En **Fin**, puede especificar la fecha y hora seleccionando la opción **El día**. 
    6. Seleccione **Activado**. El desencadenador se activa inmediatamente después de publicar la solución en Data Factory. 
    7. Seleccione **Next** (Siguiente).

        ![Desencadenador -> Nuevo/Editar](./media/how-to-schedule-azure-ssis-integration-runtime/new-trigger-window.png)
4. En la página **Trigger Run Parameters** (Parámetros de ejecución de desencadenador), revise la advertencia y seleccione **Finish** (Finalizar). 
5. Para publicar la solución en Data Factory, seleccione **Publicar todo** en el panel de la izquierda. 

    ![Publicar todo](./media/how-to-schedule-azure-ssis-integration-runtime/publish-all.png)

### <a name="monitor-the-pipeline-and-trigger-in-the-azure-portal"></a>Supervisión de la canalización y el desencadenador en Azure Portal

1. Para supervisar las ejecuciones del desencadenador y las ejecuciones de canalización, utilice la pestaña **Supervisar** de la izquierda. Para obtener instrucciones detalladas, consulte [Supervisar la canalización](quickstart-create-data-factory-portal.md#monitor-the-pipeline).

    ![Ejecuciones de la canalización](./media/how-to-schedule-azure-ssis-integration-runtime/pipeline-runs.png)
2. Para ver las ejecuciones de actividad asociadas con la de una canalización, seleccione el primer vínculo (**View Activity Runs** [Ver ejecuciones de actividad]) de la columna **Actions** (Acciones). Verá las ejecuciones de las tres actividades asociadas a cada actividad de la canalización (primera actividad Web, actividad Stored Procedure y la segunda actividad Web). Para volver a la vista de ejecuciones de canalización, seleccione el vínculo **Pipelines** (Canalizaciones) de la parte superior.

    ![Ejecuciones de actividad](./media/how-to-schedule-azure-ssis-integration-runtime/activity-runs.png)
3. También puede ver las ejecuciones de desencadenador al seleccionar **ejecuciones de desencadenador** en la lista desplegable que hay junto a las **ejecuciones de canalización** en la parte superior. 

    ![Ejecuciones de desencadenador](./media/how-to-schedule-azure-ssis-integration-runtime/trigger-runs.png)

### <a name="monitor-the-pipeline-and-trigger-with-powershell"></a>Supervisión de la canalización y el desencadenador con PowerShell

Use scripts como los ejemplos siguientes para supervisar la canalización y el desencadenador.

1. Obtenga el estado de la ejecución de una canalización.

  ```powershell
  Get-AzureRmDataFactoryV2PipelineRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -PipelineRunId $myPipelineRun
  ```

2. Obtenga información sobre el desencadenador.

  ```powershell
  Get-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name  "myTrigger"
  ```

3. Obtenga el estado de la ejecución de un desencadenador.

  ```powershell
  Get-AzureRmDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "myTrigger" -TriggerRunStartedAfter "2018-07-15" -TriggerRunStartedBefore "2018-07-16"
  ```

## <a name="next-steps"></a>Pasos siguientes
Vea la siguiente entrada de blog:
-   [Modernize and extend your ETL/ELT workflows with SSIS activities in ADF pipelines](https://blogs.msdn.microsoft.com/ssis/2018/05/23/modernize-and-extend-your-etlelt-workflows-with-ssis-activities-in-adf-pipelines/) (Modernización y ampliación de los flujos de trabajo ETL/ETL con actividades de SSIS en las canalizaciones de ADF)

Consulte los siguientes artículos de la documentación de SSIS: 

- [Deploy, run, and monitor an SSIS package on Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial) (Implementación, ejecución y supervisión de un paquete SSIS en Azure)   
- [Connect to SSIS catalog on Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database) (Conexión al catálogo de SSIS en Azure)
- [Schedule package execution on Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages) (Programación de la ejecución de un paquete en Azure)
- [Connect to on-premises data sources with Windows authentication](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) (Conexión a orígenes de datos locales con autenticación de Windows)
