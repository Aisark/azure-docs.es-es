---
title: Importación y publicación de la primera API en Azure API Management | Microsoft Docs
description: Obtenga información sobre cómo importar y publicar la primera API con API Management.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 02/24/2019
ms.author: apimpm
ms.openlocfilehash: 6a1ae2966e8d5535a5fd9aeffb5ddc3a788f85ee
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/28/2019
ms.locfileid: "70072103"
---
# <a name="import-and-publish-your-first-api"></a>Importación y publicación de la primera API 

En este tutorial se explica cómo importar una API de back-end de Especificación OpenAPI que reside en https://conferenceapi.azurewebsites.net?format=json. Esta API de back-end la proporciona Microsoft y se hospeda en Azure. 

Una vez que la API de back-end se importa en Management API (APIM), la API de APIM se convierte en una fachada de la API de back-end. En el momento en que se importa la API de back-end, el origen de la API y la API de APIM son idénticos. APIM permite personalizar la fachada según sus necesidades sin tocar la API de back-end. Para obtener más información, consulte [Transformación y protección de una API](transform-api.md). 

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Importación de la primera API
> * Prueba de la API en Azure Portal
> * Pruebe la API en el Portal para desarrolladores

![Nueva API](./media/api-management-get-started/created-api.png)

## <a name="prerequisites"></a>Requisitos previos

+ Conocer la [terminología de API Management de Azure](api-management-terminology.md).
+ Complete el siguiente inicio rápido: [Creación de una instancia de Azure API Management](get-started-create-service-instance.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-api"> </a>Importación y publicación de una API de back-end

Esta sección explica cómo importar y publicar una API de back-end de Especificación OpenAPI.
 
1. Seleccione **API** en **API MANAGEMENT**.
2. Seleccione **Especificación OpenAPI** en la lista y haga clic en **Completa** en el elemento emergente.

    ![Creación de una API](./media/api-management-get-started/create-api.png)

    Puede establecer los valores de API durante la creación o luego accediendo a la pestaña **Configuración**. El asterisco rojo junto a un campo indica que el campo es obligatorio.

    Utilice los valores de la tabla siguiente para crear su primera API.

    | Configuración                   | Valor                                              | DESCRIPCIÓN                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
    |---------------------------|----------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | **Especificación OpenAPI** | https://conferenceapi.azurewebsites.net?format=json | Hace referencia al servicio que implementa la API. API Management envía las solicitudes a esta dirección.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
    | **Nombre para mostrar**          | *API de conferencia de demostración* (API de conferencia de demostración)                              | Si presiona la tecla Tab después de escribir la dirección URL del servicio, APIM rellenará este campo en función de lo que aparece en el JSON. <br/>El nombre se muestra en el Portal para desarrolladores.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
    | **Nombre**                  | *demo-conference-api*                              | Proporciona un nombre único para la API. <br/>Si presiona la tecla Tab después de escribir la dirección URL del servicio, APIM rellenará este campo en función de lo que aparece en el JSON.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
    | **Descripción**           | Proporcione una descripción opcional de la API.        | Si presiona la tecla Tab después de escribir la dirección URL del servicio, APIM rellenará este campo en función de lo que aparece en el JSON.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
    | **Esquema URL**            | *HTTPS*                                            | Determina los protocolos que se pueden usar para acceder a la API.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
    | **Sufijo de dirección URL de API**        | *conference*                                       | El sufijo se anexa a la dirección URL base del servicio API Management. API Management distingue las API por su sufijo, por lo que el sufijo debe ser único para cada API de un publicador determinado.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
    | **Productos**              | *Sin límite*                                        | Los productos son asociaciones de una o varias API. Puede incluir varias API en un producto y ofrecerlas a los desarrolladores mediante el portal para desarrolladores. <br/>Para publicar la API, hay que asociarla a un producto (en este ejemplo, *Unlimited*). Para agregar esta nueva API a un producto, escriba el nombre del producto (también puede hacerlo más tarde desde la página de **configuración**). Este paso se puede repetir varias veces para agregar la API a varios productos.<br/>Para acceder a la API, los desarrolladores primero deben suscribirse a un producto. Al suscribirse, obtienen una clave de suscripción que funciona con cualquier API de ese producto. <br/> Si creó la instancia de APIM, ya es un administrador, así que está suscrito a todos los productos.<br/> De forma predeterminada, cada instancia de API Management incluye dos productos de ejemplo: **Starter** y **Unlimited**. |
    | **Etiquetas**                  |                                                    | Etiquetas para organizar las API. Pueden usarse etiquetas para buscar, agrupar o filtrar.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
    | **¿Definir versión de esta API?**     |                                                    | Para más información sobre las versiones, consulte [Publicación de varias versiones de la API](api-management-get-started-publish-versions.md).                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |

    >[!NOTE]
    > Para publicar la API, debe asociarla a un producto. Puede hacerlo desde la **página de configuración**.

3. Seleccione **Crear**.

> [!TIP]
> Si surgen problemas con la importación de su propia definición de API, [consulte la lista de problemas y restricciones conocidos](api-management-api-import-restrictions.md).

## <a name="test-the-new-apim-api-in-the-azure-portal"></a>Prueba de la nueva API APIM en Azure Portal

![Prueba del mapa de API](./media/api-management-get-started/01-import-first-api-01.png)

Se puede llamar a las operaciones directamente desde Azure Portal, lo que proporciona una forma cómoda de ver y probar las operaciones de una API.

1. Seleccione la API que ha creado en los pasos anteriores (desde la pestaña **API**).
2. Presione la pestaña **Prueba**.
3. Haga clic en **GetSpeakers**. En la página se muestran los campos de los parámetros de consulta, que, en este caso, no tiene ninguno, y los encabezados. Uno de los encabezados es "Ocp-Apim-Suscripción-Key", para la clave de suscripción del producto que está asociado a esta API. La clave se rellena automáticamente.
4. Presione **Enviar**.

    Back-end responde con **200 Aceptar** y algunos datos.

## <a name="call-operation"></a>Llamada a una operación desde el portal para desarrolladores

También se pueden llamar a las operaciones desde el **portal para desarrolladores** para probar las API.

1. Desplácese hasta el **portal para desarrolladores**.

    ![Portal para desarrolladores](./media/api-management-get-started/developer-portal.png)

2. Seleccione **API**, haga clic en **Demo Conference API** y, después, en **GetSpeakers**.

    En la página se muestran los campos de los parámetros de consulta, que, en este caso, no tiene ninguno, y los encabezados. Uno de los encabezados es "Ocp-Apim-Suscripción-Key", para la clave de suscripción del producto que está asociado a esta API. Si ha creado la instancia APIM, ya es administrador, por lo que la clave se rellena automáticamente.

3. Presione **Try it** (Probarlo).
4. Presione **Enviar**.

    Después de invocar una operación, el portal para desarrolladores muestra las respuestas.  

## <a name="next-steps"> </a>Pasos siguientes

En este tutorial aprendió lo siguiente:

> [!div class="checklist"]
> * Importación de la primera API
> * Prueba de la API en Azure Portal
> * Pruebe la API en el Portal para desarrolladores

Avance hasta el siguiente tutorial:

> [!div class="nextstepaction"]
> [Creación y publicación de un producto](api-management-howto-add-products.md)
