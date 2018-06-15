---
title: Controles de aplicación adaptables en Azure Security Center | Microsoft Docs
description: Este documento le ayuda a usar el control de aplicación adaptable en Azure Security Center para incluir en la lista de permitidos las aplicaciones que se ejecutan en máquinas virtuales de Azure.
services: security-center
documentationcenter: na
author: terrylan
manager: mbaldwin
editor: ''
ms.assetid: 9268b8dd-a327-4e36-918e-0c0b711e99d2
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/15/2018
ms.author: yurid
ms.openlocfilehash: 841dbbf97a7fa25aa001636ba92cc2a966be4908
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/03/2018
ms.locfileid: "32777210"
---
# <a name="adaptive-application-controls-in-azure-security-center-preview"></a>Controles de aplicación adaptables en Azure Security Center (versión preliminar)
Obtenga información acerca de cómo configurar el control de aplicación en Azure Security Center con este tutorial.

## <a name="what-are-adaptive-application-controls-in-security-center"></a>¿Qué son los controles de aplicación adaptables en Security Center?
Los controles de aplicación adaptables ayudan a controlar qué aplicaciones se pueden ejecutar en las máquinas virtuales que se encuentran en Azure y, entre otras ventajas, le ayuda a proteger las máquinas virtuales frente a malware. Security Center usa el aprendizaje automático para analizar las aplicaciones que se ejecutan en la máquina virtual y le ayuda a aplicar reglas de inclusión en listas de permitidos con esta inteligencia. Esta funcionalidad simplifica enormemente el proceso de configuración y mantenimiento de listas de aplicaciones permitidas, lo que le permite:

- Bloquear o alertar sobre intentos de ejecución de aplicaciones malintencionadas, incluidas aquellas que podrían ser omitidas por las soluciones antimalware
- Cumplir con la directiva de seguridad de la organización que dicta el uso de software con licencia únicamente.
- Evitar el uso de software no deseado en el entorno.
- Evitar la ejecución de aplicaciones anteriores y no compatibles.
- Impedir herramientas de software específicas no permitidas en la organización.
- Permiten que TI controle el acceso a información confidencial a través del uso de aplicaciones.

## <a name="how-to-enable-adaptive-application-controls"></a>¿Cómo habilitar los controles de aplicación adaptables?
Los controles de aplicación adaptables ayudan a definir un conjunto de aplicaciones que se pueden ejecutar en los grupos de recursos configurados. Esta característica solo está disponible para máquinas de Windows (todas las versiones, clásica o Azure Resource Manager). Los pasos siguientes se pueden usar para configurar la inclusión de aplicaciones en las listas de permitidos de Security Center:

1. Abra el panel **Security Center**.
2. En el panel de la izquierda, seleccione **Controles de aplicaciones adaptables** que se encuentra en **Protección en la nube avanzada**.

    ![Defensa](./media/security-center-adaptive-application/security-center-adaptive-application-fig1-new.png)

Se muestra la página **Controles de aplicaciones adaptables**.

![controls](./media/security-center-adaptive-application/security-center-adaptive-application-fig2.png)

La sección **Grupos de máquinas virtuales** contiene tres pestañas:

* **Configured** (Configurado): lista de grupos que contienen las máquinas virtuales que se configuraron con el control de aplicaciones.
* **Recommended** (Recomendado): lista de grupos para los que se recomienda el control de aplicación. Security Center utiliza el aprendizaje automático para identificar las máquinas virtuales que son buenas candidatas para el control de aplicación en función de si las máquinas virtuales ejecutan de forma coherente las mismas aplicaciones.
* **No recommendation** (Sin recomendación): lista de grupos que contienen máquinas virtuales sin ninguna recomendación de control de aplicación. Por ejemplo, máquinas virtuales en las que las aplicaciones cambian constantemente y no han alcanzado un estado estable.

> [!NOTE]
> Security Center utiliza un algoritmo de agrupación en clústeres propietario para crear grupos de máquinas virtuales, lo que garantiza que máquinas virtuales similares obtienen la directiva de control de aplicaciones recomendada óptima.
>
>

### <a name="configure-a-new-application-control-policy"></a>Configuración de una nueva directiva de control de aplicación
1. Haga clic en la pestaña **Recommended** (Recomendado) para obtener una lista de los grupos con recomendaciones de control de aplicación:

  ![Recomendado](./media/security-center-adaptive-application/security-center-adaptive-application-fig3.png)

  La lista incluye:

  - **NOMBRE**: nombre de la suscripción y grupo
  - **MÁQUINAS VIRTUALES**: número de máquinas virtuales del grupo
  - **ESTADO**: el estado de las recomendaciones, que en la mayoría de los casos será abierto
  - **GRAVEDAD**: el nivel de gravedad de las recomendaciones

2. Seleccione un grupo de recursos para abrir la opción **Crear reglas de control de aplicaciones**.

  ![Reglas de control de aplicación](./media/security-center-adaptive-application/security-center-adaptive-application-fig4.png)

