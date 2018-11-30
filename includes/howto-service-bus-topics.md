---
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 11/25/2018
ms.author: spelluru
ms.openlocfilehash: ef6d5d22f70d5fff38f90b457a7c945ab59fc67c
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/26/2018
ms.locfileid: "52331468"
---
## <a name="what-are-service-bus-topics-and-subscriptions"></a>Qué son los temas y las suscripciones de Service Bus
Las suscripciones y los temas de Service Bus son compatibles con el modelo de comunicación de mensajería de *publicación/suscripción* . Cuando se usan temas y suscripciones, los componentes de una aplicación distribuida no se comunican directamente entre sí, sino que intercambian mensajes a través de un tema, que actúa como un intermediario.

![TopicConcepts](./media/howto-service-bus-topics/sb-topics-01.png)

A diferencia de las colas de Service Bus, en las que un solo destinatario procesa cada mensaje, los temas y las suscripciones proporcionan una forma de comunicación "de uno a varios" mediante un patrón de publicación/suscripción. Es posible registrar varias suscripciones en un tema. Cuando un mensaje se envía a un tema, pasa a estar disponible para cada suscripción para la administración o el procesamiento de manera independiente.

Una suscripción a un tema se asemeja a una cola virtual que recibe copias de los mensajes que se enviaron al tema. Si lo desea, puede registrar las reglas de filtro para un tema por suscripción. Las reglas de filtro le permiten filtrar o restringir qué suscripciones de tema reciben los mensajes de un tema.

Las suscripciones y los temas de Service Bus le permiten escalar y procesar un número elevado de mensajes entre muchos usuarios y aplicaciones.

## <a name="create-a-namespace"></a>Creación de un espacio de nombres
Para comenzar a usar suscripciones y temas de Service Bus en Azure, primero debe crear un espacio de nombres de servicio. Un espacio de nombres proporciona un contenedor con un ámbito para el desvío de recursos de Service Bus en la aplicación.

Para crear un espacio de nombres:

1. Inicie sesión en [Azure Portal][Azure portal].
2. En el panel de navegación izquierdo del portal, haga clic en **Crear un recurso**, luego, en **Enterprise Integration** y, finalmente, en **Service Bus**.
3. En el cuadro de diálogo **Crear un espacio de nombres**, especifique un nombre para el espacio de nombres. El sistema realiza la comprobación automáticamente para ver si el nombre está disponible.
4. Después de asegurarse de que el espacio de nombres está disponible, elija el plan de tarifas (Básico, Estándar o Premium).
5. En el campo **Suscripción** elija la suscripción de Azure en la que se va a crear el espacio de nombres.
6. En el campo **Grupo de recursos**, elija un grupo de recursos existente en el que resida el espacio de nombres o cree uno.      
7. En **Ubicación**, elija el país o región donde se debe hospedar el espacio de nombres.
   
    ![Crear un espacio de nombres][create-namespace]
8. Haga clic en el botón **Crear**. El sistema crea ahora el espacio de nombres del servicio y lo habilita. Es posible que tenga que esperar algunos minutos mientras el sistema realiza el aprovisionamiento de los recursos para la cuenta.

### <a name="obtain-the-credentials"></a>Obtención de las credenciales
1. En la lista de espacios de nombres, haga clic en el nombre del espacio de nombres recién creado.
2. En el panel **Espacio de nombres de Service Bus**, haga clic en **Directivas de acceso compartido**.
3. En el panel **Directivas de acceso compartido**, haga clic en **RootManageSharedAccessKey**.
   
    ![información de conexión][connection-info]
4. En el panel **Directiva: RootManageSharedAccessKey**, haga clic en el botón Copiar junto a **Cadena de conexión: clave principal**, para copiar la cadena de conexión en el portapapeles para su uso posterior.
   
    ![connection-string][connection-string]

[Azure portal]: https://portal.azure.com
[create-namespace]: ./media/howto-service-bus-topics/create-namespace.png
[connection-info]: ./media/howto-service-bus-topics/connection-info.png
[connection-string]: ./media/howto-service-bus-topics/connection-string.png


