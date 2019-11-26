---
title: 'Llamada a puntos de conexión HTTP y HTTPS: Azure Logic Apps'
description: Envío de solicitudes salientes a puntos de conexión HTTP y HTTPS mediante Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 07/05/2019
tags: connectors
ms.openlocfilehash: 4f585778808b133fe5c731ab6a3189594cbfc0f9
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/23/2019
ms.locfileid: "72804229"
---
# <a name="send-outgoing-calls-to-http-or-https-endpoints-by-using-azure-logic-apps"></a>Envío de llamadas salientes a puntos de conexión HTTP o HTTPS mediante Azure Logic Apps

Con [Azure Logic Apps](../logic-apps/logic-apps-overview.md) y el desencadenador o la acción HTTP integrados, puede crear tareas y flujos de trabajo automatizados que envían periódicamente solicitudes a cualquier punto de conexión HTTP o HTTPS. En cambio, para recibir llamadas HTTP o HTTPS entrantes o responder a ellas, use el [desencadenador de solicitud o la acción de respuesta integrados](../connectors/connectors-native-reqres.md).

Por ejemplo, para supervisar el punto de conexión de servicio de su sitio web puede comprobarlo según una programación especificada. Cuando se produce un evento específico en ese punto de conexión, por ejemplo, que su sitio web deje de funcionar, el evento desencadena el flujo de trabajo de la aplicación lógica y ejecuta las acciones especificadas.

Puede usar el desencadenador HTTP como primer paso del flujo de trabajo para comprobar o *sondear* un punto de conexión según una programación periódica. En cada comprobación, el desencadenador envía una llamada o *solicitud* al punto de conexión. La respuesta del punto de conexión determina si el flujo de trabajo de la aplicación lógica se ejecuta. El desencadenador pasa a lo largo de todo el contenido desde la respuesta hasta las acciones en la aplicación lógica.

Puede usar la acción HTTP como cualquier otro paso del flujo de trabajo para llamar al extremo cuando desee. La respuesta del punto de conexión determina cómo se ejecutan las acciones restantes de su flujo de trabajo.

En función de la capacidad del punto de conexión de destino, el conector HTTP admite las versiones de Seguridad de la capa de transporte (TLS) 1.0, 1.1 y 1.2. Logic Apps negocia con el punto de conexión usando la versión compatible más alta posible. Por ejemplo, si el punto de conexión admite la versión 1.2, el conector usa esta versión primero. De lo contrario, el conector utiliza la siguiente versión compatible más alta.

## <a name="prerequisites"></a>Requisitos previos