3. En **Seleccionar máquinas virtuales**, revise la lista de máquinas virtuales recomendadas y desactive cualquiera a la que no desee aplicar el control de aplicación. A continuación, se ven dos listas:

  - **Recommended applications** (Aplicaciones recomendadas): una lista de las aplicaciones frecuentes en las máquinas virtuales dentro de este grupo y, por tanto, recomendadas para las reglas de control de aplicación por Security Center.
  - **More applications** (Más aplicaciones): una lista de las aplicaciones que son menos frecuentes en las máquinas virtuales dentro de este grupo o que se conocen como Infringibles (encontrará más información al respecto a continuación) y se recomienda para su revisión antes de aplicar las reglas.

4. Revise las aplicaciones en cada una de las listas y desactive las que no desee aplicar. Cada lista incluye:

  - **NOMBRE**: la información del certificado de una aplicación o su ruta de acceso completa
  - **TIPOS DE ARCHIVO**: el tipo de archivo de la aplicación. Puede ser EXE, Script o MSI.
  - **INFRINGIBLE**: un icono de advertencia indica si un atacante podría usar las aplicaciones para evitar las listas de aplicaciones permitidas. Se recomienda revisar estas aplicaciones antes de su aprobación.
  - **USUARIOS**: los usuarios a los que se recomienda admitir para ejecutar una aplicación

5. Cuando haya terminado de realizar las selecciones, elija **Crear**.

De forma predeterminada, Security Center siempre habilita el control de aplicación en modo *Auditoría*. Después de comprobar que la lista de permitidos no tenga efectos negativos en la carga de trabajo, puede cambiar al modo *Forzar*.

Security Center necesita al menos dos semanas de datos para crear una base de referencia y rellenar las recomendaciones únicas para cada grupo de máquinas virtuales. Los clientes nuevos del nivel estándar de Security Center experimentarán un comportamiento en el que, al principio, sus grupos de máquinas virtuales aparecen en la pestaña *Ninguna recomendación*.

> [!NOTE]
> Como procedimiento recomendado de seguridad, Security Center siempre intenta crear una regla de publicador para las aplicaciones que deben estar en la lista de permitidos y solo si una aplicación no tiene información del publicador (también conocido como no firmada), se creará una regla de ruta de acceso para la ruta de acceso completa del archivo EXE específico.
>   

### <a name="editing-and-monitoring-a-group-configured-with-application-control"></a>Edición y supervisión de un grupo configurado con control de aplicación

1. Para editar y supervisar un grupo configurado con control de aplicaciones, vuelva a la página **Controles de aplicaciones adaptables** y seleccione **CONFIGURED** (CONFIGURADO) en **Grupos de máquinas virtuales**:

  ![Grupos](./media/security-center-adaptive-application/security-center-adaptive-application-fig5.png)

  La lista incluye:

  - **NOMBRE**: nombre de la suscripción y grupo
  - **MÁQUINAS VIRTUALES**: número de máquinas virtuales del grupo
  - **MODO**: el modo Auditoría registrará los intentos de ejecución de aplicaciones que no están en la lista blanca; el modo Forzar no permitirá que se ejecuten aplicaciones que no estén en la lista blanca
  - **PROBLEMAS**: cualquier infracción actual

2. Seleccione un grupo para realizar cambios en la página **Editar directiva de control de aplicaciones**.

  ![Protección](./media/security-center-adaptive-application/security-center-adaptive-application-fig6.png)

