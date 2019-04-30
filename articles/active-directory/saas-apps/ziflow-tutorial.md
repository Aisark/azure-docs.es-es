---
title: 'Tutorial: Integración de Azure Active Directory con Ziflow | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Ziflow.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 84e60fa4-36fb-49c4-a642-95538c78f926
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/07/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 175e678365016bafd3d18f590a5434c32ac9fadd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "60798213"
---
# <a name="tutorial-azure-active-directory-integration-with-ziflow"></a>Tutorial: Integración de Azure Active Directory con Ziflow

En este tutorial, aprenderá a integrar Ziflow con Azure Active Directory (Azure AD).

La integración de Ziflow con Azure AD le proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Ziflow.
- Puede permitir que los usuarios inicien sesión automáticamente en Ziflow (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Ziflow, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en Ziflow

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. El escenario descrito en este tutorial consta de dos bloques de creación principales:

1. Adición de Ziflow desde la galería
2. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-ziflow-from-the-gallery"></a>Adición de Ziflow desde la galería
Para configurar la integración de Ziflow en Azure AD, debe agregar Ziflow de la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Ziflow desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

2. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

4. En el cuadro de búsqueda, escriba **Ziflow**, seleccione **Ziflow** en el panel de resultados y, luego, haga clic en el botón **Agregar** para agregar la aplicación.

    ![Ziflow en la lista de resultados](./media/ziflow-tutorial/tutorial_ziflow_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con Ziflow con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Ziflow para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Ziflow.

Para configurar y probar el inicio de sesión único de Azure AD con Ziflow, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para que los usuarios puedan usar esta característica.
2. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**, para probar el inicio de sesión único de Azure AD con Britta Simon.
3. **[Creación de un usuario de prueba en Ziflow](#create-a-ziflow-test-user)**: para tener un homólogo de Britta Simon en Ziflow que esté vinculado a su representación en Azure AD.
4. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**, para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y configurará el inicio de sesión único en la aplicación Ziflow.

**Para configurar el inicio de sesión único de Azure AD con Ziflow, realice los pasos siguientes:**

1. En Azure Portal, en la página de integración de la aplicación **Ziflow**, haga clic en **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

2. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.

    ![Cuadro de diálogo Inicio de sesión único](./media/ziflow-tutorial/tutorial_ziflow_samlbase.png)

3. En la sección **Ziflow Domain and URLs** (Dominio y direcciones URL de Ziflow), lleve a cabo los pasos siguientes:

    ![Información de dominio y direcciones URL de inicio de sesión único de Ziflow](./media/ziflow-tutorial/tutorial_ziflow_url.png)

     a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://ziflow-production.auth0.com/login/callback?connection=<UniqueID>`

    b. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón: `urn:auth0:ziflow-production:<UniqueID>`

    > [!NOTE]
    > Los valores anteriores no son reales. Los actualizará con el valor real, que se explica más adelante en el tutorial.

4. En la sección **Certificado de firma de SAML**, haga clic en **Certificado (Base64)** y, luego, guarde el archivo de certificado en el equipo.

    ![Vínculo de descarga del certificado](./media/ziflow-tutorial/tutorial_ziflow_certificate.png) 

5. Haga clic en el botón **Guardar** .

    ![Botón Configurar inicio de sesión único](./media/ziflow-tutorial/tutorial_general_400.png)

6. En la sección **Configuración de Ziflow**, haga clic en **Configurar Ziflow** para abrir la ventana **Configurar inicio de sesión**. Copie las **direcciones URL del servicio de inicio de sesión único de SAML y de cierre de sesión** de la sección **Referencia rápida**.

    ![Configuración de Ziflow](./media/ziflow-tutorial/tutorial_ziflow_configure.png) 

7. En otra ventana del explorador web, inicie sesión en Ziflow como administrador de seguridad.

8. Haga clic en Avatar en la esquina superior derecha y, a continuación, haga clic en **Manage account** (Administrar cuenta).

    ![Administrar configuración de Ziflow](./media/ziflow-tutorial/tutorial_ziflow_manage.png)

9. En la parte superior izquierda, haga clic en **Single Sign-On** (Inicio de sesión único).

    ![Configuración de Ziflow: inicio de sesión](./media/ziflow-tutorial/tutorial_ziflow_signon.png)

10. En la página **Single Sign-On** (Inicio de sesión único) realice los pasos siguientes:

    ![Configuración de Ziflow: inicio de sesión único](./media/ziflow-tutorial/tutorial_ziflow_page.png)

     a. En **Type** (Tipo), seleccione **SAML2.0**.

    b. En el cuadro de texto **URL de inicio de sesión**, pegue el valor de **SAML Single Sign-On Service URL** (Dirección URL del servicio de inicio de sesión único de SAML) que copió de Azure Portal.

    c. Cargue el certificado codificado en base 64 que ha descargado de Azure Portal en la **certificado de firma X509**.

    d. En el cuadro de texto **Sign Out URL** (Dirección URL de cierre de sesión), pegue el valor de **Sign-Out URL** (Dirección URL de cierre de sesión) que copió de Azure Portal.

    e. En la sección **Configuration Settings for your Identifier Provider** (Configuración del proveedor de identidades), copie el valor de identificación único resaltado y anéxelo al identificador y la dirección URL de inicio de sesión de la sección **Ziflow Domain and URLs section** (Dominio y direcciones URL de Ziflow) de Azure Portal.

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

   ![Creación de un usuario de prueba de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel izquierdo de Azure Portal, haga clic en el botón **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/ziflow-tutorial/create_aaduser_01.png)

2. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y, luego, haga clic en **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/ziflow-tutorial/create_aaduser_02.png)

3. En la parte superior del cuadro de diálogo **Todos los usuarios**, haga clic en **Agregar** para abrir el cuadro de diálogo **Agregar**.

    ![Botón Agregar](./media/ziflow-tutorial/create_aaduser_03.png)

4. En el cuadro de diálogo **Usuario** , realice los pasos siguientes:

    ![Cuadro de diálogo Usuario](./media/ziflow-tutorial/create_aaduser_04.png)

     a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    d. Haga clic en **Create**(Crear).
  
### <a name="create-a-ziflow-test-user"></a>Creación de un usuario de prueba de Ziflow

Para permitir que los usuarios de Azure AD inicien sesión en Ziflow, deben aprovisionarse en Ziflow. En Ziflow, el aprovisionamiento es una tarea manual.

Para aprovisionar una cuenta de usuario, realice estos pasos:

1. Inicie sesión en Ziflow como administrador de seguridad.

2. Vaya a **People** (Personas) en la parte superior.

    ![Configuración de Ziflow: personas](./media/ziflow-tutorial/tutorial_ziflow_people.png)

3. Haga clic en **Add** (Agregar) y, luego, en **Add user** (Agregar usuario).

    ![Configuración de Ziflow: agregar usuario](./media/ziflow-tutorial/tutorial_ziflow_add.png)

4. En la ventana emergente **Add a user** (Agregar un usuario), realice los siguientes pasos:

    ![Configuración de Ziflow: agregar usuario](./media/ziflow-tutorial/tutorial_ziflow_adduser.png)

     a. En el cuadro de texto **E-mail** (Correo electrónico), escriba el correo electrónico del usuario, por ejemplo, brittasimon@contoso.com.

    b. En el cuadro de texto **First Name** (Nombre), escriba el nombre del usuario, en este caso Britta.

    c. En el cuadro de texto **Last Name** (Apellidos), escriba los apellidos del usuario, en este caso Simon.

    d. Seleccione su rol de Ziflow.

    e. Haga clic en **Add 1 user** (Agregar 1 usuario).

    > [!NOTE]
    > El titular de la cuenta de Azure Active Directory recibirá un mensaje de correo y seguirá un vínculo para confirmar su cuenta antes de que se active.

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, permitirá que Britta Simon use el inicio de sesión único de Azure concediéndole acceso a Ziflow.

![Asignación de rol de usuario][200] 

**Para asignar a Britta Simon a Ziflow, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, navegue a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

2. En la lista de aplicaciones, seleccione **Ziflow**.

    ![Vínculo a Ziflow en la lista de aplicaciones](./media/ziflow-tutorial/tutorial_ziflow_app.png)  

3. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

4. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

6. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

7. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de Ziflow en el Panel de acceso, debería iniciar sesión automáticamente en dicha aplicación.
Para más información sobre el Panel de acceso, consulte la [introducción al Panel de acceso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/ziflow-tutorial/tutorial_general_01.png
[2]: ./media/ziflow-tutorial/tutorial_general_02.png
[3]: ./media/ziflow-tutorial/tutorial_general_03.png
[4]: ./media/ziflow-tutorial/tutorial_general_04.png

[100]: ./media/ziflow-tutorial/tutorial_general_100.png

[200]: ./media/ziflow-tutorial/tutorial_general_200.png
[201]: ./media/ziflow-tutorial/tutorial_general_201.png
[202]: ./media/ziflow-tutorial/tutorial_general_202.png
[203]: ./media/ziflow-tutorial/tutorial_general_203.png

