---
title: 'Tutorial: Creación de flujos de usuario en Azure Active Directory B2C'
description: Aprenda a crear flujos de usuario en Azure Portal para habilitar el registro, el inicio de sesión y la edición de perfiles de usuario para las aplicaciones en Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 06/07/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 111196388d0e17ecde8a2055959f2f573e43ade8
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "67056347"
---
# <a name="tutorial-create-user-flows-in-azure-active-directory-b2c"></a>Tutorial: Creación de flujos de usuario en Azure Active Directory B2C

En las aplicaciones, puede que tenga [flujos de usuario](active-directory-b2c-reference-policies.md) que permitan a los usuarios registrarse, iniciar sesión o administrar su perfil. Puede crear varios flujos de usuario de diferentes tipos en un inquilino de Azure Active Directory (Azure AD) B2C y usarlos en las aplicaciones según sea necesario. Los flujos de usuario se pueden volver a usar en todas las aplicaciones.

En este artículo, aprenderá a:

> [!div class="checklist"]
> * Creación de un flujo de usuario de registro e inicio de sesión
> * Creación de un flujo de usuario de edición de perfil
> * Creación de un flujo de usuario de restablecimiento de contraseña

En este tutorial se muestra cómo crear flujos de usuario recomendados mediante Azure Portal. Si busca información acerca de cómo configurar el flujo de las credenciales de contraseña de propietario del recurso (ROPC) en la aplicación, consulte [Configuración del flujo de credenciales de contraseña de propietario del recurso en Azure AD B2C](configure-ropc.md).

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

## <a name="prerequisites"></a>Requisitos previos

[Registre las aplicaciones](tutorial-register-applications.md) que formen parte de los flujos de usuario que desee crear.

## <a name="create-a-sign-up-and-sign-in-user-flow"></a>Creación de un flujo de usuario de registro e inicio de sesión

Este flujo de usuario de registro y de inicio de sesión controla las experiencias de registro y de inicio de sesión del cliente con una sola configuración. A los usuarios se les lleva por el proceso correcto según el contexto.

