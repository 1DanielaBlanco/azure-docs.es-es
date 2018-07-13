---
title: Conexión de equipos mediante OMS Gateway | Microsoft Docs
description: Conecte los dispositivos y los equipos supervisados por Operations Manager con OMS Gateway para enviar datos al servicio de Azure Automation y Log Analytics cuando no tienen acceso a Internet.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ae9a1623-d2ba-41d3-bd97-36e65d3ca119
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: magoedte
ms.component: na
ms.openlocfilehash: a13057769bad7fcad5f95e49102adac234ebcdb4
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/06/2018
ms.locfileid: "37868946"
---
# <a name="connect-computers-without-internet-access-using-the-oms-gateway"></a>Conexión de equipos sin acceso a Internet mediante OMS Gateway
Este documento describe cómo configurar la comunicación con Azure Automation y Log Analytics usando OMS Gateway cuando equipos que están directamente conectados o están supervisados por Operations Manager no tienen acceso a Internet.  OMS Gateway, que es un proxy de reenvío de HTTP que admite la tunelización de HTTP con el comando HTTP CONNECT, puede recopilar datos y enviarlos a Azure Automation y Log Analytics en su nombre.  

OMS Gateway admite:

* Hybrid Runbook Workers de Azure Automation  
* Equipos Windows con Microsoft Monitoring Agent directamente conectado a un área de trabajo de Log Analytics
* Equipos Linux con el Agente de OMS para Linux directamente conectado a un área de trabajo de Log Analytics  
* System Center Operations Manager 2012 SP1 con UR7, Operations Manager 2012 R2 con UR3, Operations Manager 2016 y el grupo de administración de Operations Manager versión 1801 integrado en Log Analytics.  

Si las directivas de seguridad de TI no permiten a los equipos de la red conectarse a Internet, como dispositivos de punto de venta (PDV) o servidores que admiten servicios de TI, pero necesita conectarlos a Azure Automation o Log Analytics para administrarlos y supervisarlos, se pueden configurar para que se comuniquen directamente con OMS Gateway a fin de recibir la configuración y reenviar los datos en su nombre.  Si estos equipos están configurados con el agente de OMS para conectarse directamente a un área de trabajo de Log Analytics, todos los equipos se comunicarán en su lugar con OMS Gateway.  La puerta de enlace transfiere los datos de los agentes al servicio directamente, sin analizar ninguno de los datos en tránsito.

Cuando un grupo de administración de Operations Manager se integra en Log Analytics, se pueden configurar los servidores de administración para que se conecten a OMS Gateway a fin de recibir información de configuración y enviar los datos recopilados según la solución que haya habilitado.  Los agentes de Operations Manager envían algunos datos, como las alertas, la evaluación de la configuración, el espacio de las instancias y los datos de capacidad de Operations Manager, al servidor de administración. Otros datos de gran volumen, como registros, rendimiento y eventos de seguridad de IIS se envían directamente a OMS Gateway.  Si dispone de uno o más servidores de puerta de enlace de Operations Manager implementados en una red perimetral u otra red aislada para supervisar sistemas no confiables, no puede comunicarse con OMS Gateway.  Los servidores de puerta de enlace de Operations Manager solo pueden informar a un servidor de administración.  Cuando se configura un grupo de administración de Operations Manager para comunicarse con OMS Gateway, la información de configuración de proxy se distribuye automáticamente a todos los equipos administrados por agente que están configurados para recopilar datos para Log Analytics, incluso si la configuración está vacía.    

Para proporcionar alta disponibilidad a grupos directamente conectados o grupos de Operations Management que se comunican con Log Analytics mediante la puerta de enlace, puede utilizar el equilibrio de carga de red para redirigir y distribuir el tráfico entre varios servidores de puerta de enlace.  Si un servidor de puerta de enlace deja de funcionar, el tráfico se redirige a otro nodo disponible.  

