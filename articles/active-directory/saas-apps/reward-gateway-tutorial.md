---
title: 'Tutorial: Integración de Azure Active Directory con Reward Gateway | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Reward Gateway.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 34336386-998a-4d47-ab55-721d97708e5e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/30/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 34e0e9b83dabfb5b389030248f1787e1e8ef9dd4
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/18/2019
ms.locfileid: "57840671"
---
# <a name="tutorial-azure-active-directory-integration-with-reward-gateway"></a>Tutorial: Integración de Azure Active Directory con Reward Gateway

En este tutorial, aprenderá a integrar Reward Gateway con Azure Active Directory (Azure AD).

Integrar Reward Gateway con Azure AD le proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a Reward Gateway.
- Puede permitir que los usuarios inicien sesión automáticamente en Reward Gateway (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: el nuevo Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Reward Gateway, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en Reward Gateway

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. El escenario descrito en este tutorial consta de dos bloques de creación principales:

1. Adición de Reward Gateway desde la galería
1. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-reward-gateway-from-the-gallery"></a>Adición de Reward Gateway desde la galería
Para configurar la integración de Reward Gateway en Azure AD, deberá agregar Reward Gateway desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Reward Gateway desde la galería, siga estos pasos:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Active Directory][1]

1. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![APLICACIONES][2]
    
1. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![APLICACIONES][3]

1. En el cuadro de búsqueda, escriba **Reward Gateway**.

    ![Creación de un usuario de prueba de Azure AD](./media/reward-gateway-tutorial/tutorial_rewardgateway_search.png)

