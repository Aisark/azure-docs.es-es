---
title: 'Tutorial: Integración de Azure Active Directory con Tangoe Command Premium Mobile | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Tangoe Command Premium Mobile.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 2b0b544c-9c2c-49cd-862b-ec2ee9330126
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8c9f410fa890df7aac3c3bf4d89468b92e69ba38
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/26/2019
ms.locfileid: "56883239"
---
# <a name="tutorial-azure-active-directory-integration-with-tangoe-command-premium-mobile"></a>Tutorial: Integración de Azure Active Directory con Tangoe Command Premium Mobile

En este tutorial, obtendrá información sobre cómo integrar Tangoe Command Premium Mobile con Azure Active Directory (Azure AD).

La integración de Tangoe Command Premium Mobile con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Tangoe Command Premium Mobile.
- Puede permitir que los usuarios inicien sesión automáticamente en Tangoe Command Premium Mobile (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: el nuevo Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Tangoe Command Premium Mobile, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en Tangoe Command Premium Mobile

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. El escenario descrito en este tutorial consta de dos bloques de creación principales:

1. Incorporación de Tangoe Command Premium Mobile desde la galería
1. Configuración y prueba del inicio de sesión único en Azure AD

## <a name="add-tangoe-command-premium-mobile-from-the-gallery"></a>Incorporación de Tangoe Command Premium Mobile desde la galería
Para configurar la integración de Tangoe Command Premium Mobile en Azure AD, deberá agregar Tangoe Command Premium Mobile desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Tangoe Command Premium Mobile desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Active Directory][1]

1. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![APLICACIONES][2]
    
1. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![APLICACIONES][3]

