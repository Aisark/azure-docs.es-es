---
title: 'Configuración de la autenticación de cuenta de Microsoft: Azure App Service'
description: Obtenga información acerca de cómo configurar la autenticación mediante la cuenta Microsoft para la aplicación de Servicios de aplicaciones.
author: mattchenderson
services: app-service
documentationcenter: ''
manager: syntaxc4
editor: ''
ms.assetid: ffbc6064-edf6-474d-971c-695598fd08bf
ms.service: app-service
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 04/19/2018
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: e3da856efd7d44f15f9de27c9e38375d40dc211d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "60850979"
---
# <a name="how-to-configure-your-app-service-application-to-use-microsoft-account-login"></a>Configuración de la aplicación de App Service para usar el inicio de sesión de la cuenta Microsoft
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

En este tema se muestra cómo configurar Azure App Service para usar la cuenta Microsoft como proveedor de autenticación. 

## <a name="register-microsoft-account"></a>Registro de la aplicación con la cuenta de Microsoft
1. Inicie sesión en el [Azure Portal]y vaya a la aplicación. Copie la **URL**, que posteriormente utilizará para configurar la aplicación con su cuenta Microsoft.
2. Vaya a la página [Mis aplicaciones] del Centro para desarrolladores de la cuenta Microsoft e inicie sesión con su cuenta Microsoft, si procede.
3. Haga clic en **Agregar una aplicación**, escriba el nombre de la aplicación y haga clic en **Crear**.
4. Tome nota del **Id. de aplicación**, ya que lo necesitará más adelante. 
5. En "Plataformas", haga clic en **Agregar plataforma** y seleccione "Web".
6. En "URI de redirección" proporcione el punto de conexión de la aplicación y haga clic en **Guardar**. 
   
   > [!NOTE]
   > El URI de redirección es la dirección URL de la aplicación anexada con la ruta de acceso */.auth/login/microsoftaccount/callback*. Por ejemplo: `https://contoso.azurewebsites.net/.auth/login/microsoftaccount/callback`.   
   > Asegúrese de que está utilizando el esquema HTTPS.
   
7. En "Secretos de aplicación", haga clic en **Generar nueva contraseña**. Anote el valor que aparece. Una vez que salga de esta página, la contraseña no se volverá a mostrar.

    > [!IMPORTANT]
    > La contraseña es una credencial de seguridad importante. No la comparta con nadie ni la distribuya en una aplicación cliente.
    
8. Haga clic en **Guardar**

## <a name="secrets"></a>Incorporación de información de la cuenta de Microsoft a la aplicación de App Service
1. En [Azure Portal], navegue hasta la aplicación y haga clic en **Configuración** >  **Autenticación/autorización**.
2. Si esta característica no está habilitada, **actívela**.
3. Haga clic en **Cuenta Microsoft**. Pegue los valores de identificador de la aplicación y de contraseña que obtuvo previamente y habilite opcionalmente los ámbitos que requiere la aplicación. A continuación, haga clic en **Aceptar**.
   
    ![][1]
   
    De forma predeterminada, App Service ofrece autenticación pero no restringe el acceso autorizado al contenido del sitio y a las API. Debe autorizar a los usuarios en el código de la aplicación.
4. (Opcional) Para restringir el acceso al sitio solo a los usuarios autenticados mediante la cuenta Microsoft, en **Acción necesaria cuando la solicitud no está autenticada**, seleccione **Cuenta Microsoft**. Esto requiere que todas las solicitudes se autentiquen y que todas las solicitudes no autenticadas se redirijan a la cuenta Microsoft para la autenticación.
5. Haga clic en **Save**(Guardar).

De este modo ya estará listo para usar la cuenta Microsoft para realizar la autenticación en la aplicación.

## <a name="related-content"></a>Contenido relacionado
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-microsoft-authentication/app-service-microsoftaccount-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-microsoft-authentication/mobile-app-microsoftaccount-settings.png

<!-- URLs. -->

[Mis aplicaciones]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Azure Portal]: https://portal.azure.com/
