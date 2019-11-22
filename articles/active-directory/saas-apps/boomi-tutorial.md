---
title: 'Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con Boomi | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Boomi.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 40d034ff-7394-4713-923d-1f8f2ed8bf36
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1044a78d9bf1b52a7b0dd9e47d7db6d98b45299c
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/14/2019
ms.locfileid: "74081961"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-boomi"></a>Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con Boomi

En este tutorial aprenderá a integrar Boomi con Azure Active Directory (Azure AD). Al integrar Boomi con Azure AD, podrá:

* Controlar en Azure AD quién tiene acceso a Boomi.
* Permitir que los usuarios inicien sesión automáticamente en Boomi con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

Para más información sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción habilitada para el inicio de sesión único (SSO) en Boomi.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* Boomi admite el inicio de sesión único iniciado por **IDP**

## <a name="adding-boomi-from-the-gallery"></a>Adición de Boomi desde la galería

Para configurar la integración de Boomi en Azure AD, deberá agregar Boomi desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en [Azure Portal](https://portal.azure.com) con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **Boomi** en el cuadro de búsqueda.
1. Seleccione **Boomi** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.


## <a name="configure-and-test-azure-ad-single-sign-on-for-boomi"></a>Configuración y prueba del inicio de sesión único de Azure AD para Boomi

Configure y pruebe el inicio de sesión único de Azure AD con Boomi mediante un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es preciso establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Boomi.

Para configurar y probar el inicio de sesión único de Azure AD con Boomi, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    * **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    * **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único en Boomi](#configure-boomi-sso)** : para configurar los valores de inicio de sesión único en la aplicación.
    * **[Creación de un usuario de prueba en Boomi](#create-boomi-test-user)** : para tener un homólogo de B.Simon en Boomi vinculado a la representación del usuario en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En [Azure Portal](https://portal.azure.com/), en la página de integración de la aplicación **Boomi**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configurar el inicio de sesión único con SAML**, haga clic en el icono de edición o con forma de lápiz para abrir el cuadro de diálogo **Configuración básica de SAML** y modificar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la página **Configurar el inicio de sesión único con SAML**, escriba los valores de los siguientes campos:

    a. En el cuadro de texto **Identificador**, escriba una dirección URL: `https://platform.boomi.com/`

    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://platform.boomi.com/sso/<boomi-tenant>/saml`

    > [!NOTE]
    > El valor de dirección URL de respuesta no es real. Actualícelo con la dirección URL de respuesta real. Póngase en contacto con el [equipo de soporte técnico de Boomi](https://boomi.com/company/contact/) para obtener este valor. También puede hacer referencia a los patrones que se muestran en la sección **Configuración básica de SAML** de Azure Portal.

1. La aplicación espera las aserciones de SAML en un formato específico, lo cual requiere que se agreguen asignaciones de atributos personalizados a la configuración de los atributos del token de SAML. La siguiente captura de muestra la lista de atributos predeterminados.

    ![imagen](common/default-attributes.png)

1. Además de lo anterior, la aplicación Boomi espera que se devuelvan algunos atributos más, que se muestran a continuación, en la respuesta de SAML. Estos atributos también se rellenan previamente, pero puede revisarlos según sus requisitos.

    | NOMBRE |  Atributo de origen|
    | ---------------|  --------- |
    | FEDERATION_ID | user.mail |

1. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, busque **Certificado (Base64)** y seleccione **Descargar** para descargarlo y guardarlo en el equipo.

    ![Vínculo de descarga del certificado](common/certificatebase64.png)

1. En la sección **Set up Boomi** (Configurar Boomi), copie las direcciones URL que necesite.

    ![Copiar direcciones URL de configuración](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

En esta sección, va a crear un usuario de prueba llamado B.Simon en Azure Portal.

1. En el panel izquierdo de Azure Portal, seleccione **Azure Active Directory**, **Usuarios** y **Todos los usuarios**.
1. Seleccione **Nuevo usuario** en la parte superior de la pantalla.
1. En las propiedades del **usuario**, siga estos pasos:
   1. En el campo **Nombre**, escriba `B.Simon`.  
   1. En el campo **Nombre de usuario**, escriba username@companydomain.extension. Por ejemplo, `B.Simon@contoso.com`.
   1. Active la casilla **Show password** (Mostrar contraseña) y, después, anote el valor que se muestra en el cuadro **Contraseña**.
   1. Haga clic en **Create**(Crear).

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección va a permitir que B.Simon acceda a Boomi mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **Boomi**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.

   ![Vínculo "Usuarios y grupos"](common/users-groups-blade.png)

1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.

    ![Vínculo de Agregar usuario](common/add-assign-user.png)

1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que haya un valor de rol en la aserción de SAML, en el cuadro de diálogo **Seleccionar rol**, seleccione en la lista el rol adecuado para el usuario y haga clic en el botón **Seleccionar** en la parte inferior de la pantalla.
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-boomi-sso"></a>Configuración del inicio de sesión único en Boomi

1. En otra ventana del explorador web, inicie sesión en el sitio de la empresa de Boomi como administrador.

1. Vaya a **Company Name** (Nombre de la compañía) y haga clic en **Set up** (Configurar).

1. Haga clic en la pestaña **SSO Options** (Opciones de SSO) y realice los siguientes pasos.

    ![Configuración del inicio de sesión único en la aplicación](./media/boomi-tutorial/tutorial_boomi_11.png)

    a. Marque la casilla **Enable SAML Single Sign-On** (Habilitar el inicio de sesión único de SAML).

    b. Haga clic en **Import** (Importar) para cargar el certificado descargado de Azure AD en **Identity Provider Certificate** (Certificado del proveedor de identidades).

    c. En el cuadro de texto **Identity Provider Login URL** (URL de inicio de sesión del proveedor de identidades), coloque el valor de **Dirección URL de inicio de sesión** de la ventana de configuración de aplicaciones de Azure AD.

    d. Como **ubicación del identificador de federación**, seleccione el botón de radio **Federation Id is in FEDERATION_ID Attribute element** (El id. de federación está en el elemento de atributo FEDERATION_ID).

    e. Haga clic en el botón **Guardar** .

### <a name="create-boomi-test-user"></a>Creación de un usuario de prueba de Boomi

Para permitir que los usuarios de Azure AD inicien sesión en Boomi, es necesario aprovisionar Boomi con ellos. En el caso de Boomi, el aprovisionamiento es una tarea manual.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Para aprovisionar una cuenta de usuario, realice estos pasos:

1. Inicie sesión en el sitio de la empresa de Boomi como administrador.

1. Después de iniciar sesión, vaya a **User Management** (Administración de usuarios) y vaya a **Users** (Usuarios).

    ![Usuarios](./media/boomi-tutorial/tutorial_boomi_001.png "Usuarios")

1. Haga clic en el icono **+** , se abre el cuadro de diálogo **Add/Maintain User Roles** (Agregar o mantener roles de usuario).

    ![Usuarios](./media/boomi-tutorial/tutorial_boomi_002.png "Usuarios")

    ![Usuarios](./media/boomi-tutorial/tutorial_boomi_003.png "Usuarios")

    a. En el cuadro de texto **Dirección de correo electrónico del usuario**, escriba la dirección de correo electrónico de un usuario, por ejemplo, B.Simon@contoso.com.

    b. En el cuadro de texto **First name** (Nombre), escriba el nombre del usuario, en este caso, B.

    c. En el cuadro de texto **Apellidos**, escriba el nombre del usuario, en este caso, Simon.

    d. Escriba el **id. de federación** del usuario. Cada usuario debe tener un id. de federación que identifica de forma única al usuario dentro de la cuenta.

    e. Asigne el rol **Usuario estándar** al usuario. No asigne el rol Administrador porque les proporcionaría acceso normal a Atmosphere, además de acceso de inicio de sesión único.

    f. Haga clic en **OK**.

    > [!NOTE]
    > El usuario no recibirá un correo electrónico de notificación de bienvenida con una contraseña que se pueda usar para iniciar sesión en la cuenta de AtomSphere porque su contraseña se administra mediante el proveedor de identidades. Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de Boomi que proporcione Boomi para aprovisionar cuentas de usuario de AAD.

## <a name="test-sso"></a>Prueba de SSO

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de Boomi en el Panel de acceso, debería iniciar sesión automáticamente en la solución Boomi para la que configuró el inicio de sesión único. Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionales

- [Lista de tutoriales acerca de cómo integrar aplicaciones SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [¿Qué es el acceso a las aplicaciones y el inicio de sesión único con Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [¿Qué es el acceso condicional en Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prueba de Boomi con Azure AD](https://aad.portal.azure.com/)