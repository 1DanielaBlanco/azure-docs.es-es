---
title: Introducción al servicio de base de datos relacional Azure Database for PostgreSQL
description: Proporciona información general sobre el servicio de base de datos relacional de Azure Database for MySQL.
services: postgresql
author: kamathsun
ms.author: sukamat
manager: kfile
editor: jasonwhowell
ms.custom: mvc
ms.service: postgresql
ms.topic: overview
ms.date: 02/28/2018
ms.openlocfilehash: 766373f4b9390e576285db73d9e9e9942eb6624f
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/28/2018
---
# <a name="what-is-azure-database-for-postgresql"></a>¿Qué es Azure Database for PostgreSQL?

Azure Database for PostgreSQL es un servicio de base de datos relacional en la nube de Microsoft creado para los desarrolladores y basado en la versión de la comunidad de código abierto del motor de base de datos [PostgreSQL](https://www.postgresql.org/). Azure Database for PostgreSQL ofrece:

- Alta disponibilidad integrada sin costo adicional.
- Rendimiento predecible, con precios de pago por uso inclusivos.
- Escalado sobre la marcha en segundos.
- Protección de información confidencial en reposo y en movimiento.
- Copias de seguridad automáticas y restauración a un momento dado durante un máximo de 35 días.
- Seguridad y cumplimiento de nivel empresarial.

Todas estas funcionalidades apenas requieren tareas de administración y todas se proporcionan sin costo adicional. Estas funcionalidades permiten centrarse en el desarrollo rápido de aplicaciones y en reducir el plazo de acceso al mercado, en lugar de tener que dedicar tiempo y recursos a la administración tanto de máquinas virtuales como de infraestructura. Además, puede seguir desarrollando su aplicación con las herramientas y la plataforma de código abierto de su elección, y entregar con la velocidad y eficacia que exige su negocio sin tener que aprender nuevas habilidades. 

En este artículo se presentan los principales conceptos y características de Azure Database for PostgreSQL relacionados con el rendimiento, la escalabilidad y la manejabilidad. Consulte estas guías de inicio rápido para comenzar:

- [Create an Azure Database for PostgreSQL using Azure portal](quickstart-create-server-database-portal.md) (Creación de una base de datos Azure Database for PostgreSQL con Azure Portal)
- [Create an Azure Database for PostgreSQL using the Azure CLI](quickstart-create-server-database-azure-cli.md) (Creación de una base de datos Azure Database for PostgreSQL con la CLI de Azure)

Para ver ejemplos de la CLI de Azure, consulte:

- [Azure CLI samples for Azure Database for PostgreSQL](./sample-scripts-azure-cli.md) (Ejemplos de la CLI de Azure para Azure Database for PostgreSQL)

## <a name="adjust-performance-and-scale-within-seconds"></a>Ajustar el rendimiento y la escala en cuestión de segundos
En la versión preliminar, el servicio Azure Database for PostgreSQL ofrece varios planes de tarifa: Básico, Uso general y Memoria optimizada. Cada plan ofrece capacidades de recursos diferente para admitir sus cargas de trabajo de la base de datos. Puede compilar su primera aplicación en una base de datos pequeña por poco dinero al mes y, después, ajustar la escala para satisfacer las necesidades de la solución. La escalabilidad dinámica permite a la base de datos responder de manera transparente a los cambiantes requisitos de recursos. Solo paga por los recursos que necesite y cuando los necesite. Vea los [Planes de tarifa](concepts-pricing-tiers.md) para obtener más información.

## <a name="monitoring-and-alerting"></a>Supervisión y alertas
¿Cómo se decide cuándo escalar? Use las características de supervisión y alertas integradas de Azure. Con estas herramientas, puede evaluar rápidamente cómo le afectará el ajuste de la escala en función de sus necesidades de rendimiento o almacenamiento actuales o futuras. Vea las [Alertas](howto-alert-on-metric.md) para obtener más información.

## <a name="keep-your-app-and-business-running"></a>Mantenimiento de la aplicación y el negocio en funcionamiento
El Acuerdo de Nivel de Servicio (SLA) de Azure, con una disponibilidad del 99,99 % líder del sector y con la tecnología de una red global de centros de datos administrados por Microsoft, ayuda a mantener las aplicaciones en funcionamiento de forma ininterrumpida. Con cada servidor de Azure Database for PostgreSQL, se saca provecho de las ventajas de la seguridad integrada, la tolerancia a errores y la protección de datos, algo que de lo contrario tendría que adquirir o diseñar, compilar y administrar. Con Azure Database for PostgreSQL, cada plan de tarifa ofrece un conjunto completo de opciones y características de continuidad empresarial que puede usar para empezar a trabajar y no dejar de hacerlo. Puede usar la [restauración a un momento dado](howto-restore-server-portal.md) para devolver una base de datos a un estado anterior, con un plazo máximo de 35 días. Además, si el centro de datos que hospeda las bases de datos sufre una interrupción, puede restaurar las bases de datos a partir de copias con redundancia geográfica de copias de seguridad recientes.

## <a name="secure-your-data"></a>Protección de los datos
Los servicios de bases de datos de Azure tienen una tradición de seguridad de datos que conserva Azure Database for PostgreSQL con características que limitan el acceso, protegen los datos en reposo y en movimiento, y le ayudan a supervisar la actividad. Para más información sobre la seguridad de plataforma de Azure, vaya al [Centro de confianza de Azure](https://www.microsoft.com/en-us/trustcenter/security).

La base de datos de Azure para el servicio PostgreSQL usa el cifrado de almacenamiento de los datos en reposo. Los datos, incluidas las copias de seguridad, se cifran en el disco (a excepción de los archivos temporales creados por el motor durante la ejecución de consultas). El servicio usa el cifrado AES de 256 bits que se incluye en el cifrado de almacenamiento de Azure y las claves están administradas por el sistema. El cifrado de almacenamiento siempre está activado y no se puede deshabilitar.

De forma predeterminada, la base de datos de Azure para el servicio MySQL está configurada para requerir la [seguridad de la conexión SSL](./concepts-ssl-connection-security.md) para los datos en movimiento en la red. Aplicar conexiones SSL entre el servidor de base de datos y las aplicaciones cliente ayuda a proteger contra los ataques de tipo "man in the middle" mediante el cifrado del flujo de datos entre el servidor y la aplicación.  Tiene la opción de deshabilitar el requisito de SSL para conectar el servicio de base de datos en caso de que la aplicación cliente no admita la conectividad SSL.

## <a name="next-steps"></a>Pasos siguientes
- Consulte la [página de precios](https://azure.microsoft.com/pricing/details/postgresql/) para ver comparaciones de costos y calculadoras.
- Comience por [crear su primera base de datos de Azure Database for PostgreSQL](./quickstart-create-server-database-portal.md).
- Compile la primera aplicación de Python, PHP, Ruby, C\#, Java, Node.js: [Bibliotecas de conexiones](./concepts-connection-libraries.md)