1. En el panel de resultados, seleccione **Reward Gateway** y luego haga clic en el botón **Agregar** para agregar la aplicación.

    ![Creación de un usuario de prueba de Azure AD](./media/reward-gateway-tutorial/tutorial_rewardgateway_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuración y comprobación del inicio de sesión único de Azure AD
En esta sección, podrá configurar y probar el inicio de sesión único de Azure AD con Reward Gateway con un usuario de prueba denominado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de Reward Gateway para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Reward Gateway.

Para establecer la relación de vínculo, asigne el valor de **nombre de usuario** de Azure AD como valor de **nombre de usuario** de Reward Gateway.

Para configurar y probar el inicio de sesión único de Azure AD con Reward Gateway, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configuring-azure-ad-single-sign-on)** : para permitir a los usuarios usar esta característica.
1. **[Creación de un usuario de prueba de Azure AD](#creating-an-azure-ad-test-user)** : para probar el inicio de sesión único de Azure AD con Britta Simon.
1. **[Creación de un usuario de prueba de Reward Gateway](#creating-a-reward-gateway-test-user)**: el objetivo es tener un homólogo de Britta Simon en Reward Gateway que esté vinculado a la representación del usuario en Azure AD.
1. **[Asignación del usuario de prueba de Azure AD](#assigning-the-azure-ad-test-user)** : para permitir que Britta Simon use el inicio de sesión único de Azure AD.
1. **[Testing Single Sign-On](#testing-single-sign-on)** : para comprobar si funciona la configuración.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y lo configurará en la aplicación Reward Gateway.

**Para configurar el inicio de sesión único de Azure AD con Reward Gateway, siga estos pasos:**

1. En la página de integración de la aplicación **Reward Gateway** de Azure Portal, haga clic en **Inicio de sesión único**.

    ![Configurar inicio de sesión único][4]

1. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Configurar inicio de sesión único](./media/reward-gateway-tutorial/tutorial_rewardgateway_samlbase.png)

1. En la sección **Dominio y direcciones URL de Reward Gateway**, lleve a cabo los pasos siguientes:

    ![Configurar inicio de sesión único](./media/reward-gateway-tutorial/tutorial_rewardgateway_url.png)

     a. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón:

    | |
    |--|
    | `https://<companyname>.rewardgateway.com` |
    | `https://<companyname>.rewardgateway.co.uk/` |
    | `https://<companyname>.rewardgateway.co.nz/` |
    | `https://<companyname>.rewardgateway.com.au/` |

    b. En el cuadro de texto **URL de respuesta** , escriba una dirección URL con el siguiente patrón:
    
    | |
    |--|
    |  `https://<companyname>.rewardgateway.com/Authentication/EndLogin?idp=<Unique Id>` |
    | `https://<companyname>.rewardgateway.co.uk/Authentication/EndLogin?idp=<Unique Id>` |
    | `https://<companyname>.rewardgateway.co.nz/Authentication/EndLogin?idp=<Unique Id>` |
    | `https://<companyname>.rewardgateway.com.au/Authentication/EndLogin?idp=<Unique Id>` |

    > [!NOTE] 
    > Estos valores no son reales. Actualice estos valores con el identificador y la URL de respuesta reales. Para obtener estos valores empiece por configurar una integración en el portal de Reward Manager. Encontrará más información en https://success.rewardgateway.com/authentication-integrations/microsoft-azure-for-authentication.
 
1. En la sección **Certificado de firma de SAML**, haga clic en **XML de metadatos** y luego guarde el archivo de metadatos en el equipo.

    ![Configurar inicio de sesión único](./media/reward-gateway-tutorial/tutorial_rewardgateway_certificate.png) 

1. Haga clic en el botón **Guardar** .

    ![Configurar inicio de sesión único](./media/reward-gateway-tutorial/tutorial_general_400.png)

1. Para configurar el inicio de sesión único en **Reward Gateway**, empiece por configurar una integración en el portal de Reward Manager. Utilice los metadatos descargados para obtener el certificado de firma y cárguelos durante la configuración. Encontrará más información en https://success.rewardgateway.com/authentication-integrations/microsoft-azure-for-authentication.

> [!TIP]
> Ahora puede leer una versión resumida de estas instrucciones dentro de [Azure Portal](https://portal.azure.com) mientras configura la aplicación.  Después de agregar esta aplicación desde la sección **Active Directory > Aplicaciones empresariales**, simplemente haga clic en la pestaña **Inicio de sesión único** y acceda a la documentación insertada a través de la sección **Configuración** de la parte inferior. Puede leer más aquí sobre la característica de documentación insertada: [Documentación insertada de Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD
El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

![Creación de un usuario de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel de navegación izquierdo de **Azure Portal**, haga clic en el icono de **Azure Active Directory**.

    ![Creación de un usuario de prueba de Azure AD](./media/reward-gateway-tutorial/create_aaduser_01.png) 

1. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y haga clic en **Todos los usuarios**.
    
    ![Creación de un usuario de prueba de Azure AD](./media/reward-gateway-tutorial/create_aaduser_02.png) 

1. Para abrir el cuadro de diálogo **Usuario**, haga clic en **Agregar** en la parte superior del cuadro de diálogo.
 
    ![Creación de un usuario de prueba de Azure AD](./media/reward-gateway-tutorial/create_aaduser_03.png) 

1. En la página de diálogo **Usuario**, realice los siguientes pasos:
 
    ![Creación de un usuario de prueba de Azure AD](./media/reward-gateway-tutorial/create_aaduser_04.png) 

     a. En el cuadro de texto **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la **dirección de correo electrónico** de Britta Simon.

    c. Seleccione **Mostrar contraseña** y anote el valor del cuadro **Contraseña**.

    d. Haga clic en **Create**(Crear).
 
### <a name="creating-a-reward-gateway-test-user"></a>Creación de un usuario de prueba de Reward Gateway

En esta sección, creará un usuario denominado Britta Simon en Reward Gateway. Colabore con el [equipo de soporte técnico](mailto:clientsupport@rewardgateway.com) de Reward Gateway para agregar los usuarios a la plataforma de Reward Gateway.

### <a name="assigning-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Reward Gateway.

![Asignar usuario][200] 

**Para asignar Britta Simon a Reward Gateway, siga estos pasos:**

1. En Azure Portal, abra la vista de aplicaciones, navegue a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

1. En la lista de aplicaciones, seleccione **Reward Gateway**.

    ![Configurar inicio de sesión único](./media/reward-gateway-tutorial/tutorial_rewardgateway_app.png) 

1. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Asignar usuario][202] 

1. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Asignar usuario][203]

1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

1. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

1. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="testing-single-sign-on"></a>Prueba del inicio de sesión único 

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de Reward Gateway en el panel de acceso, debería iniciar sesión automáticamente en su aplicación Reward Gateway.

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/reward-gateway-tutorial/tutorial_general_01.png
[2]: ./media/reward-gateway-tutorial/tutorial_general_02.png
[3]: ./media/reward-gateway-tutorial/tutorial_general_03.png
[4]: ./media/reward-gateway-tutorial/tutorial_general_04.png

[100]: ./media/reward-gateway-tutorial/tutorial_general_100.png

[200]: ./media/reward-gateway-tutorial/tutorial_general_200.png
[201]: ./media/reward-gateway-tutorial/tutorial_general_201.png
[202]: ./media/reward-gateway-tutorial/tutorial_general_202.png
[203]: ./media/reward-gateway-tutorial/tutorial_general_203.png

