---
title: Guía de publicación técnica de aplicaciones SaaS de Azure Marketplace
description: Guía paso a paso y listas de comprobación de publicación para la publicación de aplicaciones SaaS en Azure Marketplace
services: Marketplace, Compute, Storage, Networking, Blockchain, Security, SaaS
author: keithcharlie
ms.service: marketplace
ms.topic: article
ms.date: 07/09/2018
ms.author: keithcharlie
ms.openlocfilehash: 5106b22500b73b650742e7fe0f881c2a4c984eff
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/30/2019
ms.locfileid: "64937716"
---
# <a name="saas-applications-offer-publishing-guide"></a>Guía de publicación de ofertas de aplicaciones de SaaS

Las aplicaciones de SaaS se pueden publicar en Marketplace con tres llamadas a la acción diferentes: "Ponerse en contacto conmigo," "Probar ahora" y "Obtener ahora". En esta guía se explican estas tres opciones, incluidos los requisitos para cada una. 

## <a name="offer-overview"></a>Información general de la oferta  

Las aplicaciones de SaaS están disponibles en los escaparates de Azure. En la tabla siguiente, se describen las opciones disponibles actualmente:

| Opción de escaparate | Listado | Evaluación/transacción |  
| --- | --- | --- |  
| AppSource | Sí (Contacto) | Sí (PowerBI/Dynamics) |
| Azure Marketplace | Sin  | Sí (Aplicaciones de SaaS) |   

**Lista:**  la opción de publicación de listado consiste en un tipo de oferta del tipo Ponerse en contacto conmigo y se usa cuando una participación de nivel de evaluación o transacción no es viable. La ventaja de este enfoque es que permite que los anunciantes con una solución en el mercado comiencen a recibir inmediatamente clientes potenciales que pueden convertirse en ventas para hacer crecer su negocio.  
**Evaluación/Transacción:**  el cliente tiene la opción de comprar directamente o solicitar una versión de evaluación para la solución. Proporcionar una experiencia de Evaluación aumenta el nivel de compromiso que se ofrece a los clientes y permite que exploren la solución antes de comprarla. Con una experiencia de este tipo, tendrá mejores posibilidades de promoción en los escaparates, y deberían generarse más clientes potenciales y más valiosos a partir de la interacción con los clientes. Las versiones de evaluación deben incluir al menos soporte técnico gratuito durante el período de evaluación.  

| Oferta de aplicaciones de SaaS | Requisitos empresariales | Requisitos técnicos |  
| --- | --- | --- |  
| **Póngase en contacto con nosotros** | Sí | Sin  |  
| **PowerBI/Dynamics** | Sí | Sí (integración de Azure AD) |  
| **Aplicaciones de SaaS**| Sí | Sí (integración de Azure AD) |     

## <a name="saas-list"></a>Lista de SaaS

La llamada a la acción para obtener una lista de SaaS sin funcionalidad de evaluación ni de facturación es "Ponerse en contacto conmigo". 

No es necesario configurar Azure Active Directory para incluir una aplicación de SaaS en una lista. 

|Requisitos  |Detalles  |
|---------|---------|
|La aplicación es una oferta de SaaS  |   La solución es una oferta de SaaS y ofrece un producto de SaaS multiinquilino.      |


## <a name="saas-trial"></a>Versión de evaluación de SaaS

Una solución o aplicación se proporciona mediante una prueba de software como servicio (SaaS) gratuita. Las ofertas de evaluación gratuita se pueden presentar como una cuenta de prueba de uso limitado o duración limitada. 


|Requisitos  |Detalles  |
|---------|---------|
|La aplicación es una oferta de SaaS  |   La solución es una oferta de SaaS y ofrece un producto de SaaS multiinquilino.      |
|La aplicación está habilitada para AAD     |   El cliente se redirigirá a su dominio y hará la transacción directamente con él       |


## <a name="saas-trial-technical-requirements"></a>Requisitos técnicos de la versión de evaluación de SaaS