El agente de OMS se requiere en el equipo que ejecuta la puerta de enlace de OMS para que identifique el servicio y los puntos que necesita para establecer la comunicación y supervisar la puerta de enlace de OMS para analizar su rendimiento o sus datos de eventos.

Cada agente debe tener conectividad de red a su puerta de enlace para que los agentes puedan transferir automáticamente datos a la puerta de enlace y recibirlos de esta. No se recomienda instalar la puerta de enlace en un controlador de dominio.

El siguiente diagrama muestra el flujo de datos desde los agentes directos a Azure Automation y Log Analytics mediante el servidor de puerta de enlace.  Los agentes tienen que hacer que su configuración de proxy coincida con el mismo puerto que tiene configurado OMS Gateway para comunicarse con el servicio.  

![diagrama de comunicación de agente directo con los servicios](./media/log-analytics-oms-gateway/oms-omsgateway-agentdirectconnect.png)

El siguiente diagrama muestra el flujo de datos de un grupo de administración de Operations Manager a Log Analytics.   

![Diagrama de comunicación de Operations Manager con Log Analytics](./media/log-analytics-oms-gateway/log-analytics-agent-opsmgrconnect.png)

## <a name="prerequisites"></a>requisitos previos

Al designar un equipo para que ejecute OMS Gateway, debe tener lo siguiente:

* Windows 10, Windows 8.1, Windows 7
* Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2, Windows Server 2008
* .Net Framework 4.5
* Procesador de 4 núcleos y 8 GB de memoria como mínimo 
* Agente de OMS para Windows 

### <a name="language-availability"></a>Disponibilidad de idiomas

OMS Gateway está disponible en los idiomas siguientes:

- Chino (simplificado)
- Chino (tradicional)
- Checo
- Neerlandés
- English
- Francés
- Alemán
- Húngaro
- Italiano
- Japonés
- Coreano
- Polaco
- Portugués (Brasil)
- Portugués (Portugal)
- Ruso
- Español (internacional)

### <a name="supported-encryption-protocols"></a>Protocolos de cifrado admitidos
La puerta de enlace de OMS solo admite Seguridad de la capa de transporte (TLS) 1.0, 1.1 y 1.2.  No se admite Capa de sockets seguros (SSL).

### <a name="supported-number-of-agent-connections"></a>Número admitido de conexiones del agente
En la siguiente tabla se resalta el número admitido de agentes que se comunican con un servidor de puerta de enlace.  Esta compatibilidad se basa en los agentes que cargan aproximadamente 200 KB de datos cada 6 segundos. El volumen de datos por agente de prueba es de unos 2,7 GB por día.

|Puerta de enlace |Número aproximado de agentes admitidos|  
|--------|----------------------------------|  
|- CPU: Intel XEON CPU E5-2660 v3 @ 2,6 GHz 2 núcleos<br> - Memoria: 4 GB<br> - Ancho de banda de red: 1 Gbps| 600|  
|- CPU: Intel XEON CPU E5-2660 v3 @ 2,6 GHz 4 núcleos<br> - Memoria: 8 GB<br> - Ancho de banda de red: 1 Gbps| 1000|  

## <a name="download-the-oms-gateway"></a>Descarga de OMS Gateway

Hay dos maneras de obtener la versión más reciente del archivo de instalación de OMS Gateway.

