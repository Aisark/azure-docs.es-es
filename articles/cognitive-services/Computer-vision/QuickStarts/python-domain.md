---
title: 'Inicio rápido: Contenido específico de dominio (REST, Python)'
titleSuffix: Azure Cognitive Services
description: En esta guía de inicio rápido, usará modelos de dominio para identificar celebridades y puntos de referencia en una imagen mediante Computer Vision API con Python.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 19e87769bf4e49e5665d5f4887da9fd08e3cd6af
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/24/2019
ms.locfileid: "67340672"
---
# <a name="quickstart-use-a-domain-model-using-the-rest-api-and-python-in-computer-vision"></a>Inicio rápido: Uso de un modelo de dominio mediante la API REST y Python en Computer Vision

En esta guía de inicio rápido, usará un modelo de dominio para identificar puntos de referencia o, si lo desea, celebridades en una imagen almacenada de forma remota mediante la API de REST Computer Vision. Con el método [Reconocer contenido específico de dominio](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e200), puede aplicar un modelo específico de dominio para reconocer contenido en una imagen.

Puede ejecutar esta guía de inicio rápido paso a paso mediante un cuaderno de Jupyter en [MyBinder](https://mybinder.org). Para iniciar Binder, seleccione el botón siguiente:

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=VisionAPI.ipynb)

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/try/cognitive-services/) antes de empezar.

## <a name="prerequisites"></a>Requisitos previos

