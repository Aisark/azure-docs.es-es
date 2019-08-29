---
title: 'Configuración de la suscripción y el inicio de sesión con una cuenta Microsoft: Azure Active Directory B2C'
description: Permita suscribirse e iniciar sesión a los clientes con cuentas Microsoft en las aplicaciones con Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 14aff313ece93feabc39194ba6859e3bca6be3bc
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2019
ms.locfileid: "69622396"
---
# <a name="set-up-sign-up-and-sign-in-with-a-microsoft-account-using-azure-active-directory-b2c"></a>Configuración de la suscripción y del inicio de sesión con una cuenta Microsoft mediante Azure Active Directory B2C

## <a name="create-a-microsoft-account-application"></a>Creación de una aplicación de cuenta Microsoft

Para usar una cuenta de Microsoft como [proveedor de identidades](active-directory-b2c-reference-oidc.md) en Azure Active Directory (Azure AD) B2C, debe crear una aplicación en el inquilino de Azure AD. El inquilino de Azure AD no es el mismo que el usuario de Azure AD B2C. Si todavía no tiene una cuenta Microsoft, puede obtenerla en [https://www.live.com/](https://www.live.com/).

1. Inicie sesión en el [Azure Portal](https://portal.azure.com).
1. Asegúrese de que usa el directorio que contiene el inquilino de Azure AD. Para ello, seleccione el filtro **Directorio y suscripción** que se encuentra en el menú superior y elija el directorio que contiene el inquilino de Azure AD.
1. Elija **Todos los servicios** en la esquina superior izquierda de Azure Portal, y busque y seleccione **Registros de aplicaciones**.
1. Seleccione **Nuevo registro**.
1. Escriba el **nombre** de la aplicación. Por ejemplo, *MSAapp1*.
1. En **Supported account types** (Tipos de cuenta compatibles), seleccione **Cuentas en cualquier directorio organizativo y cuentas Microsoft personales (por ejemplo, Skype, Xbox, Outlook.com)** . Esta opción define como destino el conjunto más amplio de identidades de Microsoft.

   Para más información sobre las distintas opciones de tipo de cuenta, consulte [Inicio rápido: Registro de una aplicación en la plataforma de identidad de Microsoft](../active-directory/develop/quickstart-register-app.md).
1. En **URI de redirección (opcional)** , seleccione **Web** y escriba `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` en el cuadro de texto. Reemplace `your-tenant-name` por el nombre del inquilino de Azure AD B2C.
1. Seleccione **Registrar**.
1. Registre el **Id. de aplicación (cliente)** que se muestra en la página de información general de la aplicación. Necesitará esta información al configurar el proveedor de identidades en la siguiente sección.
1. Seleccione **Certificates & secrets** (Certificados y secretos)
1. Haga clic en **Nuevo secreto de cliente**.
1. Escriba una **Descripción** del secreto, por ejemplo, *contraseña de la aplicación 1* y haga clic en **Agregar**.
1. Anote la contraseña de la aplicación que se muestra en la columna **VALUE** (VALOR). Necesitará esta información al configurar el proveedor de identidades en la siguiente sección.

## <a name="configure-a-microsoft-account-as-an-identity-provider"></a>Configuración de una cuenta Microsoft como proveedor de identidades

1. Inicie sesión en [Azure Portal](https://portal.azure.com/) como administrador global del inquilino de Azure AD B2C.
1. Asegúrese de usar el directorio que contiene el inquilino de Azure AD B2C. Para ello, seleccione el filtro **Directorio y suscripción** en el menú superior y luego el directorio que contiene el inquilino.
1. Elija **Todos los servicios** en la esquina superior izquierda de Azure Portal, busque y seleccione **Azure AD B2C**.
1. Seleccione **Proveedores de identidades** y, luego, **Cuenta Microsoft**.
1. Escriba un **nombre**. Por ejemplo, *MSA*.
1. En **Id. de cliente**, escriba el identificador (cliente) de la aplicación de Azure AD que creó anteriormente.
1. En **Secreto de cliente**, escriba el secreto de cliente que ha anotado.
1. Seleccione **Guardar**.
