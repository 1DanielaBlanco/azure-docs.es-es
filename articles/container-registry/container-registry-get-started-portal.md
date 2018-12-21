---
title: 'Guía de inicio rápido: Creación de un registro privado de Docker en Azure: Azure Portal'
description: Aprenda rápidamente a crear un registro de contenedor privado de Docker en Azure Portal.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: quickstart
ms.date: 11/06/2018
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: 865c53fdda60f6a0384157ec68042b4b8b243a7a
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2018
ms.locfileid: "53255370"
---
# <a name="quickstart-create-a-private-container-registry-using-the-azure-portal"></a>Guía de inicio rápido: Creación de un registro de contenedor privado con Azure Portal

Azure Container Registry es un registro privado de Docker en Azure donde se pueden almacenar y administrar las imágenes de contenedor privado de Docker. En esta guía de inicio rápido, se crea un registro de contenedores con Azure Portal, se inserta una imagen del contenedor en el registro y, finalmente, se implementa el contenedor desde el registro en Azure Container Instances (ACI).

Para realizar este manual de inicio rápido, debe Docker instalado localmente. Docker proporciona paquetes que permiten configurar Docker fácilmente en cualquier sistema [Mac][docker-mac], [Windows][docker-windows] o [Linux][docker-linux].

## <a name="sign-in-to-azure"></a>Inicio de sesión en Azure

Inicie sesión en Azure Portal en https://portal.azure.com.

## <a name="create-a-container-registry"></a>Creación de un Registro de contenedor

Seleccione **Crear un recurso** > **Contenedores** > **Registro de contenedor**.

![Creación de un registro de contenedor en Azure Portal][qs-portal-01]

Escriba valores para **Nombre del Registro** y **Grupo de recursos**. El nombre del registro debe ser único dentro de Azure y contener entre 5 y 50 caracteres alfanuméricos. Para esta guía de inicio rápido, cree un grupo de recursos en la ubicación `West US` denominado `myResourceGroup`y en **SKU**, seleccione "Básico". Seleccione **Crear** para implementar la instancia de ACR.

![Creación de un registro de contenedor en Azure Portal][qs-portal-03]

En esta guía de inicio rápido, creamos un registro *Básico*. Azure Container Registry está disponible en varias SKU diferentes, que se describen brevemente en la tabla siguiente. Para obtener detalles ampliados de cada una, consulte [SKU de Azure Container Registry][container-registry-skus].

[!INCLUDE [container-registry-sku-matrix](../../includes/container-registry-sku-matrix.md)]

Cuando aparezca el mensaje **Implementación correcta**, seleccione el registro de contenedor en el portal y luego seleccione **Claves de acceso**.

![Creación de un registro de contenedor en Azure Portal][qs-portal-05]

En **Usuario administrador**, seleccione **Habilitar**. Anote los siguientes valores:

* Servidor de inicio de sesión
* Nombre de usuario
* contraseña

Estos valores los usará en los pasos siguientes al trabajar con el registro con la CLI de Docker.

![Creación de un registro de contenedor en Azure Portal][qs-portal-06]

## <a name="log-in-to-acr"></a>Inicio de sesión en ACR

Antes de insertar y extraer imágenes de contenedor, debe iniciar sesión en la instancia de ACR. Para ello, use el comando [docker login][docker-login]. Reemplace el *nombre de usuario*, la *contraseña* y el *servidor de inicio de sesión* por los anotados en el paso anterior.

```bash
docker login --username <username> --password <password> <login server>
```

El comando devolverá `Login Succeeded` una vez completado. También puede ver una advertencia de seguridad que recomienda el uso del parámetro `--password-stdin`. Cuando su uso esté fuera del ámbito de este artículo, recomendamos seguir este procedimiento recomendado. Para más información, vea la referencia del comando [docker login][docker-login].

## <a name="push-image-to-acr"></a>Inserción de una imagen en ACR

Para insertar una imagen en Azure Container Registry, primero debe tener una imagen. Si es necesario, ejecute el siguiente comando para extraer una imagen existente de Docker Hub.

```bash
docker pull microsoft/aci-helloworld
```

Antes de insertar la imagen en el registro, debe etiquetar la imagen con el nombre del servidor de inicio de sesión de ACR. Etiquete la imagen mediante el comando [docker tag][docker-tag]. Reemplace *servidor de inicio de sesión* por el nombre del servidor de inicio de sesión que anotó anteriormente. Agregue un *nombre de repositorio* como **`myrepo`** para colocar la imagen en un repositorio.

```bash
docker tag microsoft/aci-helloworld <login server>/<repository name>/aci-helloworld:v1
```

Por último, use el comando [docker push][docker-push] para insertar la imagen en la instancia de ACR. Sustituya *login server* por el nombre del servidor de inicio de sesión de su instancia de ACR y sustituya *repository name* por el nombre del repositorio que usó en el comando anterior.

```bash
docker push <login server>/<repository name>/aci-helloworld:v1
```

La salida de un comando `docker push` correcto es parecida a:

