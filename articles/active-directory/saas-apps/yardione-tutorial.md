---
title: 'Tutorial: Integración de Azure Active Directory con YardiOne | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y YardiOne.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 508957f6-caa5-4234-a7f3-90015937e4eb
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 22abdcc004f3b353042d84cf01070fde7650367b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "60629667"
---
# <a name="tutorial-azure-active-directory-integration-with-yardione"></a>Tutorial: Integración de Azure Active Directory con YardiOne

En este tutorial, aprenderá a integrar YardiOne con Azure Active Directory (Azure AD).

Integrar YardiOne con Azure AD le proporciona las siguientes ventajas:

- Puede controlar en Azure AD quién tiene acceso a YardiOne.
- Puede permitir que los usuarios inicien sesión automáticamente en YardiOne (inicio de sesión único) con sus cuentas de Azure AD.
- Puede administrar sus cuentas en una ubicación central: Azure Portal.

Si desea saber más sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con YardiOne, necesita los siguientes elementos:

- Una suscripción de Azure AD
- Una suscripción habilitada para el inicio de sesión único en YardiOne

> [!NOTE]
> Para probar los pasos de este tutorial, no se recomienda el uso de un entorno de producción.

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

- No use el entorno de producción, salvo que sea necesario.
- Si no dispone de un entorno de prueba de Azure AD, puede [obtener una versión de prueba durante un mes](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descripción del escenario
En este tutorial, puede probar el inicio de sesión único de Azure AD en un entorno de prueba. El escenario descrito en este tutorial consta de dos bloques de creación principales:

1. Adición de YardiOne desde la galería
1. Configuración y comprobación del inicio de sesión único de Azure AD

## <a name="adding-yardione-from-the-gallery"></a>Adición de YardiOne desde la galería
Para configurar la integración de YardiOne en Azure AD, deberá agregar YardiOne desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar YardiOne desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)**, haga clic en el icono de **Azure Active Directory**. 

    ![Botón Azure Active Directory][1]

1. Vaya a **Aplicaciones empresariales**. A continuación, vaya a **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales][2]
    
1. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación][3]

1. En el cuadro de búsqueda, escriba **YardiOne**, seleccione **YardiOne** en el panel de resultados y, luego, haga clic en el botón **Agregar** para agregar la aplicación.

    ![YardiOne en la lista de resultados](./media/yardione-tutorial/tutorial_yardione_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección, configurará y probará el inicio de sesión único de Azure AD con YardiOne con un usuario de prueba llamado "Britta Simon".

Para que el inicio de sesión único funcione, Azure AD debe saber cuál es el usuario homólogo de YardiOne para un usuario de Azure AD. Es decir, es necesario establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de YardiOne.

Para configurar y probar el inicio de sesión único de Azure AD con YardiOne, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)**: para que los usuarios puedan usar esta característica.
1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)**, para probar el inicio de sesión único de Azure AD con Britta Simon.
1. **[Creación de un usuario de prueba de YardiOne](#create-a-yardione-test-user)**: para tener un homólogo de Britta Simon en YardiOne que esté vinculado a la representación del usuario en Azure AD.
1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)**, para permitir que Britta Simon use el inicio de sesión único de Azure AD.
1. **[Prueba del inicio de sesión único](#test-single-sign-on)**: para comprobar si la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal y configurará el inicio de sesión único en la aplicación YardiOne.

**Para configurar el inicio de sesión único de Azure AD con YardiOne, siga estos pasos:**

1. En Azure Portal, en la página de integración de aplicaciones **YardiOne**, haga clic en **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único][4]

1. En el cuadro de diálogo **Inicio de sesión único**, en **Modo** seleccione **Inicio de sesión basado en SAML** para habilitar el inicio de sesión único.
 
    ![Cuadro de diálogo Inicio de sesión único](./media/yardione-tutorial/tutorial_yardione_samlbase.png)