1. Inicie sesión en el [Azure Portal](https://portal.azure.com).
1. Asegúrese de que usa el directorio que contiene el inquilino de Azure AD B2C. Para ello, haga clic en el **filtro de directorio y suscripción** en el menú superior y elija el directorio que contiene el inquilino.

    ![Cambiar al directorio de suscripción](./media/tutorial-create-user-flows/switch-directories.PNG)

1. Elija **Todos los servicios** en la esquina superior izquierda de Azure Portal, y busque y seleccione **Azure AD B2C**.
1. En el menú de la izquierda, en **Directivas**, seleccione **Flujos de usuario (directivas)** y **Nuevo flujo de usuario**.

    ![Selección del nuevo flujo de usuario](./media/tutorial-create-user-flows/signup-signin-user-flow.png)

1. En la pestaña **Recomendaciones**, seleccione el flujo de usuario **Sign up and sign in** (Registro e inicio de sesión).

    ![Selección de un flujo de usuario de registro e inicio de sesión](./media/tutorial-create-user-flows/signup-signin-type.png)

1. Escriba un **nombre** para el flujo de usuario. Por ejemplo, *signupsignin1*.
1. En **Proveedores de identidades**, seleccione **Registro por correo electrónico**.

    ![Establecimiento de las propiedades de flujo](./media/tutorial-create-user-flows/signup-signin-properties.png)

1. En **Atributos y notificaciones de usuario**, elija los atributos y las notificaciones que desea recopilar y enviar al usuario durante el registro. Por ejemplo, seleccione **Mostrar más** y elija los atributos y las notificaciones de **País o región**, **Nombre para mostrar** y **Código postal**. Haga clic en **OK**.

    ![Selección de atributos y notificaciones](./media/tutorial-create-user-flows/signup-signin-attributes.png)

1. Haga clic en **Crear** para agregar el flujo de usuario. El prefijo *B2C_1* se anexa automáticamente al nombre.

### <a name="test-the-user-flow"></a>Prueba del flujo de usuario

1. Seleccione el flujo de usuario que ha creado para abrir su página de información general y, luego, **Ejecutar flujo de usuario**.
1. En **Aplicación**, seleccione la aplicación web denominada *webapp1* que registró anteriormente. La **dirección URL de respuesta** debe mostrar `https://jwt.ms`.
1. Haga clic en **Ejecutar flujo de usuario** y, a continuación, seleccione **Registrarse ahora**.

    ![Ejecución del flujo de usuario](./media/tutorial-create-user-flows/signup-signin-run-now.PNG)

1. Escriba una dirección de correo electrónico válida, haga clic en **Enviar código de verificación**, escriba el código de verificación que reciba y seleccione **Comprobar código**.
1. Escriba la contraseña nueva y confírmela.
1. Seleccione su país y región, escriba el nombre que desee que aparezca, escriba un código postal y haga clic en **Crear**. El token se devuelve al `https://jwt.ms` y debe mostrarse.
1. Ahora puede ejecutar el flujo de usuario nuevo y debe ser capaz de iniciar sesión con la cuenta que ha creado. El token devuelto incluye las notificaciones que seleccionó para país/región, nombre y código postal.

## <a name="create-a-profile-editing-user-flow"></a>Creación de un flujo de usuario de edición de perfil

Si desea permitir que los usuarios editen sus perfiles en la aplicación, debe usar un flujo de usuario de edición de perfiles.

1. En el menú izquierdo de la página de información general del inquilino de Azure AD B2C, seleccione **Flujos de usuario (directivas)** y **Nuevo flujo de usuario**.
1. Seleccione el flujo de usuario **Edición de perfiles** en la pestaña Recomendado.
1. Escriba un **nombre** para el flujo de usuario. Por ejemplo, *profileediting1*.
1. En **Proveedores de identidades**, seleccione **Inicio de sesión de cuenta local**.
1. En **Atributos de usuario**, elija los atributos que desee que el cliente pueda modificar en su perfil. Por ejemplo, seleccione **Mostrar más** y elija los atributos y las notificaciones de **Nombre para mostrar** y **Puesto**. Haga clic en **OK**.
1. Haga clic en **Crear** para agregar el flujo de usuario. El prefijo *B2C_1* se anexa automáticamente al nombre.

### <a name="test-the-user-flow"></a>Prueba del flujo de usuario

1. Seleccione el flujo de usuario que ha creado para abrir su página de información general y, luego, **Ejecutar flujo de usuario**.
1. En **Aplicación**, seleccione la aplicación web denominada *webapp1* que registró anteriormente. La **dirección URL de respuesta** debe mostrar `https://jwt.ms`.
1. Haga clic en **Ejecutar flujo de usuario** y, a continuación, inicie sesión con la cuenta que creó anteriormente.
1. Ahora tiene la oportunidad de cambiar el nombre para mostrar y el puesto del usuario. Haga clic en **Continue**. El token se devuelve al `https://jwt.ms` y debe mostrarse.

## <a name="create-a-password-reset-user-flow"></a>Creación de un flujo de usuario de restablecimiento de contraseña

Para habilitar que los usuarios de la aplicación restablezcan la contraseña, se usa un flujo de usuario de restablecimiento de contraseña.

1. En el menú de la izquierda, seleccione **Flujos de usuario (directivas)** y **Nuevo flujo de usuario**.
1. Seleccione el flujo de usuario **Restablecimiento de contraseña** en la pestaña Recomendado.
1. Escriba un **nombre** para el flujo de usuario. Por ejemplo, *passwordreset1*.
1. En **Proveedores de identidades**, habilite **Reset password using email address** (Restablecer contraseña mediante la dirección de correo electrónico).
1. En Notificaciones de la aplicación, haga clic en **Mostrar más** y elija las notificaciones que quiere que se devuelvan en los tokens de autorización enviados de vuelta a su aplicación. Por ejemplo, seleccione **Id. de objeto del usuario**.
1. Haga clic en **OK**.
1. Haga clic en **Crear** para agregar el flujo de usuario. El prefijo *B2C_1* se anexa automáticamente al nombre.

### <a name="test-the-user-flow"></a>Prueba del flujo de usuario

1. Seleccione el flujo de usuario que ha creado para abrir su página de información general y, luego, **Ejecutar flujo de usuario**.
1. En **Aplicación**, seleccione la aplicación web denominada *webapp1* que registró anteriormente. La **dirección URL de respuesta** debe mostrar `https://jwt.ms`.
1. Haga clic en **Ejecutar flujo de usuario**, compruebe la dirección de correo electrónico de la cuenta que creó previamente y seleccione **Continuar**.
1. Ahora tiene la oportunidad de cambiar la contraseña para el usuario. Cambie la contraseña y seleccione **Continuar**. El token se devuelve al `https://jwt.ms` y debe mostrarse.

## <a name="next-steps"></a>Pasos siguientes

En este artículo, ha aprendido cómo:

> [!div class="checklist"]
> * Creación de un flujo de usuario de registro e inicio de sesión
> * Creación de un flujo de usuario de edición de perfil
> * Creación de un flujo de usuario de restablecimiento de contraseña

A continuación, aprenda a agregar proveedores de identidades a las aplicaciones para habilitar el inicio de sesión de usuario con proveedores como Azure AD, Amazon, GitHub, Facebook, LinkedIn, Microsoft o Twitter.

> [!div class="nextstepaction"]
> [Incorporación de proveedores de identidades a las aplicaciones en Azure AD B2C >](tutorial-add-identity-providers.md)