---
title: 'Inicio rápido: Revisión ortográfica con la API REST de Bing Spell Check y Java'
titlesuffix: Azure Cognitive Services
description: Introducción al uso de la API REST de Bing Spell Check para la revisión ortográfica y gramatical.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 04/11/2019
ms.author: aahi
ms.openlocfilehash: eeec278a1c9e4888aa4a63af3d9c86e7a8c1615c
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/02/2019
ms.locfileid: "67502912"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-java"></a>Inicio rápido: Revisión ortográfica con la API REST de Bing Spell Check y Java

Use este inicio rápido para realizar la primera llamada a la API REST de Bing Spell Check. Esta sencilla aplicación de Java envía una solicitud a la API y devuelve una lista de correcciones sugeridas. Si bien esta aplicación está escrita en Java, la API es un servicio web RESTful compatible con la mayoría de los lenguajes de programación. El código fuente de esta aplicación está disponible en [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/Search/BingSpellCheckv7.java).

## <a name="prerequisites"></a>Requisitos previos

* Kit de desarrollo de Java (JDK) 7 o superior

* Importe el archivo [gson-2.8.5.jar](https://libraries.io/maven/com.google.code.gson%3Agson) o la versión más actual de [Gson](https://github.com/google/gson). Para la ejecución de la línea de comandos, agregue el archivo `.jar` a la carpeta de Java con la clase principal.

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]

## <a name="create-and-initialize-an-application"></a>Creación e inicialización de una aplicación

1. Cree un nuevo proyecto de Java en su IDE o editor favoritos con el nombre de clase que elija e importe los siguientes paquetes.

    ```java
    import java.io.*;
    import java.net.*;
    import com.google.gson.*;
    import javax.net.ssl.HttpsURLConnection;
    ```

2. Cree variables para el host, la ruta de acceso y la clave de suscripción del punto de conexión de API. A continuación, cree variables para su mercado, el texto cuya ortografía quiere revisar y una cadena para el modo de revisión ortográfica.

    ```java
    static String host = "https://api.cognitive.microsoft.com";
    static String path = "/bing/v7.0/spellcheck";

    static String key = "<ENTER-KEY-HERE>";

    static String mkt = "en-US";
    static String mode = "proof";
    static String text = "Hollo, wrld!";
    ```

## <a name="create-and-send-an-api-request"></a>Creación y envío de una solicitud de API

1. Cree una función llamada `check()` para crear y enviar la solicitud de API. Dentro de ella, siga estos pasos. Cree una cadena para los parámetros de solicitud. Anexe el parámetro `?mkt=` a la cadena de su mercado y el parámetro `&mode=` al modo de revisión ortográfica.  

   ```java
   public static void check () throws Exception {
       String params = "?mkt=" + mkt + "&mode=" + mode;
      // add the rest of the code snippets here (except prettify() and main())...
   }
   ```

2. Cree una dirección URL mediante la combinación del host, la ruta de acceso y la cadena de parámetros del punto de conexión. Cree un nuevo objeto `HttpsURLConnection`.

    ```java
    URL url = new URL(host + path + params);
    HttpsURLConnection connection = (HttpsURLConnection) url.openConnection();
    ```

3. Abra una conexión a la dirección URL. Establezca el método de solicitud en `POST`. Agregue los parámetros de solicitud. No olvide agregar la clave de suscripción al encabezado `Ocp-Apim-Subscription-Key`.

    ```java
    connection.setRequestMethod("POST");
    connection.setRequestProperty("Content-Type", "application/x-www-form-urlencoded");
    connection.setRequestProperty("Ocp-Apim-Subscription-Key", key);
    connection.setDoOutput(true);
    ```

4. Cree un objeto `DataOutputStream` y envíe la solicitud a la API.

    ```java
        DataOutputStream wr = new DataOutputStream(connection.getOutputStream());
        wr.writeBytes("text=" + text);
        wr.flush();
        wr.close();
    ```

## <a name="format-and-read-the-api-response"></a>Formato y lectura de la respuesta de API

1. Agregue este método a la clase. Dicho método da formato a JSON para obtener una salida más legible.

    ``` java
    // This function prettifies the json response.
    public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonElement json = parser.parse(json_text);
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }
    ```

1. Cree un objeto `BufferedReader` y lea la respuesta de la API. Imprímala en la consola.
    
    ```java
    BufferedReader in = new BufferedReader(
    new InputStreamReader(connection.getInputStream()));
    String line;
    while ((line = in.readLine()) != null) {
        System.out.println(prettify(line);
    }
    in.close();
    ```

## <a name="call-the-api"></a>Llamada a la API

En la función principal de la aplicación, llame al método check() creado anteriormente.

    ```java
    public static void main(String[] args) {
        try {
            check();
        }
        catch (Exception e) {
            System.out.println (e);
        }
    }
    ```

## <a name="run-the-application"></a>Ejecución de la aplicación

Compile y ejecute el proyecto.

Si usa la línea de comandos, use los siguientes comandos para compilar y ejecutar la aplicación.

**Compilar:**
```bash
javac -classpath .;gson-2.2.2.jar\* <CLASS_NAME>.java
```

**Ejecutar:**
```bash
java -cp .;gson-2.2.2.jar\* <CLASS_NAME>
```

## <a name="example-json-response"></a>Ejemplo de respuesta JSON

Se devuelve una respuesta correcta en JSON, como se muestra en el siguiente ejemplo:

```json
{
   "_type": "SpellCheck",
   "flaggedTokens": [
      {
         "offset": 0,
         "token": "Hollo",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "Hello",
               "score": 0.9115257530801
            },
            {
               "suggestion": "Hollow",
               "score": 0.858039839213461
            },
            {
               "suggestion": "Hallo",
               "score": 0.597385084464481
            }
         ]
      },
      {
         "offset": 7,
         "token": "wrld",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "world",
               "score": 0.9115257530801
            }
         ]
      }
   ]
}
```

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Creación de una aplicación web de una sola página](../tutorials/spellcheck.md)

- [¿Qué es Bing Spell Check API?](../overview.md)
- [Referencia de Bing Spell Check API v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)