1. En la sección **YardiOne Domain and URLs** (Dominios y direcciones URL de YardiOne), lleve a cabo los pasos siguientes:

    ![Información sobre el inicio de sesión único del dominio y las direcciones URL de YardiOne](./media/yardione-tutorial/tutorial_yardione_url.png)

     a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<y1-subdomain>.yardione.com`.

    b. En el cuadro de texto **Identificador**, escriba una dirección URL con el siguiente patrón: `http://<y1-subdomain>.yardione.com/yAuth2/trust`

    > [!NOTE] 
    > Estos valores no son reales. Debe actualizarlos con la dirección URL y el identificador reales de inicio de sesión. Póngase en contacto con el [equipo de soporte de cliente de YardiOne](https://clientcentral.yardi.com) para obtener estos valores.
     
1. En la sección **Certificado de firma de SAML**, haga clic en el botón Copiar para copiar la **dirección URL de metadatos de federación de la aplicación** y péguela en el Bloc de notas. 

    ![Vínculo de descarga del certificado](./media/yardione-tutorial/tutorial_yardione_certificate.png) 
 
1. Haga clic en el botón **Guardar** .

    ![Botón Configurar inicio de sesión único](./media/yardione-tutorial/tutorial_general_400.png)

1. Para configurar el inicio de sesión único en **YardiOne**, hay que enviar la **dirección URL de metadatos de aplicación** al [equipo de soporte técnico de YardiOne](https://clientcentral.yardi.com). Dicho equipo lo configura para establecer la conexión de SSO de SAML correctamente en ambos lados.

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

   ![Creación de un usuario de prueba de Azure AD][100]

**Siga estos pasos para crear un usuario de prueba en Azure AD:**

1. En el panel izquierdo de Azure Portal, haga clic en el botón **Azure Active Directory**.

    ![Botón Azure Active Directory](./media/yardione-tutorial/create_aaduser_01.png)

1. Para mostrar la lista de usuarios, vaya a **Usuarios y grupos** y, luego, haga clic en **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](./media/yardione-tutorial/create_aaduser_02.png)

1. En la parte superior del cuadro de diálogo **Todos los usuarios**, haga clic en **Agregar** para abrir el cuadro de diálogo **Agregar**.

    ![Botón Agregar](./media/yardione-tutorial/create_aaduser_03.png)

1. En el cuadro de diálogo **Usuario** , realice los pasos siguientes:

    ![Cuadro de diálogo Usuario](./media/yardione-tutorial/create_aaduser_04.png)

     a. En el cuadro **Nombre**, escriba **BrittaSimon**.

    b. En el cuadro de texto **Nombre de usuario**, escriba la dirección de correo electrónico del usuario Britta Simon.

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro **Contraseña**.

    d. Haga clic en **Create**(Crear).
 
### <a name="create-a-yardione-test-user"></a>Creación de un usuario de prueba de YardiOne

El objetivo de esta sección es crear un usuario de prueba llamado Britta Simon en YardiOne. YardiOne admite el aprovisionamiento Just-In-Time, que está habilitado de forma predeterminada. No hay ningún elemento de acción para usted en esta sección. Durante un intento de acceso a YardiOne se crea un nuevo usuario, en caso de que no exista.

>[!Note]
>Si necesita crear manualmente un usuario, póngase en contacto con el  [equipo de soporte técnico de YardiOne](https://clientcentral.yardi.com).

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, permitirá que Britta Simon use el inicio de sesión único de Azure concediéndole acceso a YardiOne.

![Asignación de rol de usuario][200] 

**Para asignar a Britta Simon a YardiOne, siga estos pasos:**

1. En Azure Portal, abra la vista de aplicaciones, navegue a la vista de directorio y vaya a **Aplicaciones empresariales**. Luego haga clic en **Todas las aplicaciones**.

    ![Asignar usuario][201] 

1. En la lista de aplicaciones, seleccione **YardiOne**.

    ![Vínculo a YardiOne en la lista de aplicaciones](./media/yardione-tutorial/tutorial_yardione_app.png)  

1. En el menú de la izquierda, haga clic en **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"][202]

1. Haga clic en el botón **Agregar**. Después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación][203]

1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista de usuarios.

1. Haga clic en el botón **Seleccionar** del cuadro de diálogo **Usuarios y grupos**.

1. Haga clic en el botón **Asignar** del cuadro de diálogo **Agregar asignación**.
    
### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de YardiOne en el panel de acceso, debería iniciar sesión automáticamente en su aplicación YardiOne.
Para más información sobre el Panel de acceso, consulte la [introducción al Panel de acceso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionales

* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/yardione-tutorial/tutorial_general_01.png
[2]: ./media/yardione-tutorial/tutorial_general_02.png
[3]: ./media/yardione-tutorial/tutorial_general_03.png
[4]: ./media/yardione-tutorial/tutorial_general_04.png

[100]: ./media/yardione-tutorial/tutorial_general_100.png

[200]: ./media/yardione-tutorial/tutorial_general_200.png
[201]: ./media/yardione-tutorial/tutorial_general_201.png
[202]: ./media/yardione-tutorial/tutorial_general_202.png
[203]: ./media/yardione-tutorial/tutorial_general_203.png

