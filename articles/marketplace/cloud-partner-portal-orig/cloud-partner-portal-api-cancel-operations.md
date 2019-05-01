---
title: Cancelar la operación de API | Azure Marketplace
description: Cancele las operaciones.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: reference
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 70ffd13be4ba934b423e3bb5344eea0a9c36886c
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/30/2019
ms.locfileid: "64935564"
---
# <a name="cancel-operation"></a>Cancelar operación 

Esta API cancela una operación en curso en la oferta. Use [Retrieve operations API](./cloud-partner-portal-api-retrieve-operations.md) para obtener `operationId` para pasar a esta API. La cancelación suele ser una operación sincrónica pero, en algunos escenarios complejos, es posible que se necesite una operación nueva para cancelar una existente. En este caso, el cuerpo de la respuesta HTTP contiene la ubicación de la operación que se debe usar para consultar el estado.

Puede proporcionar una lista de direcciones de correo electrónico delimitadas por coma con la solicitud y la API enviará una notificación a estas direcciones sobre el progreso de la operación.

  `POST https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/cancel?api-version=2017-10-31`

<a name="uri-parameters"></a>Parámetros del identificador URI
--------------

|  **Nombre**    |      **Descripción**                                  |    **Tipo de datos**  |
| ------------ |     ----------------                                  |     -----------   |
| publisherId  |  Identificador del editor, por ejemplo, `contoso`         |   string          |
| offerId      |  Identificador de la oferta.                                     |   string          |
| api-version  |  Versión actual de la API                               |    Date           |
|  |  |  |


<a name="header"></a>Encabezado
------

|  **Nombre**              |  **Valor**         |
|  ---------             |  ----------        |
|  Content-Type          |  application/json  |
|  Autorización         |  SU TOKEN de portador |
|  |  |


<a name="body-example"></a>Ejemplo de cuerpo
------------

### <a name="request"></a>Solicitud

``` json
{
   "metadata": {
     "notification-emails": "jondoe@contoso.com"
    }
}     
```

### <a name="request-body-properties"></a>Propiedades del cuerpo de la solicitud

|  **Nombre**                |  **Descripción**                                               |
|  --------                |  ---------------                                               |
|  notification-emails     | Lista de identificadores de correo electrónico delimitados por coma para recibir una notificación del progreso de la operación de publicación. |
|  |  |


### <a name="response"></a>Response

  `Operation-Location: https://cloudpartner.azure.com/api/publishers/contoso/offers/contoso-virtualmachineoffer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8`


### <a name="response-header"></a>Encabezado de respuesta

|  **Nombre**             |    **Valor**                       |
|  ---------            |    ----------                      |
| Operation-Location    | Dirección URL que se puede consultar para determinar el estado actual de la operación. |
|  |  |


### <a name="response-status-codes"></a>Códigos de estado de respuesta

| **Código**  |  **Descripción**                                                                       |
|  ------   |  ------------------------------------------------------------------------               |
|  200      | OK. La solicitud se procesó correctamente y la operación se canceló de manera sincrónica. |
|  202      | Accepted. La solicitud se procesó correctamente y la operación está en proceso de ser cancelada. La ubicación de la operación de cancelación se devuelve en el encabezado de la respuesta. |
|  400      | Bad/Malformed request. El cuerpo de la respuesta del error podría proporcionar más información.  |
|  403      | Access Forbidden. El cliente no tiene acceso al espacio de nombres especificado en la solicitud. |
|  404      | Not found. La entidad especificada no existe. |
|  |  |
