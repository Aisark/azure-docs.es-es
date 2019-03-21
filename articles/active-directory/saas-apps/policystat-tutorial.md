---
title: 'Tutorial: Integración de Azure Active Directory con PolicyStat | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y PolicyStat.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: af5eb0f1-1c8e-4809-b0c4-8ccfb915ca42
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 602dd0fd4050a3b4336c560bbd82b32f3903b93c
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/18/2019
ms.locfileid: "57850203"
---
# <a name="tutorial-azure-active-directory-integration-with-policystat"></a>Tutorial: Integración de Azure Active Directory con PolicyStat

En este tutorial, aprenderá a integrar PolicyStat con Azure Active Directory (Azure AD).

La integración de PolicyStat con Azure AD le proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a PolicyStat.
- Puede permitir que los usuarios inicien sesión automáticamente en PolicyStat (Inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: el nuevo Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con PolicyStat, se necesitan los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en PolicyStat

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. El escenario descrito en este tutorial consta de dos bloques de creación principales:

1. Incorporación de PolicyStat desde la galería
1. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-policystat-from-the-gallery"></a>Incorporación de PolicyStat desde la galería
Para configurar la integración de PolicyStat en Azure AD, es preciso agregar PolicyStat desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar PolicyStat desde la galería, siga estos pasos:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Active Directory][1]

1. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![APLICACIONES][2]
    
1. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![APLICACIONES][3]

1. En el cuadro de búsqueda, escriba **PolicySta**.

    ![Creación de un usuario de prueba de Azure AD](./media/policystat-tutorial/tutorial_policystat_search.png)

