---
title: 'Configuración de la suscripción y del inicio de sesión con una cuenta de Amazon: Azure Active Directory B2C | Microsoft Docs'
description: Permita suscribirse e iniciar sesión a los clientes con cuentas de Amazon en las aplicaciones con Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: a796826892942879e42b2d8fd22b8cc0208a2174
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "66508629"
---
# <a name="set-up-sign-up-and-sign-in-with-an-amazon-account-using-azure-active-directory-b2c"></a>Configuración de la suscripción y del inicio de sesión con una cuenta de Amazon mediante Azure Active Directory B2C

## <a name="create-an-amazon-application"></a>Creación de una aplicación de Amazon

Para usar una cuenta de Amazon como [proveedor de identidades](active-directory-b2c-reference-oauth-code.md) en Azure Active Directory (Azure AD) B2C, tiene que crear una aplicación en su inquilino que la represente. Si aún no tiene una cuenta de Amazon, puede obtenerla en [https://www.amazon.com/](https://www.amazon.com/).

1. Inicie sesión en [Amazon Developer Center](https://login.amazon.com/) con las credenciales de su cuenta de Amazon.
2. Si aún no lo ha hecho, haga clic en **Sign up**(Registro), siga los pasos de registro para desarrolladores y acepte la directiva.
3. Seleccione **Register new application**(Registrar nueva aplicación).
4. Escriba la información de **Name** (Nombre), **Description** (Descripción) y **Privacy Notice URL** (Dirección URL de aviso de privacidad) y, a continuación, haga clic en **Save** (Guardar). El aviso de privacidad es una página administrada por el usuario que proporciona información de privacidad a los usuarios.
5. En la sección **Web Settings** (Configuración web), copie los valores de **Client ID** (Identificador de cliente). Seleccione **Show secret** (Mostrar secreto) para obtener el secreto de cliente y, a continuación, cópielo. Necesitará los dos para configurar la cuenta de Amazon como proveedor de identidades de su inquilino. **secreto de cliente** es una credencial de seguridad importante.
6. En la sección **Web Settings** (Configuración Web), seleccione **Edit** (Editar) y, a continuación, escriba `https://your-tenant-name.b2clogin.com` en **Allowed JavaScript Origins** (Orígenes de JavaScript permitidos) y `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` en **Allowed Return URLs** (Direcciones URL de devolución permitidas). Reemplace `your-tenant-name` por el nombre del inquilino. Cuando especifique el nombre de inquilino, escriba todas las letras en minúscula, aunque se haya definido con letras en mayúscula en Azure AD B2C.
7. Haga clic en **Save**(Guardar).

## <a name="configure-an-amazon-account-as-an-identity-provider"></a>Configuración de una cuenta de Amazon como proveedor de identidades

1. Inicie sesión en [Azure Portal](https://portal.azure.com/) como administrador global del inquilino de Azure AD B2C.
2. Asegúrese de que usa el directorio que contiene el inquilino de Azure AD B2C. Para ello, haga clic en el **filtro de directorio y suscripción** en el menú superior y elija el directorio que contiene el inquilino.
3. Elija **Todos los servicios** en la esquina superior izquierda de Azure Portal, busque y seleccione **Azure AD B2C**.
4. Seleccione **Proveedores de identidades** y, después, **Agregar**.
5. Escriba la información de **Name** (Nombre). Por ejemplo, escriba*Amazon*.
6. Seleccione **Identity provider type** (Tipo de proveedor de identidades), seleccione **Amazon** y haga clic en **OK** (Aceptar).
7. Seleccione **Set up this identity provider** (Configurar este proveedor de identidades) y escriba el identificador de cliente que anotó anteriormente como el valor de **Client ID**, y el secreto de cliente que registró como **Client Secret** de la aplicación de Amazon que creó anteriormente.
8. Haga clic en **OK** (Aceptar) y, luego, en **Create** (Crear) para guardar la configuración de Amazon.

