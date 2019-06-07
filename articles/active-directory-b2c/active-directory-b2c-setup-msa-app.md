---
title: 'Configuración de la suscripción y del inicio de sesión con una cuenta Microsoft: Azure Active Directory B2C | Microsoft Docs'
description: Permita suscribirse e iniciar sesión a los clientes con cuentas Microsoft en las aplicaciones con Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: c0d236fbe350a36bc67e26349fe3c3145bd01616
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/04/2019
ms.locfileid: "66508447"
---
# <a name="set-up-sign-up-and-sign-in-with-a-microsoft-account-using-azure-active-directory-b2c"></a>Configuración de la suscripción y del inicio de sesión con una cuenta Microsoft mediante Azure Active Directory B2C

## <a name="create-a-microsoft-account-application"></a>Creación de una aplicación de cuenta Microsoft

Para usar una cuenta Microsoft como [proveedor de identidades](active-directory-b2c-reference-oidc.md) en Azure Active Directory (Azure AD) B2C, es preciso crear una aplicación en un inquilino que la represente. Si aún no tiene una cuenta Microsoft, puede obtenerla en [https://www.live.com/](https://www.live.com/).

1. Inicie sesión en el [Portal de registro de aplicaciones de Microsoft](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) con las credenciales de su cuenta Microsoft.
2. En la esquina superior derecha, seleccione **Agregar una aplicación**.
3. Escriba el **nombre** de la aplicación. Por ejemplo, *MSAapp1*.
4. Seleccione **Generar nueva contraseña** y asegúrese de copiar la contraseña que se va a usar al configurar el proveedor de identidades. Además, copie el **Id. de aplicación**. 
5. Seleccione **Agregar plataforma** y elija **Web**.
4. Escriba `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` en **Direcciones URL de redireccionamiento**. Reemplace `your-tenant-name` por el nombre del inquilino.
5. Seleccione **Guardar**.

## <a name="configure-a-microsoft-account-as-an-identity-provider"></a>Configuración de una cuenta Microsoft como proveedor de identidades

1. Inicie sesión en [Azure Portal](https://portal.azure.com/) como administrador global del inquilino de Azure AD B2C.
2. Asegúrese de que usa el directorio que contiene el inquilino de Azure AD B2C. Para ello, haga clic en el **filtro de directorio y suscripción** en el menú superior y elija el directorio que contiene el inquilino.
3. Elija **Todos los servicios** en la esquina superior izquierda de Azure Portal, busque y seleccione **Azure AD B2C**.
4. Seleccione **Proveedores de identidades** y, después, **Agregar**.
5. Especifique un **nombre**. Por ejemplo, escriba *MSA*.
6. Haga clic en **Tipo de proveedor de identidades**, seleccione **Cuenta Microsoft** y haga clic en **Aceptar**.
7. Seleccione **Configurar este proveedor de identidades** y escriba el identificador de aplicación que anotó anteriormente como **identificador de cliente** y la contraseña que registró como **secreto de cliente** de la aplicación de la cuenta Microsoft que creó antes.
8. Haga clic en **OK** (Aceptar) y en **Create** (Crear) para guardar la configuración de la cuenta Microsoft.