Los requisitos técnicos para las aplicaciones SaaS son simples. Los anunciantes solo tienen que integrarse con Azure Active Directory (Azure AD) para su publicación. La integración de Azure AD con aplicaciones está bien documentada y Microsoft proporciona varios SDK y recursos para lograr esto.  

Para empezar, se recomienda contar con una suscripción dedicada a la publicación en Azure Marketplace, lo que permitirá aislar el trabajo de otras iniciativas. Una vez hecho esto, se puede empezar a implementar la aplicación de SaaS en esta suscripción para iniciar el trabajo de desarrollo.  

La documentación, ejemplos y guía recomendada de Azure Active Directory se encuentran en los siguientes sitios: 

* [Guía del desarrollador de Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)

* [Integración con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-to-integrate)

* [Integración de aplicaciones con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)

* [Hoja de ruta de Azure: seguridad e identidad](https://azure.microsoft.com/roadmap/?category=security-identity)

Para ver tutoriales de vídeo, revise lo siguiente:

* [Azure Active Directory Authentication with Vittorio Bertocci](https://channel9.msdn.com/Shows/XamarinShow/Episode-27-Azure-Active-Directory-Authentication-with-Vittorio-Bertocci?term=azure%20active%20directory%20integration) (Autenticación de Azure Active Directory con Vittorio Bertocci)

* [Azure Active Directory Identity Technical Briefing - Part 1 of 2](https://channel9.msdn.com/Blogs/MVP-Enterprise-Mobility/Azure-Active-Directory-Identity-Technical-Briefing-Part-1-of-2?term=azure%20active%20directory%20integration) (Sesión informativa técnica sobre identidad de Azure Active Directory - Parte 1 de 2)

* [Azure Active Directory Identity Technical Briefing - Part 2 of 2](https://channel9.msdn.com/Blogs/MVP-Azure/Azure-Active-Directory-Identity-Technical-Briefing-Part-2-of-2?term=azure%20active%20directory%20integration) (Sesión informativa técnica sobre identidad de Azure Active Directory - Parte 2 de 2)

* [Building Apps with Microsoft Azure Active Directory](https://channel9.msdn.com/Blogs/Windows-Development-for-the-Enterprise/Building-Apps-with-Microsoft-Azure-Active-Directory?term=azure%20active%20directory%20integration) (Creación de aplicaciones con Microsoft Azure Active Directory)

* [Vídeos de Microsoft Azure sobre Active Directory](https://azure.microsoft.com/resources/videos/index/?services=active-directory)

La capacitación gratuita de Azure Active Directory está disponible en  
* [Microsoft Azure for IT Pros Content Series: Azure Active Directory](https://mva.microsoft.com/training-courses/microsoft-azure-for-it-pros-content-series-azure-active-directory-16754?l=N0e23wtxC_2106218965)

Además, Azure Active Directory proporciona un sitio para buscar actualizaciones del servicio   
* [Actualizaciones de servicios de Azure AD](https://azure.microsoft.com/updates/?product=active-directory)

## <a name="using-azure-active-directory-to-enable-trials"></a>Uso de Azure Active Directory para habilitar las evaluaciones  

Microsoft autentica a todos los usuarios de Marketplace con Azure AD, de ahí que cuando un usuario autenticado hace clic en la lista de versiones de evaluación de Marketplace y se le redirige a su entorno de evaluación, puede aprovisionar al usuario directamente en una versión de evaluación sin necesidad de un paso adicional de inicio de sesión. El token que recibe la aplicación de Azure AD durante la autenticación incluye información valiosa del usuario que se puede usar para crear una cuenta de usuario en su aplicación, lo que le permite automatizar la experiencia de aprovisionamiento y aumentar la probabilidad de conversión. Para obtener más información sobre el token, consulte [Tokens de ejemplo](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims).

El uso de Azure AD para habilitar la autenticación con un solo clic en su aplicación o versión de evaluación hace lo siguiente:  
* Optimiza la experiencia del cliente de Marketplace a la versión de Evaluación.  
* Mantiene la sensación de una "experiencia dentro del producto" incluso cuando se redirige al usuario desde Marketplace hasta el dominio o el entorno de evaluación.  
* Reduce la probabilidad de abandono en el redireccionamiento al no haber un paso adicional de inicio de sesión.  
* Reduce las barreras de implementación para la gran población de usuarios de Azure AD.  

## <a name="certifying-your-azure-ad-integration-for-marketplace"></a>Certificación de la integración de Azure AD para Marketplace  

Puede certificar la integración de Azure AD de varias maneras, en función de si la aplicación es de un inquilino único o si es multiinquilino, y de si no está familiarizado con el inicio de sesión único (SSO) federado de Azure AD o si ya lo admite.  

**Para aplicaciones multiinquilino:**  

Si ya admite Azure AD, siga estos pasos:
1.  Registre la aplicación en Azure Portal
2.  Habilite la característica de compatibilidad multiinquilino en Azure AD para obtener una experiencia de evaluación con un clic. Se puede encontrar información más específica [aquí](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).  

Si no está familiarizado con el SSO federado de Azure AD, haga lo siguiente: 
1.  Registre la aplicación en Azure Portal
2.  Desarrolle el SSO con Azure AD mediante [OpenID Connect](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code) o [OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code).
3.  Habilite la característica de compatibilidad multiinquilino en AAD para obtener la experiencia de evaluación "con un clic". Se puede encontrar información más específica [aquí](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified).  

**Para la aplicación de inquilino único, use cualquiera de las siguientes opciones:**  
* Agregue usuarios al directorio como usuarios invitados con [Azure B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)
* Aprovisione manualmente versiones de evaluación para los clientes mediante la opción de "Contacto"
* Desarrolle una "versión de prueba" por cliente
* Compile una aplicación de demostración de ejemplo multiinquilino con SSO.

## <a name="saas-subscriptions"></a>Suscripciones de SaaS

Use el tipo de oferta Aplicación SaaS para permitir que el cliente compre su solución técnica basada en SaaS como una suscripción. Se deben cumplir los requisitos siguientes para la aplicación de SaaS:
- Determinar el precio y la facturación con una tarifa plana mensual.
- Proporcionar un método para actualizar o cancelar el servicio en cualquier momento.
Microsoft hospeda la transacción comercial. Microsoft factura al cliente en su nombre. Para usar la facturación de una aplicación de SaaS como una suscripción, debe habilitar su propia API de servicio de administración de suscripciones. Su API de servicio de administración de suscripciones debe comunicarse directamente con las API de Azure Resource Manager. Su API de servicio de administración de suscripciones debe admitir el aprovisionamiento, la actualización y la cancelación del servicio.

| Requisito | Detalles |  
|:--- |:--- |  
|Facturación y medición | El precio de su oferta se fija en una tarifa plana mensual. Los precios y las funcionalidades "true-up" basados en uso no se admiten en este momento. |  
|Cancelación | El cliente puede cancelar su oferta en cualquier momento. |  
|Página de aterrizaje de transacción | Usted hospeda una página de aterrizaje de transacción personalizada de Azure donde los usuarios pueden crear y administrar su cuenta de servicio SaaS. |   
| API de suscripción | Usted expone un servicio que puede interactuar con la suscripción SaaS para crear, actualizar y eliminar una cuenta de usuario y un plan de servicio. Los cambios importantes en la API deben admitirse dentro de un plazo de 24 horas. Los cambios no importantes en la API se publicarán de forma periódica. |  

>[!Note]
>Opt channel en cloud Solution Providers (CSP) asociado ya está disponible.  Consulte [Cloud Solution Providers](./cloud-solution-providers.md) para obtener más información sobre la oferta a través de Microsoft CSP de marketing de canales asociado.

## <a name="next-steps"></a>Pasos siguientes
Si aún no lo ha hecho,

- [Regístrese](https://azuremarketplace.microsoft.com/sell) en Marketplace.

Si está registrado y está creando una oferta nueva o trabajando en una existente,

- [Inicie sesión en Cloud Partner Portal](https://cloudpartner.azure.com) para crear o completar la oferta
- Consulte [Oferta de aplicación SaaS de Azure](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-offer) para más información.
