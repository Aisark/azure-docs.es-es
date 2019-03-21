---
title: archivo de inclusión
description: archivo de inclusión
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/13/2018
ms.author: dadobali
ms.custom: include file
ms.openlocfilehash: 2b30f95e050887130db1b2395f51e543a50e25d0
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/19/2019
ms.locfileid: "58203761"
---
## <a name="register-your-application"></a>Registrar su aplicación

Puede registrar su aplicación de dos maneras distintas, como se describe en las dos secciones siguientes.

### <a name="option-1-express"></a>Opción 1: Express

1. Vaya al [Portal de registro de aplicaciones de Microsoft](https://apps.dev.microsoft.com/portal/register-app?appType=mobileAndDesktopApp&appTech=android&step=configure).
2. En **Application Name** (Nombre de aplicación) escriba el nombre de su aplicación.
3. Asegúrese de que la casilla **Guided Setup** (Instalación guiada) esté activada y, luego, seleccione **Create** (Crear).
4. Siga las instrucciones para obtener el identificador de aplicación y péguelo en el código.

### <a name="option-2-advanced"></a>Opción 2: Avanzado

1. Vaya al [Portal de registro de aplicaciones de Microsoft](https://apps.dev.microsoft.com/portal/register-app).
2. En el cuadro**Application Name** (Nombre de la aplicación) escriba el nombre de su aplicación.
3. Asegúrese de que la casilla **Guided Setup** (Instalación guiada) esté desactivada y, luego, seleccione **Create** (Crear).
4. Seleccione **Add Platform** (Agregar plataforma), seleccione **Native Application** (Aplicación nativa) y, luego, seleccione **Save** (Guardar).
5. En **app** > **java** > **{host}.{namespace}**, abra `MainActivity`. 
6. Reemplace *[Enter the application Id here]* (Escriba el Id. de aplicación aquí) con el Id. de la aplicación o del cliente:

    ```java
    final static String CLIENT_ID = "[Enter the application Id here]";
    ```
   <!-- Workaround for Docs conversion bug -->
7. En **app** > **manifests**, abra el archivo *AndroidManifest.xml*.
8. En `manifest\application`, agregue la siguiente actividad. La actividad `BrowserTabActivity` que permite a Microsoft volver a llamar a la aplicación una vez completada la autenticación:

    ```xml
    <!--Intent filter to capture System Browser calling back to our app after sign-in-->
    <activity
        android:name="com.microsoft.identity.client.BrowserTabActivity">
        <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />
            
            <!--Add in your scheme/host from registered redirect URI-->
            <!--By default, the scheme should be similar to 'msal[appId]' -->
            <data android:scheme="msal[Enter the application Id here]"
                android:host="auth" />
        </intent-filter>
    </activity>
    ```
   <!-- Workaround for Docs conversion bug -->
9. En `BrowserTabActivity`, reemplace `[Enter the application Id here]` por el Id. de la aplicación o del cliente.
