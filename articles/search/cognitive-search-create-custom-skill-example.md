---
title: 'Ejemplo: crear una aptitud personalizada en una canalización de Cognitive Search: Azure Search'
description: Aquí se muestra cómo utilizar Text Translate API en la aptitud personalizada asignada a una canalización de indexación de Cognitive Search en Azure Search.
manager: pablocas
author: luiscabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 82d49a6a82251f440c06db03edc92851fce87741
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/02/2019
ms.locfileid: "65023629"
---
# <a name="example-create-a-custom-skill-using-the-text-translate-api"></a>Ejemplo: crear una aptitud personalizada mediante Text Translate API

En este ejemplo aprenderá a crear una aptitud personalizada de Web API que acepte texto en cualquier idioma y lo traduzca al inglés. Este ejemplo usa una [Función de Azure ](https://azure.microsoft.com/services/functions/) para encapsular [Translate Text API](https://azure.microsoft.com/services/cognitive-services/translator-text-api/) para que pueda implementar la interfaz de la aptitud personalizada.

## <a name="prerequisites"></a>requisitos previos

+ Si no está familiarizado con la interfaz de entrada/salida que debe implementar una aptitud personalizada, le recomendamos que lea el artículo sobre la [interfaz de la aptitud personalizada](cognitive-search-custom-skill-interface.md).

+ [Regístrese para usar Translator Text API](../cognitive-services/translator/translator-text-how-to-signup.md) y obtenga una clave de API para consumirla.

+ Instale la [versión 15.5 de Visual Studio 2017](https://www.visualstudio.com/vs/) o posterior, incluyendo la carga de trabajo de desarrollo de Azure.

## <a name="create-an-azure-function"></a>Creación de una Función de Azure

Aunque este ejemplo utiliza una función de Azure para hospedar una Web API, esta acción no es necesaria.  Siempre que cumpla con los [requisitos de interfaz de una aptitud cognitiva](cognitive-search-custom-skill-interface.md), no importa el enfoque que quiera usar. Sin embargo, Azure Functions facilita la creación de una aptitud personalizada.

### <a name="create-a-function-app"></a>Creación de una aplicación de función

1. En Visual Studio, seleccione **Nuevo** > **Proyecto** en el menú Archivo.

1. En el cuadro de diálogo Nuevo proyecto, seleccione **Instalado**, expanda **Visual C#** > **Nube**, seleccione **Azure Functions**, escriba un nombre para el proyecto y haga clic en **Aceptar**. El nombre de la aplicación de función debe ser válido como espacio de nombres de C#, por lo que no debe usar guiones bajos, guiones u otros caracteres no alfanuméricos.

1. Seleccione **v2 de Azure Functions (.NET Core)**. También puede hacerlo con la versión 1, pero el código que se escribe a continuación utiliza la plantilla de la versión 2.

1. Asegúrese de que el tipo es **Desencadenador HTTP**.

1. En la cuenta de almacenamiento, seleccione **Ninguno**, ya que no necesitará ningún almacenamiento para esta función.

1. Seleccione **Aceptar** para crear el proyecto de función y la función que se desencadena mediante HTTP.

### <a name="modify-the-code-to-call-the-translate-cognitive-service"></a>Modifique el código para llamar al Servicio cognitivo de traducción.

Visual Studio crea un proyecto y dentro de él una clase que contiene código reutilizable para el tipo de función seleccionada. El atributo *FunctionName* en el método establece el nombre de la función. El atributo *HttpTrigger* especifica que una solicitud HTTP desencadena la función.

A continuación, reemplace todo el contenido del archivo *Function1.cs* con el siguiente código:

```csharp
using System;
using System.Net.Http;
using System.Collections.Generic;
using System.Threading.Tasks;
using System.IO;
using System.Text;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Azure.WebJobs.Host;
using Newtonsoft.Json;

namespace TranslateFunction
{
    // This function will simply translate messages sent to it.
    public static class Function1
    {
        static string path = "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0";

        // NOTE: Replace this example key with a valid subscription key.
        static string key = "<enter your api key here>";

        #region classes used to serialize the response
        private class WebApiResponseError
        {
            public string message { get; set; }
        }

        private class WebApiResponseWarning
        {
            public string message { get; set; }
        }

        private class WebApiResponseRecord
        {
            public string recordId { get; set; }
            public Dictionary<string, object> data { get; set; }
            public List<WebApiResponseError> errors { get; set; }
            public List<WebApiResponseWarning> warnings { get; set; }
        }

        private class WebApiEnricherResponse
        {
            public List<WebApiResponseRecord> values { get; set; }
        }
        #endregion

        [FunctionName("Translate")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Function, "post", Route = null)]HttpRequest req,
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");

            string recordId = null;
            string originalText = null;
            string toLanguage = null;
            string translatedText = null;

            string requestBody = new StreamReader(req.Body).ReadToEnd();
            dynamic data = JsonConvert.DeserializeObject(requestBody);

            // Validation
            if (data?.values == null)
            {
                return new BadRequestObjectResult(" Could not find values array");
            }
            if (data?.values.HasValues == false || data?.values.First.HasValues == false)
            {
                // It could not find a record, then return empty values array.
                return new BadRequestObjectResult(" Could not find valid records in values array");
            }

            recordId = data?.values?.First?.recordId?.Value as string;
            originalText = data?.values?.First?.data?.text?.Value as string;
            toLanguage = data?.values?.First?.data?.language?.Value as string;

            if (recordId == null)
            {
                return new BadRequestObjectResult("recordId cannot be null");
            }

            translatedText = TranslateText(originalText, toLanguage).Result;
        
            // Put together response.
            WebApiResponseRecord responseRecord = new WebApiResponseRecord();
            responseRecord.data = new Dictionary<string, object>();
            responseRecord.recordId = recordId;
            responseRecord.data.Add("text", translatedText);

            WebApiEnricherResponse response = new WebApiEnricherResponse();
            response.values = new List<WebApiResponseRecord>();
            response.values.Add(responseRecord);

            return (ActionResult)new OkObjectResult(response);
        }


        /// <summary>
        /// Use Cognitive Service to translate text from one language to another.
        /// </summary>
        /// <param name="originalText">The text to translate.</param>
        /// <param name="toLanguage">The language you want to translate to.</param>
        /// <returns>Asynchronous task that returns the translated text. </returns>
        async static Task<string> TranslateText(string originalText, string toLanguage)
        {
            System.Object[] body = new System.Object[] { new { Text = originalText } };
            var requestBody = JsonConvert.SerializeObject(body);

            var uri = $"{path}&to={toLanguage}";

            string result = "";

            using (var client = new HttpClient())
            using (var request = new HttpRequestMessage())
            {
                request.Method = HttpMethod.Post;
                request.RequestUri = new Uri(uri);
                request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");
                request.Headers.Add("Ocp-Apim-Subscription-Key", key);

                var response = await client.SendAsync(request);
                var responseBody = await response.Content.ReadAsStringAsync();

                dynamic data = JsonConvert.DeserializeObject(responseBody);
                result = data?.First?.translations?.First?.text?.Value as string;

            }
            return result;
        }
    }
}
```

Asegúrese de escribir su propio valor de *clave* en el método *TranslateText*, según la clave que obtuvo al registrarse en Translate Text API.

Este ejemplo es una simple opción de enriquecimiento que solo funciona en un registro a la vez. Este dato es importante más adelante, al establecer el tamaño del lote para el conjunto de aptitudes.

## <a name="test-the-function-from-visual-studio"></a>Pruebe la función desde Visual Studio

Presione **F5** para ejecutar el programa y probar los comportamientos de la función. En este caso, vamos a usar la siguiente función para traducir un texto de español a inglés. Use Postman o Fiddler para emitir una llamada como la que se muestra a continuación:

```http
POST https://localhost:7071/api/Translate
```
### <a name="request-body"></a>Cuerpo de la solicitud
```json
{
   "values": [
        {
            "recordId": "a1",
            "data":
            {
               "text":  "Este es un contrato en Inglés",
               "language": "en"
            }
        }
   ]
}
```
### <a name="response"></a>Response
Debería ver una respuesta similar a la del siguiente ejemplo:

```json
{
    "values": [
        {
            "recordId": "a1",
            "data": {
                "text": "This is a contract in English"
            },
            "errors": null,
            "warnings": null
        }
    ]
}
```

## <a name="publish-the-function-to-azure"></a>Publicar la función en Azure

Cuando esté satisfecho con el comportamiento de la función, puede publicarla.

1. En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto y seleccione **Publicar**. Elija **Crear nueva** > **Publicar**.

1. Si todavía no tiene conectado Visual Studio a la cuenta de Azure, seleccione **Agregar una cuenta...**

1. Siga las instrucciones que aparecen en pantalla. Se le pedirá que especifique la cuenta de Azure, el grupo de recursos, el plan de hospedaje y la cuenta de almacenamiento que quiera usar. Puede crear un nuevo grupo de recursos, un nuevo plan de hospedaje y una cuenta de almacenamiento si aún no los tiene. Cuando haya terminado, seleccione **Crear**.

1. Una vez completada la implementación, tenga en cuenta la dirección URL del sitio. Es la dirección de la aplicación de función en Azure. 

1. En [Azure Portal](https://portal.azure.com), vaya al grupo de recursos y busque la función de traducción que publicó. En la sección **Administrar**, verá las claves de host. Seleccione el ícono **Copiar** de la clave de host *predeterminada*.  

## <a name="test-the-function-in-azure"></a>Pruebe la función en Azure

Ahora que tiene la clave de host predeterminada, pruebe la función de la siguiente manera:

```http
POST https://translatecogsrch.azurewebsites.net/api/Translate?code=[enter default host key here]
```
### <a name="request-body"></a>Cuerpo de la solicitud
```json
{
   "values": [
        {
            "recordId": "a1",
            "data":
            {
               "text":  "Este es un contrato en Inglés",
               "language": "en"
            }
        }
   ]
}
```

Esta opción debería producir un resultado similar al que vio anteriormente al ejecutar la función en el entorno local.

## <a name="connect-to-your-pipeline"></a>Conéctese a la canalización
Ahora que tiene una nueva aptitud personalizada, puede agregarla al conjunto de aptitudes. En el siguiente ejemplo se muestra cómo llamar a la aptitud. Como la aptitud no maneja los lotes, agregue una instrucción para que el tamaño máximo de lote sea de ```1``` y así enviar documentos de uno en uno.

```json
{
    "skills": [
      ...,  
      {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "Our new translator custom skill",
        "uri": "https://translatecogsrch.azurewebsites.net/api/Translate?code=[enter default host key here]",
        "batchSize":1,
        "context": "/document",
        "inputs": [
          {
            "name": "text",
            "source": "/document/content"
          },
          {
            "name": "language",
            "source": "/document/destinationLanguage"
          }
        ],
        "outputs": [
          {
            "name": "text",
            "targetName": "translatedText"
          }
        ]
      }
  ]
}
```

## <a name="next-steps"></a>Pasos siguientes
Felicidades. Acaba de crear su primera opción de enriquecimiento personalizada. Si quiere agregar su propia funcionalidad personalizada, puede seguir el mismo patrón. 

+ [Agregar una aptitud personalizada a una canalización de Cognitive Search](cognitive-search-custom-skill-interface.md)
+ [Cómo definir un conjunto de aptitudes](cognitive-search-defining-skillset.md)
+ [Crear un conjunto de aptitudes (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Cómo asignar campos enriquecidos](cognitive-search-output-field-mapping.md)
