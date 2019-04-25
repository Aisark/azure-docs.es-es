---
author: PatAltimore
ms.service: active-directory-b2c
ms.topic: include
origin.date: 11/03/2016
ms.date: 04/04/2019
ms.author: v-junlch
ms.openlocfilehash: fcd71f74e0b00934958828024094773e42496b66
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "60454847"
---
[!INCLUDE [active-directory-b2c-portal-add-application](active-directory-b2c-portal-add-application.md)]

Para registrar la API web, use la configuración que se especifica en la tabla.

![Ejemplo de configuración de registro para la nueva api web](./media/active-directory-b2c-register-web-api/b2c-new-web-api-settings.png)

| Configuración      | Valor de ejemplo  | DESCRIPCIÓN                                        |
| ------------ | ------- | -------------------------------------------------- |
| **Nombre** | API de B2C de Contoso | Escriba un **Nombre** para la aplicación que describa la API a los consumidores. | 
| **Incluir aplicación web o API web** | SÍ | Seleccione **Sí** para una API web. |
| **Permitir flujo implícito** | Sí | Seleccione **Sí** si su aplicación usa el [inicio de sesión de OpenID Connect](../articles/active-directory-b2c/active-directory-b2c-reference-oidc.md). |
| **URL de respuesta** | `https://localhost:44316/` | Las direcciones URL de respuesta son puntos de conexión en los que Azure AD B2C devolverá los tokens que su aplicación solicite. En este ejemplo, la API web es local y la escucha se realiza en el puerto 44316. |
| **URI de id. de aplicación** | api | Este es el identificador utilizado para la API web. Se genera el identificador URI completo, incluido el dominio. |

Haga clic en **Crear** para registrar la aplicación.

La aplicación recién registrada aparece en la lista de aplicaciones del inquilino B2C. Seleccione la API web de la lista. Se muestra el panel de propiedades de la API.

![Propiedades de la API web](./media/active-directory-b2c-register-web-api/b2c-web-api-properties.png)

Tome nota del **Id. de cliente de aplicación** único. Use el identificador en el código de la aplicación.

