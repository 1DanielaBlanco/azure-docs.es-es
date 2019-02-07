---
title: Reentrenamiento de un servicio web clásico
titleSuffix: Azure Machine Learning Studio
description: Obtenga información acerca de cómo volver a entrenar un modelo y actualizar el servicio web mediante programación para utilizar el modelo recién entrenado en Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: article
author: ericlicoding
ms.author: amlstudiodocs
ms.custom: seodec18, previous-ms.author=yahajiza, previous-author=YasinMSFT
ms.date: 04/19/2017
ms.openlocfilehash: 04dac55feaa6826e1b8b591df61e8ad413f24dad
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/31/2019
ms.locfileid: "55509412"
---
# <a name="retrain-a-classic-azure-machine-learning-studio-web-service"></a>Repetición del entrenamiento de un servicio web clásico en Azure Machine Learning Studio
El servicio web predictivo que implementó es el punto de conexión de puntuación predeterminado. Los puntos de conexión predeterminados se mantienen sincronizados con los experimentos de entrenamiento y puntuación originales y, por tanto, el modelo entrenado de un punto de conexión predeterminado no se puede reemplazar. Para reciclar el servicio web, debe agregar un nuevo punto de conexión al servicio web.

## <a name="prerequisites"></a>Requisitos previos
Debe haber configurado un experimento de entrenamiento y un experimento predictivo tal como se muestra en los [modelos de reciclaje de Machine Learning mediante programación](retrain-models-programmatically.md).

> [!IMPORTANT]
> El experimento predictivo debe implementarse como un servicio web Machine Learning clásico.
>
>

Para obtener más información sobre la implementación de servicios web, vea el artículo sobre [implementación de un servicio web Azure Machine Learning](publish-a-machine-learning-web-service.md).

## <a name="add-a-new-endpoint"></a>Adición de un punto de conexión nuevo
El servicio web predictivo que implementó contiene un punto de conexión de puntuación predeterminado que se mantiene sincronizado con la formación original y el modelo entrenado de experimentos de puntuación. Para actualizar el servicio web a un nuevo modelo entrenado, debe crear un nuevo punto de conexión para la puntuación.

Para crear un nuevo punto de conexión de puntuación, en el servicio web predictivo que se puede actualizar con el modelo entrenado:

> [!NOTE]
> Asegúrese de agregar el punto de conexión al servicio web predictivo y no al de entrenamiento. Si ha implementado correctamente un servicio web predictivo y otro de entrenamiento, debería ver dos servicios web independientes. El servicio web predictivo debe terminar con "[predictive exp.]".
>
>

Hay dos formas en que puede agregar un nuevo punto de conexión a un servicio web:

1. De manera programática
2. Uso del portal de servicios web de Microsoft Azure

