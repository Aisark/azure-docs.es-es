---
title: 'Tutorial: Integración de Azure Active Directory con ServiceChannel | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y ServiceChannel.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: c3546eab-96b5-489b-a309-b895eb428053
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/3/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b4be5087af70e10e5a73ea2a183a25b326aea664
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/06/2019
ms.locfileid: "57433474"
---
# <a name="tutorial-azure-active-directory-integration-with-servicechannel"></a>Tutorial: Integración de Azure Active Directory con ServiceChannel

En este tutorial, obtendrá información sobre cómo integrar ServiceChannel con Azure Active Directory (Azure AD).

La integración de ServiceChannel con Azure AD proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a ServiceChannel.
- Puede habilitar que los usuarios inicien sesión automáticamente en ServiceChannel (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: el Portal de administración de Azure

Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con ServiceChannel, se necesitan los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para inicio de sesión único en ServiceChannel

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No debe usar el entorno de producción, a menos que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. El escenario descrito en este tutorial consta de dos bloques de creación principales:

1. Agregar ServiceChannel desde la galería
1. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-servicechannel-from-the-gallery"></a>Agregar ServiceChannel desde la galería
Para configurar la integración de ServiceChannel en Azure AD, deberá agregar ServiceChannel desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar ServiceChannel desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo del **[Portal de administración de Azure](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Active Directory][1]

1. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![APLICACIONES][2]
    
1. Haga clic en el botón **Agregar** situado en la parte superior del cuadro de diálogo.

    ![APLICACIONES][3]

1. En el cuadro de búsqueda, escriba **ServiceChannel**.

    ![Creación de un usuario de prueba de Azure AD](./media/servicechannel-tutorial/tutorial-servicechannel_000.png)

1. En el panel de resultados, seleccione **ServiceChannel** y haga clic en el botón **Agregar** para agregar la aplicación.

    ![Creación de un usuario de prueba de Azure AD](./media/servicechannel-tutorial/tutorial-servicechannel_2.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección, configurará y probará el inicio de sesión único de Azure AD con ServiceChannel con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de ServiceChannel para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de ServiceChannel.

Esta relación de vínculo se establece mediante la asignación del valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en ServiceChannel.

Para configurar y probar el inicio de sesión único de Azure AD con ServiceChannel, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-sign-on)** : para permitir a los usuarios usar esta característica.
1. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
1. **[Creación de un usuario de prueba de ServiceChannel](#creating-a-servicechannel-test-user)**: para probar el inicio de sesión único de Azure AD con Britta Simon.
1. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
1. **[Testing Single Sign-On](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en el Portal de administración de Azure y configurará el inicio de sesión único en la aplicación ServiceChannel.

**Para configurar el inicio de sesión único de Azure AD con ServiceChannel, siga estos pasos:**

1. En el Portal de administración de Azure, en la página de integración de la aplicación **ServiceChannel**, haga clic en **Inicio de sesión único**.

    ![Configurar inicio de sesión único][4]

1. En el cuadro de diálogo **Inicio de sesión único**, en **Modo**, seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Configurar inicio de sesión único](./media/servicechannel-tutorial/tutorial-servicechannel_01.png)

1. En la sección **Dominio y direcciones URL de ServiceChannel**, lleve a cabo los pasos siguientes:

    ![Configurar inicio de sesión único](./media/servicechannel-tutorial/tutorial-servicechannel_urls.png)

     a. En el cuadro de texto **Identificador**, escriba el valor como `http://adfs.<domain>.com/adfs/service/trust`.

    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://<customer domain>.servicechannel.com/saml/acs`.

    > [!NOTE] 
    > Tenga en cuenta que estos no son valores reales. Estos valores se tienen que actualizar con los valores reales de Identificador y URL de respuesta. Aquí le recomendamos que utilice el valor de cadena único en el identificador. Póngase en contacto con el [equipo de soporte técnico de ServiceChannel](https://servicechannel.zendesk.com/hc/en-us) para obtener estos valores.

1. La aplicación ServiceChannel espera las aserciones de SAML en un formato específico, que requiere que se agreguen asignaciones de atributos personalizados a la configuración de los atributos del token de SAML. La siguiente captura de pantalla le muestra un ejemplo de esto. **NameIdentifier (identificador de usuario)** es la única notificación obligatoria y el valor predeterminado es **user.userprincipalname**, pero ServiceChannel espera que se le asigne **user.mail**. Si va a habilitar el aprovisionamiento de usuarios Just-In-Time, debe agregar las siguientes notificaciones tal y como se muestra a continuación. La notificación **role** debe asignarse a **user.assignedroles**, que contiene el rol del usuario.  

    Puede consultar la guía de ServiceChannel [aquí](https://servicechannel.zendesk.com/hc/en-us/articles/217514326-Azure-AD-Configuration-Example) para obtener más información sobre las notificaciones.
    
    ![Configurar inicio de sesión único](./media/servicechannel-tutorial/tutorial_servicechannel_attribute.png)

    > [!NOTE] 
    > Consulte [Administración del acceso mediante RBAC y Azure Portal](../../role-based-access-control/role-assignments-portal.md) para obtener información sobre cómo configurar el **rol** en Azure AD.

1. En la sección **Atributos de usuario**, haga clic en **Ver y editar todos los demás atributos de usuario** y establezca los atributos.

    | Nombre del atributo | Valor de atributo |
    | --- | --- |    
    | Rol| user.assignedroles |

     a. Haga clic en **Agregar atributo** para abrir el cuadro de diálogo **Agregar atributo**.

    ![Configurar inicio de sesión único](./media/servicechannel-tutorial/tutorial_servicechannel_04.png)

    ![Configurar inicio de sesión único](./media/servicechannel-tutorial/tutorial_servicechannel_05.png)
    
    b. En el cuadro de texto **Nombre**, escriba el nombre que se muestra para la fila.
    
    c. En la lista **Valor**, seleccione el atributo que se muestra para esa fila.
    
    d. Haga clic en **Aceptar**.
    
1. En la sección **Certificado de firma de SAML**, haga clic en **Certificado (Base64)** y, luego, guarde el archivo de certificado en el equipo.

    ![Configurar inicio de sesión único](./media/servicechannel-tutorial/tutorial-servicechannel_05.png) 

1. Haga clic en **Save**(Guardar).

    ![Configurar inicio de sesión único](./media/servicechannel-tutorial/tutorial_general_400.png)

1. En la sección **Configuración de ServiceChannel**, haga clic en **Configurar ServiceChannel** para abrir la ventana **Configurar inicio de sesión**. Tenga en cuenta la **Id. de entidad de SAML** desde el **referencia rápida** sección.

1. Para configurar el inicio de sesión único en el lado de **ServiceChannel**, debe enviar el **certificado (Base64)** descargado y el **Id. de entidad de SAML** al [equipo de soporte técnico de ServiceChannel](https://servicechannel.zendesk.com/hc/en-us). Ellos realizarán esta operación para establecer la conexión de SSO de SAML correctamente en ambos lados.

### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en el Portal de administración de Azure llamado Britta Simon.

![Creación de un usuario de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo del **Portal de administración de Azure**, haga clic en el icono de **Azure Active Directory**.

    ![Creación de un usuario de prueba de Azure AD](./media/servicechannel-tutorial/create_aaduser_01.png) 

1. Vaya a **Usuarios y grupos** y haga clic en **Todos los usuarios** para mostrar la lista de usuarios.
    
    ![Creación de un usuario de prueba de Azure AD](./media/servicechannel-tutorial/create_aaduser_02.png) 

1. En la parte superior del diálogo, haga clic en **Agregar** para abrir el diálogo **Usuario**.
 
    ![Creación de un usuario de prueba de Azure AD](./media/servicechannel-tutorial/create_aaduser_03.png) 

1. En la página de diálogo **Usuario**, realice los siguientes pasos:
 
    ![Creación de un usuario de prueba de Azure AD](./media/servicechannel-tutorial/create_aaduser_04.png) 

     a. En el cuadro de texto **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la **dirección de correo electrónico** de Britta Simon.

    c. Seleccione **Mostrar contraseña** y anote el valor del cuadro **Contraseña**.

    d. Haga clic en **Create**(Crear). 

### <a name="creating-a-servicechannel-test-user"></a>Crear un usuario de prueba de ServiceChannel

La aplicación admite el aprovisionamiento de usuarios Just-In-Time y, tras la autenticación, los usuarios se crearán automáticamente en la aplicación. Para el aprovisionamiento de usuario completo, póngase en contacto con el [equipo de soporte técnico de ServiceChannel](https://servicechannel.zendesk.com/hc/en-us).

### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, permitirá que Britta Simon use el inicio de sesión único de Azure concediéndole acceso a ServiceChannel.

![Asignar usuario][200] 

**Para asignar Britta Simon a ServiceChannel, realice los pasos siguientes:**

1. En el Portal de administración de Azure, abra la vista de aplicaciones, vaya a la vista de directorio y vaya a **Aplicaciones empresariales**. A continuación, haga clic en **All applications** (Todas las aplicaciones).

    ![Asignar usuario][201] 

1. En la lista de aplicaciones, seleccione **ServiceChannel**.

    ![Configurar inicio de sesión único](./media/servicechannel-tutorial/tutorial-servicechannel_app01.png) 

1. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Asignar usuario][202] 

1. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Asignar usuario][203]

1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

1. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

1. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de ServiceChannel en el panel de acceso, debería iniciar sesión automáticamente en la aplicación ServiceChannel.

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)


<!--Image references-->

[1]: ./media/servicechannel-tutorial/tutorial_general_01.png
[2]: ./media/servicechannel-tutorial/tutorial_general_02.png
[3]: ./media/servicechannel-tutorial/tutorial_general_03.png
[4]: ./media/servicechannel-tutorial/tutorial_general_04.png

[100]: ./media/servicechannel-tutorial/tutorial_general_100.png

[200]: ./media/servicechannel-tutorial/tutorial_general_200.png
[201]: ./media/servicechannel-tutorial/tutorial_general_201.png
[202]: ./media/servicechannel-tutorial/tutorial_general_202.png
[203]: ./media/servicechannel-tutorial/tutorial_general_203.png
