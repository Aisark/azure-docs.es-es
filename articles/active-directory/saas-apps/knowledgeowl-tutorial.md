---
title: 'Tutorial: Integración de Azure Active Directory con KnowledgeOwl | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y KnowledgeOwl.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 2ae30996-864d-4872-90bc-f770e1ea159a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c8c86ad6e4b11f21c648083fac35e15eec7658c1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "60262196"
---
# <a name="tutorial-azure-active-directory-integration-with-knowledgeowl"></a>Tutorial: Integración de Azure Active Directory con KnowledgeOwl

En este tutorial, obtendrá información sobre cómo integrar KnowledgeOwl con Azure Active Directory (Azure AD).

La integración de KnowledgeOwl con Azure AD le proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a KnowledgeOwl.
- Puede permitir que los usuarios inicien sesión automáticamente en KnowledgeOwl (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con KnowledgeOwl, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único de KnowledgeOwl.

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. El escenario descrito en este tutorial consta de dos bloques de creación principales:

1. Agregar KnowledgeOwl desde la galería
1. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-knowledgeowl-from-the-gallery"></a>Agregar KnowledgeOwl desde la galería
Para configurar la integración de KnowledgeOwl en Azure AD, es preciso agregar KnowledgeOwl desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar KnowledgeOwl desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

1. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
1. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

1. En el cuadro de búsqueda, escriba **KnowledgeOwl**, seleccione **KnowledgeOwl** en el panel de resultados y, a continuación, haga clic en el botón **Agregar** para agregar la aplicación.

    ![KnowledgeOwl en la lista de resultados](./media/knowledgeowl-tutorial/tutorial_knowledgeowl_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con KnowledgeOwl con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de KnowledgeOwl para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de KnowledgeOwl.

Para configurar y probar el inicio de sesión único de Azure AD con KnowledgeOwl, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para que los usuarios puedan usar esta característica.
1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**, para probar el inicio de sesión único de Azure AD con Britta Simon.
1. **[Crear un usuario de prueba de KnowledgeOwl](#create-a-knowledgeowl-test-user)**: para tener un homólogo de Britta Simon en KnowledgeOwl que esté vinculado a la representación del usuario en Azure AD.
1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**, para permitir que Britta Simon use el inicio de sesión único de Azure AD.
1. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y lo configurará en la aplicación KnowledgeOwl.

**Para configurar el inicio de sesión único de Azure AD con KnowledgeOwl, siga estos pasos:**

1. En la página de integración de la aplicación **KnowledgeOwl** de Azure Portal, haga clic en **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

1. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Cuadro de diálogo Inicio de sesión único](./media/knowledgeowl-tutorial/tutorial_knowledgeowl_samlbase.png)

1. En la sección **Dominio y direcciones URL de KnowledgeOwl**, realice los siguientes pasos si quiere configurar la aplicación en el modo que inicia **IDP**:

    ![Información sobre el dominio y las direcciones URL de inicio de sesión único de KnowledgeOwl](./media/knowledgeowl-tutorial/tutorial_knowledgeowl_url.png)

     a. En el cuadro de texto **Identificador (id. de entidad)**, escriba una dirección URL con el siguiente patrón:

    |||
    |-|-|
    | `https://app.knowledgeowl.com/sp`|
    | `https://app.knowledgeowl.com/sp/id/<unique ID>`|
    |||

    b. En el cuadro de texto **URL de respuesta** , escriba una dirección URL con el siguiente patrón:

    |||
    |-|-|
    | `https://subdomain.knowledgeowl.com/help/saml-login`|
    | `https://subdomain.knowledgeowl.com/docs/saml-login`|
    | `https://subdomain.knowledgeowl.com/home/saml-login`|
    | `https://privatedomain.com/help/saml-login`|
    | `https://privatedomain.com/docs/saml-login`|
    | `https://privatedomain.com/home/saml-login`|
    |||

1. Active **Mostrar configuración avanzada de URL** y siga estos pasos si desea configurar la aplicación en el modo iniciado por **SP**:

    ![Información sobre el dominio y las direcciones URL de inicio de sesión único de KnowledgeOwl](./media/knowledgeowl-tutorial/tutorial_knowledgeowl_url1.png)

    En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón:
    
    |||
    |-|-|
    | `https://subdomain.knowledgeowl.com/help/saml-login`|
    | `https://subdomain.knowledgeowl.com/docs/saml-login`|
    | `https://subdomain.knowledgeowl.com/home/saml-login`|
    | `https://privatedomain.com/help/saml-login`|
    | `https://privatedomain.com/docs/saml-login`|
    | `https://privatedomain.com/home/saml-login`|
    |||
     
    > [!NOTE]
    > Estos valores no son reales. Deberá actualizar este valor con la dirección URL de respuesta, el identificador y la dirección URL de inicio de sesión reales, tal como se explica más adelante en el tutorial.

1. La aplicación KnowledgeOwl espera las aserciones de SAML en un formato específico, que requiere que se agreguen asignaciones de atributos personalizados a la configuración de los atributos del token de SAML. Configure las siguientes notificaciones para esta aplicación. Puede administrar los valores de estos atributos en la sección "**Atributos de usuario**" de la página de integración de aplicaciones.

    ![Configurar inicio de sesión único](./media/knowledgeowl-tutorial/attribute.png)

1. En la sección **Atributos de usuario** del cuadro de diálogo **Inicio de sesión único**, configure el atributo del token de SAML como muestra la imagen anterior y realice los siguientes pasos:
    
    | Nombre del atributo | Valor de atributo | Espacio de nombres|
    | ------------------- | -------------------- | -----|
    | ssoid | user.mail | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|
    
     a. Haga clic en **Agregar atributo** para abrir el cuadro de diálogo **Agregar atributo**.
    
    ![Configurar inicio de sesión único](./media/knowledgeowl-tutorial/tutorial_attribute_04.png)

    ![Configurar inicio de sesión único](./media/knowledgeowl-tutorial/tutorial_attribute_05.png)

    b. En el cuadro de texto **Nombre**, escriba el nombre que se muestra para la fila.
    
    c. En la lista **Valor**, seleccione el atributo que se muestra para esa fila.

    d. En la lista **Espacio de nombres**, escriba el valor del espacio de nombres que se muestra para esa fila.
    
    e. Haga clic en **Aceptar**.

1. En la sección **Certificado de firma de SAML**, haga clic en **Certificado (sin procesar)** y, a continuación, guarde el archivo de certificado en el equipo.

    ![Vínculo de descarga del certificado](./media/knowledgeowl-tutorial/tutorial_knowledgeowl_certificate.png) 

1. Haga clic en el botón **Guardar** .

    ![Botón Configurar inicio de sesión único](./media/knowledgeowl-tutorial/tutorial_general_400.png)
    
1. En la sección **Configuración de KnowledgeOwl**, haga clic en **Configurar KnowledgeOwl** para abrir la ventana **Configurar inicio de sesión**. Copie la **URL del servicio de inicio de sesión único de SAML, el identificador de entidad de SAML y la dirección URL de cierre de sesión** de la sección **Referencia rápida**.

    ![Configuración de KnowledgeOwl](./media/knowledgeowl-tutorial/tutorial_knowledgeowl_configure.png)

1. En otra ventana del explorador web, inicie sesión en el sitio KnowledgeOwl de la empresa como administrador.

1. Haga clic en **Configuración** y, a continuación, seleccione **Seguridad**.

     ![Configuración de KnowledgeOwl](./media/knowledgeowl-tutorial/configure1.png)

1. Desplácese hasta la opción **SAML SSO Integration** (Integración de SSO de SAML) y siga los pasos a continuación:
    
     ![Configuración de KnowledgeOwl](./media/knowledgeowl-tutorial/configure2.png)

      a. Seleccione **Enable SAML SSO** (Habilitar SSO de SAML).

     b. Copie el valor de **SP entity ID** (Identificador de entidad del proveedor de servicios) y péguelo en el cuadro de texto **Identificador (id. de entidad)**, que se encuentra en la opción **Dominio y direcciones URL de KnowledgeOwl** de Azure Portal.

     c. Copie el valor de **SP Login URL** (Dirección URL de inicio de sesión SP) y péguelo en el cuadro de texto **Sign-on URL and Reply URL** (Direcciones URL de inicio de sesión y respuesta), que se encuentra en la opción **Dominio y direcciones URL de KnowledgeOwl** de Azure Portal.

     d. En el cuadro de texto **IdP entityID**, pegue el valor de **SAML Entity ID** (Id. de entidad de SAML) que copió de Azure Portal.

     e. En el cuadro de texto **IdP Login URL** (Dirección URL de inicio de sesión de IdP), pegue el valor de **SAML Single Sign-On Service URL** (Dirección URL del servicio de inicio de sesión único de SAML) que ha copiado de Azure Portal.

     f. En el cuadro de texto **IDP Logout URL** (Dirección URL de cierre de sesión de IDP), pegue el valor de la **dirección URL de cierre de sesión** que copió de Azure Portal.

     g. Cargue el certificado que descargó de Azure Portal haciendo clic en **Upload IdP Certificate** (Cargar certificado de IdP).

     h. Haga clic en **Map SAML Attributes** (Asignar atributos de SAML) para asignar atributos y realice los pasos siguientes:
    
     ![Configuración de KnowledgeOwl](./media/knowledgeowl-tutorial/configure3.png)

    * Escriba `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/ssoid` en el cuadro de texto **Id. de SSO**.
    * Escriba `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` en el cuadro de texto **Nombre de usuario / correo electrónico**.
    * Escriba `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` en el cuadro de texto **Nombre**.
    * Escriba `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` en el cuadro de texto **Apellidos**.
    * Haga clic en **Guardar**

      i. Haga clic en **Guardar** en la parte inferior de la página.

      ![Configuración de KnowledgeOwl](./media/knowledgeowl-tutorial/configure4.png)

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

   ![Creación de un usuario de prueba de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel izquierdo de Azure Portal, haga clic en el botón **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/knowledgeowl-tutorial/create_aaduser_01.png)

1. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y, luego, haga clic en **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/knowledgeowl-tutorial/create_aaduser_02.png)

1. En la parte superior del cuadro de diálogo **Todos los usuarios**, haga clic en **Agregar** para abrir el cuadro de diálogo **Agregar**.

    ![Botón Agregar](./media/knowledgeowl-tutorial/create_aaduser_03.png)

1. En el cuadro de diálogo **Usuario** , realice los pasos siguientes:

    ![Cuadro de diálogo Usuario](./media/knowledgeowl-tutorial/create_aaduser_04.png)

     a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    d. Haga clic en **Create**(Crear).
 
### <a name="create-a-knowledgeowl-test-user"></a>Crear un usuario de prueba de KnowledgeOwl

El objetivo de esta sección es crear una usuaria de prueba llamada Britta Simon en KnowledgeOwl. KnowledgeOwl admite el aprovisionamiento Just-In-Time, que está habilitado de forma predeterminada. No hay ningún elemento de acción para usted en esta sección. Durante un intento de acceder a KnowledgeOwl se crea un nuevo usuario, en caso de que no exista.
>[!Note]
>Si necesita crear manualmente un usuario, póngase en contacto con el  [equipo de soporte técnico de KnowledgeOwl](mailto:support@knowledgeowl.com).

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a KnowledgeOwl.

![Asignación de rol de usuario][200] 

**Para asignar Britta Simon a KnowledgeOwl, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, navegue a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

1. En la lista de aplicaciones, seleccione **KnowledgeOwl**.

    ![Vínculo a KnowledgeOwl en la lista de aplicaciones](./media/knowledgeowl-tutorial/tutorial_knowledgeowl_app.png)  

1. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

1. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

1. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

1. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de KnowledgeOwl en el Panel de acceso, debería iniciar sesión automáticamente en la aplicación KnowledgeOwl.
Para más información sobre el Panel de acceso, consulte la [introducción al Panel de acceso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/knowledgeowl-tutorial/tutorial_general_01.png
[2]: ./media/knowledgeowl-tutorial/tutorial_general_02.png
[3]: ./media/knowledgeowl-tutorial/tutorial_general_03.png
[4]: ./media/knowledgeowl-tutorial/tutorial_general_04.png

[100]: ./media/knowledgeowl-tutorial/tutorial_general_100.png

[200]: ./media/knowledgeowl-tutorial/tutorial_general_200.png
[201]: ./media/knowledgeowl-tutorial/tutorial_general_201.png
[202]: ./media/knowledgeowl-tutorial/tutorial_general_202.png
[203]: ./media/knowledgeowl-tutorial/tutorial_general_203.png

