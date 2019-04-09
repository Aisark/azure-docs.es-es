---
title: 'Registrar una aplicación de SaaS: Azure Marketplace | Microsoft Docs'
description: Explica cómo registrar una aplicación SaaS con Azure portal.
services: Azure, Marketplace, Cloud Partner Portal, Azure portal
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: pbutlerm
ms.openlocfilehash: fbc542ea2ed76d99d551d668b00bad1fb3719a9f
ms.sourcegitcommit: e43ea344c52b3a99235660960c1e747b9d6c990e
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2019
ms.locfileid: "59012941"
---
# <a name="register-a-saas-application"></a>Registrar una aplicación SaaS

En este artículo se explica cómo registrar una aplicación SaaS con Microsoft [portal Azure](https://portal.azure.com/).  Después de un registro correcto, recibirá un token de seguridad de Azure Active Directory (Azure AD) que puede usar para tener acceso a las API de realización de SaaS.  ¿Para obtener más información acerca de Azure AD, consulte [qué es la autenticación?](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios)


## <a name="service-to-service-authentication-flow"></a>Flujo de autenticación de servicio a servicio

El siguiente diagrama muestra el flujo de suscripción de un nuevo cliente y cuándo se utilizan estas API:

![Flujo de la API de la oferta de SaaS](./media/saas-offer-publish-api-flow-v1.png)

Azure no impone ninguna restricción en la autenticación que el servicio SaaS expone a los usuarios finales. Sin embargo, se realiza la autenticación con las API de entrega SaaS con un token de seguridad de Azure AD, normalmente puede obtenido al registrar la aplicación SaaS a través del portal de Azure. 


## <a name="register-an-azure-ad-secured-app"></a>Registrar una aplicación protegida por AD Azure

Cualquier aplicación que quiera usar las funciones de Azure AD debe registrarse primero en un inquilino de Azure AD. Este proceso de registro implica proporcionar a Azure AD los detalles de la aplicación, como la dirección URL donde se encuentra, la dirección URL para enviar respuestas cuando se autentica un usuario, el identificador URI que identifica la aplicación, etc.  Para registrar una aplicación nueva mediante Azure Portal, realice los pasos siguientes:

1.  Inicie sesión en el [Azure Portal](https://portal.azure.com/).
2.  Si la cuenta proporciona acceso a más de uno, haga clic en la cuenta en la esquina superior derecha y establezca la sesión del portal en el inquilino de Azure AD deseado.
3.  En el panel de navegación izquierdo, haga clic en el servicio **Azure Active Directory**, haga clic en **Registros de aplicaciones** y haga clic en **Nuevo registro de aplicación**.

    ![Registros de aplicaciones SaaS de AD](./media/saas-offer-app-registration-v1.png)

4.  En la página Crear, escriba la información de registro de la aplicación:
    -   **Nombre**: Especifique un nombre de aplicación significativo.
    -   **Tipo de aplicación**: 
        - Seleccione **Nativo** para las [aplicaciones cliente](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#client-application) que están instaladas localmente en un dispositivo. Esta configuración se utiliza para [clientes nativos](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#native-client) públicos de OAuth.
        - Seleccione **Aplicación web/API** para las [aplicaciones cliente](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#client-application) y las [aplicaciones de recursos/API](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#resource-server) que están instaladas en un servidor seguro. Esta configuración se utiliza para [clientes web](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#web-client) confidenciales de OAuth y [clientes basados en agente de usuario](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#user-agent-based-client) públicos.
        La misma aplicación también puede exponer tanto un cliente como un recurso o API.
    -   **URL de inicio de sesión**: Para aplicaciones web o aplicaciones de la API, proporcione la dirección URL base de la aplicación. Por ejemplo, **http://localhost:31544** podría ser la dirección URL de una aplicación web que se ejecuta en la máquina local. Los usuarios utilizan esta dirección URL para iniciar sesión en una aplicación cliente web.
    -   **URI de redirección**: Para aplicaciones nativas, proporcione el identificador URI que utiliza Azure AD para devolver las respuestas de los tokens. Escriba un valor específico para la aplicación, por ejemplo, **http://MyFirstAADApp**.

        ![Registros de aplicaciones SaaS de AD](./media/saas-offer-app-registration-v1-2.png)

        Para obtener ejemplos específicos para las aplicaciones nativas o aplicaciones web, consulte la Guía de inicio rápido guiado configuraciones que están disponibles en el *comenzar* sección de la [Guía de desarrolladores de Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide).

5.  Cuando haya terminado, haga clic en **Crear**. Azure AD le asigna un único *Id. de aplicación* a la aplicación y\'re dedicado a la aplicación\'página principal del registro. En función de que se trate de una aplicación web o nativa, se proporcionan opciones diferentes para agregar funcionalidades adicionales a la aplicación.

>[!Note]
>De forma predeterminada, la aplicación recién registrada está configurada para permitir que a solo los usuarios desde el mismo inquilino para iniciar sesión en su aplicación.


## <a name="using-the-azure-ad-security-token"></a>Con el token de seguridad de Azure AD

Una vez que se ha registrado su aplicación, puede solicitar un token de seguridad de Azure AD mediante programación.  Se espera el publicador para utilizar este token y realizar una solicitud para resolverlo.  Al usar las distintas API de entrega, el parámetro de token de consulta está en la dirección URL cuando se redirige al usuario al sitio Web de SaaS de Azure.  Este token es válido solo durante una hora.  Además, debe URL descodificar el valor del token desde el explorador antes de usarlo.

Para obtener más información acerca de estos tokens, consulte [tokens de acceso de Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/access-tokens).


### <a name="get-a-token-based-on-the-azure-ad-app"></a>Obtención de un token basado en la aplicación de Azure AD

Método HTTP

`GET`

*URL de la solicitud*

**https://login.microsoftonline.com/*{tenantId}*/oauth2/token**

*Parámetro URI*

|  **Nombre de parámetro**  | **Obligatorio**  | **DESCRIPCIÓN**                               |
|  ------------------  | ------------- | --------------------------------------------- |
| tenantId             | True          | Identificador de inquilino de la aplicación de AAD registrada   |
|  |  |  |


*Encabezado de solicitud*

|  **Nombre de encabezado**  | **Obligatorio** |  **DESCRIPCIÓN**                                   |
|  --------------   | ------------ |  ------------------------------------------------- |
|  Content-Type     | True         | Tipo de contenido asociado a la solicitud. El valor predeterminado es `application/x-www-form-urlencoded`.  |
|  |  |  |


*Cuerpo de la solicitud*

| **Nombre de propiedad**   | **Obligatorio** |  **DESCRIPCIÓN**                                                          |
| -----------------   | -----------  | ------------------------------------------------------------------------- |
|  Grant_type         | True         | Tipo de concesión. El valor predeterminado es `client_credentials`.                    |
|  Client_id          | True         |  Identificador de cliente o aplicación asociado a la aplicación de Azure AD.                  |
|  client_secret      | True         |  Contraseña asociada a la aplicación de Azure AD.                               |
|  Recurso           | True         |  Recurso de destino para el que se solicita el token. El valor predeterminado es `62d94f6c-d599-489b-a797-3e10e42fbe22`. |
|  |  |  |


*Response*

|  **NOMBRE**  | **Type**       |  **DESCRIPCIÓN**    |
| ---------- | -------------  | ------------------- |
| 200 OK    | TokenResponse  | La solicitud se realizó correctamente   |
|  |  |  |

*TokenResponse*

Ejemplo de token de respuesta:

``` json
  {
      "token_type": "Bearer",
      "expires_in": "3600",
      "ext_expires_in": "0",
      "expires_on": "15251…",
      "not_before": "15251…",
      "resource": "62d94f6c-d599-489b-a797-3e10e42fbe22",
      "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayIsImtpZCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayJ9…"
  }               
```


## <a name="next-steps"></a>Pasos siguientes

Ahora puede usar la aplicación de Azure AD protegido el [API versión 2 de realización de SaaS](./cpp-saas-fulfillment-api-v2.md).
