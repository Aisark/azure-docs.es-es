---
title: Portal de suscripción de autoservicio para la colaboración B2B de Azure Active Directory | Microsoft Docs
description: La colaboración con Azure Active Directory B2B posibilita las relaciones entre empresas al permitir que los asociados empresariales accedan de forma selectiva a las aplicaciones corporativas.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: sample
ms.date: 05/08/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 28db43a345fb29a7529136ddfb929f5a48ccb1f8
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/16/2019
ms.locfileid: "65785428"
---
# <a name="self-service-portal-for-azure-ad-b2b-collaboration-sign-up"></a>Portal de autoservicio para el registro en la colaboración B2B de Azure AD

Los clientes pueden hacer muchas cosas con las características integradas que se exponen mediante [Azure Portal](https://portal.azure.com) y el [panel de acceso a las aplicaciones](https://myapps.microsoft.com) para los usuarios finales. Sin embargo, puede que necesite personalizar el flujo de trabajo de incorporación para los usuarios de B2B para ajustarse a las necesidades de su organización. Puede hacerlo con la [API de invitación](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation).

Como una organización que invita, puede que no sepa de antemano quiénes son los colaboradores externos individuales y quién necesita acceso a sus recursos. Necesita una forma de que los usuarios de empresas asociadas se registren ellos mismos con un conjunto de directivas que usted, como la organización que invita, controla. Este escenario es posible mediante las API. Hay un [proyecto de ejemplo en GitHub](https://github.com/Azure/active-directory-dotnet-graphapi-b2bportal-web) que hace justamente eso.

Este proyecto de GitHub muestra cómo las organizaciones pueden usar las API para proporcionar una funcionalidad de registro de autoservicio basada en directivas para sus asociados de confianza, con reglas que determinan a qué aplicaciones pueden tener acceso. Los usuarios asociados pueden acceder a los recursos cuando los necesitan. Pueden hacerlo de forma segura, sin necesidad de que la organización que invita los incorpore manualmente. Puede implementar fácilmente el proyecto en una suscripción de Azure de su elección.

## <a name="as-is-code"></a>Código “tal cual”

Este código está disponible como ejemplo para mostrar el uso de la API de invitación B2B de Azure Active Directory. Su equipo de desarrollo o el asociado deben realizar la personalización y revisión antes de implementarlo en un escenario de producción.

## <a name="next-steps"></a>Pasos siguientes

* [¿Qué es la colaboración B2B de Azure AD?](what-is-b2b.md)
* [Concesión de licencias de colaboración B2B de Azure AD](licensing-guidance.md)
* [Preguntas frecuentes sobre la colaboración B2B de Azure Active Directory (P+F)](faq.md)