### <a name="programmatically-add-an-endpoint"></a>Incorporación de un punto de conexión mediante programación
También puede agregar puntos de conexión de puntuación mediante el código de ejemplo proporcionado en este [repositorio de GitHub](https://github.com/hning86/azuremlps#add-amlwebserviceendpoint).

### <a name="use-the-microsoft-azure-web-services-portal-to-add-an-endpoint"></a>Uso del portal de servicios web de Microsoft Azure para agregar un punto de conexión
1. En Machine Learning Studio, en la columna de navegación izquierda, haga clic en Servicios web.
2. En la parte inferior del panel de servicios web, haga clic en **Manage endpoints preview**(Administrar versión preliminar de puntos de conexión).
3. Haga clic en **Agregar**.
4. Escriba un nombre y una descripción para el nuevo punto de conexión. Seleccione el nivel de registro y si los datos de ejemplo están habilitados. Para más información sobre los registros, vea [Habilitar el registro para los servicios web de Machine Learning](web-services-logging.md).

## <a name="update-the-added-endpoints-trained-model"></a>Actualización del modelo entrenado del punto de conexión agregado
Para completar el proceso de reentrenamiento, debe actualizar el modelo entrenado del nuevo punto de conexión que ha agregado.

Si agregó el punto de conexión mediante el código de ejemplo, esto incluye la ubicación de la dirección URL de ayuda identificada por el valor *HelpLocationURL* de la salida.

Para recuperar la dirección URL de la ruta de acceso:

1. Copie y pegue la URL en el explorador.
2. Haga clic en el vínculo Actualizar recurso.
3. Copie la dirección URL de POST de la solicitud PATCH. Por ejemplo: 
   
     PATCH URL: https://management.azureml.net/workspaces/00bf70534500b34rebfa1843d6/webservices/af3er32ad393852f9b30ac9a35b/endpoints/newendpoint2

Ahora puede usar el modelo entrenado para actualizar el punto de conexión de puntuación que creó anteriormente.

El código de ejemplo siguiente muestra cómo utilizar *BaseLocation*, *RelativeLocation*, *SasBlobToken* y el valor de PATCH URL para actualizar el punto de conexión.

    private async Task OverwriteModel()
    {
        var resourceLocations = new
        {
            Resources = new[]
            {
                new
                {
                    Name = "Census Model [trained model]",
                    Location = new AzureBlobDataReference()
                    {
                        BaseLocation = "https://esintussouthsus.blob.core.windows.net/",
                        RelativeLocation = "your endpoint relative location", //from the output, for example: “experimentoutput/8946abfd-79d6-4438-89a9-3e5d109183/8946abfd-79d6-4438-89a9-3e5d109183.ilearner”
                        SasBlobToken = "your endpoint SAS blob token" //from the output, for example: “?sv=2013-08-15&sr=c&sig=37lTTfngRwxCcf94%3D&st=2015-01-30T22%3A53%3A06Z&se=2015-01-31T22%3A58%3A06Z&sp=rl”
                    }
                }
            }
        };

        using (var client = new HttpClient())
        {
            client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", apiKey);

            using (var request = new HttpRequestMessage(new HttpMethod("PATCH"), endpointUrl))
            {
                request.Content = new StringContent(JsonConvert.SerializeObject(resourceLocations), System.Text.Encoding.UTF8, "application/json");
                HttpResponseMessage response = await client.SendAsync(request);

                if (!response.IsSuccessStatusCode)
                {
                    await WriteFailedResponse(response);
                }

                // Do what you want with a successful response here.
            }
        }
    }

Se puede obtener *apiKey* y *endpointUrl* para la llamada desde el panel del punto de conexión.

El valor del parámetro *Name* de *Resources* debe coincidir con el nombre del recurso del modelo entrenado guardado en el experimento predictivo. Para obtener el nombre del recurso:

1. Inicie sesión en el [Azure Portal](https://portal.azure.com).
2. Haga clic en **Machine Learning**en el menú izquierdo.
3. En Nombre, haga clic en el área de trabajo y, a continuación, haga clic en **Servicios web**.
4. En Nombre, haga clic en **Census Model [predictive exp.]** (Modelo de censo [exp. predictivo]).
5. Haga clic en el nuevo punto de conexión que ha agregado.
6. En el panel del punto de conexión, haga clic en **Actualizar recurso**.
7. En la página de documentación de la API de actualización de recurso para el servicio web, encontrará el **nombre del recurso** en **Updatable Resources** (Recursos actualizables).

Si el token SAS expira antes de que termine de actualizar el punto de conexión, deberá realizar una operación GET con el identificador de trabajo para obtener un nuevo token.

Si el código se ha ejecutado correctamente, el nuevo punto de conexión debería comenzar a utilizar el modelo reentrenado en aproximadamente 30 segundos.

## <a name="summary"></a>Resumen
Mediante el uso de las API de reentrenamiento, puede actualizar el modelo entrenado de un servicio web predictivo habilitando escenarios como:

* Reentrenamiento de modelos periódicos con nuevos datos.
* Distribución de un modelo entre los clientes con el fin de permitirles reentrenar el modelo mediante sus propios datos.

## <a name="next-steps"></a>Pasos siguientes
[Solución de problemas del reentrenamiento de un servicio web clásico de Azure Machine Learning](troubleshooting-retraining-models.md)