```
The push refers to repository [specificregistryname.azurecr.io/myrepo/aci-helloworld]
31ba1ebd9cf5: Pushed
cd07853fe8be: Pushed
73f25249687f: Pushed
d8fbd47558a8: Pushed
44ab46125c35: Pushed
5bef08742407: Pushed
v1: digest: sha256:565dba8ce20ca1a311c2d9485089d7ddc935dd50140510050345a1b0ea4ffa6e size: 1576
```

## <a name="list-container-images"></a>Lista de imágenes de contenedor

Para mostrar las imágenes en su instancia de ACR, vaya al registro en el portal, seleccione **Repositorios** y luego seleccione el repositorio que creó con `docker push`.

En este ejemplo, se selecciona el repositorio **aci-helloworld**, y se puede ver la imagen etiquetada con `v1` en **ETIQUETAS**.

![Creación de un registro de contenedor en Azure Portal][qs-portal-09]

## <a name="deploy-image-to-aci"></a>Implementación de la imagen en ACI

Para implementar en una instancia desde el registro, debe navegar hasta el repositorio (aci-helloworld) y, a continuación, hacer clic en el botón de puntos suspensivos situado junto a v1.

![Inicio de una instancia de Azure Container Instance desde el portal][qs-portal-10]

Aparecerá un menú contextual, seleccione **Ejecutar instancia**:

![Inicio del menú contextual de ACI][qs-portal-11]

Rellene el campo **Nombre del contenedor**, asegúrese de que está seleccionada la suscripción correcta y seleccione el **grupo de recursos** existente: "myResourceGroup". Para asegurarse de que se habilitan las opciones de "Dirección IP pública", seleccione **Sí** y haga clic en **Aceptar** para iniciar la instancia de Azure Container.

![Inicio de las opciones de implementación de ACI][qs-portal-12]

Cuando se inicia una implementación, en el panel del portal aparece un icono que indica el progreso de la implementación. Una vez finalizada la implementación, el icono se actualiza para mostrar el nuevo grupo de contenedores **mycontainer**.

![Estado de la implementación de ACI][qs-portal-13]

Seleccione el grupo de contenedores mycontainer para mostrar las propiedades del grupo de contenedores. Anote la **dirección IP** del grupo de contenedores, además del valor de **STATUS** del contenedor.

![Detalles del contenedor de ACI][qs-portal-14]

## <a name="view-the-application"></a>Visualización de la aplicación

Una vez que el contenedor pasa al estado **En ejecución**, vaya a la dirección IP que anotó en el paso anterior para mostrar la aplicación.

![Aplicación Hola mundo en el explorador][qs-portal-15]

## <a name="clean-up-resources"></a>Limpieza de recursos

Para limpiar los recursos, vaya al grupo de recursos **myResourceGroup** en el portal. Una vez cargado el grupo de recursos, haga clic en **Eliminar grupo de recursos** para eliminar el grupo de recursos, la instancia de Azure Container Registry y todas las instancias de Azure Container Instances.

![Eliminación de un grupo de recursos en Azure Portal][qs-portal-08]

## <a name="next-steps"></a>Pasos siguientes

En esta guía de inicio rápido, ha creado una instancia de Azure Container Registry con la CLI de Azure e iniciado dicha instancia con Azure Container Instances. Siga el tutorial de Azure Container Instances para información más detallada de ACI.

> [!div class="nextstepaction"]
> [Tutoriales de Azure Container Instances][container-instances-tutorial-prepare-app]

<!-- IMAGES -->
[qs-portal-01]: ./media/container-registry-get-started-portal/qs-portal-01.png
[qs-portal-02]: ./media/container-registry-get-started-portal/qs-portal-02.png
[qs-portal-03]: ./media/container-registry-get-started-portal/qs-portal-03.png
[qs-portal-04]: ./media/container-registry-get-started-portal/qs-portal-04.png
[qs-portal-05]: ./media/container-registry-get-started-portal/qs-portal-05.png
[qs-portal-06]: ./media/container-registry-get-started-portal/qs-portal-06.png
[qs-portal-07]: ./media/container-registry-get-started-portal/qs-portal-07.png
[qs-portal-08]: ./media/container-registry-get-started-portal/qs-portal-08.png
[qs-portal-09]: ./media/container-registry-get-started-portal/qs-portal-09.png
[qs-portal-10]: ./media/container-registry-get-started-portal/qs-portal-10.png
[qs-portal-11]: ./media/container-registry-get-started-portal/qs-portal-11.png
[qs-portal-12]: ./media/container-registry-get-started-portal/qs-portal-12.png
[qs-portal-13]: ./media/container-registry-get-started-portal/qs-portal-13.png
[qs-portal-14]: ./media/container-registry-get-started-portal/qs-portal-14.png
[qs-portal-15]: ./media/container-registry-get-started-portal/qs-portal-15.png

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[container-instances-tutorial-prepare-app]: ../container-instances/container-instances-tutorial-prepare-app.md
[container-registry-skus]: container-registry-skus.md
