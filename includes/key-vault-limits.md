---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: ed0c387f9785336fbf18b3fd3c0cd9a7b09df633
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/21/2018
ms.locfileid: "52279934"
---
Transacciones clave (N.º máximo de transacciones permitidas en 10 segundos, por almacén y región<sup>1</sup>):

|Tipo de clave|Clave HSM<br>Clave CREAR|Clave HSM<br>Las restantes transacciones|Clave de software<br>Clave CREAR|Clave de software<br>Las restantes transacciones|
|:---|---:|---:|---:|---:|
|2048 bits de RSA|5|1000|10|2000|
|3072 bits de RSA|5|250|10|500|
|4096 bits de RSA|5|125|10|250|
|ECC P-256|5|1000|10|2000|
|ECC P-384|5|1000|10|2000|
|ECC P-521|5|1000|10|2000|
|ECC SECP256K1|5|1000|10|2000|
|

Secretos, claves de cuentas de almacenamiento administradas y transacciones de almacén:
| Tipo de transacciones | N.º máximo de transacciones permitidas en 10 segundos, por almacén y región<sup>1</sup> |
| --- | --- |
| Todas las transacciones |2000 |
|

Vea la [Guía de las limitaciones de Azure Key Vault](../articles/key-vault/key-vault-ovw-throttling.md) para obtener información sobre cómo controlar la limitación cuando se superan estos límites.

<sup>1</sup> El límite global para la suscripción para todos los tipos de transacciones es 5 veces el límite del almacén de claves. Por ejemplo, en "HSM - otras transacciones" el límite es de 5000 transacciones en 10 segundos por suscripción.
