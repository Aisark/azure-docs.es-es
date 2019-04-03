---
title: 'Tutorial: Integración de Azure Active Directory con Insignia SAML SSO | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory e Insignia SAML SSO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 828c981c-c3dd-4eb2-8699-0f732baa43f6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/28/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: dc492142200c7fd028360d01c2602904531550dc
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/03/2019
ms.locfileid: "58884489"
---
# <a name="tutorial-azure-active-directory-integration-with-insignia-saml-sso"></a>Tutorial: Integración de Azure Active Directory con Insignia SAML SSO

En este tutorial aprenderá a integrar Insignia SAML SSO con Azure Active Directory (Azure AD).

La integración de Insignia SAML SSO con Azure AD le proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Insignia SAML SSO.
- Puede permitir que los usuarios inicien sesión automáticamente en Insignia SAML (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Insignia SAML SSO, necesita lo siguiente:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en Insignia SAML SSO

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. El escenario descrito en este tutorial consta de dos bloques de creación principales:

1. Adición de Insignia SAML SSO desde la galería
1. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-insignia-saml-sso-from-the-gallery"></a>Adición de Insignia SAML SSO desde la galería
Para configurar la integración de Insignia SAML SSO en Azure AD, tiene que agregar Insignia SAML SSO desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Insignia SAML SSO desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

1. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
1. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

1. En el cuadro de búsqueda, escriba **Insignia SAML SSO**, seleccione **Insignia SAML SSO** en el panel de resultados y, luego, haga clic en el botón **Agregar** para agregar la aplicación.

    ![Insignia SAML SSO en la lista de resultados](./media/insigniasamlsso-tutorial/tutorial_insigniasamlsso_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, configurará y probará el inicio de sesión único de Azure AD con Insignia SAML SSO con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Insignia SAML SSO para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Insignia SAML SSO.

Para establecer la relación de vínculo, en Insignia SAML SSO, asigne el valor de **nombre de usuario** de Azure AD como valor de **nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con Insignia SAML SSO, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para que los usuarios puedan usar esta característica.
1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**, para probar el inicio de sesión único de Azure AD con Britta Simon.
1. **[Creación de un usuario de prueba de Insignia SAML SSO](#create-an-insignia-saml-sso-test-user)**: para tener un homólogo de Britta Simon en Insignia SAML SSO que esté vinculado a la representación del usuario en Azure AD.
1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**, para permitir que Britta Simon use el inicio de sesión único de Azure AD.
1. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección habilitará el inicio de sesión único de Azure AD en Azure Portal y configurará el inicio de sesión único en la aplicación Insignia SAML SSO.

**Para configurar el inicio de sesión único en Azure AD con Insignia SAML SSO, realice los pasos siguientes:**

1. En la página de integración de la aplicación **Insignia SAML SSO** de Azure Portal, haga clic en **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

1. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Cuadro de diálogo Inicio de sesión único](./media/insigniasamlsso-tutorial/tutorial_insigniasamlsso_samlbase.png)

1. En la sección **Dominio y direcciones URL de Insignia SAML SSO**, lleve a cabo los pasos siguientes:

    ![Información de dominio y direcciones URL de inicio de sesión único de Insignia SAML SSO](./media/insigniasamlsso-tutorial/tutorial_insigniasamlsso_url.png)

     a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón:
    
    | |
    |--|
    | `https://<customername>.insigniails.com/ils` |
    | `https://<customername>.insigniails.com/` |
    | `https://<customername>.insigniailsusa.com/` |

    b. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón: `https://<customername>.insigniailsusa.com/<uniqueid>`

    > [!NOTE] 
    > Estos valores no son reales. Debe actualizarlos con la dirección URL y el identificador reales de inicio de sesión. Póngase en contacto con el [equipo de soporte de cliente de Insignia SAML SSO](https://www.insigniasoftware.com/insignia/Techsupport.aspx) para obtener estos valores. 
 

1. En la sección **Certificado de firma de SAML**, haga clic en **Certificado (Base64)** y, luego, guarde el archivo de certificado en el equipo.

    ![Vínculo de descarga del certificado](./media/insigniasamlsso-tutorial/tutorial_insigniasamlsso_certificate.png) 

1. Haga clic en el botón **Guardar** .

    ![Botón Configurar inicio de sesión único](./media/insigniasamlsso-tutorial/tutorial_general_400.png)

1. En la sección **Configuración de Insignia SAML SSO**, haga clic en **Configurar Insignia SAML SSO** para abrir la ventana **Configurar inicio de sesión único**. Copie los valores **Sign-Out URL y SAML Single Sign-On Service URL** (Dirección URL de cierre de sesión y Dirección URL del servicio de inicio de sesión único de SAML) de la **sección de referencia rápida**.

    ![Configuración de Insignia SAML SSO](./media/insigniasamlsso-tutorial/tutorial_insigniasamlsso_configure.png) 

1. Para configurar el inicio de sesión único en **Insignia SAML SSO**, es preciso enviar los valores descargados de **Certificado (Base64)**, **Sign-Out URL (Dirección URL de cierre de sesión) y SAML Single Sign-On Service URL (Dirección URL del servicio de inicio de sesión único de SAML)** al [equipo de soporte técnico de Insignia SAML SSO](https://www.insigniasoftware.com/insignia/Techsupport.aspx). Dicho equipo lo configura para establecer la conexión de SSO de SAML correctamente en ambos lados.

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más aquí sobre la característica de documentación insertada: [Documentación insertada de Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

   ![Creación de un usuario de prueba de Azure AD][100]

**Para crear un usuario de prueba en Azure AD, realice los pasos siguientes:**

1. En el panel izquierdo de Azure Portal, haga clic en el botón **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/insigniasamlsso-tutorial/create_aaduser_01.png)

1. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y, luego, haga clic en **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/insigniasamlsso-tutorial/create_aaduser_02.png)

1. En la parte superior del cuadro de diálogo **Todos los usuarios**, haga clic en **Agregar** para abrir el cuadro de diálogo **Agregar**.

    ![Botón Agregar](./media/insigniasamlsso-tutorial/create_aaduser_03.png)

1. En el cuadro de diálogo **Usuario** , realice los pasos siguientes:

    ![Cuadro de diálogo Usuario](./media/insigniasamlsso-tutorial/create_aaduser_04.png)

     a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    d. Haga clic en **Create**(Crear).
 
### <a name="create-an-insignia-saml-sso-test-user"></a>Creación de un usuario de prueba de Insignia SAML SSO

En esta sección, creará un usuario llamado Britta Simon en Insignia Library System. Trabaje con el [equipo de soporte técnico de Insignia Library System](https://www.insigniasoftware.com/insignia/Techsupport.aspx) para agregar los usuarios a la plataforma de Insignia Library System.

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, concederá acceso a Britta Simon a Insignia SAML SSO para que use el inicio de sesión único de Azure.

![Asignación de rol de usuario][200] 

**Para asignar a Britta Simon a Insignia SAML SSO, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, navegue a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

1. En la lista de aplicaciones, seleccione **Insignia SAML SSO**.

    ![Vínculo a Insignia SAML SSO en la lista de aplicaciones](./media/insigniasamlsso-tutorial/tutorial_insigniasamlsso_app.png)  

1. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

1. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

1. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

1. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de Insignia SAML SSO del panel de acceso, debería iniciar sesión automáticamente en su aplicación Insignia SAML SSO.
Para más información sobre el Panel de acceso, consulte la [introducción al Panel de acceso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/insigniasamlsso-tutorial/tutorial_general_01.png
[2]: ./media/insigniasamlsso-tutorial/tutorial_general_02.png
[3]: ./media/insigniasamlsso-tutorial/tutorial_general_03.png
[4]: ./media/insigniasamlsso-tutorial/tutorial_general_04.png

[100]: ./media/insigniasamlsso-tutorial/tutorial_general_100.png

[200]: ./media/insigniasamlsso-tutorial/tutorial_general_200.png
[201]: ./media/insigniasamlsso-tutorial/tutorial_general_201.png
[202]: ./media/insigniasamlsso-tutorial/tutorial_general_202.png
[203]: ./media/insigniasamlsso-tutorial/tutorial_general_203.png

