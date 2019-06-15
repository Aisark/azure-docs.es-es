---
title: Uso de Azure Active Directory para autenticar soluciones de servicio de Azure Batch | Microsoft Docs
description: Batch admite Azure AD para la autenticación desde el servicio de Batch.
services: batch
documentationcenter: .net
author: laurenhughes
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/18/2018
ms.author: lahugh
ms.openlocfilehash: 0ca22cfe99e77cd2ed3c5a966fb2412444103d71
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "64922442"
---
# <a name="authenticate-batch-service-solutions-with-active-directory"></a>Autenticación de soluciones de servicio de Batch con Active Directory

Azure Batch admite la autenticación con [Azure Active Directory][aad_about] (Azure AD). Azure Active Directory es el directorio basado en la nube multiinquilino y el servicio de administración de identidades de Microsoft. El propio Azure usa Azure AD para la autenticación de sus clientes, administradores de servicios y usuarios de la organización.

Al utilizar la autenticación de Azure AD con Azure Batch, puede autenticar de una de estas dos maneras:

- Uso de **autenticación integrada** para autenticar un usuario que está interactuando con la aplicación. Una aplicación que use la autenticación integrada recopila las credenciales de un usuario y las usa para autenticar el acceso a los recursos de Batch.
- Uso de una **entidad de servicio** para autenticar una aplicación desatendida. Una entidad de servicio define la directiva y los permisos de una aplicación para representar la aplicación cuando se accede a los recursos en tiempo de ejecución.

