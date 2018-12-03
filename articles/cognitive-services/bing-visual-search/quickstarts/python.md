---
title: 'Inicio rápido: Creación de una consulta de búsqueda visual, Python - Bing Visual Search'
titleSuffix: Azure Cognitive Services
description: Aquí le mostramos cómo cargar una imagen a Bing Visual Search API y obtener información detallada sobre la misma.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-visual-search
ms.topic: quickstart
ms.date: 5/16/2018
ms.author: scottwhi
ms.openlocfilehash: 3a0d92e42eed097e244118a60ec0a4223c9cedf5
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/28/2018
ms.locfileid: "52440948"
---
# <a name="quickstart-your-first-bing-visual-search-query-in-python"></a>Inicio rápido: Su primera consulta de Bing Visual Search en Python

Bing Visual Search API devuelve información sobre una imagen proporcionada. Puede proporcionar la imagen utilizando la dirección URL de la misma, un token de conclusiones, o simplemente cargando una imagen. Para obtener más información acerca de estas opciones, consulte [What is Bing Visual Search API?](../overview.md) (¿Qué es Bing Visual Search API?). En este artículo se muestra la forma de cargar una imagen. Cargar una imagen puede serle de utilidad en aquellos momentos en los que use un dispositivo móvil para, por ejemplo, hacer una foto de un lugar interesante y así poder obtener información acerca de él. Asimismo, estos detalles pueden incluir curiosidades sobre ese lugar de interés. 

Si carga una imagen local, a continuación verá los datos del formulario que debe incluir en el cuerpo del elemento POST. Los datos del formulario deben incluir el encabezado Content-Disposition. Asimismo, el parámetro `name` se debe establecer en "imagen" y el parámetro `filename` se puede establecer en cualquier cadena. El contenido del formulario es el binario de la imagen. Recuerde que el tamaño de imagen máximo que puede cargar es de 1 MB. 

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

En este artículo se incluye una aplicación simple de consola que envía una solicitud de Bing Visual Search API y muestra los resultados de búsqueda de JSON. Si bien esta aplicación está escrita en Python, la API es un servicio web RESTful compatible con cualquier lenguaje de programación que pueda realizar solicitudes HTTP y analizar JSON. 

## <a name="prerequisites"></a>Requisitos previos

Necesita [Python 3](https://www.python.org/) para ejecutar este código.

Para esta guía de inicio rápido, sera preciso iniciar una suscripción en el nivel de precios S9 como se muestra en [Precios de Cognitive Services: Bing Search API](https://azure.microsoft.com/en-us/pricing/details/cognitive-services/search-api/). 

Para iniciar una suscripción en Azure Portal:
1. Escriba "BingSearchV7" en el cuadro de texto de la parte superior de Azure Portal que dice `Search resources, services, and docs`.  
2. En Marketplace en la lista desplegable, seleccione `Bing Search v7`.
3. Escriba `Name` para el nuevo recurso.
4. Seleccione la suscripción `Pay-As-You-Go`.
5. Seleccione el plan de tarifa `S9`.
6. Haga clic en `Enable` para iniciar la suscripción.

## <a name="running-the-walkthrough"></a>Ejecución del tutorial

Para ejecutar esta aplicación, siga estos pasos:

1. Cree un nuevo proyecto de Python en su IDE o editor favorito.
2. Cree un archivo denominado visualsearch.py y agregue el código que se muestra en esta guía de inicio rápido.
3. Reemplace el valor `SUBSCRIPTION_KEY` con la clave de suscripción.
3. Reemplace el valor `imagePath` con la ruta de acceso de la imagen que va a cargar.
4. Ejecute el programa.



A continuación se muestra cómo enviar un mensaje mediante los datos de formularios con varias partes en Python.

```python
"""Bing Visual Search upload image example"""

# Download and install Python at https://www.python.org/
# Run the following in a command console window
# pip3 install requests

import requests, json


BASE_URI = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch'

SUBSCRIPTION_KEY = '<yoursubscriptionkeygoeshere>'

HEADERS = {'Ocp-Apim-Subscription-Key': SUBSCRIPTION_KEY}

imagePath = '<pathtoyourimagetouploadgoeshere>'

file = {'image' : ('myfile', open(imagePath, 'rb'))}

def main():
    
    try:
        response = requests.post(BASE_URI, headers=HEADERS, files=file)
        response.raise_for_status()
        print_json(response.json())

    except Exception as ex:
        raise ex


def print_json(obj):
    """Print the object as json"""
    print(json.dumps(obj, sort_keys=True, indent=2, separators=(',', ': ')))



# Main execution
if __name__ == '__main__':
    main()
```


## <a name="next-steps"></a>Pasos siguientes

[Obtenga información acerca de una imagen mediante un token de conclusión](../use-insights-token.md)  
[Tutorial sobre la carga de imágenes en Bing Visual Search](../tutorial-visual-search-image-upload.md)
[Tutorial sobre la aplicación de una sola página en Bing Visual Search](../tutorial-bing-visual-search-single-page-app.md)  
[Introducción a Bing Visual Search](../overview.md)  
[Pruébelo](https://aka.ms/bingvisualsearchtryforfree)  
[Obtenga una clave de acceso para evaluación gratuita](https://azure.microsoft.com/try/cognitive-services/?api=bing-visual-search-api)  
[Referencia a Bing Visual Search API](https://aka.ms/bingvisualsearchreferencedoc)
