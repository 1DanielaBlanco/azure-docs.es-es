---
title: Introducción a la seguridad de Azure SQL Database | Microsoft Docs
description: Aprenda sobre la seguridad de SQL Database y SQL Server, incluidas las diferencias entre la nube y SQL Server local.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aliceku
ms.author: aliceku
ms.reviewer: vanto, carlrab, emlisa
manager: craigg
ms.date: 10/22/2018
ms.openlocfilehash: 2a1d8a993f805c6ef814088af6fc4e3051519e37
ms.sourcegitcommit: 1d3353b95e0de04d4aec2d0d6f84ec45deaaf6ae
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2018
ms.locfileid: "50248802"
---
# <a name="an-overview-of-azure-sql-database-security-capabilities"></a>Información general sobre las funcionalidades de seguridad de Azure SQL Database

Este artículo describe los fundamentos de la protección de la capa de datos de una aplicación que use Azure SQL Database. Con este artículo, podrá empezar a trabajar con recursos para proteger datos, controlar el acceso y realizar una supervisión proactiva.

Para obtener una descripción completa de las características de seguridad disponibles en todas las versiones de SQL, consulte [Seguridad y protección (motor de base de datos)](https://msdn.microsoft.com/library/bb510589). Asimismo, tiene información adicional disponible en las [Notas del producto técnicas acerca de la seguridad y Azure SQL Database](https://download.microsoft.com/download/A/C/3/AC305059-2B3F-4B08-9952-34CDCA8115A9/Security_and_Azure_SQL_Database_White_paper.pdf) (en PDF).

## <a name="protect-data"></a>Protección de datos

### <a name="encryption"></a>Cifrado

Para proteger los datos, SQL Database cifra los datos en movimiento a través del protocolo de [Seguridad de la capa de transporte](https://support.microsoft.com/kb/3135244), los datos en reposo a través del [Cifrado de datos transparente](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) y los datos en uso a través de [Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx).

> [!IMPORTANT]
> Azure SQL Database aplica cifrado (SSL/TLS) en todo momento para todas las conexiones, lo que garantiza que todos los datos se cifran "en tránsito" entre la base de datos y el cliente. Esto ocurre independientemente del valor de **Encrypt** o **TrustServerCertificate** en la cadena de conexión.
>
> En la cadena de conexión de la aplicación, asegúrese de especificar una conexión cifrada y *no* confiar en el certificado de servidor (para el controlador ADO.NET, este es **Encrypt=True** y  **TrustServerCertificate=False**). Esto contribuye a evitar que la aplicación sea objeto de un ataque de tipo "Man in the middle", al obligar a la aplicación a comprobar el servidor e imponer el cifrado. Si obtiene la cadena de conexión en Azure Portal, tendrá la configuración correcta.
>
> Para información sobre TLS y la conectividad, consulte [Consideraciones de TLS](sql-database-connect-query.md#tls-considerations-for-sql-database-connectivity).

Si desea conocer otras formas de cifrar datos, considere:

- [Cifrado de nivel de celda](https://msdn.microsoft.com/library/ms179331.aspx) para cifrar columnas concretas, o incluso celdas de datos, con distintas claves de cifrado.
-  Si necesita un módulo de seguridad de hardware o tecnología BYOK (Bring Your Own Key) para el cifrado transparente de datos, considere la posibilidad de utilizar [Cifrado de datos transparente de Azure SQL: compatibilidad con Bring Your Own Key](transparent-data-encryption-byok-azure-sql.md).

### <a name="data-discovery--classification"></a>Clasificación y detección de datos

La opción Clasificación y detección de datos (actualmente en su versión preliminar) proporciona funcionalidades avanzadas integradas en Azure SQL Database para detectar, clasificar, etiquetar y proteger la información confidencial de las bases de datos. Las funciones de detección y clasificación de la información confidencial más importante (empresarial, financiera, médica, personal, etc.) desempeñan un rol fundamental en el modo en que se protege la información de su organización. Puede servir como infraestructura para:

- Varios escenarios de seguridad, como la supervisión (auditoría) y las alertas relacionadas con accesos anómalos a información confidencial.
- Controlar el acceso y mejorar la seguridad de las bases de datos que contienen información altamente confidencial.
- Ayudar a cumplir los requisitos de cumplimiento de normas y los estándares relacionados con la privacidad de datos.

Para obtener más información, consulte [Azure SQL Database Data Discovery and Classification](sql-database-data-discovery-and-classification.md) (Detección y clasificación de datos de Azure SQL Database).

## <a name="control-access"></a>Control de acceso

SQL Database protege los datos mediante la limitación del acceso a la base de datos a través de reglas de firewall, de mecanismos de autenticación que requieren que los usuarios prueben su identidad y de la autorización a través de pertenencias y permisos basados en roles, así como la seguridad de nivel de fila y el enmascaramiento dinámico de datos. Para obtener información acerca del uso de las características de control de acceso en SQL Database, consulte [Control de acceso](sql-database-control-access.md).

> [!IMPORTANT]
> La administración de bases de datos y servidores lógicos en Azure se controlan mediante las asignaciones de roles de su cuenta de usuario del portal. Para obtener más información sobre este artículo, consulte [Introducción al control de acceso basado en roles en Azure Portal](../role-based-access-control/overview.md).

### <a name="firewall-and-firewall-rules"></a>Firewall y reglas de firewall

Para ayudarle a proteger los datos, los firewalls impiden todo acceso al servidor de bases de datos hasta que especifique los equipos que tienen permiso mediante [reglas de firewall](sql-database-firewall-configure.md). Asimismo, otorgan acceso a las bases de datos según la dirección IP de origen de cada solicitud.

### <a name="authentication"></a>Autenticación

La autenticación de SQL Database indica cómo probar su identidad al conectarse a la base de datos. SQL Database admite dos tipos de autenticación:

- **Autenticación de SQL**

  Este método de autenticación utiliza un nombre de usuario y una contraseña. Al crear el servidor lógico de la base de datos, especificó un inicio de sesión de "administrador de servidor" con un nombre de usuario y una contraseña. Con estas credenciales, puede autenticarse en cualquier base de datos en ese servidor como propietario de la base de datos, o "dbo".

- **Autenticación con Azure Active Directory**

  Este método de autenticación usa las identidades administradas por Azure Active Directory y es compatible con dominios administrados e integrados. Use la autenticación de Active Directory (seguridad integrada) [siempre que sea posible](https://msdn.microsoft.com/library/ms144284.aspx). Si desea usar la autenticación de Azure Active Directory, debe crear otro administrador de servidor llamado "administrador de Azure AD" con permiso para administrar usuarios y grupos de Azure AD. Este administrador también puede realizar todas las operaciones de un administrador de servidor normal. Consulte el tutorial [Conectar a la SQL Database mediante la autenticación de Azure Active Directory](sql-database-aad-authentication.md) , para obtener información acerca de cómo crear un administrador de Azure AD y así habilitar la autenticación de Azure Active Directory.

### <a name="authorization"></a>Autorización

Autorización indica las acciones que pueden realizar los usuarios en Azure SQL Database, algo que controlan los permisos de nivel de objeto y las pertenencias a roles de bases de datos de la cuenta de usuario. Como procedimiento recomendado, debe conceder a los usuarios los privilegios mínimos necesarios. La cuenta de administrador de servidor con la que se está conectando forma parte de db_owner, que tiene autoridad para realizar cualquier acción en la base de datos. Guarde esta cuenta para implementar las actualizaciones de los esquemas y otras operaciones de administración. Utilice la cuenta "ApplicationUser" con permisos más limitados para conectarse desde la aplicación a la base de datos con los privilegios mínimos que necesita la aplicación.

### <a name="row-level-security"></a>Seguridad de nivel de fila

La seguridad de nivel de fila permite a los clientes controlar el acceso a las filas de una tabla de base de datos en función de las características del usuario que ejecuta una consulta (como, por ejemplo, la pertenencia a un grupo o el contexto de ejecución). Para más información, consulte [Seguridad de nivel de fila](https://docs.microsoft.com/sql/relational-databases/security/row-level-security).

### <a name="dynamic-data-masking"></a>Enmascaramiento de datos dinámicos

El enmascaramiento dinámico de datos de SQL Database limita la exposición de información confidencial mediante su enmascaramiento a los usuarios sin privilegios. La característica Enmascaramiento dinámico de datos detecta automáticamente información potencialmente confidencial en Azure SQL Database y proporciona recomendaciones accionables para enmascarar estos campos, con un impacto mínimo en el nivel de aplicación. Su funcionamiento consiste en ocultar los datos confidenciales del conjunto de resultados de una consulta en los campos designados de la base de datos, mientras que los datos de la base de datos no cambian. Para más información, consulte [Enmascaramiento dinámico de datos de SQL Database](sql-database-dynamic-data-masking-get-started.md).

## <a name="proactive-monitoring"></a>Supervisión proactiva

SQL Database protege los datos proporcionando funcionalidades de auditoría y detección de amenazas.

### <a name="auditing"></a>Auditoría

SQL Database Auditing realiza un seguimiento de las actividades de la base de datos y ayuda a mantener el cumplimiento normativo, para lo que graba eventos de base de datos en un registro de auditoría de su cuenta de Azure Storage. La auditoría permite conocer las actividades en curso de la base de datos, así como analizar e investigar la actividad histórica para identificar posibles amenazas o supuestas infracciones de seguridad y abusos. Para obtener más información, consulte [Introducción a la auditoría de SQL Database](sql-database-auditing.md).  

### <a name="threat-detection"></a>Detección de amenazas

Detección de amenazas complementa la auditoría, ya que proporciona una capa adicional de inteligencia de seguridad integrada en el servicio de Azure SQL Database que detecta intentos inusuales y potencialmente dañinos para obtener acceso a las bases de datos o vulnerarlas. Recibirá alertas de actividades sospechosas, vulnerabilidades potenciales y ataques por inyección de código SQL, así como patrones anómalos de acceso a bases de datos. Las alertas de Detección de amenazas pueden verse en [Azure Security Center](https://azure.microsoft.com/services/security-center/) y proporcionar detalles de actividad sospechosa y la acción recomendada sobre cómo investigar y mitigar la amenaza. Detección de amenazas cuesta 15 USD/servidor/mes. Pruébelo gratis durante los primeros 60 días. Para más información, consulte [Introducción a Detección de amenazas de SQL Database](sql-database-threat-detection.md).

## <a name="compliance"></a>Cumplimiento normativo

Además de las anteriores características y funcionalidades que pueden ayudar a la aplicación a cumplir distintos requisitos de seguridad, Azure SQL Database también participa en las auditorías regulares y ha obtenido la certificación de una serie de normas de cumplimiento. Para más información, consulte el [Centro de confianza de Microsoft Azure](https://azure.microsoft.com/support/trust-center/), donde podrá encontrar la lista más reciente de [certificaciones de cumplimiento de SQL Database](https://www.microsoft.com/trustcenter/compliance/complianceofferings).

## <a name="security-management"></a>Administración de la seguridad

SQL Database le ayuda a administrar la seguridad de los datos con análisis de bases de datos y un panel de seguridad centralizado mediante la [evaluación de vulnerabilidades de SQL](sql-vulnerability-assessment.md).

**[Evaluación de vulnerabilidades de SQL](sql-vulnerability-assessment.md)**: es una herramienta fácil de configurar que se integra en Azure SQL Database y permite detectar, realizar un seguimiento y corregir posibles vulnerabilidades de la base de datos. La evaluación ejecuta un análisis de vulnerabilidades en la base de datos y genera un informe que proporciona visibilidad sobre el estado de seguridad, incluidos los pasos útiles para resolver problemas de seguridad y mejorar la seguridad de la base de datos. Es posible personalizar un informe de evaluación para su entorno estableciendo una línea de base aceptable para las configuraciones de permisos, configuraciones de características y configuración de base de datos. Esto puede ayudarle a:

- Satisfacer los requisitos de cumplimiento que requieren los informes de análisis de base de datos.
- Cumplir los estándares de privacidad de datos.
- Supervisar un entorno de base de datos dinámico en el que es complicado realizar un seguimiento de los cambios.

Para más información, vea [Evaluación de vulnerabilidad de SQL](sql-vulnerability-assessment.md).

## <a name="next-steps"></a>Pasos siguientes

- Para obtener información acerca del uso de las características de control de acceso en SQL Database, consulte [Control de acceso](sql-database-control-access.md).
- Para más información sobre la auditoría de bases de datos, consulte [SQL Database auditing](sql-database-auditing.md) (Auditoría de SQL Database).
- Para más información sobre la detección de amenaza, consulte [SQL Database threat detection](sql-database-threat-detection.md) (Detección de amenazas de SQL Database).
