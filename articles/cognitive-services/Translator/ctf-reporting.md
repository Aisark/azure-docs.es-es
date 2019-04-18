---
title: Informes de Collaborative Translation Framework (CTF) - Translator Text API
titlesuffix: Azure Cognitive Services
description: Cómo usar los informes de Collaborative Translation Framework (CTF).
services: cognitive-services
author: v-pawal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: v-jansko
ms.openlocfilehash: da321aa2a4db441fa5bb51e4986d00889cb7482d
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/18/2019
ms.locfileid: "58917403"
---
# <a name="how-to-use-collaborative-translation-framework-ctf-reporting"></a>Cómo usar los informes de Collaborative Translation Framework (CTF)

> [!NOTE]
> Este método está obsoleto. No está disponible en la versión 3.0 de Translator Text API.
> 
> Collaborative Translations Framework (CTF), que se utilizaba antes para la versión 2.0 de Translator Text API, ha quedado en desuso a partir del 1 de febrero de 2018. Las funciones AddTranslation y AddTranslationArray permiten a los usuarios proporcionar correcciones a través del marco Collaborative Translation Framework. Después del 31 de enero de 2018, estas dos funciones no aceptan nuevos envíos de frases, y los usuarios reciben un mensaje de error. Estas funciones se han retirado y no se reemplazarán.
> 
> Hay una funcionalidad similar disponible en Translator Hub API, que le permite generar un sistema de traducción personalizada con su terminología y estilo y que puede invocar mediante el identificador de categoría de Translator Text API. Translator Hub: [https://hub.microsofttranslator.com](https://hub.microsofttranslator.com). Translator Hub API: [https://hub.microsofttranslator.com/swagger](https://hub.microsofttranslator.com/swagger).

La API de informes de Collaborative Translation Framework (CTF) devuelve estadísticas y el contenido real en el almacén de CTF. Esta API es diferente del método GetTranslations() por lo siguiente:
* Devuelve el contenido traducido y su recuento total solo de su cuenta (appId o cuenta de Azure Marketplace).
* Devuelve el contenido traducido y su recuento total sin necesidad de una coincidencia de la frase de origen.
* No devuelve la traducción automática.

## <a name="endpoint"></a>Punto de conexión
El punto de conexión de la API de informes de CTF es https://api.microsofttranslator.com/v2/beta/ctfreporting.svc.


## <a name="methods"></a>Métodos
| NOMBRE |    DESCRIPCIÓN|
|:---|:---|
| Método GetUserTranslationCounts | Obtiene los recuentos de las traducciones creadas por el usuario. |
| Método GetUserTranslations | Recupera las traducciones creadas por el usuario. |

Estos métodos le permiten:
* Recuperar el conjunto completo de traducciones y correcciones del usuario del identificador de la cuenta para su descarga.
* Obtener la lista de los colaboradores frecuentes. Asegúrese de que se proporciona el nombre de usuario correcto en AddTranslation().
* Crear una interfaz de usuario (IU) que permite a los usuarios de confianza ver todos los candidatos disponibles, si es necesario restringido a una parte de su sitio, según el prefijo URI.

> [!NOTE]
> Ambos métodos son relativamente lentos y costosos. Se recomienda usarlos con moderación.

## <a name="getusertranslationcounts-method"></a>Método GetUserTranslationCounts

Este método obtiene el recuento de traducciones que se crean por el usuario. Proporciona la lista de recuentos de traducción agrupados por los parámetros uriPrefix, from, to, user, minRating y maxRating.

**Sintaxis**

> [!div class="tabbedCodeSnippets"]
> ```cs
> UserTranslationCount[]GetUserTranslationCounts(
>            string appId,
>            string uriPrefix,
>            string from,
>            string to,
>            int? minRating,
>            int? maxRating,
>            string user,
>            string category
>            DateTime? minDateUtc,
>            DateTime? maxDateUtc,
>            int? skip,
>            int? take);
> ```

**Parámetros**

| Parámetro | DESCRIPCIÓN |
|:---|:---|
| appId | **Obligatorio** Si se usa el encabezado de autorización, deje el campo appid vacío o incluya una cadena que contenga "Bearer" + " " + token de acceso.|
| uriPrefix | **Opcional** Una cadena que contiene el prefijo del URI de la traducción.|
| De | **Opcional** Una cadena que representa el código de idioma del texto de traducción. |
| to | **Opcional** Una cadena que representa el código de idioma al que se va a traducir el texto.|
| minRating| **Opcional** Un valor entero que representa la clasificación de calidad mínima para el texto traducido. El valor válido se encuentra entre -10 y 10. El valor predeterminado es 1.|
| maxRating| **Opcional** Un valor entero que representa la clasificación de calidad máxima para el texto traducido. El valor válido se encuentra entre -10 y 10. El valor predeterminado es 1.|
| user | **Opcional** Una cadena que se utiliza para filtrar los resultados según el autor del envío. |
| categoría| **Opcional** Una cadena que contiene la categoría o el dominio de la traducción. Este parámetro admite solo la opción predeterminada general.|
| minDateUtc| **Opcional** La fecha desde la que desea recuperar las traducciones. La fecha debe tener el formato UTC. |
| maxDateUtc| **Opcional** La fecha hasta la que desea recuperar las traducciones. La fecha debe tener el formato UTC. |
| skip| **Opcional** El número de resultados que desea omitir en una página. Por ejemplo, si desea omitir las 20 primeras filas de los resultados y ver a partir del 21º registro de resultados, especifique 20 en este parámetro. El valor predeterminado para este parámetro es 0.|
| take | **Opcional** El número de resultados que desea recuperar. El número máximo de cada solicitud es 100. El valor predeterminado es 100.|

> [!NOTE]
> Los parámetros de solicitud skip y take habilitan la paginación para un gran número de registros de resultados.

**Valor devuelto**

El conjunto de resultados contiene la matriz de **UserTranslationCount**. Cada UserTranslationCount tiene los siguientes elementos:

| Campo | DESCRIPCIÓN |
|:---|:---|
| Número| Número de resultados que se recupera|
| De | Idioma de origen|
| Rating| Clasificación que se aplica por el remitente en la llamada al método AddTranslation()|
| Para| Idioma de destino|
| Identificador URI| URI que se aplica en la llamada al método AddTranslation()|
| Usuario| Nombre del usuario|

**Excepciones**

| Excepción | Message | Condiciones |
|:---|:---|:---|
| ArgumentOutOfRangeException | El parámetro "**maxDateUtc**"debe ser mayor o igual que "**minDateUtc**".| El valor del parámetro **maxDateUtc** es menor que el valor del parámetro **minDateUtc**.|
| TranslateApiException | IP está por encima de la cuota.| <ul><li>Se alcanzó el límite para el número de solicitudes por minuto.</li><li>El tamaño de la solicitud sigue estando limitado a 10 000 caracteres.</li><li>Una cuota por hora y por día limitan el número de caracteres que aceptará la API de Microsoft Translator.</li></ul>|
| TranslateApiException | AppId está por encima de la cuota.| El identificador de la aplicación superó la cuota por hora o por día.|

> [!NOTE]
> La cuota se ajustará para garantizar la equidad entre todos los usuarios del servicio.

**Vea ejemplos de código en GitHib**
* [C#](https://github.com/MicrosoftTranslator/Documentation-Code-TextAPI/blob/master/ctf/ctf-getusertranslationcounts-example-csharp.md)
* [PHP](https://github.com/MicrosoftTranslator/Documentation-Code-TextAPI/blob/master/ctf/ctf-getusertranslationcounts-example-php.md)

## <a name="getusertranslations-method"></a>Método GetUserTranslations

Este método recupera las traducciones creadas por el usuario. Proporciona las traducciones agrupadas por los parámetros uriPrefix, from, to, user, minRating y maxRating.

**Sintaxis**

> [!div class="tabbedCodeSnippets"]
> ```cs
> UserTranslation[] GetUserTranslations (
>             string appId,
>             string uriPrefix,
>             string from,
>             string to,
>             int? minRating,
>             int? maxRating,
>             string user,
>             string category
>             DateTime? minDateUtc,
>             DateTime? maxDateUtc,
>             int? skip,
>             int? take);
> ```

**Parámetros**

| Parámetro | DESCRIPCIÓN |
|:---|:---|
| appId | **Obligatorio** Si se usa el encabezado de autorización, deje el campo appid vacío o incluya una cadena que contenga "Bearer" + " " + token de acceso.|
| uriPrefix| **Opcional** Una cadena que contiene el prefijo del URI de la traducción.|
| De| **Opcional** Una cadena que representa el código de idioma del texto de traducción.|
| to| **Opcional** Una cadena que representa el código de idioma al que se va a traducir el texto.|
| minRating| **Opcional** Un valor entero que representa la clasificación de calidad mínima para el texto traducido. El valor válido se encuentra entre -10 y 10. El valor predeterminado es 1.|
| maxRating| **Opcional** Un valor entero que representa la clasificación de calidad máxima para el texto traducido. El valor válido se encuentra entre -10 y 10. El valor predeterminado es 1.|
| user| **Opcional. Una cadena que se utiliza para filtrar los resultados según el autor del envío**|
| categoría| **Opcional** Una cadena que contiene la categoría o el dominio de la traducción. Este parámetro admite solo la opción predeterminada general.|
| minDateUtc| **Opcional** La fecha desde la que desea recuperar las traducciones. La fecha debe tener el formato UTC.|
| maxDateUtc| **Opcional** La fecha hasta la que desea recuperar las traducciones. La fecha debe tener el formato UTC.|
| skip| **Opcional** El número de resultados que desea omitir en una página. Por ejemplo, si desea omitir las 20 primeras filas de los resultados y ver a partir del 21º registro de resultados, especifique 20 en este parámetro. El valor predeterminado para este parámetro es 0.|
| take| **Opcional** El número de resultados que desea recuperar. El número máximo de cada solicitud es 100. El valor predeterminado es 50.|

> [!NOTE]
> Los parámetros de solicitud skip y take habilitan la paginación para un gran número de registros de resultados.

**Valor devuelto**

El conjunto de resultados contiene la matriz de **UserTranslation**. Cada UserTranslation tiene los siguientes elementos:

| Campo | DESCRIPCIÓN |
|:---|:---|
| CreatedDateUtc| La fecha de creación de la entrada mediante AddTranslation()|
| De| Idioma de origen|
| OriginalText| Texto de lenguaje de origen que se usa al enviar la solicitud|
|Rating |Clasificación que se aplica por el remitente en la llamada al método AddTranslation()|
|Para|    Idioma de destino|
|TranslatedText|    Traducción tal y como se envió en la llamada al método AddTranslation()|
|Identificador URI|   URI que se aplica en la llamada al método AddTranslation()|
|Usuario   |Nombre del usuario|

**Excepciones**

| Excepción | Message | Condiciones |
|:---|:---|:---|
| ArgumentOutOfRangeException | El parámetro "**maxDateUtc**"debe ser mayor o igual que "**minDateUtc**".| El valor del parámetro **maxDateUtc** es menor que el valor del parámetro **minDateUtc**.|
| TranslateApiException | IP está por encima de la cuota.| <ul><li>Se alcanzó el límite para el número de solicitudes por minuto.</li><li>El tamaño de la solicitud sigue estando limitado a 10 000 caracteres.</li><li>Una cuota por hora y por día limitan el número de caracteres que aceptará la API de Microsoft Translator.</li></ul>|
| TranslateApiException | AppId está por encima de la cuota.| El identificador de la aplicación superó la cuota por hora o por día.|

> [!NOTE]
> La cuota se ajustará para garantizar la equidad entre todos los usuarios del servicio.

**Vea ejemplos de código en GitHib**
* [C#](https://github.com/MicrosoftTranslator/Documentation-Code-TextAPI/blob/master/ctf/ctf-getusertranslations-example-csharp.md)
* [PHP](https://github.com/MicrosoftTranslator/Documentation-Code-TextAPI/blob/master/ctf/ctf-getusertranslations-example-php.md)