3. En **Modo de protección**, puede seleccionar entre las siguientes opciones:

  - **Auditoría**: en este modo, la solución de control de aplicación no exige el cumplimiento de las reglas y solo audita la actividad en las máquinas virtuales protegidas. Esto se recomienda para escenarios donde desea primero observar el comportamiento general antes de bloquear la ejecución de una aplicación en la máquina virtual de destino.
  - **Forzar**: en este modo, la solución de control de aplicación exige el cumplimiento de las reglas y se asegura de bloquear las aplicaciones cuya ejecución no está permitida.

  Como se mencionó anteriormente, una nueva directiva de control de aplicación siempre se configura en modo *Auditoría* de forma predeterminada. En **Extensión de directiva** puede agregar sus propias rutas de acceso de aplicación que desea incluir en la lista de permitidos. Una vez que agrega estas rutas de acceso, Security Center crea las reglas adecuadas para estas aplicaciones, además de las que ya están configuradas.

  En la sección **Problemas recientes** se enumeran las infracciones actuales.

  ![Issues](./media/security-center-adaptive-application/security-center-adaptive-application-fig7.png)

  Esta lista incluye:
  - **PROBLEMAS**: cualquier infracción que se haya registrado, incluidas las siguientes:

      - **ViolationsBlocked**: cuando la solución está activada en modo Exigir y se intenta ejecutar una aplicación que no está en la lista de permitidos.
      - **ViolationsAudited**: cuando la solución está activada en modo Auditoría y se ejecuta una aplicación que no está en la lista de permitidos.

 - **NÚMERO DE MÁQUINAS VIRTUALES**: número de máquinas virtuales con este tipo de problema.

  Si hace clic en cada línea, se le redirige a la página [Registro de actividad de Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs), donde puede ver información acerca de todas las máquinas virtuales con este tipo de infracción. Si hace clic en los tres puntos al final de cada línea, puede eliminar esa entrada en concreto. En la sección **Máquinas virtuales configuradas** se enumeran las máquinas virtuales a la que se aplican las reglas.

  ![Máquinas virtuales configuradas](./media/security-center-adaptive-application/security-center-adaptive-application-fig8.png)

  En **Reglas de inclusión en lista aprobada de publicadores**, la lista contiene:

  - **REGLA**: aplicaciones para las que se ha creado una regla de publicador en función de la información del certificado que se ha encontrado para cada aplicación
  - **TIPO DE ARCHIVO**: los tipos de archivo cubiertos por una regla de publicador específica. Puede ser uno de los siguientes: EXE, Script o MSI.
  - **USUARIOS**: número de usuarios con permiso para ejecutar cada aplicación

  Consulte [Descripción de las reglas de publicador en Applocker](https://docs.microsoft.com/windows/device-security/applocker/understanding-the-publisher-rule-condition-in-applocker) para más información.

  ![Reglas de inclusión en listas de permitidos](./media/security-center-adaptive-application/security-center-adaptive-application-fig9.png)

  Si hace clic en los puntos suspensivos al final de cada línea, puede eliminar la regla concreta o editar los usuarios permitidos.

  La sección **Reglas de inclusión en lista blanca de rutas** enumera la ruta de toda la aplicación (incluyendo el tipo de archivo específico) para las aplicaciones que no están firmadas con un certificado digital, pero que siguen estado en las reglas de inclusión en lista blanca.

  > [!NOTE]
  > De forma predeterminada, como procedimiento recomendado de seguridad, Security Center siempre intenta crear una regla de publicador para los archivos EXE que deben estar en la lista de permitidos, y solo si un archivo EXE no tiene información del publicador (también conocido como no firmado), se creará una regla de ruta de acceso para la ruta de acceso completa del archivo EXE específico.

  ![Reglas de inclusión en listas de rutas de acceso permitidas](./media/security-center-adaptive-application/security-center-adaptive-application-fig10.png)

  La lista contiene:
  - **NOMBRE**: la ruta de acceso completa del archivo ejecutable
  - **TIPO DE ARCHIVO**: los tipos de archivo cubiertos por una regla de ruta de acceso específica. Puede ser uno de los siguientes: EXE, Script o MSI.
  - **USUARIOS**: número de usuarios con permiso para ejecutar cada aplicación

  Si hace clic en los puntos suspensivos al final de cada línea, puede eliminar la regla concreta o editar los usuarios permitidos.

4. Después de realizar cambios en la página **Controles de aplicación adaptables**, haga clic en el botón **Guardar**. Si decide no aplicar los cambios, haga clic en **Descartar**.

### <a name="not-recommended-list"></a>Lista Ninguna recomendación

Security Center solo recomienda las listas de aplicaciones permitidas para las máquinas virtuales que ejecutan un conjunto estable de aplicaciones. No se crean recomendaciones si las aplicaciones en las máquinas virtuales asociadas cambian continuamente.

![Recomendación](./media/security-center-adaptive-application/security-center-adaptive-application-fig11.png)

La lista contiene:
- **NOMBRE**: nombre de la suscripción y grupo
- **MÁQUINAS VIRTUALES**: número de máquinas virtuales del grupo

## <a name="next-steps"></a>Pasos siguientes
En este documento ha aprendido a usar el control de aplicación adaptable en Azure Security Center para incluir en la lista de permitidos las aplicaciones que se ejecutan en máquinas virtuales de Azure. Para obtener más información sobre el Centro de seguridad de Azure, consulte los siguientes recursos:

* [Administración y respuesta a las alertas de seguridad en Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Aprenda a administrar las alertas y responder a incidentes de seguridad en Security Center.
* [Supervisión del estado de seguridad en Azure Security Center](security-center-monitoring.md). Aprenda a supervisar el estado de los recursos de Azure.
* [Comprensión de las alertas de seguridad en Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Obtenga información acerca de los distintos tipos de alertas de seguridad.
* [Guía de solución de problemas de Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Obtenga información acerca de cómo solucionar problemas comunes en Security Center.
* [Preguntas más frecuentes sobre Azure Security Center](security-center-faq.md). Preguntas más frecuentes acerca del uso del servicio.
* [Blog de seguridad de Azure](http://blogs.msdn.com/b/azuresecurity/). Encuentre artículos de blog sobre el cumplimiento y la seguridad de Azure.
