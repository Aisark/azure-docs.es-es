---
title: 'Tutorial: Integración de Azure Active Directory con Pingboard | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Pingboard.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a26418a5159781eab96cf7730d669ccb65742d86
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/18/2019
ms.locfileid: "57861492"
---
# <a name="tutorial-azure-active-directory-integration-with-pingboard"></a>Tutorial: Integración de Azure Active Directory con Pingboard

En este tutorial, aprenderá a integrar Pingboard con Azure Active Directory (Azure AD).

La integración de Pingboard con Azure AD le proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Pingboard.
- Puede permitir que los usuarios inicien sesión automáticamente en Pingboard (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: el nuevo Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Pingboard, se necesitan los siguientes elementos:

- Una suscripción de Azure AD
- Suscripción a Pingboard con la opción de inicio de sesión único habilitada

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. El escenario descrito en este tutorial consta de dos bloques de creación principales:

1. Agregar Pingboard desde la galería
1. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-pingboard-from-the-gallery"></a>Agregar Pingboard desde la galería
Para configurar la integración de Pingboard en Azure AD, deberá agregarlo desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Pingboard desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

1. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Aplicaciones empresariales][2]

1. Haga clic en el botón **Agregar** situado en la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

1. En el cuadro de búsqueda, escriba **Pingboard**, seleccione **Pingboard** en el panel de resultados y, a continuación, haga clic en el botón **Agregar** para agregar la aplicación.

    ![Pingboard en la lista de resultados](./media/pingboard-tutorial/tutorial_pingboard_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, configurará y probará el inicio de sesión único de Azure AD con Pingboard con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Pingboard para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Pingboard.

Esta relación de vínculo se establece mediante la asignación del valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en Pingboard.

Para configurar y probar el inicio de sesión único de Azure AD con Pingboard, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para que los usuarios puedan usar esta característica.
1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**, para probar el inicio de sesión único de Azure AD con Britta Simon.
1. **[Crear un usuario de prueba de Pingboard](#create-a-pingboard-test-user)**: para tener un homólogo de Britta Simon en Pingboard que esté vinculado a la representación del usuario en Azure AD.
1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**, para permitir que Britta Simon use el inicio de sesión único de Azure AD.
1. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y configurará el inicio de sesión único en la aplicación Pingboard.

**Para configurar el inicio de sesión único de Azure AD con Pingboard, realice los pasos siguientes:**

1. En Azure Portal, en la página de integración de aplicaciones de **Pingboard**, haga clic en **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

1.  En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.

    ![Cuadro de diálogo Inicio de sesión único](./media/pingboard-tutorial/tutorial_pingboard_samlbase.png)

1. En la sección **Dominio y direcciones URL de Pingboard**, realice los siguientes pasos si quiere configurar la aplicación en el modo iniciado por **IDP**:

    ![IDP de información de dominio y direcciones URL de inicio de sesión único de Pingboard](./media/pingboard-tutorial/tutorial_pingboard_url.png)

     a. En el cuadro de texto **Identificador**, escriba el valor como `http://app.pingboard.com/sp`.

    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://<entity-id>.pingboard.com/auth/saml/consume`.

1. Active **Mostrar configuración avanzada de URL**, si desea volver a configurar la aplicación en modo iniciado por **SP**:

    ![SP de información de dominio y direcciones URL de inicio de sesión único de Pingboard](./media/pingboard-tutorial/tutorial_pingboard_sp_initiated01.png)

     En el cuadro de texto **URL de inicio de sesión**, escriba la dirección URL con el siguiente patrón: `https://<sub-domain>.pingboard.com/sign_in`.

    > [!NOTE]
    > Tenga en cuenta que estos no son valores reales. Actualice estos valores con los valores reales de URL de respuesta y URL de inicio de sesión. Póngase en contacto con el [equipo de soporte técnico de Pingboard](https://support.pingboard.com/) para obtener estos valores.

1. En la sección **Certificado de firma de SAML**, haga clic en **XML de metadatos** y luego guarde el archivo XML en el equipo.

    ![XML de metadatos de Pingboard](./media/pingboard-tutorial/tutorial_pingboard_certificate.png)

1. Haga clic en el botón **Guardar** .

    ![Botón Configurar inicio de sesión único](./media/pingboard-tutorial/tutorial_general_400.png)

1. Para configurar SSO en Pingboard, abra una nueva ventana del explorador e inicie sesión en su cuenta de Pingboard. Debe ser un administrador de Pingboard para configurar el inicio de sesión único.

1. En el menú superior, seleccione **Aplicaciones > Integraciones**.

    ![Configurar inicio de sesión único](./media/pingboard-tutorial/Pingboard_integration.png)

1. En la página **Integraciones**, busque el icono **"Azure Active Directory"** y haga clic en él.

    ![Integración de inicio de sesión único de Pingboard](./media/pingboard-tutorial/Pingboard_aad.png)

1. En el estado modal que sigue, haga clic en **"Configurar"**.

    ![Botón de configuración de Pingboard](./media/pingboard-tutorial/Pingboard_configure.png)

1. En la siguiente página, podrá ver el mensaje "La integración de inicio de sesión único de Azure SSO está habilitada". Abra el archivo XML de metadatos descargado en el Bloc de notas y pegue el contenido en **metadatos de IDP**.

    ![Pantalla de configuración de inicio de sesión único de Pingboard](./media/pingboard-tutorial/Pingboard_sso_configure.png)

1. El archivo se validará y, si todo está correcto, se habilitará el inicio de sesión único.

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

![Creación de un usuario de prueba de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo de **Azure Portal**, haga clic en el icono de **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/pingboard-tutorial/create_aaduser_01.png) 

1. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y haga clic en **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/pingboard-tutorial/create_aaduser_02.png)

1. En la parte superior del diálogo, haga clic en **Agregar** para abrir el diálogo **Usuario**.

    ![Botón Agregar](./media/pingboard-tutorial/create_aaduser_03.png) 

1. En la página de diálogo **Usuario**, realice los siguientes pasos:

    ![Cuadro de diálogo Usuario](./media/pingboard-tutorial/create_aaduser_04.png)

     a. En el cuadro de texto **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la **dirección de correo electrónico** de Britta Simon.

    c. Seleccione **Mostrar contraseña** y anote el valor del cuadro **Contraseña**.

    d. Haga clic en **Create**(Crear).

### <a name="create-a-pingboard-test-user"></a>Crear un usuario de prueba de Pingboard

El objetivo de esta sección es crear una usuaria llamada Britta Simon en Pingboard. Pingboard admite el aprovisionamiento automático de usuarios, que está habilitado de forma predeterminada. [Aquí](pingboard-provisioning-tutorial.md) puede encontrar más información sobre cómo configurar el aprovisionamiento automático de usuarios.

**Para crear un usuario manualmente, siga los pasos siguientes:**

1. Inicie sesión en el sitio de la compañía Pingboard como administrador.

1. Haga clic en el botón **“Add Employee”** (Agregar empleado) en la página **Directory** (Directorio).

    ![Agregar empleado](./media/pingboard-tutorial/create_testuser_add.png)

1. En la página del cuadro de diálogo **Agregar empleado**, realice los siguientes pasos:

    ![Invitar a contactos](./media/pingboard-tutorial/create_testuser_name.png)

     a. En el cuadro de texto **Nombre completo**, escriba el nombre completo de un usuario; por ejemplo, **Britta Simon**.

    b. En el **correo electrónico** cuadro de texto, escriba la dirección de correo electrónico del usuario como **brittasimon\@contoso.com**.

    c. En el cuadro de texto **Puesto**, escriba el puesto de Britta Simon.

    d. En la lista desplegable **Ubicación**, seleccione la ubicación de Britta Simon.

    e. Haga clic en **Agregar**.

1. Se muestra una pantalla de confirmación para confirmar la adición del usuario.

    ![confirmar](./media/pingboard-tutorial/create_testuser_confirm.png)

    > [!NOTE]
    > El titular de la cuenta de Azure Active Directory recibirá un mensaje de correo y seguirá un vínculo para confirmar su cuenta antes de que se active.

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, podrá darle permiso a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Pingboard.

![Asignar usuario][200] 

**Para asignar a Britta Simon a Pingboard, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, navegue a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

1. En la lista de aplicaciones, seleccione **Pingboard**.

    ![Vínculo a Pingboard en la lista de aplicaciones](./media/pingboard-tutorial/tutorial_pingboard_app.png) 

1. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202] 

1. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

1. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

1. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.
Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](../user-help/active-directory-saas-access-panel-introduction.md).

Al hacer clic en el icono de Pingboard en el panel de acceso, debería iniciar sesión automáticamente en su aplicación Pingboard.

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Configuración del aprovisionamiento de usuarios](pingboard-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/pingboard-tutorial/tutorial_general_01.png
[2]: ./media/pingboard-tutorial/tutorial_general_02.png
[3]: ./media/pingboard-tutorial/tutorial_general_03.png
[4]: ./media/pingboard-tutorial/tutorial_general_04.png

[100]: ./media/pingboard-tutorial/tutorial_general_100.png

[200]: ./media/pingboard-tutorial/tutorial_general_200.png
[201]: ./media/pingboard-tutorial/tutorial_general_201.png
[202]: ./media/pingboard-tutorial/tutorial_general_202.png
[203]: ./media/pingboard-tutorial/tutorial_general_203.png
