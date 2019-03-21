---
title: 'Inicio rápido: Contenido de imagen específico de dominio, REST, PHP'
titleSuffix: Azure Cognitive Services
description: En esta guía de inicio rápido, usará un modelo de dominio para identificar puntos de referencia en una imagen mediante Computer Vision API con PHP.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 03/11/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 2c1801296247ace081a34a137fe3f3bea7a55974
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/19/2019
ms.locfileid: "57877027"
---
# <a name="quickstart-recognize-domain-specific-content-using-the-rest-api-and-php-with-computer-vision"></a>Inicio rápido: Reconocer contenido específico del dominio mediante la API REST y PHP con Computer Vision

En esta guía de inicio rápido, usará un modelo de dominio para identificar puntos de referencia o, si lo desea, celebridades en una imagen almacenada de forma remota mediante la API de REST Computer Vision. Con el método [Reconocer contenido específico de dominio](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e200), puede aplicar un modelo específico de dominio para reconocer contenido en una imagen.

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) antes de empezar.

## <a name="prerequisites"></a>Requisitos previos

- Debe tener [PHP](https://secure.php.net/downloads.php) instalado.
- Debe tener [Pear](https://pear.php.net) instalado.
- Debe tener una clave de suscripción para Computer Vision. Para obtener una clave de suscripción, consulte [Obtención de claves de suscripción](../Vision-API-How-to-Topics/HowToSubscribe.md).

## <a name="create-and-run-the-sample"></a>Creación y ejecución del ejemplo

Para crear y ejecutar el ejemplo, siga estos pasos:

1. Instalación del paquete [`HTTP_Request2`](https://pear.php.net/package/HTTP_Request2) de PHP5.
   1. Abra una ventana del símbolo del sistema como administrador.
   1. Ejecute el siguiente comando:

      ```console
      pear install HTTP_Request2
      ```

   1. Una vez que el paquete se haya instalado correctamente, cierre la ventana del símbolo del sistema.

1. Copie el código siguiente en un editor de texto.
1. Realice los siguientes cambios en el código donde sea necesario:
    1. Reemplace el valor de `subscriptionKey` por la clave de suscripción.
    1. Reemplace el valor de `uriBase` por la dirección URL del punto de conexión para el método [Reconocer contenido específico de dominio](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e200) desde la región de Azure donde obtuvo las claves de suscripción, si es necesario.
    1. También puede reemplazar el valor de `imageUrl` por la dirección URL de una imagen diferente que desee analizar.
    1. También puede reemplazar el valor del parámetro de solicitud `domain` por `celebrites` si desea usar el modelo de dominio `celebrities` en lugar del modelo de dominio `landmarks`.
1. Guarde el código como un archivo con una extensión `.php`. Por ejemplo, `use-domain-model.php`.
1. Abra una ventana del explorador con compatibilidad con PHP.
1. Arrastre y coloque el archivo en la ventana del explorador.

```php
<html>
<head>
    <title>Analyze Domain Model Sample</title>
</head>
<body>
<?php
// Replace <Subscription Key> with a valid subscription key.
$ocpApimSubscriptionKey = '<Subscription Key>';

// You must use the same location in your REST call as you used to obtain
// your subscription keys. For example, if you obtained your subscription keys
// from westus, replace "westcentralus" in the URL below with "westus".
$uriBase = 'https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/';

// Change 'landmarks' to 'celebrities' to use the Celebrities model.
$domain = 'landmarks';

$imageUrl =
    'https://upload.wikimedia.org/wikipedia/commons/2/23/Space_Needle_2011-07-04.jpg';

require_once 'HTTP/Request2.php';

$request = new Http_Request2($uriBase . 'models/' . $domain . '/analyze');
$url = $request->getUrl();

$headers = array(
    // Request headers
    'Content-Type' => 'application/json',
    'Ocp-Apim-Subscription-Key' => $ocpApimSubscriptionKey
);
$request->setHeader($headers);

$parameters = array(
    // Request parameters
    'model' => $domain
);
$url->setQueryVariables($parameters);

$request->setMethod(HTTP_Request2::METHOD_POST);

// Request body parameters
$body = json_encode(array('url' => $imageUrl));

// Request body
$request->setBody($body);

try
{
    $response = $request->send();
    echo "<pre>" .
        json_encode(json_decode($response->getBody()), JSON_PRETTY_PRINT) . "</pre>";
}
catch (HttpException $ex)
{
    echo "<pre>" . $ex . "</pre>";
}
?>
</body>
</html>
```

## <a name="examine-the-response"></a>Examen de la respuesta

Se devuelve una respuesta correcta en JSON. El sitio web de ejemplo analiza y muestra una respuesta correcta en la ventana del explorador, parecida a la del ejemplo siguiente:

```json
{
    "result": {
        "landmarks": [
            {
                "name": "Space Needle",
                "confidence": 0.9998177886009216
            }
        ]
    },
    "requestId": "4d26587b-b2b9-408d-a70c-1f8121d84b0d",
    "metadata": {
        "height": 4132,
        "width": 2096,
        "format": "Jpeg"
    }
}
```

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando ya no sea necesario, elimine el archivo y después desinstale el paquete `HTTP_Request2` de PHP5. Para desinstalar el paquete, realice los siguientes pasos:

1. Abra una ventana del símbolo del sistema como administrador.
2. Ejecute el siguiente comando:

   ```console
   pear uninstall HTTP_Request2
   ```

3. Una vez que el paquete se haya desinstalado correctamente, cierre la ventana del símbolo del sistema.

## <a name="next-steps"></a>Pasos siguientes

Explore las versiones de Computer Vision API que se usan para analizar una imagen, detectar celebridades y sitios emblemáticos, crear una miniatura y extraer texto impreso y manuscrito. Para experimentar rápidamente con la versión de Computer Vision API, pruebe la [consola de pruebas de Open API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Explore Computer Vision API](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