* Una suscripción de Azure. Si no tiene una suscripción de Azure, [regístrese para obtener una cuenta gratuita de Azure](https://azure.microsoft.com/free/).

* La dirección URL del punto de conexión de destino al que quiere llamar.

* Conocimientos básicos acerca de [cómo crear aplicaciones lógicas](../logic-apps/quickstart-create-first-logic-app-workflow.md). Si no está familiarizado con las aplicaciones lógicas, consulte [¿Qué es Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

* La aplicación lógica desde la que quiere llamar al punto de conexión de destino. Para comenzar con el desencadenador HTTP, [cree una aplicación lógica en blanco](../logic-apps/quickstart-create-first-logic-app-workflow.md). Para usar la acción de HTTP, inicie la aplicación lógica con el desencadenador que quiera. En este ejemplo se usa el desencadenador HTTP como primer paso.

## <a name="add-an-http-trigger"></a>Agregar un desencadenador HTTP

Este desencadenador integrado realiza una llamada HTTP a la dirección URL especificada para un punto de conexión y devuelve una respuesta.

1. Inicie sesión en el [Azure Portal](https://portal.azure.com). Abra la aplicación lógica en blanco en el diseñador de aplicación lógica.

1. En **Elegir una acción**, en el cuadro de búsqueda, escriba "http" como filtro. En la lista **Desencadenadores**, seleccione el desencadenador **HTTP**.

   ![Selección del desencadenador HTTP](./media/connectors-native-http/select-http-trigger.png)

   En este ejemplo se cambia el nombre del desencadenador a "desencadenador HTTP" para que el paso tenga un nombre más descriptivo. En el ejemplo, más adelante se agrega una acción HTTP, y ambos nombres deben ser únicos.

1. Proporcione los valores de los [parámetros del desencadenador HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md##http-trigger) que quiere incluir en la llamada al punto de conexión de destino. Establezca la periodicidad con la que quiere que el desencadenador compruebe el punto de conexión de destino.

   Si selecciona un tipo de autenticación distinto de **Ninguno**, la configuración de autenticación difiere en función de la selección. Para obtener más información, consulte [Incorporación de la autenticación en las llamadas salientes](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound) más adelante en este tema.

   ![Introducción de los parámetros de desencadenador HTTP](./media/connectors-native-http/http-trigger-parameters.png)

1. Para agregar otros parámetros disponibles, abra la lista **Agregar nuevo parámetro** y seleccione los parámetros que quiera.

1. Continúe creando el flujo de trabajo de la aplicación lógica con acciones que se ejecuten cuando se activa el desencadenador.

1. Cuando haya terminado, recuerde guardar la aplicación lógica. En la barra de herramientas del diseñador, seleccione **Save** (Guardar).

## <a name="add-an-http-action"></a>Adición de una acción HTTP

Esta acción integrada realiza una llamada HTTP a la dirección URL especificada para un punto de conexión y devuelve una respuesta.

1. Inicie sesión en el [Azure Portal](https://portal.azure.com). Abra la aplicación lógica en el Diseñador de aplicaciones lógicas.

   En este ejemplo se usa el desencadenador HTTP como primer paso.

1. En el paso en el que quiera agregar la acción HTTP, seleccione **Nuevo paso**.

   Para agregar una acción entre un paso y otro, mueva el puntero sobre la flecha entre ellos. Seleccione el signo más ( **+** ) que aparece y, luego, seleccione **Agregar una acción**.

1. En **Elegir una acción**, en el cuadro de búsqueda, escriba "http" como filtro. En la lista **Acciones**, seleccione la acción **HTTP**.

   ![Selección de la acción de HTTP](./media/connectors-native-http/select-http-action.png)

   En este ejemplo se cambia el nombre de la acción por "acción HTTP" para que el paso tenga un nombre más descriptivo.

1. Proporcione los valores de los [parámetros de la acción HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md##http-action) que quiere incluir en la llamada al punto de conexión de destino.

   Si selecciona un tipo de autenticación distinto de **Ninguno**, la configuración de autenticación difiere en función de la selección. Para obtener más información, consulte [Incorporación de la autenticación en las llamadas salientes](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound) más adelante en este tema.

   ![Introducción de los parámetros de acción de HTTP](./media/connectors-native-http/http-action-parameters.png)

1. Para agregar otros parámetros disponibles, abra la lista **Agregar nuevo parámetro** y seleccione los parámetros que quiera.

1. Cuando haya terminado, recuerde guardar la aplicación lógica. En la barra de herramientas del diseñador, seleccione **Save** (Guardar).

## <a name="content-with-multipartform-data-type"></a>Contenido con el tipo multipart/form-data

Para controlar el contenido que tiene el tipo `multipart/form-data` en las solicitudes HTTP, puede agregar un objeto JSON que incluya los atributos `$content-type` y `$multipart` al cuerpo de la solicitud HTTP con este formato.

```json
"body": {
   "$content-type": "multipart/form-data",
   "$multipart": [
      {
         "body": "<output-from-trigger-or-previous-action>",
         "headers": {
            "Content-Disposition": "form-data; name=file; filename=<file-name>"
         }
      }
   ]
}
```

Por ejemplo, supongamos que tiene una aplicación lógica que envía una solicitud HTTP POST para un archivo de Excel a un sitio web mediante la API de ese sitio, que admite el tipo `multipart/form-data`. Este es el aspecto de esta acción:

![Datos de formulario de varias partes](./media/connectors-native-http/http-action-multipart.png)

Este es el mismo ejemplo que muestra la definición de JSON de la acción HTTP en la definición de flujo de trabajo subyacente:

```json
{
   "HTTP_action": {
      "body": {
         "$content-type": "multipart/form-data",
         "$multipart": [
            {
               "body": "@trigger()",
               "headers": {
                  "Content-Disposition": "form-data; name=file; filename=myExcelFile.xlsx"
               }
            }
         ]
      },
      "method": "POST",
      "uri": "https://finance.contoso.com"
   },
   "runAfter": {},
   "type": "Http"
}
```

## <a name="connector-reference"></a>Referencia de conectores

Para obtener más información acerca de los parámetros de desencadenador y acción, consulte las siguientes secciones:

* [Parámetros de desencadenador HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md##http-trigger)
* [Parámetros de acción HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md##http-action)

### <a name="output-details"></a>Detalles de salida

Aquí tiene más información acerca de las salidas de un desencadenador o una acción HTTP, que devuelve esta información:

| Nombre de propiedad | type | DESCRIPCIÓN |
|---------------|------|-------------|
| headers | object | Encabezados de la solicitud |
| body | object | Objeto JSON | Objeto con el contenido del cuerpo de la solicitud |
| status code | int | Código de estado de la solicitud |
|||

| status code | DESCRIPCIÓN |
|-------------|-------------|
| 200 | OK |
| 202 | Accepted |
| 400 | Solicitud incorrecta |
| 401 | No autorizado |
| 403 | Prohibido |
| 404 | No encontrado |
| 500 | Error interno del servidor Error desconocido. |
|||

## <a name="next-steps"></a>Pasos siguientes

* Obtenga más información sobre otros [conectores de Logic Apps](../connectors/apis-list.md)