Para más información acerca de Azure AD, consulte la [Documentación de Azure Active Directory](https://docs.microsoft.com/azure/active-directory/).

## <a name="endpoints-for-authentication"></a>Puntos de conexión para autenticación

Para autenticar aplicaciones de Batch con Azure AD, debe incluir algunos puntos de conexión conocidos en el código.

### <a name="azure-ad-endpoint"></a>Punto de conexión de Azure AD

El punto de conexión de la autoridad de Azure AD básico es:

`https://login.microsoftonline.com/`

Para autenticar con Azure AD, use este punto de conexión junto con el identificador de inquilino (identificador del directorio). El identificador de inquilino identifica el inquilino de Azure AD que se usará para la autenticación. Para recuperar el identificador de inquilino, siga los pasos descritos en [Obtención del identificador de inquilino de Azure Active Directory](#get-the-tenant-id-for-your-active-directory):

`https://login.microsoftonline.com/<tenant-id>`

> [!NOTE] 
> El punto de conexión específico del inquilino es necesario al autenticar con una entidad de servicio. 
> 
> El punto de conexión específico del inquilino es opcional, aunque recomendable, al autenticar mediante autenticación integrada. Sin embargo, también puede utilizar el punto de conexión común de Azure AD. El punto de conexión común proporciona una interfaz genérica de recopilación de credenciales cuando no se proporciona un inquilino específico. El punto de conexión común es `https://login.microsoftonline.com/common`.
>
>

Para más información sobre los puntos de conexión de Azure AD, consulte [Escenarios de autenticación para Azure AD][aad_auth_scenarios].

### <a name="batch-resource-endpoint"></a>Punto de conexión de recursos de Batch

Use el **punto de conexión de recursos de Azure Batch** para adquirir un token para autenticar las solicitudes al servicio de Batch:

`https://batch.core.windows.net/`

## <a name="register-your-application-with-a-tenant"></a>Registro de la aplicación con un inquilino

El primer paso para usar Azure AD para autenticar es registrar la aplicación en un inquilino de Azure AD. El registro de la aplicación le permite llamar a la [biblioteca de autenticación de Active Directory] [ aad_adal] (ADAL) de Azure desde el código. La ADAL proporciona una API para autenticar con Azure AD desde la aplicación. La aplicación debe registrarse tanto si tiene previsto usar la autenticación integrada como una entidad de servicio.

Al registrar la aplicación, facilita información acerca de la aplicación a Azure AD. Azure AD proporciona un identificador de aplicación (también denominado *Id. de cliente*) que se utiliza para asociar la aplicación con Azure AD en tiempo de ejecución. Para conocer más detalles acerca del identificador de la aplicación, consulte [Objetos de aplicación y de entidad de servicio de Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md).

Siga los pasos que aparecen en la sección [Incorporación de una aplicación](../active-directory/develop/quickstart-register-app.md) en [Integración de aplicaciones con Azure Active Directory][aad_integrate] para registrar la aplicación de Batch. Si registra la aplicación como una aplicación nativa, puede especificar cualquier URI válido para el **URI de redirección**. No es necesario que sea un punto de conexión real.

Una vez registrada la aplicación, verá el identificador de la aplicación:

![Registro de la aplicación de Batch con Azure AD](./media/batch-aad-auth/app-registration-data-plane.png)

Para más información sobre el registro de una aplicación, consulte [Escenarios de autenticación para Azure AD](../active-directory/develop/authentication-scenarios.md).

## <a name="get-the-tenant-id-for-your-active-directory"></a>Obtención del identificador de inquilino para Active Directory

El identificador de inquilino identifica al inquilino de Azure AD que proporciona servicios de autenticación a la aplicación. Para obtener el identificador de inquilino, siga estos pasos:

1. En Azure Portal, seleccione Active Directory.
2. Haga clic en **Propiedades**.
3. Copie el valor GUID proporcionado para el **identificador de directorio**. Este valor también se denomina identificador de inquilino.

![Copia del identificador del directorio](./media/batch-aad-auth/aad-directory-id.png)


## <a name="use-integrated-authentication"></a>Uso de autenticación integrada

Para autenticar mediante autenticación integrada, deberá conceder a la aplicación permisos para conectarse a la API del servicio de Batch. Este paso permite a la aplicación autenticar llamadas en la API del servicio de Batch con Azure AD.

Una vez que haya registrado la aplicación, siga estos pasos en Azure Portal para concederle acceso al servicio Batch:

1. En el panel de navegación izquierdo de Azure Portal, elija **Todos los servicios**. Haga clic en **Registros de aplicaciones**.
2. Busque el nombre de la aplicación en la lista de registros de aplicación:

    ![Buscar el nombre de la aplicación](./media/batch-aad-auth/search-app-registration.png)

3. Haga clic en la aplicación y en **Configuración**. En la sección **Acceso de API**, seleccione **Permisos necesarios**.
4. En la hoja **Permisos necesarios**, haga clic en el botón **Agregar**.
5. En **Seleccionar una API**, busque la API de Batch. Busque cada una de estas cadenas hasta que encuentre la API:
    1. **MicrosoftAzureBatch**.
    2. **Microsoft Azure Batch**. Los inquilinos más recientes de Azure AD pueden utilizar este nombre.
    3. **ddbf3205-c6bd-46ae-8127-60eb93363864** es el identificador de Batch API. 
6. Cuando la haya encontrado, selecciónela y haga clic en **Guardar**.
7. En **Seleccionar permisos**, active la casilla situada junto a **Access Azure Batch Service** (Acceder al servicio Azure Batch) y haga clic en **Seleccionar**.
8. Haga clic en **Done**(Listo).

La ventana **Permisos necesarios** muestra ahora que la aplicación de Azure AD tiene acceso tanto a ADAL como a la API del servicio Batch. La primera vez que registra la aplicación con Azure AD, se conceden permisos a ADAL automáticamente.

![Concesión de permisos de API](./media/batch-aad-auth/required-permissions-data-plane.png)

## <a name="use-a-service-principal"></a>Uso de una entidad de servicio 

Para autenticar una aplicación que se ejecute desatendida, utilice una entidad de servicio. Una vez que haya registrado la aplicación, siga estos pasos en Azure Portal para configurar una entidad de servicio:

1. Solicite una clave secreta para la aplicación.
2. Asigne un rol RBAC a la aplicación.

### <a name="request-a-secret-key-for-your-application"></a>Solicitud de una clave secreta para la aplicación

Cuando la aplicación se autentica con una entidad de servicio, envía el identificador de la aplicación y una clave secreta a Azure AD. Debe crear y copiar la clave secreta que usará desde el código.

Siga estos pasos en Azure Portal:

1. En el panel de navegación izquierdo de Azure Portal, elija **Todos los servicios**. Haga clic en **Registros de aplicaciones**.
2. Busque el nombre de la aplicación en la lista de registros de aplicaciones.
3. Haga clic en la aplicación y en **Configuración**. En la sección **Acceso de API**, seleccione **Claves**.
4. Para crear una clave, escriba una descripción de la clave. A continuación, seleccione una duración para la clave de uno o dos años. 
5. Haga clic en el botón **Guardar** para crear y mostrar la clave. Copie el valor de la clave en un lugar seguro, puesto que no podrá acceder a ella nuevamente después de abandonar la hoja. 

    ![Creación de una clave secreta](./media/batch-aad-auth/secret-key.png)

### <a name="assign-an-rbac-role-to-your-application"></a>Asignación de un rol RBAC a la aplicación

Para autenticarse con una entidad de servicio, debe asignar un rol RBAC a la aplicación. Siga estos pasos:

1. En Azure Portal, vaya a la cuenta de Batch utilizada por la aplicación.
2. En la hoja **Configuración** de la cuenta de Batch, seleccione **Control de acceso (IAM)** .
3. Haga clic en la pestaña **Asignaciones de roles**.
4. Haga clic en el botón **Agregar asignación de roles**. 
5. En el menú desplegable **Rol**, elija el rol _Colaborador_ o _Lector_ para la aplicación. Para más información sobre estos roles, consulte [Introducción al control de acceso basado en roles en Azure Portal](../role-based-access-control/overview.md).  
6. Escriba el nombre de la aplicación en el campo **Seleccionar**. Seleccione la aplicación de la lista y haga clic en **Guardar**.

La aplicación debe aparecer ahora en la configuración de control de acceso con un rol RBAC asignado. 

![Asignación de un rol RBAC a la aplicación](./media/batch-aad-auth/app-rbac-role.png)

### <a name="get-the-tenant-id-for-your-azure-active-directory"></a>Obtención del identificador de inquilino para Azure Active Directory

El identificador de inquilino identifica al inquilino de Azure AD que proporciona servicios de autenticación a la aplicación. Para obtener el identificador de inquilino, siga estos pasos:

1. En Azure Portal, seleccione Active Directory.
2. Haga clic en **Propiedades**.
3. Copie el valor GUID proporcionado para el **identificador de directorio**. Este valor también se denomina identificador de inquilino.

![Copia del identificador del directorio](./media/batch-aad-auth/aad-directory-id.png)


## <a name="code-examples"></a>Ejemplos de código

Los ejemplos de código de esta sección muestran cómo autenticar con Azure AD mediante la autenticación integrada y con una entidad de servicio. La mayoría de estos ejemplos de código usan .NET, pero los conceptos son similares para otros lenguajes.

> [!NOTE]
> Un token de autenticación de Azure AD expira después de una hora. Cuando se usa un objeto **BatchClient** de larga duración, se recomienda que recupere un token de ADAL en cada solicitud para asegurarse de que siempre tiene un token válido. 
>
>
> Para lograr esto en. NET, escriba un método que recupere el token de Azure AD y pase ese método a un objeto **BatchTokenCredentials** como un delegado. El método delegado se llama en cada solicitud al servicio Batch para asegurarse de que se proporciona un token válido. De forma predeterminada ADAL almacena en caché los tokens, por lo que solo se recuperará un nuevo token de Azure AD si es necesario. Para más información, consulte [Escenarios de autenticación para Azure AD][aad_auth_scenarios].
>
>

### <a name="code-example-using-azure-ad-integrated-authentication-with-batch-net"></a>Ejemplo de código: uso de la autenticación integrada de Azure AD con .NET de Batch

Para autenticar mediante la autenticación integrada de .NET de Batch, consulte el paquete de [.NET de Azure Batch](https://www.nuget.org/packages/Microsoft.Azure.Batch/) y el paquete de [ADAL](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).

Incluya las siguientes instrucciones `using` en el código:

```csharp
using Microsoft.Azure.Batch;
using Microsoft.Azure.Batch.Auth;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Haga referencia al punto de conexión de Azure AD en el código, incluido el identificador de inquilino. Para recuperar el identificador de inquilino, siga los pasos descritos en [Obtención del identificador de inquilino de Azure Active Directory](#get-the-tenant-id-for-your-active-directory):

```csharp
private const string AuthorityUri = "https://login.microsoftonline.com/<tenant-id>";
```

Haga referencia al punto de conexión del recurso para el servicio de Batch:

```csharp
private const string BatchResourceUri = "https://batch.core.windows.net/";
```

Haga referencia a la cuenta de Batch:

```csharp
private const string BatchAccountUrl = "https://myaccount.mylocation.batch.azure.com";
```

Especifique el identificador (Id. de cliente) de su aplicación. El identificador de la aplicación está disponible en el registro de la aplicación en Azure Portal:

```csharp
private const string ClientId = "<application-id>";
```

También puede copiar el URI de redirección especificado, en caso de que registrara la aplicación como aplicación nativa. El URI de redireccionamiento especificado en el código debe coincidir con el URI de redireccionamiento que proporcionó al registrar la aplicación:

```csharp
private const string RedirectUri = "http://mybatchdatasample";
```

Escriba un método de devolución de llamada para adquirir el token de autenticación de Azure AD. El método de devolución de llamada **GetAuthenticationTokenAsync** que se menciona aquí llama a ADAL para autenticar a un usuario que está interactuando con la aplicación. El método **AcquireTokenAsync** proporcionado por ADAL solicita al usuario sus credenciales y la aplicación se inicia una vez que el usuario las proporciona (a menos que ya tenga credenciales en caché):

```csharp
public static async Task<string> GetAuthenticationTokenAsync()
{
    var authContext = new AuthenticationContext(AuthorityUri);

    // Acquire the authentication token from Azure AD.
    var authResult = await authContext.AcquireTokenAsync(BatchResourceUri, 
                                                        ClientId, 
                                                        new Uri(RedirectUri), 
                                                        new PlatformParameters(PromptBehavior.Auto));

    return authResult.AccessToken;
}
```

Construya un objeto **BatchTokenCredentials** que tome el delegado como parámetro. Use esas credenciales para abrir un objeto **BatchClient**. Puede usar ese objeto **BatchClient** para las operaciones posteriores del servicio de Batch:

```csharp
public static async Task PerformBatchOperations()
{
    Func<Task<string>> tokenProvider = () => GetAuthenticationTokenAsync();

    using (var client = await BatchClient.OpenAsync(new BatchTokenCredentials(BatchAccountUrl, tokenProvider)))
    {
        await client.JobOperations.ListJobs().ToListAsync();
    }
}
```

### <a name="code-example-using-an-azure-ad-service-principal-with-batch-net"></a>Ejemplo de código: uso de una entidad de servicio de Azure AD con .NET de Batch

Para autenticar mediante una entidad de servicio de .NET de Batch, consulte el paquete de [.NET de Azure Batch](https://www.nuget.org/packages/Azure.Batch/) y el paquete de [ADAL](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).

Incluya las siguientes instrucciones `using` en el código:

```csharp
using Microsoft.Azure.Batch;
using Microsoft.Azure.Batch.Auth;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Haga referencia al punto de conexión de Azure AD en el código, incluido el identificador de inquilino. Cuando se usa una entidad de servicio, debe proporcionar un punto de conexión específico del inquilino. Para recuperar el identificador de inquilino, siga los pasos descritos en [Obtención del identificador de inquilino de Azure Active Directory](#get-the-tenant-id-for-your-active-directory):

```csharp
private const string AuthorityUri = "https://login.microsoftonline.com/<tenant-id>";
```

Haga referencia al punto de conexión del recurso para el servicio de Batch:  

```csharp
private const string BatchResourceUri = "https://batch.core.windows.net/";
```

Haga referencia a la cuenta de Batch:

```csharp
private const string BatchAccountUrl = "https://myaccount.mylocation.batch.azure.com";
```

Especifique el identificador (Id. de cliente) de su aplicación. El identificador de la aplicación está disponible en el registro de la aplicación en Azure Portal:

```csharp
private const string ClientId = "<application-id>";
```

Especifique la clave secreta que copió de Azure Portal:

```csharp
private const string ClientKey = "<secret-key>";
```

Escriba un método de devolución de llamada para adquirir el token de autenticación de Azure AD. El método de devolución de llamada **GetAuthenticationTokenAsync** que se menciona aquí llama a ADAL para una autenticación desatendida:

```csharp
public static async Task<string> GetAuthenticationTokenAsync()
{
    AuthenticationContext authContext = new AuthenticationContext(AuthorityUri);
    AuthenticationResult authResult = await authContext.AcquireTokenAsync(BatchResourceUri, new ClientCredential(ClientId, ClientKey));

    return authResult.AccessToken;
}
```

Construya un objeto **BatchTokenCredentials** que tome el delegado como parámetro. Use esas credenciales para abrir un objeto **BatchClient**. Después, puede usar ese objeto **BatchClient** para las operaciones posteriores del servicio Batch:

```csharp
public static async Task PerformBatchOperations()
{
    Func<Task<string>> tokenProvider = () => GetAuthenticationTokenAsync();

    using (var client = await BatchClient.OpenAsync(new BatchTokenCredentials(BatchAccountUrl, tokenProvider)))
    {
        await client.JobOperations.ListJobs().ToListAsync();
    }
}
```
### <a name="code-example-using-an-azure-ad-service-principal-with-batch-python"></a>Ejemplo de código: uso de una entidad de servicio de Azure AD con Python de Batch

Para la autenticación mediante una entidad de servicio de Python de Batch, instale los módulos [azure-batch](https://pypi.org/project/azure-batch/) y [azure-common](https://pypi.org/project/azure-common/) y cree una referencia a ellos.


```python
from azure.batch import BatchServiceClient
from azure.common.credentials import ServicePrincipalCredentials
```

Al usar una entidad de servicio, debe proporcionar el identificador de inquilino. Para recuperar el identificador de inquilino, siga los pasos descritos en [Obtención del identificador de inquilino de Azure Active Directory](#get-the-tenant-id-for-your-active-directory):

```python
TENANT_ID = "<tenant-id>";
```

Haga referencia al punto de conexión del recurso para el servicio de Batch:  

```python
RESOURCE = "https://batch.core.windows.net/";
```

Haga referencia a la cuenta de Batch:

```python
BATCH_ACCOUNT_URL = "https://myaccount.mylocation.batch.azure.com";
```

Especifique el identificador (Id. de cliente) de su aplicación. El identificador de la aplicación está disponible en el registro de la aplicación en Azure Portal:

```python
CLIENT_ID = "<application-id>";
```

Especifique la clave secreta que copió de Azure Portal:

```python
SECRET = "<secret-key>";
```

Cree un objeto **ServicePrincipalCredentials**:

```python
credentials = ServicePrincipalCredentials(
    client_id=CLIENT_ID,
    secret=SECRET,
    tenant=TENANT_ID,
    resource=RESOURCE
)
```

Utilice las credenciales del servicio principal para abrir un objeto **BatchServiceClient**. A continuación, use ese objeto **BatchServiceClient** para las operaciones posteriores del servicio Batch.

```python
    batch_client = BatchServiceClient(
    credentials,
    base_url=BATCH_ACCOUNT_URL
)
```

## <a name="next-steps"></a>Pasos siguientes

* Para más información acerca de Azure AD, consulte la [Documentación de Azure Active Directory](https://docs.microsoft.com/azure/active-directory/). Puede consultar los ejemplos detallados sobre el uso de ADAL que están disponibles en la biblioteca de [ejemplos de código de Azure](https://azure.microsoft.com/resources/samples/?service=active-directory).

* Para más información acerca de las entidades de servicio, consulte [Objetos de aplicación y de entidad de servicio de Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md). Para crear una entidad de servicio mediante Azure Portal, consulte [Uso del portal para crear una aplicación de Azure Active Directory y una entidad de servicio con acceso a los recursos](../active-directory/develop/howto-create-service-principal-portal.md). Las entidades de servicio también se pueden crear con PowerShell o la CLI de Azure.

* Para autenticar aplicaciones de administración de Batch con Azure AD, consulte [Autenticación de soluciones de administración de Batch con Active Directory](batch-aad-auth-management.md).

* Para obtener un ejemplo de Python de cómo crear un cliente de Batch que se autentica utilizando un token de Azure AD, consulte el ejemplo [Deploying Azure Batch Custom Image with a Python Script](https://github.com/azurebigcompute/recipes/blob/master/Azure%20Batch/CustomImages/CustomImagePython.md) (Implementación de una imagen personalizada de Azure Batch con un script de Python).

[aad_about]:../active-directory/fundamentals/active-directory-whatis.md "¿Qué es Azure Active Directory?"
[aad_adal]: ../active-directory/active-directory-authentication-libraries.md
[aad_auth_scenarios]: ../active-directory/active-directory-authentication-scenarios.md "Escenarios de autenticación para Azure AD"
[aad_integrate]: ../active-directory/active-directory-integrating-applications.md "Integración de aplicaciones con Azure Active Directory"
[azure_portal]: https://portal.azure.com
