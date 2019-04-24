---
title: 'Tutorial: Integración de Azure Active Directory con Land Gorilla Client | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Land Gorilla.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/13/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b2e1288885476aeeba01f966b615c23c5fe51081
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "60261000"
---
# <a name="tutorial-azure-active-directory-integration-with-land-gorilla-client"></a>Tutorial: Integración de Azure Active Directory con Land Gorilla Client

En este tutorial, obtendrá información sobre cómo integrar Land Gorilla Client con Azure Active Directory (Azure AD).

La integración de Land Gorilla Client con Azure AD le proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Land Gorilla Client
- Puede permitir que los usuarios inicien sesión automáticamente en Land Gorilla Client (inicio de sesión único) con sus cuentas de Azure AD
- Puede administrar sus cuentas en una ubicación central: el Portal de administración de Azure

Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](../manage-apps/what-is-single-sign-on.md).


## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Land Gorilla Client, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en Land Gorilla Client


> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.


Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No debe usar el entorno de producción, a menos que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. El escenario descrito en este tutorial consta de dos bloques de creación principales:

1. Agregar Land Gorilla Client desde la galería
1. Configuración y comprobación del inicio de sesión único de Azure AD


## <a name="adding-land-gorilla-client-from-the-gallery"></a>Agregar Land Gorilla Client desde la galería
Para configurar la integración de Land Gorilla Client en Azure AD, deberá agregar Land Gorilla Client desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Land Gorilla Client desde la galería, siga estos pasos:**

1. En el panel de navegación izquierdo del **[Portal de administración de Azure](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Active Directory][1]

1. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![APLICACIONES][2]
    
1. Haga clic en el botón **Agregar** situado en la parte superior del cuadro de diálogo.

    ![APLICACIONES][3]

1. En el cuadro de búsqueda, escriba **Land Gorilla Client**.

    ![Creación de un usuario de prueba de Azure AD](./media/landgorilla-tutorial/tutorial_landgorilla_search.png)

