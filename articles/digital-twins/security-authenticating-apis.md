---
title: Descripción de la autenticación en las API desde Azure Digital Twins | Microsoft Docs
description: Aprenda a conectarse y autenticarse con las API mediante Azure Digital Twins.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 09/30/2019
ms.openlocfilehash: 53b53fa5185ade87dfdd21f0805641657d49d676
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/23/2019
ms.locfileid: "72800215"
---
# <a name="connect-to-and-authenticate-with-apis"></a>Conexión y autenticación con las API

Azure Digital Twins utiliza Azure Active Directory (Azure AD) para autenticar a los usuarios y proteger a las aplicaciones. Azure AD admite la autenticación de diversas arquitecturas modernas. Todas ellas se basan en los protocolos estándar del sector OAuth 2.0 u OpenID Connect. Además, los desarrolladores pueden usar Azure AD para compilar las aplicaciones de inquilino único y línea de negocio (LOB). Los desarrolladores pueden usar Azure AD para desarrollar aplicaciones para varios inquilinos.

Para obtener información general de Azure AD, visite la [página de aspectos básicos](https://docs.microsoft.com/azure/active-directory/fundamentals/) para acceder a guías paso a paso, conceptos y guías de inicio rápido.

> [!TIP]
> Siga el [tutorial](tutorial-facilities-setup.md) para configurar y ejecutar una aplicación de ejemplo de Azure Digital Twins.

Para integrar una aplicación o un servicio con Azure AD, el desarrollador debe registrar primero la aplicación en Azure AD. Para obtener instrucciones detalladas y capturas de pantalla, consulte [este inicio rápido](../active-directory/develop/quickstart-register-app.md).

Azure AD admite [cinco escenarios principales de aplicación](../active-directory/develop/v2-app-types.md):

* Aplicación de página única (SPA): un usuario tiene que iniciar sesión en una aplicación de página única protegida por Azure AD.
* Explorador web a aplicación web: un usuario tiene que iniciar sesión en una aplicación web protegida por Azure AD.
* Aplicación nativa a API web: una aplicación nativa que se ejecuta en teléfonos, tabletas o equipos tiene que autenticar a un usuario para obtener recursos de una API web protegida por Azure AD.
* Aplicación web a API web: una aplicación web tiene que obtener recursos de una API web protegida por Azure AD.
* Aplicación de servidor o de demonio a API web: una aplicación de demonio o de servidor sin interfaz de usuario web tiene que obtener recursos de una API web protegida por Azure AD.

> [!IMPORTANT]
> Azure Digital Twins admite las dos bibliotecas de autenticación siguientes:
> * La [Biblioteca de autenticación de Microsoft (MSAL)](https://docs.microsoft.com/azure/active-directory/develop/msal-overview) más reciente
> * La [Biblioteca de autenticación de Azure Active Directory (ADAL)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries)

## <a name="call-digital-twins-from-a-middle-tier-web-api"></a>Llamada a Digital Twins desde una API web de nivel intermedio

Cuando los desarrolladores diseñan soluciones de Digital Twins, normalmente crean una aplicación o API web de nivel intermedio. La aplicación o la API, a continuación, llama a la API de Digital Twins de un nivel inferior. Para admitir la arquitectura de la solución web estándar, asegúrese de que los usuarios primero:

1. Se autentiquen con la aplicación de nivel intermedio.

1. Adquieran un token en nombre de OAuth 2.0 durante la autenticación.

1. A continuación, el token adquirido se usa para autenticarse con API o para llamar a API que están en niveles inferiores con el flujo "en nombre de".

Para obtener instrucciones sobre cómo coordinar el flujo en el nombre de otra persona, vea [Flujo en nombre de OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow). También puede ver ejemplos de código en [Calling a downstream web API](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof) (Llamada a una API web de bajada).

## <a name="next-steps"></a>Pasos siguientes

Para configurar y probar Azure Digital Twins mediante el flujo de concesión implícita de OAuth 2.0, lea [Configure Postman](./how-to-configure-postman.md) (Configurar Postman).

Para obtener información acerca de la seguridad en Azure Digital Twins, consulte [Create and manage role assignments](./security-create-manage-role-assignments.md) (Creación y administración de las asignaciones de roles).