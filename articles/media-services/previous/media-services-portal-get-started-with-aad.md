---
title: Introducción a la autenticación de Azure AD mediante Azure Portal | Microsoft Docs
description: Obtenga información sobre cómo usar Azure Portal para acceder a la autenticación de Azure Active Directory (Azure AD) para utilizar la API de Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 4428187c985c1004c88f2ac20b0e5811803cce2a
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/12/2020
ms.locfileid: "77162776"
---
# <a name="get-started-with-azure-ad-authentication-by-using-the-azure-portal"></a>Introducción a la autenticación de Azure AD mediante Azure Portal

> [!NOTE]
> No hay características o funcionalidades nuevas para agregar a Media Services, versión 2. <br/>Finalice la compra de la versión más reciente, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Consulte también la [guía de migración de la versión v2 a v3](../latest/migrate-from-v2-to-v3.md).

Obtenga información sobre cómo usar Azure Portal para acceder a la autenticación de Azure Active Directory (Azure AD) para tener acceso a la API de Azure Media Services.

## <a name="prerequisites"></a>Prerrequisitos

- Una cuenta de Azure. Si no tiene cuenta, comience con una [evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/). 
- Una cuenta de Media Services. Para más información, vea [Creación de una cuenta de Azure Media Services mediante Azure Portal](media-services-portal-create-account.md).
- Asegúrese de revisar la [información general sobre el acceso a la API de Azure Media Services con la autenticación de Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

Al utilizar la autenticación de Azure AD con Azure Media Services, tiene dos opciones de autenticación:

- **Autenticación de usuario**. Autenticar a alguien que usa la aplicación para interactuar con los recursos de Media Services. La aplicación interactiva en primer lugar debe solicitar al usuario las credenciales. Un ejemplo es una aplicación de consola de administración que usan los usuarios autorizados para supervisar trabajos de codificación o streaming en vivo. 
- **Autenticación de entidad de servicio**. Autenticar un servicio. Las aplicaciones que normalmente utilizan este método de autenticación son aplicaciones que ejecutan servicios de demonio, servicios de nivel intermedio o trabajos programados: Web Apps, Function Apps, Logic Apps, API o un microservicio.

> [!IMPORTANT]
> Actualmente, Media Services es compatible con el modelo de autenticación de Azure Access Control Service. Sin embargo, la autorización de Access Control dejará de usarse el 1 de junio de 2018. Se recomienda migrar tan pronto como sea posible al modelo de autenticación de Azure AD.

## <a name="select-the-authentication-method"></a>Selección del método de autenticación

1. En [Azure Portal](https://portal.azure.com/), seleccione la cuenta de Media Services.
2. Seleccione cómo conectarse a la API de Media Services.

    ![Página de selección del método de conexión](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started01.png)

## <a name="user-authentication"></a>Autenticación de usuarios

Para conectarse a la API de Media Services mediante la opción de autenticación de usuario, la aplicación cliente debe solicitar un token de Azure AD que tiene los siguientes parámetros:  

* Punto de conexión de inquilino de Azure AD
* URI del recurso de Media Services
* Id. de cliente de aplicación de Media Services (nativo) 
* URI de redireccionamiento de aplicación de Media Services (nativo) 
* URI del recurso de Media Services de REST

Puede obtener los valores para estos parámetros en la página de la **API de Media Services con la autenticación de usuario**. 

![Página de conexión con la autenticación de usuario](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started02.png)

Si se conecta a la API de Media Services mediante el SDK de Microsoft .NET para Media Services, los valores necesarios están disponibles como parte del SDK. Para más información, vea [Use Azure AD authentication to access the Azure Media Services API with .NET](media-services-dotnet-get-started-with-aad.md) (Uso de la autenticación de Azure AD para acceder a la API de Azure Media Services con .NET).

Si no usa el SDK del cliente .NET para Media Services, debe crear manualmente una solicitud de token de Azure AD con los parámetros descritos anteriormente. Para más información, vea [Procedimiento para usar la Biblioteca de autenticación de Azure AD para obtener el token de Azure AD](../../active-directory/azuread-dev/active-directory-authentication-libraries.md).

## <a name="service-principal-authentication"></a>Autenticación de entidad de servicio

Para conectarse a la API de Media Services mediante la opción de la entidad de servicio, la aplicación de nivel intermedio (Web API o aplicación web) necesita solicitar un token de Azure AD que tenga los parámetros siguientes:  

* Punto de conexión de inquilino de Azure AD
* URI del recurso de Media Services 
* URI del recurso de Media Services de REST
* Valores de aplicación de Azure AD: el **Id. de cliente** y el **secreto de cliente**

Puede obtener los valores para estos parámetros en la página de **conexión a la API de Media Services con la entidad de servicio**. Utilice esta página para crear una aplicación de Azure AD o seleccionar una existente. Después de seleccionar la aplicación de Azure AD, puede obtener el identificador de cliente (identificador de aplicación) y generar los valores del secreto de cliente (clave). 

![Página de conexión a la entidad de servicio](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started04.png)

Cuando la hoja **Entidad de servicio** se abre, se selecciona la primera aplicación de Azure AD que cumple los criterios siguientes:

- Se trata de una aplicación registrada de Azure AD.
- Tiene permisos de control de acceso basado en roles de colaborador o propietario en la cuenta.

Después de crear o seleccionar una aplicación de Azure AD, puede crear y copiar un secreto de cliente (clave) y el identificador de cliente (identificador de aplicación). El secreto de cliente y el identificador de cliente son necesarios para obtener el token de acceso en este escenario.

Si no tiene permisos para crear aplicaciones de Azure AD en su dominio, no se muestran los controles de la aplicación de Azure AD de la hoja y se muestra un mensaje de advertencia.

Si se conecta a la API de Media Services mediante el SDK de Media Services para .NET, vea [Usar autenticación de Azure AD para acceder a la API de Azure Media Services con .NET](media-services-dotnet-get-started-with-aad.md).

Si no usa el SDK del cliente .NET para Media Services, debe crear manualmente una solicitud de token de Azure AD con los parámetros descritos anteriormente. Para más información, vea [Procedimiento para usar la Biblioteca de autenticación de Azure AD para obtener el token de Azure AD](../../active-directory/azuread-dev/active-directory-authentication-libraries.md).

### <a name="get-the-client-id-and-client-secret"></a>Obtención del identificador de cliente y del secreto de cliente

Después de seleccionar una aplicación de Azure AD existente o seleccionar la opción para crear una, aparecen los siguientes botones:

![Botón Administrar permisos y botón Administrar aplicación](./media/media-services-portal-get-started-with-aad/media-services-portal-manage.png)

Para abrir la hoja Aplicación de Azure AD, haga clic en **Administrar aplicación**. En la hoja **Administrar aplicación**, puede obtener el identificador de cliente de la aplicación (Id. de la aplicación). Para generar un secreto de cliente (clave), seleccione **Claves**.

![Opción Claves de la hoja Administrar aplicación](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started06.png) 

### <a name="manage-permissions-and-the-application"></a>Administrar los permisos y la aplicación

Después de seleccionar la aplicación de Azure AD, puede administrar la aplicación y los permisos. Para configurar la aplicación de Azure AD para acceder a otras aplicaciones, haga clic en **Administrar permisos**. Para las tareas de administración, como cambiar claves y direcciones URL de respuesta, o para editar el manifiesto de la aplicación, haga clic en **Administrar aplicación**.

### <a name="edit-the-apps-settings-or-manifest"></a>Edición de la configuración o el manifiesto de la aplicación

Para editar la configuración o el manifiesto de la aplicación, haga clic en **Administrar aplicación**.

![Página de Administrar aplicación](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started05.png)

## <a name="next-steps"></a>Pasos siguientes

Empiece a trabajar en la [carga de archivos en la cuenta](media-services-portal-upload-files.md).
