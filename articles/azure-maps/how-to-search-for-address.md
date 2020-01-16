---
title: Búsqueda de una dirección mediante el servicio de búsqueda de Azure Maps | Microsoft Docs
description: Aprenda cómo buscar una dirección mediante el servicio de búsqueda de Azure Maps
author: walsehgal
ms.author: v-musehg
ms.date: 04/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 56194bcfb9531def87a9918ad442a2927413c964
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/25/2019
ms.locfileid: "75432959"
---
# <a name="find-an-address-using-the-azure-maps-search-service"></a>Búsqueda de una dirección mediante el servicio de búsqueda de Azure Maps

El servicio de búsqueda de Maps es un conjunto de API RESTful diseñadas para que los desarrolladores busquen direcciones, lugares, puntos de interés, listados de empresas y otra información geográfica. El servicio de búsqueda asigna una latitud y longitud a una dirección específica, calle transversal, característica geográfica o punto de interés (POI). Los valores de latitud y longitud devueltos por la búsqueda se pueden usar como parámetros en otros servicios de Maps como las rutas y el flujo de tráfico.

En este artículo, aprenderá a:

* Buscar una dirección mediante [Fuzzy Search API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy).
* Buscar una dirección junto con sus propiedades y coordenadas.
* Realizar una [búsqueda de dirección inversa](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) para buscar una dirección postal.
* Buscar una intersección de calles mediante [Search Address Reverse Cross Street API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreversecrossstreet).

## <a name="prerequisites"></a>Prerequisites

