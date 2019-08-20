---
title: 'Tutorial: Integración de Azure Active Directory con Zoom | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Zoom.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 0ebdab6c-83a8-4737-a86a-974f37269c31
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/08/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8e36d1bb91e70e21ee1940e189bfedaebafa4412
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2019
ms.locfileid: "68975944"
---
# <a name="tutorial-integrate-zoom-with-azure-active-directory"></a>Tutorial: Integración de Zoom con Azure Active Directory

En este tutorial, obtendrá información sobre cómo integrar Zoom con Azure Active Directory (Azure AD). Al integrar Zoom con Azure AD, puede hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a Zoom.
* Permitir que los usuarios inicien sesión automáticamente en Zoom con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

Para más información sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Suscripción habilitada para el inicio de sesión único (SSO) en Zoom.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* Zoom admite el inicio de sesión único iniciado por **SP**.

## <a name="adding-zoom-from-the-gallery"></a>Adición de Zoom desde la galería

Para configurar la integración de Zoom en Azure AD, deberá agregar Zoom desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en [Azure Portal](https://portal.azure.com) con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **Zoom** en el cuadro de búsqueda.
1. Seleccione **Zoom** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-zoom"></a>Configuración y prueba del inicio de sesión único de Azure AD para Zoom

Configure y pruebe el inicio de sesión único de Azure AD con Zoom mediante un usuario de prueba llamado **B. Simon**. Para que el inicio de sesión único funcione, es necesario establecer una relación de vinculación entre un usuario de Azure AD y el usuario relacionado de Zoom.

Para configurar y probar el inicio de sesión único de Azure AD con Zoom, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
2. **[Configuración del inicio de sesión único en Zoom](#configure-zoom-sso)** , para configurar los valores de Inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba de Zoom](#create-zoom-test-user)** , para tener un homólogo de B. Simon en Zoom que esté vinculado a su representación en Azure AD.
3. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

## <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En [Azure Portal](https://portal.azure.com/), en la página de integración de aplicaciones de **Zoom**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, seleccione **SAML**.
1. En la página **Configurar el inicio de sesión único con SAML**, haga clic en el icono de edición o con forma de lápiz para abrir el cuadro de diálogo **Configuración básica de SAML** y modificar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la sección **Configuración básica de SAML**, siga estos pasos:

    a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<companyname>.zoom.us`

    b. En el cuadro de texto **Identificador (id. de entidad)** , escriba una dirección URL con el siguiente patrón: `<companyname>.zoom.us`

    > [!NOTE]
    > Estos valores no son reales. Actualice estos valores con la dirección URL y el identificador reales de inicio de sesión. Contacte con el [equipo de soporte al cliente de Zoom](https://support.zoom.us/hc/en-us) para obtener estos valores. También puede hacer referencia a los patrones que se muestran en la sección **Configuración básica de SAML** de Azure Portal.

5. La aplicación Zoom espera las aserciones de SAML en un formato específico, lo que requiere que se agreguen asignaciones de atributos personalizados a la configuración de los atributos del token de SAML. La siguiente captura de muestra la lista de atributos predeterminados. Haga clic en el icono  **Editar**  para abrir el cuadro de diálogo  **Atributos de usuario** .

    ![imagen](common/edit-attribute.png)

6. Además de lo anterior, la aplicación Zoom espera que se usen algunos atributos más en la respuesta de SAML. En la sección  **Notificaciones del usuario**  del cuadro de diálogo  **Atributos de usuario** , realice los siguientes pasos para agregar el atributo Token SAML como se muestra en la tabla siguiente: 

    | NOMBRE | Espacio de nombres  |  Atributo de origen|
    | ---------------| --------------- | --------- |
    | Dirección de correo electrónico  | user.mail  | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/mail` |
    | Nombre  | user.givenname  | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` |
    | Apellidos  | user.surname  | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` |
    | Número de teléfono  | user.telephonenumber  | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/phone` |
    | department  | user.department  | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/department` |
    | role |    user.assignedrole |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/role` |

    > [!NOTE]
    > Haga clic [aquí](https://docs.microsoft.com/azure/active-directory/develop/active-directory-enterprise-app-role-management) para saber cómo configurar el valor Role en Azure AD.

    a. Haga clic en **Agregar nueva notificación** para abrir el cuadro de diálogo **Administrar las notificaciones del usuario**.

    ![imagen](common/new-save-attribute.png)

    ![imagen](common/new-attribute-details.png)

    b. En el cuadro de texto **Nombre**, escriba el nombre que se muestra para la fila.

    c. Seleccione **Atributo** como origen.

    d. En la lista **Atributo de origen**, escriba el valor de atributo que se muestra para esa fila.

    e. Haga clic en **Aceptar**.

    f. Haga clic en **Save**(Guardar).

    > [!NOTE]
    > Zoom puede esperar una reclamación de grupo en la carga útil de SAML, por lo que si ha creado algún grupo, póngase en contacto con el [equipo de soporte técnico de Zoom](https://support.zoom.us/hc/en-us) con la información del grupo para que también puedan configurar esta información de grupo al final. También debe proporcionar el identificador de objeto al [equipo de soporte técnico de Zoom](https://support.zoom.us/hc/en-us) para que puedan configurarlo al final. Siga el [documento](https://support.zoom.us/hc/en-us/articles/115005887566) para obtener el identificador de objeto.

4. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, busque **Certificado (Base64)** y seleccione **Descargar** para descargarlo y guardarlo en el equipo.

    ![Vínculo de descarga del certificado](common/certificatebase64.png)

6. En la sección **Configurar Zoom**, copie las direcciones URL adecuadas según sus necesidades.

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

En esta sección, va a permitir que B. Simon acceda a Zoom mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **Zoom**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"](common/users-groups-blade.png)

1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.

    ![Vínculo de Agregar usuario](common/add-assign-user.png)

1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que haya un valor de rol en la aserción de SAML, en el cuadro de diálogo **Seleccionar rol**, seleccione en la lista el rol adecuado para el usuario y haga clic en el botón **Seleccionar** en la parte inferior de la pantalla.
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-zoom-sso"></a>Configuración del inicio de sesión único de Zoom

1. En otra ventana del explorador web, inicie sesión en su sitio de la compañía de Zoom como administrador.

2. Haga clic en la pestaña **Inicio de sesión único** .

    ![Pestaña de Inicio de sesión único](./media/zoom-tutorial/ic784700.png "Inicio de sesión único")

3. Haga clic en la pestaña **Control de seguridad** y luego vaya a la configuración de **Inicio de sesión único**.

4. En la sección Inicio de sesión único, siga estos pasos:

    ![Sección de Inicio de sesión único](./media/zoom-tutorial/ic784701.png "Inicio de sesión único")

    a. En el cuadro de texto **Dirección URL de la página de inicio de sesión**, pegue el valor de la **dirección URL de inicio de sesión** que ha copiado de Azure Portal.

    b. Para el valor **Dirección URL de cierre de sesión**, debe ir a Azure Portal y hacer clic en **Azure Active Directory** a la izquierda y, a continuación, ir a **Registros de aplicaciones**.

    ![Botón Azure Active Directory](./media/zoom-tutorial/appreg.png)

    c. Haga clic en **Puntos de conexión**.

    ![Botón del punto de conexión](./media/zoom-tutorial/endpoint.png)

    d. Copie el valor de **Punto de conexión de cierre de sesión de SAML-P** y péguelo en el cuadro de texto **Dirección URL de cierre de sesión**.

    ![Botón de Copiar punto de conexión](./media/zoom-tutorial/endpoint1.png)

    e. Abra el certificado codificado en base 64 en el Bloc de notas, copie su contenido en el Portapapeles y luego péguelo en el cuadro de texto **Certificado de proveedor de identidades** .

    f. En el cuadro de texto **Issuer** (Emisor), pegue el valor de **Identificador Azure AD** que ha copiado de Azure Portal. 

    g. Haga clic en **Save**(Guardar).

    > [!NOTE]
    > Para obtener más información, visite la documentación de Zoom [https://zoomus.zendesk.com/hc/articles/115005887566](https://zoomus.zendesk.com/hc/articles/115005887566).

### <a name="create-zoom-test-user"></a>Creación de un usuario de prueba de Zoom

Para permitir que los usuarios de Azure AD inicien sesión en Zoom, deben aprovisionarse en Zoom. En el caso de Zoom, el aprovisionamiento es una tarea manual.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Para aprovisionar una cuenta de usuario, realice estos pasos:

1. Inicie sesión en su sitio de la compañía de **Zoom** como administrador.

2. En la pestaña **Administración de cuentas** haga clic en **Administración de usuarios**.

3. En la sección Administración de usuarios, haga clic en **Agregar usuarios**.

    ![Administración de usuarios](./media/zoom-tutorial/ic784703.png "Administración de usuarios")

4. En la página **Agregar usuarios** , realice los pasos siguientes:

    ![Agregar usuarios](./media/zoom-tutorial/ic784704.png "Agregar usuarios")

    a. Como **Tipo de usuario**, seleccione **Básico**.

    b. En el cuadro de texto **Correos electrónicos** , escriba la dirección de correo electrónico de una cuenta de Azure AD válida que quiera suministrar.

    c. Haga clic en **Agregar**.

> [!NOTE]
> Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de Zoom que proporcione Zoom para aprovisionar cuentas de usuario de Azure Active Directory.

## <a name="test-sso"></a>Prueba de SSO 

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de Zoom en el panel de acceso y debería iniciar sesión automáticamente en la versión de Zoom para la que configuró el inicio de sesión único. Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionales

- [Lista de tutoriales acerca de cómo integrar aplicaciones SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [¿Qué es el acceso a las aplicaciones y el inicio de sesión único con Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [¿Qué es el acceso condicional en Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

