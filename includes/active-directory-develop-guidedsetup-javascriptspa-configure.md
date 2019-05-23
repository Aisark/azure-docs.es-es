---
title: archivo de inclusión
description: archivo de inclusión
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: nacanuma
ms.custom: include file
ms.openlocfilehash: 387adcdf8bdabf90bc1e691a7a8ec9ae0a8e90dc
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/23/2019
ms.locfileid: "66121694"
---
## <a name="register-your-application"></a>Registrar su aplicación

1. Inicie sesión en [Azure Portal](https://portal.azure.com/) para registrar una aplicación.
1. Si la cuenta proporciona acceso a más de un inquilino, haga clic en la cuenta en la esquina superior derecha y establezca la sesión del portal en el inquilino de Azure AD deseado.
1. Vaya a la página [Registros de aplicaciones](https://go.microsoft.com/fwlink/?linkid=2083908) de la plataforma de identidad de Microsoft para desarrolladores.
1. Cuando se abra la página **Registrar una aplicación**, escriba el nombre de su aplicación.
1. En **Supported account types** (Tipos de cuenta compatibles), seleccione **Accounts in any organizational directory and personal Microsoft accounts** (Cuentas en cualquier directorio de organización y cuentas personales de Microsoft).
1. En la sección **URI de redirección**, seleccione la plataforma **Web** y establezca como su valor la dirección URL de la aplicación según su servidor web. Consulte las secciones siguientes para obtener instrucciones sobre cómo establecer y obtener la URL de redireccionamiento en Visual Studio y Node.
1. Cuando termine, seleccione **Registrar**.
1. En la página de **Información general** de la aplicación, anote el valor en **Id. de aplicación (cliente)**.
1. Para esta guía, se requiere que habilite el [flujo de concesión implícita](../articles/active-directory/develop/v2-oauth2-implicit-grant-flow.md). En el panel de navegación izquierdo de la aplicación registrada, seleccione **Autenticación**.
1. En **Configuración avanzada**, vaya a **Concesión implícita** y habilite las casillas **Tokens de id.** y **Tokens de acceso**. Los tokens de identificador y los tokens de acceso son necesarios, ya que esta aplicación debe iniciar la sesión de los usuarios y llamar a una API.
1. Seleccione **Guardar**.

> #### <a name="setting-the-redirect-url-for-node"></a>Configuración de la URL de redirección para Node
> Para Node.js, puede establecer el puerto del servidor web en el archivo *server.js*. En este tutorial se usa el puerto 30662 como referencia, pero puede usar cualquier otro puerto que esté disponible. Siga estas instrucciones para configurar una dirección URL de redireccionamiento en la información de registro de aplicación:<br/>
> - Vuelva a *Registro de aplicaciones* y establezca `http://localhost:30662/` como `Redirect URL`, o utilice `http://localhost:[port]/` si usa un puerto TCP personalizado (donde *[port]* es el número de puerto TCP personalizado).

<p>

> #### <a name="visual-studio-instructions-for-obtaining-the-redirect-url"></a>Instrucciones en Visual Studio para obtener la dirección URL de redireccionamiento
> Siga estos pasos para obtener la dirección URL de redireccionamiento:
> 1. En el **Explorador de soluciones**, seleccione el proyecto y fíjese en la ventana **Propiedades**. Si no ve una ventana **Propiedades**, presione **F4**.
> 2. Copie el valor de **URL** en el Portapapeles:<br/> ![Propiedades del proyecto](media/active-directory-develop-guidedsetup-javascriptspa-configure/vs-project-properties-screenshot.png)<br />
> 3. Vuelva a *Registro de aplicaciones* y pegue el valor como **URL de redireccionamiento**.

#### <a name="configure-your-javascript-spa"></a>Configuración de JavaScript SPA

1. En el archivo `index.html` creado durante la instalación del proyecto, agregue la información de registro de aplicación. Agregue el código siguiente en la parte superior, dentro de las etiquetas `<script></script>` en el cuerpo del archivo `index.html`:

    ```javascript
    var msalConfig = {
        auth: {
            clientId: "Enter_the_Application_Id_here",
            authority: "https://login.microsoftonline.com/Enter_the_Tenant_Info_Here"
        },
        cache: {
            cacheLocation: "localStorage",
            storeAuthStateInCookie: true
        }
    };
    ```

    Donde:
    - `Enter_the_Application_Id_here`: es el **identificador de aplicación (cliente)** de la aplicación que registró.
    - `Enter_the_Tenant_Info_Here` se establece como una de las opciones siguientes:
       - Si la aplicación admite **Solo las cuentas de este directorio organizativo**, reemplace este valor por el **identificador de inquilino** o el **nombre de inquilino** (por ejemplo, contoso.microsoft.com)
       - Si la aplicación admite **Cuentas en cualquier directorio organizativo**, reemplace este valor por `organizations`
       - Si la aplicación admite **Cuentas en cualquier directorio organizativo y cuentas Microsoft personales**, reemplace este valor por `common`. Para restringir la compatibilidad con *Personal Microsoft accounts only* (Solo cuentas Microsoft personales), reemplace este valor por `consumers`.
