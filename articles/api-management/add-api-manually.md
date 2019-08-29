---
title: Adición manual de una API mediante Azure Portal| Microsoft Docs
description: En este tutorial se muestra cómo utilizar la API de administración (APIM) para agregar manualmente una API.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 08/27/2018
ms.author: apimpm
ms.openlocfilehash: 5440333360549c5df2da57c97b24dcc77436ba4b
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/28/2019
ms.locfileid: "70072700"
---
# <a name="add-an-api-manually"></a>Adición manual de una API

En los pasos de este artículo se explica cómo usar Azure Portal para agregar manualmente una API a la instancia de API Management (APIM). Un escenario común es si quiere crear una API en blanco y definirla manualmente cuando desee simular la API. Para obtener más información sobre la simulación de una API, consulte el artículo sobre cómo [simular respuestas de API](mock-api-responses.md).

Si desea importar una API existente, consulte la sección de [temas relacionados](#related-topics).

En este artículo, creará una API en blanco y especificará [httpbin.org](https://httpbin.org) (un servicio de prueba público) como API de back-end.

## <a name="prerequisites"></a>Requisitos previos

Complete el siguiente inicio rápido: [Creación de una instancia de Azure API Management](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-an-api"></a>Creación de una API

1. Seleccione **API** en **API MANAGEMENT**.
2. En el menú izquierdo, seleccione **+ Agregar API**.
3. Seleccione **API en blanco** en la lista.

    ![API en blanco](media/add-api-manually/blank-api.png)
4. Escriba la configuración de la API.

    |**Nombre**|**Valor**|**Descripción**|
    |---|---|---|
    |**Nombre para mostrar**|*API en blanco*|El nombre se muestra en el Portal para desarrolladores.|
    |**Nombre**|*blank-api*|Proporciona un nombre único para la API.|
    |**Dirección URL de servicio web** (opcional)|*https://httpbin.org*| Si desea simular una API, recomendamos no escribir nada. <br/>En este caso, escribimos [https://httpbin.org](https://httpbin.org). Se trata de un servicio de prueba público. <br/>Si desea importar una API que se asigne automáticamente a un back-end, consulte uno de los temas de la sección de [temas relacionados](#related-topics).|
    |**Esquema URL**|*HTTPS*|En este caso, aunque el back-end tenga acceso HTTP no seguro, especifique un acceso HTTPS de APIM seguro al back-end. <br/>Este tipo de escenario (HTTPS a HTTP) se denomina "terminación HTTPS". Podría hacerlo si su API se encuentra dentro de una red virtual (donde sabe que el acceso está protegido aunque no se utilice HTTPS). <br/>Puede usar la terminación HTTPS para ahorrar algunos ciclos de CPU.|
    |**Sufijo de dirección URL**|*hbin*| El sufijo es un nombre que identifica esta API concreta en esta instancia de APIM. Debe ser exclusivo en esta instancia de APIM.|
    |**Productos**|*Sin límite*|Publique la API asociándola a un producto. Si desea que la API se publique y esté disponible para los desarrolladores, agréguela a un producto. Puede hacerlo durante la creación de la API o configurarla más adelante.<br/><br/>Los productos son asociaciones de una o varias API. Puede incluir varias API y ofrecerlas a los desarrolladores mediante el portal para desarrolladores. <br/>En primer lugar, los desarrolladores deben suscribirse a un producto para acceder a la API. Al suscribirse, obtienen una clave de suscripción que funciona con cualquier API de ese producto. Si creó la instancia de APIM, ya es un administrador, así que, de forma predeterminada, está suscrito a todos los productos.<br/><br/> De forma predeterminada, cada instancia de API Management incluye dos productos de ejemplo: **Starter** y **Unlimited**.| 
5. Seleccione **Crear**.

En este momento, no tiene ninguna operación en APIM que se asigne a las operaciones en la API de back-end. Si se llama a una operación que se expone a través del back-end, pero no a través de la APIM, obtendrá una respuesta **404**.

>[!NOTE] 
> De forma predeterminada, cuando agrega una API, aunque esté conectado a algún servicio back-end, APIM no expondrá ninguna operación hasta que incorpore a la lista blanca. Para incluir en la lista blanca una operación del servicio de back-end, cree una operación de APIM que se asigne a la operación de back-end.

## <a name="add-and-test-an-operation"></a>Adición y prueba de una operación

En esta sección se muestra cómo agregar una operación "/get" para asignarla a la operación de back-end "http://httpbin.org/get".

### <a name="add-an-operation"></a>Agregar una operación

1. Seleccione la API que creó en los pasos anteriores.
2. Haga clic en **+ Agregar operación**.
3. En la **URL**, seleccione **GET** y escriba " */get*" en el recurso.
4. Escriba "*FetchData*" en **Nombre para mostrar**.
5. Seleccione **Guardar**.

### <a name="test-an-operation"></a>Probar una operación

Pruebe la operación en Azure Portal. También puede probarla en el **Portal para desarrolladores**.

1. Seleccione la pestaña **Prueba**.
2. Seleccione **FetchData**.
3. Presione **Enviar**.

Se muestra la respuesta que genera la operación "http://httpbin.org/get". Si desea transformar las operaciones, consulte [Transformación y protección de una API](transform-api.md).

## <a name="add-and-test-a-parameterized-operation"></a>Adición y prueba de una operación con parámetros

En esta sección se explica cómo agregar una operación que toma un parámetro. En este caso, se asigna la operación a "http://httpbin.org/status/200".

### <a name="add-the-operation"></a>Adición de la operación

1. Seleccione API que creó en los pasos anteriores.
2. Haga clic en **+ Agregar operación**.
3. En la **URL**, seleccione **GET** y escriba " */status/{code}* " en el recurso. Si lo desea, puede proporcionar cierta información asociada a este parámetro. Por ejemplo, escriba "*Número*" en **TYPE** y "*200*" (valor predeterminado) en **VALUES**.
4. Escriba "GetStatus" en **Nombre para mostrar**.
5. Seleccione **Guardar**.

### <a name="test-the-operation"></a>Prueba de la operación 

Pruebe la operación en Azure Portal.  También puede probarla en el **Portal para desarrolladores**.

1. Seleccione la pestaña **Prueba**.
2. Seleccione **GetStatus**. De forma predeterminada, el valor de código se establece en "*200*". Puede cambiarlo para probar otros valores. Por ejemplo, escriba "*418*".
3. Presione **Enviar**.

    Se muestra la respuesta que genera la operación "http://httpbin.org/status/200". Si desea transformar las operaciones, consulte [Transformación y protección de una API](transform-api.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Transformación y protección de una API publicada](transform-api.md)