Para realizar cualquier llamada a las API del servicio Azure Maps, necesita una cuenta de Maps y una clave. Siga las instrucciones que se indican en [Creación de una cuenta](quick-demo-map-app.md#create-an-account-with-azure-maps) para crear una suscripción a una cuenta de Azure Maps y siga los pasos para [obtener la clave principal](quick-demo-map-app.md#get-the-primary-key-for-your-account) de la cuenta. Para más información sobre la autenticación en Azure Maps, consulte [Administración de la autenticación en Azure Maps](./how-to-manage-authentication.md).

Este artículo utiliza la [aplicación Postman](https://www.getpostman.com/apps) para generar llamadas REST. Puede usar cualquier entorno de desarrollo de API que prefiera.

## <a name="using-fuzzy-search"></a>Uso de la búsqueda aproximada

La API predeterminada del servicio de búsqueda es [búsqueda aproximada](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) y es útil cuando no se sabe cuáles son las entradas de usuario para una consulta de búsqueda. La API combina la búsqueda de POI y geocodificación en una "búsqueda de una sola línea" canónica. Por ejemplo, la API puede controlar las entradas de cualquier dirección o una combinación de tokens de POI. La API también se puede ponderar con una posición contextual (par de latitud y longitud), completamente restringida por una coordenada y un radio, o se puede ejecutar de forma más general sin ningún punto de anclaje de polarización geográfica.

La mayoría de las consultas de búsqueda tienen como valor predeterminado `maxFuzzyLevel=1` para aumentar el rendimiento y reducir los resultados inusuales. Este valor predeterminado puede invalidarse según requiera la solicitud mediante la transmisión del parámetro de consulta `maxFuzzyLevel=2` o `3`.

### <a name="search-for-an-address-using-fuzzy-search"></a>Buscar una dirección mediante la búsqueda aproximada

1. Abra la aplicación Postman, haga clic en Nuevo | Crear nuevo y seleccione **Solicitud GET**. Escriba un nombre de solicitud de **búsqueda aproximada**, seleccione una colección o carpeta donde guardarlo y haga clic en **Guardar**.

2. En la pestaña Builder, seleccione el método HTTP **GET** y escriba la dirección URL de solicitud para el punto de conexión de API.

    ![Búsqueda aproximada](./media/how-to-search-for-address/fuzzy_search_url.png)

    | Parámetro | Valor sugerido |
    |---------------|------------------------------------------------|
    | HTTP method | GET |
    | URL de la solicitud | [https://atlas.microsoft.com/search/fuzzy/json?](https://atlas.microsoft.com/search/fuzzy/json?) |
    | Authorization | Sin autenticación |

    El atributo **json** de la ruta de acceso URL determina el formato de respuesta. Usa json en todo este artículo para una fácil utilización y legibilidad. Puede encontrar los formatos de respuesta disponibles en la definición de **obtención de búsqueda aproximada** de la [referencia de la API funcional de Maps](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy).

3. Haga clic en **Params** (Parámetros) y especifique los siguientes pares clave-valor para utilizar como parámetros de consulta o ruta de acceso en la dirección URL de la solicitud:

    ![Búsqueda aproximada](./media/how-to-search-for-address/fuzzy_search_params.png)

    | Clave | Value |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | subscription-key | \<la clave de Azure Maps\> |
    | Query | pizza |

4. Haga clic en **Enviar** y revise el cuerpo de la respuesta.

    La cadena de consulta ambigua de "pizza" devolvió 10 [resultados de punto de interés](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi#searchpoiresponse) (POI) con categorías correspondientes a "pizza" y "restaurante". Cada resultado devuelve una dirección, valores de latitud y longitud, ventanilla y puntos de entrada para la ubicación.
  
    Los resultados son variados para esta consulta, no se asocian a ninguna ubicación de referencia determinada. Puede usar el parámetro **countrySet** para especificar solo los países o las regiones para los que su aplicación necesita cobertura, ya que el comportamiento predeterminado es buscar en todo el mundo, lo que puede provocar la devolución de resultados innecesarios.

5. Agregue la siguiente clave o par clave-valor a la sección **Params** y haga clic en **Enviar**:

    | Clave | Value |
    |------------------|-------------------------|
    | countrySet | US |
  
    Ahora los resultados están limitados por el código de país y la consulta devuelve pizzerías de los Estados Unidos.
  
    Para proporcionar resultados para una ubicación, puede consultar un punto de interés y usar los valores de latitud y longitud devueltos en la llamada al servicio de búsqueda aproximada. En este caso, usó el servicio de búsqueda para devolver la ubicación de Space Needle de Seattle y utilizó los valores de latitud y longitud para orientar la búsqueda.
  
6. En Params (Parámetros), escriba los siguientes pares clave-valor y haga clic en **Enviar**:

    ![Búsqueda aproximada](./media/how-to-search-for-address/fuzzy_search_latlon.png)
  
    | Clave | Value |
    |-----|------------|
    | lat | 47,620525 |
    | lon | -122,349274 |

## <a name="search-for-address-properties-and-coordinates"></a>Buscar las coordenadas y las propiedades de dirección

Puede pasar una dirección completa o parcial a la API de búsqueda de direcciones y recibir una respuesta que incluye propiedades de la dirección detalladas como la municipalidad o la subdivisión, así como valores posicionales de latitud y longitud.

1. En Postman, haga clic en **Nueva solicitud** | **Solicitud GET** y asígnele el nombre **Búsqueda de direcciones**.
2. En la pestaña Builder, seleccione el método HTTP **GET**, escriba la dirección URL de solicitud para el punto de conexión de API y seleccione un protocolo de autorización, si procede.

    ![Búsqueda de direcciones](./media/how-to-search-for-address/address_search_url.png)
  
    | Parámetro | Valor sugerido |
    |---------------|------------------------------------------------|
    | HTTP method | GET |
    | URL de la solicitud | [https://atlas.microsoft.com/search/address/json?](https://atlas.microsoft.com/search/address/json?) |
    | Authorization | Sin autenticación |

3. Haga clic en **Params** (Parámetros) y especifique los siguientes pares clave-valor para utilizar como parámetros de consulta o ruta de acceso en la dirección URL de la solicitud:
  
    ![Búsqueda de direcciones](./media/how-to-search-for-address/address_search_params.png)
  
    | Clave | Value |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | subscription-key | \<la clave de Azure Maps\> |
    | Query | 400 Broad St, Seattle, WA 98109 |
  
4. Haga clic en **Enviar** y revise el cuerpo de la respuesta.
  
    En este caso, especificó una consulta de dirección completa y recibe un solo resultado en el cuerpo de respuesta.
  
5. En Params (Parámetros), edite la cadena de consulta de modo que tenga el siguiente valor:
    ```plaintext
        400 Broad, Seattle
    ```

6. Agregue la siguiente clave o par clave-valor a la sección **Params** y haga clic en **Enviar**:

    | Clave | Value |
    |-----|------------|
    | typeahead | true |

    La marca **typeahead** indica a la API de búsqueda de direcciones que trate la consulta como una entrada parcial y devuelva una matriz de valores predictivos.

## <a name="search-for-a-street-address-using-reverse-address-search"></a>Buscar una dirección mediante la búsqueda de direcciones inversa

1. En Postman, haga clic en **Nueva solicitud** | **Solicitud GET** y asígnele el nombre **Búsqueda de direcciones inversa**.

2. En la pestaña Builder, seleccione el método HTTP **GET** y escriba la dirección URL de solicitud para el punto de conexión de API.
  
    ![Dirección URL de Búsqueda de direcciones inversa](./media/how-to-search-for-address/reverse_address_search_url.png)
  
    | Parámetro | Valor sugerido |
    |---------------|------------------------------------------------|
    | HTTP method | GET |
    | URL de la solicitud | [https://atlas.microsoft.com/search/address/reverse/json?](https://atlas.microsoft.com/search/address/reverse/json?) |
    | Authorization | Sin autenticación |
  
3. Haga clic en **Params** (Parámetros) y especifique los siguientes pares clave-valor para utilizar como parámetros de consulta o ruta de acceso en la dirección URL de la solicitud:
  
    ![Parámetros de Búsqueda de direcciones inversa](./media/how-to-search-for-address/reverse_address_search_params.png)
  
    | Clave | Value |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | subscription-key | \<la clave de Azure Maps\> |
    | Query | 47,591180,-122,332700 |
  
4. Haga clic en **Enviar** y revise el cuerpo de la respuesta.

    La respuesta incluye información de dirección clave sobre Safeco Field.
  
5. Agregue la siguiente clave o par clave-valor a la sección **Params** y haga clic en **Enviar**:

    | Clave | Value |
    |-----|------------|
    | number | true |

    Si el parámetro de consulta [number](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) se envía con la solicitud, la respuesta puede incluir el lado de la calle (izquierda o derecha) y también una posición de desplazamiento para ese número.
  
6. Agregue la siguiente clave o par clave-valor a la sección **Params** y haga clic en **Enviar**:

    | Clave | Value |
    |-----|------------|
    | returnSpeedLimit | true |
  
    Cuando se establece el parámetro de consulta [returnSpeedLimit](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse), la respuesta devuelve el límite de velocidad publicado.

7. Agregue la siguiente clave o par clave-valor a la sección **Params** y haga clic en **Enviar**:

    | Clave | Value |
    |-----|------------|
    | returnRoadUse | true |

    Cuando se establece el parámetro de consulta [returnRoadUse](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse), la respuesta devuelve la matriz de uso de carretera para códigos geográficos inversos a nivel de calle.

8. Agregue la siguiente clave o par clave-valor a la sección **Params** y haga clic en **Enviar**:

    | Clave | Value |
    |-----|------------|
    | roadUse | true |

    Puede restringir la consulta de código geográfico inversa a un tipo específico de uso de carretera mediante el parámetro de consulta [roadUse](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse).
  
## <a name="search-for-the-cross-street-using-reverse-address-cross-street-search"></a>Buscar la calle transversal mediante la búsqueda de calles transversales de direcciones inversa

1. En Postman, haga clic en **Nueva solicitud** | **Solicitud GET** y asígnele el nombre **Búsqueda de calles transversales de direcciones inversa**.

2. En la pestaña Builder, seleccione el método HTTP **GET** y escriba la dirección URL de solicitud para el punto de conexión de API.
  
    ![Búsqueda de calles transversales de direcciones inversa](./media/how-to-search-for-address/reverse_address_search_url.png)
  
    | Parámetro | Valor sugerido |
    |---------------|------------------------------------------------|
    | HTTP method | GET |
    | URL de la solicitud | [https://atlas.microsoft.com/search/address/reverse/crossstreet/json?](https://atlas.microsoft.com/search/address/reverse/crossstreet/json?) |
    | Authorization | Sin autenticación |
  
3. Haga clic en **Params** (Parámetros) y especifique los siguientes pares clave-valor para utilizar como parámetros de consulta o ruta de acceso en la dirección URL de la solicitud:
  
    | Clave | Value |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | subscription-key | \<la clave de Azure Maps\> |
    | Query | 47,591180,-122,332700 |
  
4. Haga clic en **Enviar** y revise el cuerpo de la respuesta.

## <a name="next-steps"></a>Pasos siguientes

- Explore la documentación de la API del [servicio de búsqueda de Azure Maps](https://docs.microsoft.com/rest/api/maps/search).