1. En el cuadro de búsqueda, escriba **Tangoe Command Premium Mobile**, seleccione **Tangoe Command Premium Mobile** desde el panel de resultados y, luego, haga clic en el botón **Agregar** para agregar la aplicación.

    ![Incorporación de Tangoe Command Premium Mobile desde la galería](./media/tangoe-tutorial/tutorial_tangoe_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD
En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con Tangoe Command Premium Mobile con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Tangoe Command Premium Mobile para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Tangoe Command Premium Mobile.

Para establecer la relación de vínculo, en Tangoe Command Premium Mobile, asigne el valor de **nombre de usuario** de Azure AD como valor de **Nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con Tangoe Command Premium Mobile, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para que los usuarios puedan usar esta característica.
1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**, para probar el inicio de sesión único de Azure AD con Britta Simon.
1. **[Creación de un usuario de prueba de Tangoe Command Premium Mobile](#create-a-tangoe-command-premium-mobile-test-user)** : para tener un homólogo de Britta Simon en Tangoe Command Premium Mobile que esté vinculado a la representación de ella en Azure AD.
1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**, para permitir que Britta Simon use el inicio de sesión único de Azure AD.
1. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si funciona la configuración.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y configurará el inicio de sesión único en la aplicación Tangoe Command Premium Mobile.

**Para configurar el inicio de sesión único de Azure AD con Tangoe Command Premium Mobile, realice los pasos siguientes:**

1. En Azure Portal, en la página de integración de la aplicación **Tangoe Command Premium Mobile**, haga clic en **Inicio de sesión único**.

    ![Configurar inicio de sesión único][4]

1. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Inicio de sesión basado en SAML](./media/tangoe-tutorial/tutorial_tangoe_samlbase.png)

1. En la sección **Dominio y direcciones URL de Tangoe Command Premium Mobile**, lleve a cabo los pasos siguientes:

    ![Dominio y direcciones URL de Tangoe Command Premium Mobile](./media/tangoe-tutorial/tutorial_tangoe_url.png)

     a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://sso.tangoe.com/sp/startSSO.ping?PartnerIdpId=<tenant issuer>&TARGET=<target page url>`.

    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://sso.tangoe.com/sp/ACS.saml2`.

    > [!NOTE] 
    > Estos valores no son reales. Actualice estos valores con los valores reales de URL de respuesta y URL de inicio de sesión. Póngase en contacto con el [equipo de soporte técnico de Tangoe Command Premium Mobile Client](https://www.tangoe.com/contact-us/) para obtener estos valores. 

1. En la sección **Certificado de firma de SAML**, haga clic en **XML de metadatos** y luego guarde el archivo de metadatos en el equipo.

    ![Sección Certificado de firma SAML](./media/tangoe-tutorial/tutorial_tangoe_certificate.png) 

1. Haga clic en el botón **Guardar** .

    ![Botón Guardar](./media/tangoe-tutorial/tutorial_general_400.png)
    
1. En la sección **Tangoe Command Premium Mobile Configuration**, haga clic en **Configurar Tangoe Command Premium Mobile** para abrir la ventana **Configurar inicio de sesión**. Copie la **URL del servicio de inicio de sesión único de SAML, el identificador de entidad de SAML y la dirección URL de cierre de sesión** de la sección **Referencia rápida**.

    ![Sección de configuración de Tangoe Command Premium Mobile](./media/tangoe-tutorial/tutorial_tangoe_configure.png) 

1. Para configurar el inicio de sesión único para su aplicación, póngase en contacto con el [equipo de soporte técnico de Tangoe Command Premium Mobile Client](https://www.tangoe.com/contact-us/) y proporcione lo siguiente:

   - El archivo de metadatos descargado
   - El **identificador de entidad de SAML**
   - La **dirección URL del servicio de inicio de sesión único de SAML**
   - La **dirección URL de cierre de sesión**

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más aquí sobre la característica de documentación insertada: [Documentación insertada de Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

![Creación de un usuario de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo de **Azure Portal**, haga clic en el icono de **Azure Active Directory**.

    ![Creación de un usuario de prueba de Azure AD](./media/tangoe-tutorial/create_aaduser_01.png) 

1. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y haga clic en **Todos los usuarios**.
    
    ![Usuarios y grupos -> Todos los usuarios](./media/tangoe-tutorial/create_aaduser_02.png) 

1. Para abrir el cuadro de diálogo **Usuario**, haga clic en **Agregar** en la parte superior del cuadro de diálogo.
 
    ![Agregar usuario](./media/tangoe-tutorial/create_aaduser_03.png) 

1. En la página de diálogo **Usuario**, realice los siguientes pasos:
 
    ![Página del cuadro de diálogo Usuario](./media/tangoe-tutorial/create_aaduser_04.png) 

     a. En el cuadro de texto **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la **dirección de correo electrónico** de Britta Simon.

    c. Seleccione **Mostrar contraseña** y anote el valor del cuadro **Contraseña**.

    d. Haga clic en **Create**(Crear).
 
### <a name="create-a-tangoe-command-premium-mobile-test-user"></a>Crear un usuario de prueba de Tangoe Command Premium Mobile

En esta sección, creará un usuario llamado Britta Simon en Tangoe Command Premium Mobile. 

La aplicación Tangoe Command Premium Mobile necesita que todos los usuarios estén aprovisionados en la aplicación antes de realizar el inicio de sesión único. Así pues, colabore con el [equipo de soporte técnico de Tangoe Command Premium Mobile Client](https://www.tangoe.com/contact-us/) para aprovisionar todos estos usuarios en la aplicación. 

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, permitirá que Britta Simon use el inicio de sesión único de Azure concediéndole acceso a Tangoe Command Premium Mobile.

![Asignar usuario][200] 

**Para asignar Britta Simon a Tangoe Command Premium Mobile, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, navegue a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

1. En la lista de aplicaciones, seleccione **Tangoe comando Premium Mobile**.

    ![Tangoe Command Premium Mobile en la lista de aplicaciones](./media/tangoe-tutorial/tutorial_tangoe_app.png) 

1. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Asignar usuario][202] 

1. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Asignar usuario][203]

1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

1. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

1. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de SSO de Azure AD mediante el panel de acceso.

Al hacer clic en el icono de Tangoe Command Premium Mobile en el panel de acceso, debería iniciar sesión automáticamente en su aplicación Tangoe Command Premium Mobile. Para más información sobre el Panel de acceso, consulte la [introducción al Panel de acceso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/tangoe-tutorial/tutorial_general_01.png
[2]: ./media/tangoe-tutorial/tutorial_general_02.png
[3]: ./media/tangoe-tutorial/tutorial_general_03.png
[4]: ./media/tangoe-tutorial/tutorial_general_04.png

[100]: ./media/tangoe-tutorial/tutorial_general_100.png

[200]: ./media/tangoe-tutorial/tutorial_general_200.png
[201]: ./media/tangoe-tutorial/tutorial_general_201.png
[202]: ./media/tangoe-tutorial/tutorial_general_202.png
[203]: ./media/tangoe-tutorial/tutorial_general_203.png

