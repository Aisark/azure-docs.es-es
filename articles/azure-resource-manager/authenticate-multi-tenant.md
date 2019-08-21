---
title: 'Autenticación entre inquilinos: Azure Resource Manager'
description: Se describe cómo Azure Resource Manager gestiona las solicitudes de autenticación entre inquilinos.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: tomfitz
ms.openlocfilehash: 625a17156eaf199af0d51151c6fd37769b8f7b4a
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2019
ms.locfileid: "68848764"
---
# <a name="authenticate-requests-across-tenants"></a>Autenticación de solicitudes entre inquilinos

Al crear una aplicación multiinquilino, puede que deba administrar las solicitudes de autenticación para los recursos que se encuentran en distintos inquilinos. Un caso común es cuando una máquina virtual de un inquilino debe unirse a una red virtual de otro inquilino. Azure Resource Manager proporciona un valor de encabezado para almacenar los tokens auxiliares a fin de autenticar las solicitudes en diferentes inquilinos.

## <a name="header-values-for-authentication"></a>Valores de encabezado de autenticación

La solicitud tiene los siguientes valores de encabezado de autenticación:

| Nombre de encabezado | DESCRIPCIÓN | Valor de ejemplo |
| ----------- | ----------- | ------------ |
| Authorization | Token primario | Bearer &lt;primary-token&gt; |
| x-ms-authorization-auxiliary | Tokens auxiliares | Bearer &lt;auxiliary-token1&gt;, EncryptedBearer &lt;auxiliary-token2&gt;, Bearer &lt;auxiliary-token3&gt; |

El encabezado auxiliar puede contener hasta tres tokens auxiliares. 

En el código de la aplicación multiinquilino, obtenga el token de autenticación de los otros inquilinos y almacénelos en los encabezados auxiliares. Todos los tokens deben proceder del mismo usuario o de la misma aplicación. Se debe haber invitado al usuario o a la aplicación como invitados a los otros inquilinos.

## <a name="processing-the-request"></a>Procesamiento de la solicitud

Cuando la aplicación envía una solicitud a Resource Manager, la solicitud se ejecuta con la identidad del token primario. El token primario debe ser válido y no debe haber expirado. Este token debe ser de un inquilino que pueda administrar la suscripción.

Cuando la solicitud hace referencia a un recurso de otro inquilino, Resource Manager comprueba los tokens auxiliares para determinar si se puede procesar la solicitud. Todos los tokens auxiliares del encabezado deben ser válidos y no deben haber expirado. Si alguno de los tokens ha expirado, Resource Manager devuelve un código de respuesta 401. La respuesta incluye el identificador de cliente y el identificador de inquilino del token que no es válido. Si el encabezado auxiliar contiene un token válido para el inquilino, se procesa la solicitud entre inquilinos.

## <a name="next-steps"></a>Pasos siguientes
* Para aprender a enviar solicitudes de autenticación con las API de Azure Resource Manager, consulte [Uso de la API de autenticación de Resource Manager para acceder a suscripciones](resource-manager-api-authentication.md).
* Para más información sobre los tokens, consulte [Tokens de acceso de Azure Active Directory](/azure/active-directory/develop/access-tokens).