1. En el panel de resultados, seleccione **Land Gorilla Client** y luego haga clic en el botón **Agregar** para agregar la aplicación.

    ![Creación de un usuario de prueba de Azure AD](./media/landgorilla-tutorial/tutorial_landgorilla_addfromgallery.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección, configurará y probará el inicio de sesión único de Azure AD con Land Gorilla Client con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Land Gorilla Client para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Land Gorilla Client.

Esta relación de vínculo se establece mediante la asignación del valor del **nombre de usuario** en Azure AD como el valor del **nombre de usuario** en Land Gorilla Client.

Para configurar y probar el inicio de sesión único de Azure AD con Land Gorilla Client, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-sign-on)** : para permitir a los usuarios usar esta característica.
1. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con un grupo limitado.
1. **[Creación de un usuario de prueba de Land Gorilla](#creating-a-land-gorilla-test-user)**: para probar el inicio de sesión único de Azure AD con Britta Simon.
1. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
1. **[Testing Single Sign-On](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en el Portal de administración de Azure y configurará el inicio de sesión único en la aplicación Land Gorilla Client.

**Para configurar el inicio de sesión único de Azure AD con Land Gorilla Client, realice los pasos siguientes:**

1. En el Portal de administración de Azure, en la página de integración de la aplicación **Land Gorilla Client**, haga clic en **Inicio de sesión único**.

    ![Configurar inicio de sesión único][4]

1. En el cuadro de diálogo **Inicio de sesión único**, en **Modo**, seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Configurar inicio de sesión único](./media/landgorilla-tutorial/tutorial_landgorilla_samlbase.png)

1. En la sección **Dominio y direcciones URL de Land Gorilla Client**, lleve a cabo los pasos siguientes:

    ![Configurar inicio de sesión único](./media/landgorilla-tutorial/tutorial_landgorilla_url_02.png)

     a. En el cuadro de texto **Identificador**, escriba un valor con el siguiente patrón: 
    
    `https://<customer domain>.landgorilla.com/` 
    
    `https://www.<customer domain>.landgorilla.com`

    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con uno de los siguientes patrones:

    `https://<customer domain>.landgorilla.com/simplesaml/module.php/core/authenticate.php`

    `https://www.<customer domain>.landgorilla.com/simplesaml/module.php/core/authenticate.php`

    `https://<customer domain>.landgorilla.com/simplesaml/module.php/saml/sp/saml2-acs.php/default-sp`
    
    `https://www.<customer domain>.landgorilla.com/simplesaml/module.php/saml/sp/saml2-acs.php/default-sp`

    > [!NOTE] 
    > Tenga en cuenta que estos no son valores reales. Estos valores se tienen que actualizar con los valores reales de Identificador y URL de respuesta. Aquí le recomendamos que utilice el valor de cadena único en el identificador. Póngase en contacto con el [equipo de soporte de Land Gorilla Client](https://www.landgorilla.com/support/) para obtener estos valores. 

1. En la sección **Certificado de firma de SAML**, haga clic en **XML de metadatos** y luego guarde el archivo XML en el equipo.

    ![Configurar inicio de sesión único](./media/landgorilla-tutorial/tutorial_landgorilla_certificate.png) 

1. Haga clic en el botón **Guardar** .

    ![Configurar inicio de sesión único](./media/landgorilla-tutorial/tutorial_general_400.png) 

1. Para configurar el inicio de sesión único en el lado de su aplicación Land Gorilla, póngase en contacto con el [equipo de soporte de Land Gorilla Client](https://www.landgorilla.com/support/) para proporcionarles el archivo **XML de metadatos** descargado.


### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en el Portal de administración de Azure llamado Britta Simon.

![Creación de un usuario de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo del **Portal de administración de Azure**, haga clic en el icono de **Azure Active Directory**.

    ![Creación de un usuario de prueba de Azure AD](./media/landgorilla-tutorial/create_aaduser_01.png) 

1. Vaya a **Usuarios y grupos** y haga clic en **Todos los usuarios** para mostrar la lista de usuarios.
    
    ![Creación de un usuario de prueba de Azure AD](./media/landgorilla-tutorial/create_aaduser_02.png) 

1. En la parte superior del diálogo, haga clic en **Agregar** para abrir el diálogo **Usuario**.
 
    ![Creación de un usuario de prueba de Azure AD](./media/landgorilla-tutorial/create_aaduser_03.png) 

1. En la página de diálogo **Usuario**, realice los siguientes pasos:
 
    ![Creación de un usuario de prueba de Azure AD](./media/landgorilla-tutorial/create_aaduser_04.png) 

     a. En el cuadro de texto **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la **dirección de correo electrónico** de Britta Simon.

    c. Seleccione **Mostrar contraseña** y anote el valor del cuadro **Contraseña**.

    d. Haga clic en **Create**(Crear). 

### <a name="creating-a-land-gorilla-test-user"></a>Creación de un usuario de prueba de Land Gorilla

Trabaje con el [equipo de soporte técnico de Land Gorilla](https://www.landgorilla.com/support/) para agregar los usuarios a la plataforma de Land Gorilla.
    
### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, va a habilitar a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Land Gorilla Client.

![Asignar usuario][200] 

**Para asignar a Britta Simon a Land Gorilla Client, realice los pasos siguientes:**

1. En el Portal de administración de Azure, abra la vista de aplicaciones, vaya a la vista de directorio y vaya a **Aplicaciones empresariales**. A continuación, haga clic en **All applications** (Todas las aplicaciones).

    ![Asignar usuario][201] 

1. En la lista de aplicaciones, seleccione **Land Gorilla Client**.

    ![Configurar inicio de sesión único](./media/landgorilla-tutorial/tutorial_landgorilla_app.png) 

1. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Asignar usuario][202] 

1. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Asignar usuario][203]

1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

1. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

1. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    


### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de Land Gorilla Client en el Panel de acceso, debería iniciar sesión automáticamente en su aplicación Land Gorilla Client.


## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/landgorilla-tutorial/tutorial_general_01.png
[2]: ./media/landgorilla-tutorial/tutorial_general_02.png
[3]: ./media/landgorilla-tutorial/tutorial_general_03.png
[4]: ./media/landgorilla-tutorial/tutorial_general_04.png

[100]: ./media/landgorilla-tutorial/tutorial_general_100.png
[200]: ./media/landgorilla-tutorial/tutorial_general_200.png
[201]: ./media/landgorilla-tutorial/tutorial_general_201.png
[202]: ./media/landgorilla-tutorial/tutorial_general_202.png
[203]: ./media/landgorilla-tutorial/tutorial_general_203.png
