---
title: 'Tutorial: Integración de Azure Active Directory con IQNavigator VMS | Microsoft Docs'
description: Obtenga información sobre cómo configurar el inicio de sesión único entre Azure Active Directory y IQNavigator VMS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: a8a09b25-dfa5-4c31-aea2-53bf1853b365
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f5a0700a63d21d089573f757716e08fb03665b28
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/19/2019
ms.locfileid: "58165001"
---
# <a name="tutorial-azure-active-directory-integration-with-iqnavigator-vms"></a>Tutorial: Integración de Azure Active Directory con IQNavigator VMS

En este tutorial, obtendrá información sobre cómo integrar IQNavigator VMS con Azure Active Directory (Azure AD).

La integración de IQNavigator VMS con Azure AD proporciona las siguientes ventajas:

- En Azure AD puede controlar quién tiene acceso a IQNavigator VMS.
- Puede permitir que los usuarios inicien sesión automáticamente en IQNavigator VMS (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: el nuevo Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con IQNavigator VMS, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Suscripción habilitada para un inicio de sesión único en IQNavigator VMS

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, aquí puede obtener una versión de prueba de un mes [oferta de prueba](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. El escenario descrito en este tutorial consta de dos bloques de creación principales:

1. Incorporación de IQNavigator VMS desde la galería
1. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-iqnavigator-vms-from-the-gallery"></a>Incorporación de IQNavigator VMS desde la galería
Para configurar la integración de IQNavigator VMS en Azure AD, deberá agregar IQNavigator VMS desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar IQNavigator VMS desde la galería, siga estos pasos:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Active Directory][1]

1. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![APLICACIONES][2]
    
1. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![APLICACIONES][3]

1. En el cuadro de búsqueda, escriba **IQNavigator VMS**.

    ![Creación de un usuario de prueba de Azure AD](./media/iqnavigatorvms-tutorial/tutorial_iqnavigatorvms_search.png)

1. En el panel de resultados, seleccione **IQNavigator VMS** y haga clic en el botón **Agregar** para agregar la aplicación.

    ![Creación de un usuario de prueba de Azure AD](./media/iqnavigatorvms-tutorial/tutorial_iqnavigatorvms_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con IQNavigator VMS con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD necesita saber cuál es el usuario homólogo de IQNavigator VMS para un usuario de Azure AD. Es decir, es preciso establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de IQNavigator VMS.

Para establecer la relación de vínculo, en IQNavigator VMS, asigne el valor de **nombre de usuario** de Azure AD como valor de **Nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con IQNavigator VMS, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-sign-on)** : para permitir a los usuarios usar esta característica.
1. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
1. **[Creación de un usuario de prueba de IQNavigator VMS](#creating-a-iqnavigator-vms-test-user)**  : para tener un homólogo de Britta Simon en IQNavigator VMS que esté vinculado a la representación del usuario en Azure AD.
1. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
1. **[Testing Single Sign-On](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y lo configurará en la aplicación IQNavigator VMS.

**Para configurar el inicio de sesión único de Azure AD con IQNavigator VMS, realice los pasos siguientes:**

1. En la página de integración de la aplicación **IQNavigator VMS** de Azure Portal, haga clic en **Inicio de sesión único**.

    ![Configurar inicio de sesión único][4]

1. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.

    ![Configurar inicio de sesión único](./media/iqnavigatorvms-tutorial/tutorial_iqnavigatorvms_samlbase.png)

1. En la sección **Dominio y direcciones URL de IQNavigator VMS**, lleve a cabo los pasos siguientes:

    ![Configurar inicio de sesión único](./media/iqnavigatorvms-tutorial/tutorial_iqnavigatorvms_url.png)

     a. En el cuadro de texto **Identificador**, escriba la dirección URL: `iqn.com`

    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://<subdomain>.iqnavigator.com/security/login?client_name=https://sts.window.net/<instance name>`.

1. Active la casilla **Mostrar configuración avanzada de URL** y realice el siguiente paso:

    ![Configurar inicio de sesión único](./media/iqnavigatorvms-tutorial/tutorial_iqnavigatorvms_url1.png)

    En el cuadro de texto **Estado de la retransmisión**, escriba una dirección URL que siga este patrón:`https://<subdomain>.iqnavigator.com`

    > [!NOTE]
    > Estos valores no son reales. Actualice estos valores con la dirección URL de respuesta y el estado de la retransmisión. Para obtener estos valores, póngase en contacto con el [equipo de soporte técnico de cliente de IQNavigator VMS](https://www.beeline.com/iqn-product-support/).

1. En la sección **Certificado de firma de SAML** , haga clic en el botón Copiar para copiar la  **dirección URL de metadatos de federación de la aplicación**  y péguela en el Bloc de notas.
    
    ![Configurar inicio de sesión único](./media/iqnavigatorvms-tutorial/tutorial_metadataurl.png)

1. La aplicación IQNavigator espera el valor de identificador de usuario único con la notificación del identificador de nombre. El cliente puede asignar el valor correcto de la notificación del identificador de nombre. En este caso, asignamos user.UserPrincipalName para la demostración. Pero debe asignar el valor correcto según la configuración de su organización.

    ![Configurar inicio de sesión único](./media/iqnavigatorvms-tutorial/tutorial_iqnavigatorvms_attribute.png)

1. Haga clic en el botón **Guardar** .

    ![Configurar inicio de sesión único](./media/iqnavigatorvms-tutorial/tutorial_general_400.png)

1. En la sección **Configuración de IQNavigator VMS**, haga clic en **Configurar IQNavigator VMS** para abrir la ventana **Configurar inicio de sesión**. Copie la **URL del servicio de inicio de sesión único de SAML, el identificador de entidad de SAML y la dirección URL de cierre de sesión** de la sección **Referencia rápida**.

    ![Configurar inicio de sesión único](./media/iqnavigatorvms-tutorial/tutorial_iqnavigatorvms_configure.png)

1. Para configurar el inicio de sesión único en **IQNavigator VMS**, es preciso enviar la **dirección URL de metadatos de federación de la aplicación**, la **dirección URL de cierre de sesión, el identificador de entidad de SAML y la dirección URL del servicio de inicio de sesión único de SAML** al [equipo de soporte técnico de IQNavigator VMS](https://www.beeline.com/iqn-product-support/). Dicho equipo lo configura para establecer la conexión de SSO de SAML correctamente en ambos lados.

### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

![Creación de un usuario de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo de **Azure Portal**, haga clic en el icono de **Azure Active Directory**.

    ![Creación de un usuario de prueba de Azure AD](./media/iqnavigatorvms-tutorial/create_aaduser_01.png) 

1. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y haga clic en **Todos los usuarios**.
    
    ![Creación de un usuario de prueba de Azure AD](./media/iqnavigatorvms-tutorial/create_aaduser_02.png)

1. Para abrir el cuadro de diálogo **Usuario**, haga clic en **Agregar** en la parte superior del cuadro de diálogo.

    ![Creación de un usuario de prueba de Azure AD](./media/iqnavigatorvms-tutorial/create_aaduser_03.png)

1. En la página de diálogo **Usuario**, realice los siguientes pasos:
 
    ![Creación de un usuario de prueba de Azure AD](./media/iqnavigatorvms-tutorial/create_aaduser_04.png) 

     a. En el cuadro de texto **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la **dirección de correo electrónico** de Britta Simon.

    c. Seleccione **Mostrar contraseña** y anote el valor del cuadro **Contraseña**.

    d. Haga clic en **Create**(Crear).

### <a name="creating-an-iqnavigator-vms-test-user"></a>Creación de un usuario de prueba de IQNavigator VMS

El objetivo de esta sección es crear un usuario llamado Britta Simon en IQNavigator VMS. Colabore con el [equipo de soporte técnico de IQNavigator VMS](https://www.beeline.com/iqn-product-support/) para agregar los usuarios a la cuenta de IQNavigator VMS.

### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a IQNavigator VMS.

![Asignar usuario][200]

**Para asignar Britta Simon a IQNavigator VMS, siga estos pasos:**

1. En Azure Portal, abra la vista de aplicaciones, navegue a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201]

1. En la lista de aplicaciones, seleccione **IQNavigator VMS**.

    ![Configurar inicio de sesión único](./media/iqnavigatorvms-tutorial/tutorial_iqnavigatorvms_app.png)

1. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Asignar usuario][202]

1. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Asignar usuario][203]

1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

1. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

1. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de IQNavigator VMS en el Panel de acceso, debería iniciar sesión automáticamente en su aplicación de IQNavigator VMS.
Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/iqnavigatorvms-tutorial/tutorial_general_01.png
[2]: ./media/iqnavigatorvms-tutorial/tutorial_general_02.png
[3]: ./media/iqnavigatorvms-tutorial/tutorial_general_03.png
[4]: ./media/iqnavigatorvms-tutorial/tutorial_general_04.png

[100]: ./media/iqnavigatorvms-tutorial/tutorial_general_100.png

[200]: ./media/iqnavigatorvms-tutorial/tutorial_general_200.png
[201]: ./media/iqnavigatorvms-tutorial/tutorial_general_201.png
[202]: ./media/iqnavigatorvms-tutorial/tutorial_general_202.png
[203]: ./media/iqnavigatorvms-tutorial/tutorial_general_203.png