- Debe tener [Python](https://www.python.org/downloads/) instalado si desea ejecutar el ejemplo localmente.
- Debe tener una clave de suscripción para Computer Vision. Puede obtener una clave de evaluación gratuita en la página [Pruebe Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision). O bien, siga las instrucciones que se indican en [Creación de una cuenta de Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) para suscribirse a Computer Vision y obtener su clave.

## <a name="create-and-run-the-landmarks-sample"></a>Creación y ejecución del ejemplo de puntos de referencia

Para crear y ejecutar el ejemplo de puntos de referencia, siga estos pasos:

1. Copie el código siguiente en un editor de texto.
1. Realice los siguientes cambios en el código donde sea necesario:
    1. Reemplace el valor de `subscription_key` por la clave de suscripción.
    1. Reemplace el valor de `vision_base_url` por la dirección URL del punto de conexión del recurso de Computer Vision en la región de Azure donde obtuvo las claves de suscripción, si es necesario.
    1. También puede reemplazar el valor de `image_url` por la dirección URL de una imagen diferente en la que desea detectar puntos de referencia.
1. Guarde el código como un archivo con la extensión `.py`. Por ejemplo, `get-landmarks.py`.
1. Abra una ventana de símbolo del sistema.
1. En el símbolo del sistema, utilice el comando `python` para ejecutar el ejemplo. Por ejemplo, `python get-landmarks.py`.

```python
import requests
# If you are using a Jupyter notebook, uncomment the following line.
# %matplotlib inline
import matplotlib.pyplot as plt
from PIL import Image
from io import BytesIO

# Replace <Subscription Key> with your valid subscription key.
subscription_key = "<Subscription Key>"
assert subscription_key

# You must use the same region in your REST call as you used to get your
# subscription keys. For example, if you got your subscription keys from
# westus, replace "westcentralus" in the URI below with "westus".
#
# Free trial subscription keys are generated in the "westus" region.
# If you use a free trial subscription key, you shouldn't need to change
# this region.
vision_base_url = "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/"

landmark_analyze_url = vision_base_url + "models/landmarks/analyze"

# Set image_url to the URL of an image that you want to analyze.
image_url = "https://upload.wikimedia.org/wikipedia/commons/f/f6/" + \
    "Bunker_Hill_Monument_2005.jpg"

headers = {'Ocp-Apim-Subscription-Key': subscription_key}
params = {'model': 'landmarks'}
data = {'url': image_url}
response = requests.post(
    landmark_analyze_url, headers=headers, params=params, json=data)
response.raise_for_status()

# The 'analysis' object contains various fields that describe the image. The
# most relevant landmark for the image is obtained from the 'result' property.
analysis = response.json()
assert analysis["result"]["landmarks"] is not []
print(analysis)
landmark_name = analysis["result"]["landmarks"][0]["name"].capitalize()

# Display the image and overlay it with the landmark name.
image = Image.open(BytesIO(requests.get(image_url).content))
plt.imshow(image)
plt.axis("off")
_ = plt.title(landmark_name, size="x-large", y=-0.1)
```

## <a name="examine-the-response-for-the-landmarks-sample"></a>Análisis de la respuesta del ejemplo de puntos de referencia

Se devuelve una respuesta correcta en JSON. La página web de ejemplo analiza y muestra una respuesta correcta en la ventana del símbolo del sistema, parecida a la del ejemplo siguiente:

```json
{
  "result": {
    "landmarks": [
      {
        "name": "Bunker Hill Monument",
        "confidence": 0.9768505096435547
      }
    ]
  },
  "requestId": "659a10cd-44bb-44db-9147-a295b853b2b8",
  "metadata": {
    "height": 1600,
    "width": 1200,
    "format": "Jpeg"
  }
}
```

## <a name="create-and-run-the-celebrities-sample"></a>Creación y ejecución del ejemplo de celebridades

Para crear y ejecutar el ejemplo de puntos de referencia, siga estos pasos:

1. Copie el código siguiente en un editor de texto.
1. Realice los siguientes cambios en el código donde sea necesario:
    1. Reemplace el valor de `subscription_key` por la clave de suscripción.
    1. Reemplace el valor de `vision_base_url` por la dirección URL del punto de conexión del recurso de Computer Vision en la región de Azure donde obtuvo las claves de suscripción, si es necesario.
    1. También puede reemplazar el valor de `image_url` por la dirección URL de una imagen diferente en la que desea detectar celebridades.
1. Guarde el código como un archivo con la extensión `.py`. Por ejemplo, `get-celebrities.py`.
1. Abra una ventana de símbolo del sistema.
1. En el símbolo del sistema, utilice el comando `python` para ejecutar el ejemplo. Por ejemplo, `python get-celebrities.py`.

```python
import requests
# If you are using a Jupyter notebook, uncomment the following line.
# %matplotlib inline
import matplotlib.pyplot as plt
from PIL import Image
from io import BytesIO

# Replace <Subscription Key> with your valid subscription key.
subscription_key = "<Subscription Key>"
assert subscription_key

vision_base_url = "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/"

celebrity_analyze_url = vision_base_url + "models/celebrities/analyze"

# Set image_url to the URL of an image that you want to analyze.
image_url = "https://upload.wikimedia.org/wikipedia/commons/d/d9/" + \
    "Bill_gates_portrait.jpg"

headers = {'Ocp-Apim-Subscription-Key': subscription_key}
params = {'model': 'celebrities'}
data = {'url': image_url}
response = requests.post(
    celebrity_analyze_url, headers=headers, params=params, json=data)
response.raise_for_status()

# The 'analysis' object contains various fields that describe the image. The
# most relevant celebrity for the image is obtained from the 'result' property.
analysis = response.json()
assert analysis["result"]["celebrities"] is not []
print(analysis)
celebrity_name = analysis["result"]["celebrities"][0]["name"].capitalize()

# Display the image and overlay it with the celebrity name.
image = Image.open(BytesIO(requests.get(image_url).content))
plt.imshow(image)
plt.axis("off")
_ = plt.title(celebrity_name, size="x-large", y=-0.1)
```

## <a name="examine-the-response-for-the-celebrities-sample"></a>Análisis de la respuesta del ejemplo de celebridades

Se devuelve una respuesta correcta en JSON. La página web de ejemplo analiza y muestra una respuesta correcta en la ventana del símbolo del sistema, parecida a la del ejemplo siguiente:


```json
{
  "result": {
    "celebrities": [
      {
        "faceRectangle": {
          "top": 123,
          "left": 156,
          "width": 187,
          "height": 187
        },
        "name": "Bill Gates",
        "confidence": 0.9993845224380493
      }
    ]
  },
  "requestId": "f14ec1d0-62d4-4296-9ceb-6b5776dc2020",
  "metadata": {
    "height": 521,
    "width": 550,
    "format": "Jpeg"
  }
}
```

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando ya no los necesite, elimine los archivos de los dos ejemplos.

## <a name="next-steps"></a>Pasos siguientes

Explore una aplicación de Python que usa Computer Vision para realizar el reconocimiento óptico de caracteres (OCR), crear miniaturas con recorte inteligente, y detectar, clasificar, etiquetar y describir características visuales, como caras, en una imagen. Para experimentar rápidamente con la versión de Computer Vision API, pruebe la [consola de pruebas de Open API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Tutorial de Computer Vision API para Python](../Tutorials/PythonTutorial.md)
