---
title: Opciones de ayuda y soporte técnico para los desarrolladores de aplicaciones de Azure AD | Microsoft Docs
description: Infórmese sobre cómo obtener ayuda y soporte técnico para preguntas relacionadas con el desarrollo, y para resolver los problemas que pueden surgir al crear aplicaciones que se integran con las identidades de Microsoft (Azure Active Directory y cuenta Microsoft)
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/23/2019
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev
ms.openlocfilehash: e85a768d8e4f11ca3637bc8f0b394cfb1ea9744a
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/23/2020
ms.locfileid: "76697479"
---
# <a name="support-and-help-options-for-developers"></a>Opciones de ayuda y soporte técnico para desarrolladores

Si está empezando a integrarse con Azure Active Directory (Azure AD), las identidades de Microsoft o Microsoft Graph API, o si está implementando una nueva característica en una aplicación, hay ocasiones en las que puede necesitar ayuda de la comunidad de usuarios o querer saber las opciones de soporte técnico que tienen como desarrollador. Este artículo le ayuda a conocer estas opciones, entre ellas:

> [!div class="checklist"]
> * Cómo buscar si la pregunta sobre su problema no se ha contestado en la comunidad o si existe documentación para la característica que está intentando implementar
> * En algunos casos, puede que lo que desea es usar nuestras herramientas de soporte técnico para ayudarle a depurar un problema específico
> * Si no encuentra la respuesta que necesita, puede formular una pregunta en *Stack Overflow*
> * Si tiene algún problema con una de nuestras bibliotecas de autenticación, genere un *problema de GitHub*
> * Por último, si necesita hablar con alguien puede abrir una solicitud de soporte técnico

## <a name="search"></a>Search

Si tiene alguna pregunta relacionada con el desarrollo, puede que encuentre la respuesta en nuestra documentación, los [ejemplos de GitHub](https://github.com/azure-samples) o las respuestas a las preguntas de [Stack Overflow](https://www.stackoverflow.com).

### <a name="scoped-search"></a>Búsqueda restringida

Para obtener resultados más rápido, limite su búsqueda a Stack Overflow, la documentación y los ejemplos de código utilizando la consulta siguiente en su motor de búsqueda favorito:

```
{Your Search Terms} (site:stackoverflow.com OR site:docs.microsoft.com OR site:github.com/azure-samples OR site:cloudidentity.com OR site:developer.microsoft.com/graph)
```

Donde *{Your Search Terms}* son sus palabras clave de búsqueda.

## <a name="use-the-development-support-tools"></a>Uso de las herramientas de soporte técnico de desarrollo

| Herramienta  | Descripción  |
|---------|---------|
| [jwt.ms](https://jwt.ms) | Pegue un identificador o un token de acceso para descodificar los valores y nombres de notificaciones. |
| [Explorador de Microsoft Graph](https://developer.microsoft.com/graph/graph-explorer)| Esta herramienta le permite realizar las solicitudes y ver las respuestas en Microsoft Graph API. |

## <a name="post-a-question-to-stack-overflow"></a>Publique una pregunta en Stack Overflow

Stack Overflow es el canal preferido para las preguntas relacionadas con el desarrollo. Aquí, los miembros de la comunidad de desarrolladores y los miembros del equipo de Microsoft se implican directamente para ayudarle a solucionar problemas.

Si no encuentra una respuesta a su pregunta a través de la búsqueda, envíe una nueva pregunta a Stack Overflow. Use una de las siguientes etiquetas cuando formule preguntas para ayudar a la comunidad a identificar y responder su pregunta más rápidamente:

|Componente o área  | Etiquetas |
|---------|---------|
| Biblioteca ADAL | [[adal]](https://stackoverflow.com/questions/tagged/adal) |
| Biblioteca MSAL     | [[msal]](https://stackoverflow.com/questions/tagged/msal) |
| Middleware OWIN  | [[azure-active directory]](https://stackoverflow.com/questions/tagged/azure-active-directory) |
| [B2B de Azure](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)  | [[azure-ad-b2b]](https://stackoverflow.com/questions/tagged/azure-ad-b2b) |
| [B2C de Azure](https://azure.microsoft.com/services/active-directory-b2c/)  | [[azure-ad-b2c]](https://stackoverflow.com/questions/tagged/azure-ad-b2c) |
| [Microsoft Graph API](https://developer.microsoft.com/graph/) | [[microsoft-graph]](https://stackoverflow.com/questions/tagged/microsoft-graph) |
| Cualquier otra área relacionada con temas de autenticación o autorización | [[azure-active directory]](https://stackoverflow.com/questions/tagged/azure-active-directory) |

Las siguientes publicaciones de Stack Overflow contienen sugerencias sobre cómo formular preguntas y agregar código fuente. Seguir estas directrices aumentan las posibilidades de que los miembros de la comunidad evalúen y respondan a su pregunta rápidamente:

* [Cómo se puede formular una buena pregunta](https://stackoverflow.com/help/how-to-ask)
* [Cómo crear un ejemplo mínimo, completo y comprobable](https://stackoverflow.com/help/mcve)

## <a name="create-a-github-issue"></a>Creación de un problema de GitHub

Si encuentra un error o un problema relacionado con nuestras bibliotecas, genere un problema en los repositorios de GitHub. Dado que nuestra bibliotecas son de código abierto, también puede enviar una solicitud de incorporación de cambios.

Para ver una lista de bibliotecas y sus repositorios de GitHub, consulte lo siguiente:

* Bibliotecas [ADAL](active-directory-authentication-libraries.md) y repositorios de GitHub
* Bibliotecas y repositorios de GitHub [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) [MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angularjs/README.md), [MSAL.Android](https://github.com/AzureAD/microsoft-authentication-library-for-android) y [MSAL.obj_c](https://github.com/AzureAD/microsoft-authentication-library-for-objc)

## <a name="open-a-support-request"></a>Apertura de una solicitud de soporte técnico

Si necesita hablar con alguien, puede abrir una solicitud de soporte técnico. Si es un cliente de Azure, tiene varias opciones de soporte técnico. Para comparar los planes, consulte [esta página](https://azure.microsoft.com/support/plans/). El soporte técnico Developer también está disponible para los clientes de Azure. Para obtener información sobre cómo adquirir planes de soporte técnico de Developer, consulte [esta página](https://azure.microsoft.com/support/plans/developer/).

* Si ya tiene un plan de soporte técnico de Azure, [abra una solicitud de soporte aquí](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

* Si no es un cliente de Azure, también puede abrir una solicitud de soporte técnico con Microsoft a través de [nuestro soporte comercial](https://support.microsoft.com/en-us/gp/contactus81?Audience=Commercial).

También puede intentar un [agente virtual](https://support.microsoft.com/contactus/?ws=support) para obtener soporte técnico o formular alguna pregunta.
