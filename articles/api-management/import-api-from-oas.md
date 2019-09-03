---
title: Importación de una especificación OpenAPI mediante Azure Portal | Microsoft Docs
description: Obtenga información sobre cómo importar una especificación OpenAPI con API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 07/12/2019
ms.author: apimpm
ms.openlocfilehash: b96cfe9813eef9caf1f1f21e43470a23c7032cb1
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/28/2019
ms.locfileid: "70072120"
---
# <a name="import-an-openapi-specification"></a>Importación de una especificación OpenAPI

En este artículo se explica cómo importar una API de back-end de Especificación OpenAPI que reside en https://conferenceapi.azurewebsites.net?format=json. Esta API de back-end la proporciona Microsoft y se hospeda en Azure. El artículo también muestra cómo probar la API de APIM.

> [!IMPORTANT]
> Consulte este [documento](https://blogs.msdn.microsoft.com/apimanagement/2018/04/11/important-changes-to-openapi-import-and-export/) para obtener información importante y consejos relacionados con la importación de OpenAPI.

En este artículo, aprenderá a:

> [!div class="checklist"]
> * Importación de una API de back-end de la especificación OpenAPI
> * Prueba de la API en Azure Portal
> * Pruebe la API en el Portal para desarrolladores

## <a name="prerequisites"></a>Requisitos previos

Complete el siguiente inicio rápido: [Creación de una instancia de Azure API Management](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-api"> </a>Importación y publicación de una API de back-end

1. Seleccione **API** en **API MANAGEMENT**.
2. Seleccione **Especificación OpenAPI** de la lista **Add a new API** (Agregar una nueva API).
    ![Especificación OpenAPI](./media/import-api-from-oas/oas-api.png)
3. Especifique la configuración adecuada. Puede establecer los valores de API durante la creación. Como alternativa, puede establecer algunos de ellos más adelante accediendo a la pestaña **Configuración**. <br/> Si presiona **Tab**, algunos de los campos, o todos, se rellenan con la información del servicio de back-end especificado.

    ![Creación de una API](./media/api-management-get-started/create-api.png)

    |Configuración|Valor|DESCRIPCIÓN|
    |---|---|---|
    |**Especificación OpenAPI**|https://conferenceapi.azurewebsites.net?format=json|Hace referencia al servicio que implementa la API. API Management envía las solicitudes a esta dirección.|
    |**Nombre para mostrar**|*API de conferencia de demostración* (API de conferencia de demostración)|Si presiona la tecla Tab después de escribir la dirección URL del servicio, APIM rellenará este campo en función de lo que aparece en el JSON. <br/>El nombre se muestra en el Portal para desarrolladores.|
    |**Nombre**|*demo-conference-api*|Proporciona un nombre único para la API. <br/>Si presiona la tecla Tab después de escribir la dirección URL del servicio, APIM rellenará este campo en función de lo que aparece en el JSON.|
    |**Descripción**|Proporcione una descripción opcional de la API.|Si presiona la tecla Tab después de escribir la dirección URL del servicio, APIM rellenará este campo en función de lo que aparece en el JSON.|
    |**Sufijo de dirección URL de API**|*conference*|El sufijo se anexa a la dirección URL base del servicio API Management. API Management distingue las API por su sufijo, por lo que el sufijo debe ser único para cada API de un publicador determinado.|
    |**Esquema URL**|*HTTPS*|Determina los protocolos que se pueden usar para acceder a la API. |
    |**Productos**|*Sin límite*| Publique la API asociándola a un producto. Para agregar, opcionalmente, esta nueva API a un producto, escriba el nombre del producto. Este paso se puede repetir varias veces para agregar la API a varios productos.<br/>Los productos son asociaciones de una o varias API. Puede incluir varias API y ofrecerlas a los desarrolladores mediante el portal para desarrolladores. En primer lugar, los desarrolladores deben suscribirse a un producto para acceder a la API. Al suscribirse, obtienen una clave de suscripción que funciona con cualquier API de ese producto. Si creó la instancia de APIM, ya es un administrador, así que, de forma predeterminada, está suscrito a todos los productos.<br/> De forma predeterminada, cada instancia de API Management incluye dos productos de ejemplo: **Starter** y **Unlimited**. |

4. Seleccione **Crear**.

> [!NOTE]
> Las limitaciones de la importación de API se documentan en [otro artículo](api-management-api-import-restrictions.md).

## <a name="test-the-new-apim-api-in-the-azure-portal"></a>Prueba de la nueva API APIM en Azure Portal

Se puede llamar a las operaciones directamente desde Azure Portal, lo que proporciona una forma cómoda de ver y probar las operaciones de una API.

![Prueba de la API](./media/api-management-get-started/01-import-first-api-01.png)

1. Seleccione la API que creó en los pasos anteriores.
2. Presione la pestaña **Prueba**.
3. Haga clic en **GetSpeakers**.

    La página muestra los campos de los parámetros de consulta, pero, en este caso, no tiene ninguno. La página también muestra los campos de los encabezados. Uno de los encabezados es "Ocp-Apim-Suscripción-Key", para la clave de suscripción del producto que está asociado a esta API. Si ha creado la instancia APIM, ya es administrador, por lo que la clave se rellena automáticamente.
4. Presione **Enviar**.

    Back-end responde con **200 Aceptar** y algunos datos.

## <a name="call-operation"> </a>Llamada a una operación desde el portal para desarrolladores

También se pueden llamar a las operaciones desde **portal para desarrolladores** para probar las API.

1. Seleccione la API que creó en el paso "Importación y publicación de una API de back-end".
2. Presione **Portal para desarrolladores**.

    ![Prueba en el Portal para desarrolladores](./media/api-management-get-started/developer-portal.png)

    Se abre el sitio "Portal para desarrolladores".
3. Seleccione **API**.
4. Seleccione **Demo Conference API** (API de conferencia de demostración).
5. Haga clic en **GetSpeakers**.

    La página muestra los campos de los parámetros de consulta, pero, en este caso, no tiene ninguno. La página también muestra los campos de los encabezados. Uno de los encabezados es "Ocp-Apim-Suscripción-Key", para la clave de suscripción del producto que está asociado a esta API. Si ha creado la instancia APIM, ya es administrador, por lo que la clave se rellena automáticamente.
6. Presione **Try it** (Probarlo).
7. Presione **Enviar**.

    Después de invocar una operación, el portal para desarrolladores mostrará el **estado de respuesta**, los **encabezados de respuesta** y el **contenido de respuesta**.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Transformación y protección de una API publicada](transform-api.md)