1. Descargarla del [Centro de descarga de Microsoft](https://www.microsoft.com/download/details.aspx?id=54443).

2. Descargarla desde Azure Portal.  Después de iniciar sesión en Azure Portal:  

   1. Vaya a la lista de servicios y, luego, seleccione **Log Analytics**.  
   2. Seleccione un área de trabajo.
   3. En su hoja de área de trabajo, en **General**, haga clic en **Inicio rápido**.
   4. En **Elegir un origen de datos para conectarse al área de trabajo**, haga clic en **Equipos**.
   5. En la hoja **Agente directo**, haga clic en **Descargar OMS Gateway**.<br><br> ![descargar OMS Gateway](./media/log-analytics-oms-gateway/download-gateway.png)

o 

   1. En la hoja de área de trabajo en **Configuración**, haga clic en **Configuración avanzada**.
   2. Vaya a **Orígenes conectados** > **Servidores de Windows** y haga clic en **Descargar puerta de enlace de OMS**.

## <a name="install-the-oms-gateway"></a>Instalación de OMS Gateway

Para instalar una puerta de enlace, realice los pasos siguientes.  Si tiene instalada una versión anterior, anteriormente denominada *Reenviador de Log Analytics*, se actualizará a esta versión.  

1. En la carpeta de destino, haga doble clic en **OMS Gateway.msi**.
2. En la página **principal**, haga clic en **Siguiente**.<br><br> ![Asistente para la instalación de la puerta de enlace](./media/log-analytics-oms-gateway/gateway-wizard01.png)<br> 
3. En la página del **contrato de licencia**, seleccione **I accept the terms in the License Agreement** (Acepto los términos del Contrato de licencia) para aceptar el CLUF y, después, haga clic en **Siguiente**.
4. En la página de **dirección del proxy y puerto**:
   1. Escriba el número de puerto TCP que se va a usar para la puerta de enlace. El programa de instalación configura una regla de entrada con este número de puerto en el firewall de Windows.  El valor predeterminado es 8080.
      El intervalo válido del número de puerto es 1 - 65535. Si el valor especificado no se encuentra en este intervalo, aparece un mensaje de error.
   2. Opcionalmente, si el servidor en que está instalada la puerta de enlace necesita comunicarse a través de un proxy, escriba la dirección del proxy al que debe conectarse la puerta de enlace. Por ejemplo, `http://myorgname.corp.contoso.com:80`.  Si está en blanco, la puerta de enlace intentará conectarse a Internet directamente.  Si el servidor proxy requiere autenticación, especifique el nombre de usuario y la contraseña.<br><br> ![Configuración del proxy en el Asistente de la puerta de enlace](./media/log-analytics-oms-gateway/gateway-wizard02.png)<br>   
   3. Haga clic en **Next**.
5. Si Microsoft Update no está habilitado, aparecerá la página de Microsoft Update, donde puede elegir habilitarlo. Elija la opción que desee y haga clic en **Siguiente**. De lo contrario, continúe con el paso siguiente.
6. En la página de la **carpeta de destino**, deje la carpeta predeterminada C:\Archivos de programa\OMS Gateway o escriba la ubicación en la que desea instalar la puerta de enlace y haga clic en **Siguiente**.
7. En la página **Preparado para instalar...**, haga clic en **Instalar**. Puede aparecer un Control de cuentas de usuario que solicite permiso para realizar la instalación. Si aparece, haga clic en **Sí**.
8. Una vez completada la instalación, haga clic en **Finalizar**. Para comprobar que el servicio se está ejecutando, abra el complemento services.msc y compruebe que **OMS Gateway** aparece en la lista de servicios y que su estado sea **En ejecución**.<br><br> ![Servicios: OMS Gateway](./media/log-analytics-oms-gateway/gateway-service.png)  

## <a name="configure-network-load-balancing"></a>Configuración de equilibrio de carga de red 
Puede configurar la puerta de enlace para lograr alta disponibilidad con equilibrio de carga de red (NLB) mediante Equilibrio de carga de red de Microsoft (NLB) o equilibradores de carga basados en hardware.  El equilibrador de carga administra el tráfico mediante el redireccionamiento de las conexiones solicitadas desde los agentes de OMS o los servidores de administración de Operations Manager a través de sus nodos. Si un servidor de puerta de enlace deja de funcionar, el tráfico se redirige a otros nodos.

Para más información sobre cómo diseñar e implementar un clúster de equilibrio de carga de red de Windows Server 2016, consulte [Equilibrio de carga de red](https://technet.microsoft.com/windows-server-docs/networking/technologies/network-load-balancing).  Los pasos siguientes describen cómo configurar un clúster de equilibrio de carga de red de Microsoft.  

1. Inicie sesión en el servidor Windows que sea miembro del clúster NLB con una cuenta administrativa.  
2. Abra el Administrador de equilibrio de carga de red en el Administrador del servidor, haga clic en **Herramientas** y, luego, en **Administrador de equilibrio de carga de red**.
3. Para conectar un servidor de OMS Gateway con Microsoft Monitoring Agent instalado, haga clic con el botón derecho en la dirección IP del clúster y, luego, haga clic en **Agregar host al clúster**.<br><br> ![Administrador de equilibrio de carga de red – Agregar host al clúster](./media/log-analytics-oms-gateway/nlb02.png)<br> 
4. Escriba la dirección IP del servidor de la puerta de enlace al que desea conectarse.<br><br> ![Administrador de equilibrio de carga de red – Agregar host al clúster: Conectar](./media/log-analytics-oms-gateway/nlb03.png) 
    
## <a name="configure-oms-agent-and-operations-manager-management-group"></a>Configuración de agentes de OMS y de un grupo de administración de Operations Manager
La sección siguiente incluye pasos sobre cómo configurar directamente los agentes de OMS conectados, un grupo de administración de Operations Manager o Hybrid Runbook Workers de Azure Automation con la puerta de enlace de OMS para comunicarse con Azure Automation o Log Analytics.  

### <a name="configure-standalone-oms-agent"></a>Configuración de agentes de OMS de forma independiente
Para entender los requisitos y los pasos sobre cómo instalar el agente de OMS en equipos Windows conectados directamente a Log Analytics, consulte [Conexión de equipos Windows al servicio Log Analytics](log-analytics-windows-agents.md) o para equipos Linux, consulte [Conexión de equipos Linux a Log Analytics](log-analytics-quick-collect-linux-computer.md). En lugar de especificar un servidor proxy al configurar al agente, sustituya ese valor por la dirección IP del servidor de puerta de enlace de OMS y su número de puerto.  Si ha implementado varios servidores de puerta de enlace detrás de un equilibrador de carga de red, la configuración de proxy del agente de OMS es la dirección IP virtual de NLB.  

Para información relacionada con el Hybrid Runbook Worker de Automation, consulte la [Implementación de Hybrid Runbook Worker](../automation/automation-hybrid-runbook-worker.md).

### <a name="configure-operations-manager---all-agents-use-the-same-proxy-server"></a>Configuración de Operations Manager: todos los agentes usan el mismo servidor proxy
Configure Operations Manager para agregar la puerta de enlace del servidor.  La configuración de proxy de Operations Manager se aplica automáticamente a todos los agentes que informan a Operations Manager, incluso si la configuración está vacía.  

Si desea utilizar la puerta de enlace para que sea compatible con Operations Manager, debe tener:

* Microsoft Monitoring Agent (versión del agente: **8.0.10900.0** o posterior) instalado en el servidor de la puerta de enlace y configurado para las áreas de trabajo de Log Analytics con las que desea comunicarse.
* La puerta de enlace debe tener conectividad a Internet o estar conectada a un servidor proxy que la tenga.

> [!NOTE]
> Si no especifica ningún valor para la puerta de enlace, se insertan valores en blanco para todos los agentes.
> 

Si se trata de la primera vez que se registra el grupo de administración de Operations Manager con un área de trabajo de Log Analytics, la opción para especificar la configuración de proxy para el grupo de administración no está disponible en la consola del operador.  El grupo de administración tiene que estar registrado correctamente con el servicio para que esta opción esté disponible.  Tiene que actualizar la configuración de proxy del sistema con Netsh en el sistema desde el que se esté ejecutando la consola del operador para configurar la integración y todos los servidores de administración del grupo de administración.  

1. Abra un símbolo del sistema con privilegios elevados.
   a. Vaya a **Inicio** y escriba **cmd**.
   b. Haga clic con el botón derecho en el **símbolo del sistema** y seleccione Ejecutar como administrador**.
2. Escriba el siguiente comando y presione **Entrar**:

    `netsh winhttp set proxy <proxy>:<port>`

Después de completar la integración con Log Analytics, puede quitar el cambio mediante la ejecución de `netsh winhttp reset proxy` y luego usar la opción **Configurar servidor proxy** en la consola del operador para especificar el servidor de OMS Gateway. 

1. Abra la consola de Operations Manager y en **Operations Management Suite**, haga clic en **Conexión** y en **Configurar servidor proxy**.<br><br> ![Operations Manager: Configurar servidor proxy](./media/log-analytics-oms-gateway/scom01.png)<br> 
2. Seleccione **Use un servidor proxy para obtener acceso al servicio Operations Management Suite** y, a continuación, escriba la dirección IP del servidor de OMS Gateway o la dirección IP virtual de NLB. Asegúrese de que su prefijo es `http://`.<br><br> ![Operations Manager: dirección de servidor proxy](./media/log-analytics-oms-gateway/scom02.png)<br> 
3. Haga clic en **Finalizar** El grupo de administración de Operations Manager está ahora configurado para comunicarse mediante el servidor de puerta de enlace con el servicio Log Analytics.

### <a name="configure-operations-manager---specific-agents-use-proxy-server"></a>Configuración de Operations Manager: determinados agentes usan el servidor proxy
En entornos grandes o complejos, es posible que desee tener solo determinados servidores (o grupos) que utilicen el servidor de OMS Gateway.  En estos servidores, no se puede actualizar el agente Operations Manager directamente, ya que el valor global para el grupo de administración sobrescribe este valor.  En su lugar, debe invalidar la regla utilizada para insertar estos valores.  

> [!NOTE] 
> Esta misma técnica de configuración se puede utilizar para permitir el uso de varios servidores de OMS Gateway en su entorno.  Por ejemplo, puede requerir que se especifiquen determinados servidores de OMS Gateway en cada región.
>  

1. Abra la consola de Operations Manager y seleccione el área de trabajo **Creación**.  
2. En el área de trabajo Creación, seleccione **Reglas** y haga clic en el botón **Ámbito** de la barra de herramientas de Operations Manager. Si este botón no está disponible, compruebe que tiene un objeto, y no una carpeta, seleccionado en el panel Supervisión. El cuadro de diálogo **Objetos de módulo de administración de ámbito** muestra una lista de clases, grupos u objetos de destino comunes. 
3. Escriba **Servicio de mantenimiento** en el campo **Buscar** y selecciónelo en la lista.  Haga clic en **OK**.  
4. Busque la regla **Advisor Proxy Setting Rule** (Regla de configuración de proxy de Advisor) y en la barra de herramientas de la consola de operaciones, haga clic en **Invalidaciones**, seleccione **Override the Rule\For a specific object of class: Health Service** (Invalidar la regla\Para un objeto de clase específico: servicio de mantenimiento) y, luego, un objeto específico de la lista.  Si lo desea, puede crear un grupo personalizado que incluya el objeto de servicio de mantenimiento de los servidores a los que desea aplicar esta invalidación y, después, aplicar la invalidación para ese grupo.
5. En el cuadro de diálogo **Propiedades de invalidación**, haga clic para poner una marca de verificación en la columna **Invalidar** junto al parámetro **WebProxyAddress**.  En el campo **Valor de invalidación**, escriba la dirección URL del servidor de OMS Gateway y asegúrese de que el prefijo sea `http://`.  

    >[!NOTE]
    > No es necesario habilitar la regla, porque ya se administra automáticamente con una invalidación incluida en el módulo de administración de reemplazo de referencia segura de Microsoft System Center Advisor dirigido al grupo de servidores de supervisión de Microsoft System Center Advisor.
    >   

6. Seleccione un módulo de administración de la lista **Seleccionar módulo de administración de destino** o cree un nuevo módulo de administración no sellado haciendo clic en **Nuevo**. 
7. Cuando haya completado los cambios, haga clic en **Aceptar**. 

### <a name="configure-for-automation-hybrid-workers"></a>Configuración de Hybrid Workers de Automation
Si tiene Hybrid Runbook Workers de Automation en su entorno, los pasos siguientes proporcionan soluciones temporales manuales para configurar la puerta de enlace y que esta sea compatible con él.

En los pasos siguientes, es preciso saber en qué región de Azure reside la cuenta de Automation. Para buscar la ubicación:

1. Inicie sesión en el [Azure Portal](https://portal.azure.com/).
2. Seleccione el servicio Azure Automation.
3. Seleccione la cuenta de Azure Automation apropiada.
4. Vea su región en **Ubicación**.<br><br> ![Azure Portal – Ubicación de la cuenta de Automation](./media/log-analytics-oms-gateway/location.png)  

Utilice las siguientes tablas para identificar la dirección URL de cada ubicación:

**Direcciones URL del servicio de datos del tiempo de ejecución del trabajo**

| **ubicación** | **URL** |
| --- | --- |
| Centro-Norte de EE. UU |ncus-jobruntimedata-prod-su1.azure-automation.net |
| Europa occidental |we-jobruntimedata-prod-su1.azure-automation.net |
| Centro-Sur de EE. UU |scus-jobruntimedata-prod-su1.azure-automation.net |
| Este de EE. UU. 2 |eus2-jobruntimedata-prod-su1.azure-automation.net |
| Canadá central |cc-jobruntimedata-prod-su1.azure-automation.net |
| Europa del Norte |ne-jobruntimedata-prod-su1.azure-automation.net |
| Sudeste de Asia |sea-jobruntimedata-prod-su1.azure-automation.net |
| India Central |cid-jobruntimedata-prod-su1.azure-automation.net |
| Japón |jpe-jobruntimedata-prod-su1.azure-automation.net |
| Australia |ase-jobruntimedata-prod-su1.azure-automation.net |

**Direcciones URL de servicio Agente**

| **ubicación** | **URL** |
| --- | --- |
| Centro-Norte de EE. UU |ncus-agentservice-prod-1.azure-automation.net |
| Europa occidental |we-agentservice-prod-1.azure-automation.net |
| Centro-Sur de EE. UU |scus-agentservice-prod-1.azure-automation.net |
| Este de EE. UU. 2 |eus2-agentservice-prod-1.azure-automation.net |
| Canadá central |cc-agentservice-prod-1.azure-automation.net |
| Europa del Norte |ne-agentservice-prod-1.azure-automation.net |
| Sudeste de Asia |sea-agentservice-prod-1.azure-automation.net |
| India Central |cid-agentservice-prod-1.azure-automation.net |
| Japón |jpe-agentservice-prod-1.azure-automation.net |
| Australia |ase-agentservice-prod-1.azure-automation.net |

Si el equipo se registra automáticamente como Hybrid Runbook Worker para que las revisiones se apliquen mediante la solución de administración de actualizaciones, siga estos pasos:

1. Agregue las direcciones URL del servicio de datos en tiempo de ejecución del trabajo a la lista de hosts permitidos de OMS Gateway. Por ejemplo: `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
2. Reinicie el servicio de OMS Gateway mediante el siguiente cmdlet de PowerShell: `Restart-Service OMSGatewayService`

Si el equipo está incorporado en Azure Automation mediante el cmdlet de registro de Hybrid Runbook Worker, siga estos pasos:

1. Agregue la dirección URL del registro del servicio Agente en la lista de hosts permitidos de OMS Gateway. Por ejemplo: `Add-OMSGatewayAllowedHost ncus-agentservice-prod-1.azure-automation.net`
2. Agregue las direcciones URL del servicio de datos en tiempo de ejecución del trabajo a la lista de hosts permitidos de OMS Gateway. Por ejemplo: `Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
3. Reinicie el servicio de OMS Gateway.
    `Restart-Service OMSGatewayService`

## <a name="useful-powershell-cmdlets"></a>Cmdlets de PowerShell útiles
Los cmdlets pueden ayudarle a completar las tareas necesarias para actualizar la configuración de la OMS Gateway. Antes de usarlos, asegúrese de:

1. Instalar OMS Gateway (MSI).
2. Abra una ventana de la consola de PowerShell.
3. Para importar el módulo, escriba este comando: `Import-Module OMSGateway`
4. Si no se ha producido ningún error en el paso anterior, el módulo se ha importado correctamente y se pueden usar los cmdlets. Escriba `Get-Module OMSGateway`
5. Después de realizar los cambios mediante los cmdlets, asegúrese de reiniciar el servicio Gateway.

Un error en el paso 3 indica que el módulo no se ha importado. El error puede producirse si PowerShell no puede encontrar el módulo. Es posible encontrarlo en la ruta de instalación de la puerta de enlace: *C:\Archivos de programa\Microsoft OMS Gateway\PowerShell\OmsGateway*.

| **Cmdlet** | **Parámetros** | **Descripción** | **Ejemplo** |
| --- | --- | --- | --- |  
| `Get-OMSGatewayConfig` |Clave |Obtiene la configuración del servicio |`Get-OMSGatewayConfig` |  
| `Set-OMSGatewayConfig` |Clave (se requiere) <br> Valor |Cambia la configuración del servicio |`Set-OMSGatewayConfig -Name ListenPort -Value 8080` |  
| `Get-OMSGatewayRelayProxy` | |Obtiene la dirección del proxy de retransmisión (ascendente) |`Get-OMSGatewayRelayProxy` |  
| `Set-OMSGatewayRelayProxy` |Dirección<br> Nombre de usuario<br> Contraseña |Establece la dirección (y credencial) del proxy de retransmisión (ascendente) |1. Establezca un proxy de retransmisión y la credencial:<br> `Set-OMSGatewayRelayProxy`<br>`-Address http://www.myproxy.com:8080`<br>`-Username user1 -Password 123` <br><br> 2. Establezca un proxy de retransmisión que no necesite autenticación: `Set-OMSGatewayRelayProxy`<br> `-Address http://www.myproxy.com:8080` <br><br> 3. Desactive la configuración del proxy de retransmisión:<br> `Set-OMSGatewayRelayProxy` <br> `-Address ""` |  
| `Get-OMSGatewayAllowedHost` | |Obtiene el host permitido actualmente (solo el host permitido configurado localmente, no se incluyen los hosts permitidos que se descargan automáticamente) |`Get-OMSGatewayAllowedHost` | 
| `Add-OMSGatewayAllowedHost` |Host (obligatorio) |Agrega el host a la lista de permitidos |`Add-OMSGatewayAllowedHost -Host www.test.com` |  
| `Remove-OMSGatewayAllowedHost` |Host (obligatorio) |Quita el host de la lista de permitidos |`Remove-OMSGatewayAllowedHost`<br> `-Host www.test.com` |  
| `Add-OMSGatewayAllowedClientCertificate` |Asunto (obligatorio) |Agrega el asunto del certificado de cliente a la lista de permitidos |`Add-OMSGatewayAllowed`<br>`ClientCertificate` <br> `-Subject mycert` |  
| `Remove-OMSGatewayAllowedClientCertificate` |Asunto (obligatorio) |Quita el asunto del certificado de cliente de la lista de permitidos |`Remove-OMSGatewayAllowed` <br> `ClientCertificate` <br> `-Subject mycert` |  
| `Get-OMSGatewayAllowedClientCertificate` | |Obtiene los asuntos de certificado de cliente permitidos actualmente (solo los asuntos permitidos configurados localmente, no se incluyen los asuntos permitidos que se descargan automáticamente) |`Get-`<br>`OMSGatewayAllowed`<br>`ClientCertificate` |  

## <a name="troubleshooting"></a>solución de problemas
Para recopilar eventos registrados por la puerta de enlace, debe tener también instalado el agente de OMS.<br><br> ![Visor de eventos: registro de OMS Gateway](./media/log-analytics-oms-gateway/event-viewer.png)

**Identificadores de evento y descripciones de OMS Gateway**

La siguiente tabla muestra los identificadores de evento y las descripciones de los eventos del registro de la OMS Gateway.

| **Id** | **Descripción** |
| --- | --- |
| 400 |Cualquier error de la aplicación que no tiene un identificador específico |
| 401 |Configuración errónea. Por ejemplo: listenPort = "text", en lugar de un entero |
| 402 |Excepción al analizar los mensajes de protocolo de enlace TLS |
| 403 |Error de red. Por ejemplo: no se puede conectar al servidor de destino |
| 100 |Información general |
| 101 |El servicio se ha iniciado |
| 102 |El servicio se ha detenido |
| 103 |Se ha recibido un comando HTTP CONNECT del cliente |
| 104 |No es un comando HTTP CONNECT |
| 105 |El servidor de destino no está en la lista de permitidos o el puerto de destino no es un puerto seguro (443) <br> <br> Asegúrese de que el agente MMA del servidor de puerta de enlace y los agentes que se comunican con la puerta de enlace están conectados a la misma área de trabajo de Log Analytics. |
| 105 |ERROR TcpConnection – Invalid Client certificate: CN=Gateway <br><br> Asegúrese de que: <br>    <br> - Utiliza una puerta de enlace con el número de versión 1.0.395.0, o uno superior. <br> - Tanto el agente MMA del servidor de puerta de enlace como los agentes que se comunican con la puerta de enlace están conectados a la misma área de trabajo de Log Analytics. |
| 106 |La puerta de enlace de OMS solo admite TLS 1.0, TLS 1.1 y 1.2.  No admite SSL. Para las versiones de protocolo TLS/SSL no admitidas, la puerta de enlace de OMS genera el identificador de evento 106.|
| 107 |Se ha comprobado la sesión de TLS |

**Contadores de rendimiento que se recopilan**

La siguiente tabla muestra los contadores de rendimiento disponibles para OMS Gateway. Los contadores se pueden agregar mediante el Monitor de rendimiento.

| **Name** | **Descripción** |
| --- | --- |
| OMS Gateway/Conexión de cliente activa |Número de conexiones de red (TCP) de cliente activas |
| OMS Gateway/Recuento de errores |Número de errores |
| OMS Gateway/Cliente conectado |Número máximo de clientes conectados |
| OMS Gateway/Recuento de rechazos |Número de rechazos debido a un error de validación de TLS |

![Contadores de rendimiento de OMS Gateway](./media/log-analytics-oms-gateway/counters.png)

## <a name="get-assistance"></a>Obtención de ayuda
Cuando haya iniciado sesión en Azure Portal, puede crear una solicitud de asistencia con OMS Gateway o cualquier otro servicio o característica de un servicio de Azure.
Para solicitar asistencia, haga clic en el signo de interrogación en la esquina superior derecha del portal y, después, en **Nueva solicitud de soporte técnico**. A continuación, complete el formulario de solicitud de soporte técnico nuevo.

![Nueva solicitud de soporte](./media/log-analytics-oms-gateway/support.png)

## <a name="next-steps"></a>Pasos siguientes
[Agregue orígenes de datos](log-analytics-data-sources.md) para recopilar datos de los orígenes conectados y almacénelos en el área de trabajo de Log Analytics.