1. En el panel de resultados, seleccione **PolicyStat** y luego haga clic en el botón **Agregar** para agregar la aplicación.

    ![Creación de un usuario de prueba de Azure AD](./media/policystat-tutorial/tutorial_policystat_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección, configurará y probará el inicio de sesión único de Azure AD con PolicyStat con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de PolicyStat para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de PolicyStat.

Para establecer la relación de vínculo en PolicyStat, asigne el valor de **nombre de usuario** de Azure AD como valor de **Nombre de usuario**.

Para configurar y probar el inicio de sesión único de Azure AD con PolicyStat, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-sign-on)** : para permitir a los usuarios usar esta característica.
1. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
1. **[Creación de un usuario de prueba de PolicyStat](#creating-a-policystat-test-user)**: para tener un homólogo de Britta Simon en PolicyStat vinculado a la representación del usuario en Azure AD.
1. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
1. **[Testing Single Sign-On](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y lo configurará en la aplicación PolicyStat.

**Para configurar el inicio de sesión único de Azure AD con PolicyStat, realice los pasos siguientes:**

1. En Azure Portal, en la página de integración de la aplicación **PolicyStat**, haga clic en **Inicio de sesión único**.

    ![Configurar inicio de sesión único][4]

1. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Configurar inicio de sesión único](./media/policystat-tutorial/tutorial_policystat_samlbase.png)

1. En la sección **Dominio y direcciones URL de PolicyStat**, lleve a cabo los pasos siguientes:

    ![Configurar inicio de sesión único](./media/policystat-tutorial/tutorial_policystat_url.png)

     a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<companyname>.policystat.com`.

    b. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón: `https://<companyname>.policystat.com/saml2/metadata/`

    > [!NOTE] 
    > Estos valores no son reales. Debe actualizarlos con la dirección URL y el identificador reales de inicio de sesión. Póngase en contacto con el [equipo de soporte al cliente de PolicyStat](http://www.policystat.com/support/) para obtener estos valores. 
 
1. En la sección **Certificado de firma de SAML**, haga clic en **XML de metadatos** y luego guarde el archivo de metadatos en el equipo.

    ![Configurar inicio de sesión único](./media/policystat-tutorial/tutorial_policystat_certificate.png) 

1. El objetivo de esta sección es describir cómo se habilita la autenticación de los usuarios en PolicyStat con su cuenta de Azure AD usando el protocolo SAML basado en la federación.

    La aplicación PolicyStat espera las aserciones de SAML en un formato específico, que requiere que se agreguen asignaciones de atributos personalizados a la configuración de los **atributos del token de SAML**.  

     La siguiente captura de pantalla le muestra un ejemplo de esto.

     ![Atributos](./media/policystat-tutorial/tutorial_policystat_attribute.png "Atributos")

1. Para agregar las asignaciones de los atributos necesarios, realice los pasos siguientes:

    | Nombre del atributo    |   Valor de atributo |
    |------------------- | -------------------- |
    | uid | ExtractMailPrefix([mail]) |
    
     a. Haga clic en **Agregar atributo** para abrir el cuadro de diálogo **Agregar atributo**.

    ![Configurar inicio de sesión único](./media/policystat-tutorial/tutorial_policystat_04.png)

    ![Configurar inicio de sesión único](./media/policystat-tutorial/tutorial_policystat_addatribute.png)
    
    b. En el cuadro de texto **Nombre de atributo**, escriba **uid**.

    c. En el cuadro de texto **Valor del atributo**, seleccione **ExtractMailPrefix()**.    
   
    d. En la lista **Correo**, seleccione **User.mail**.
    
    e. Haga clic en **Aceptar**.

1. Haga clic en el botón **Guardar** .

    ![Configurar inicio de sesión único](./media/policystat-tutorial/tutorial_general_400.png)

1. En otra ventana del explorador web, inicie sesión en el sitio de la compañía PolicyStat como administrador.

1. Haga clic en la pestaña **Administración** y en **Configuración de inicio de sesión único** en el panel de navegación izquierdo.
   
    ![Menú Administrator](./media/policystat-tutorial/ic808633.png "Menú Administrator")

1. En la sección **Configuración**, seleccione **Habilitar la integración de inicio de sesión único**.
   
    ![Configuración de inicio de sesión único](./media/policystat-tutorial/ic808634.png "Configuración de inicio de sesión único")

1. Haga clic en **Configurar atributos** y, en la sección **Configurar atributos**, realice los pasos siguientes:
   
    ![Configuración de inicio de sesión único](./media/policystat-tutorial/ic808635.png "Configuración de inicio de sesión único")
   
     a. En el cuadro de texto **Atributo de nombre de usuario**, escriba **uid**.

    b. En el cuadro de texto **Atributo de nombre**, escriba el **nombre** del usuario, **Britta**.

    c. En el cuadro de texto **Atributo de apellido**, escriba el **apellido** del usuario, **Simon**.

    d. En el **atributo de correo electrónico** cuadro de texto, escriba **emailaddress** del usuario **BrittaSimon\@contoso.com**.

    e. Haga clic en **Guardar cambios**.

1. Haga clic en **Sus metadatos de IDP** y en la sección **Sus metadatos de IDP**, realice los pasos siguientes:
   
    ![Configuración de inicio de sesión único](./media/policystat-tutorial/ic808636.png "Configuración de inicio de sesión único")
   
     a. Abra el archivo de metadatos descargado, copie el contenido y luego péguelo en el cuadro de texto **Metadatos del proveedor de identidades**.

    b. Haga clic en **Guardar cambios**.

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más aquí sobre la característica de documentación insertada: [Documentación insertada de Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

![Creación de un usuario de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo de **Azure Portal**, haga clic en el icono de **Azure Active Directory**.

    ![Creación de un usuario de prueba de Azure AD](./media/policystat-tutorial/create_aaduser_01.png) 

1. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y haga clic en **Todos los usuarios**.
    
    ![Creación de un usuario de prueba de Azure AD](./media/policystat-tutorial/create_aaduser_02.png) 

1. Para abrir el cuadro de diálogo **Usuario**, haga clic en **Agregar** en la parte superior del cuadro de diálogo.
 
    ![Creación de un usuario de prueba de Azure AD](./media/policystat-tutorial/create_aaduser_03.png) 

1. En la página de diálogo **Usuario**, realice los siguientes pasos:
 
    ![Creación de un usuario de prueba de Azure AD](./media/policystat-tutorial/create_aaduser_04.png) 

     a. En el cuadro de texto **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la **dirección de correo electrónico** de Britta Simon.

    c. Seleccione **Mostrar contraseña** y anote el valor del cuadro **Contraseña**.

    d. Haga clic en **Create**(Crear).
 
### <a name="creating-a-policystat-test-user"></a>Creación de un usuario de prueba de PolicyStat

Para permitir que los usuarios de Azure AD inicien sesión en PolicyStat, deben aprovisionarse en PolicyStat.  

PolicyStat admite aprovisionamiento de usuarios justo a tiempo. Esto significa que no es necesario agregar usuarios manualmente a PolicyStat. Los usuarios se agregarán automáticamente en su primer inicio de sesión a través del inicio de sesión único.

>[!NOTE]
>Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de PolicyStat ofrecida por PolicyStat para aprovisionar cuentas de usuario de Azure AD.
> 

### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, concederá acceso a Britta Simon a PolicyStat para que use el inicio de sesión único de Azure.

![Asignar usuario][200] 

**Para asignar a Britta Simon a PolicyStat, realice los pasos siguientes:**

1. En Azure Portal, abra la vista de aplicaciones, navegue a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

1. En la lista de aplicaciones, seleccione **PolicyStat**.

    ![Configurar inicio de sesión único](./media/policystat-tutorial/tutorial_policystat_app.png) 

1. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Asignar usuario][202] 

1. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Asignar usuario][203]

1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

1. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

1. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de PolicyStat en el Panel de acceso, debería iniciar sesión automáticamente en su aplicación PolicyStat.
Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/policystat-tutorial/tutorial_general_01.png
[2]: ./media/policystat-tutorial/tutorial_general_02.png
[3]: ./media/policystat-tutorial/tutorial_general_03.png
[4]: ./media/policystat-tutorial/tutorial_general_04.png

[100]: ./media/policystat-tutorial/tutorial_general_100.png

[200]: ./media/policystat-tutorial/tutorial_general_200.png
[201]: ./media/policystat-tutorial/tutorial_general_201.png
[202]: ./media/policystat-tutorial/tutorial_general_202.png
[203]: ./media/policystat-tutorial/tutorial_general_203.png

