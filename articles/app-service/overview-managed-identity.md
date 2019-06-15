---
title: 'Introducción a las identidades administradas: Azure App Service | Microsoft Docs'
description: Guía de configuración y referencia conceptual para identidades administradas en Azure App Service y Azure Functions
services: app-service
author: mattchenderson
manager: cfowler
editor: ''
ms.service: app-service
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 11/20/2018
ms.author: mahender
ms.openlocfilehash: 0942d5ba7b31ddb2c0dec5fe979f1331d1bf3bfd
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "66136969"
---
# <a name="how-to-use-managed-identities-for-app-service-and-azure-functions"></a>Cómo usar identidades administradas para App Service y Azure Functions

> [!NOTE] 
> La compatibilidad de identidades administradas con App Service en Linux y Web App for Containers se encuentra actualmente en versión preliminar.

> [!Important] 
> Las identidades administradas de App Service y Azure Functions no se comportarán según lo esperado si la aplicación se migra entre suscripciones e inquilinos. La aplicación necesitará obtener una nueva identidad, lo que se puede hacer deshabitando y volviendo a habilitar la función. Consulte [Eliminación de una identidad](#remove) a continuación. Los recursos de nivel inferior también necesitarán tener directivas de acceso actualizadas para utilizar la nueva identidad.

En este tema se muestra cómo crear una identidad administrada para las aplicaciones App Service y Azure Functions y cómo usarla para acceder a otros recursos. Una identidad administrada de Azure Active Directory permite a la aplicación acceder fácilmente a otros recursos protegidos por AAD, como Azure Key Vault. La identidad está administrada por la plataforma Azure y no requiere que aprovisione o rote los secretos. Para más información sobre las identidades administradas en AAD, consulte [Identidades administradas para recursos de Azure](../active-directory/managed-identities-azure-resources/overview.md).

La aplicación puede tener dos tipos de identidades: 
- Una **identidad asignada por el sistema** está asociada a la aplicación y se elimina si se elimina la aplicación. Una aplicación solo puede tener una identidad asignada por el sistema. La compatibilidad con identidades asignadas por el sistema está disponible con caracter general para las aplicaciones de Windows. 
- Una **identidad asignada por el usuario** es un recurso de Azure independiente que puede asignarse a la aplicación. Una aplicación puede tener varias identidades asignadas por el usuario. La compatibilidad con identidades asignadas por el usuario está en versión preliminar para todos los tipos de aplicación.

## <a name="adding-a-system-assigned-identity"></a>Adición de una identidad asignada por el sistema

Para crear una aplicación con una identidad asignada por el sistema se requiere la configuración de una propiedad adicional en la aplicación.

### <a name="using-the-azure-portal"></a>Uso de Azure Portal

Para configurar una identidad administrada en el portal, primero creará una aplicación como lo hace normalmente y, a continuación, habilitará la característica.

1. Cree una aplicación en el portal como lo haría normalmente. Navegue hasta el portal.

2. Si utiliza una aplicación de función, vaya a **Características de la plataforma**. Para otros tipos de aplicación, desplácese hacia abajo hasta el grupo **Configuración** en el panel de navegación izquierdo.

3. Seleccione **Identidad administrada**.

4. En la pestaña **Asignado por el sistema**, cambie **Estado** a **Activado**. Haga clic en **Save**(Guardar).

![Identidad administrada en App Service](media/app-service-managed-service-identity/msi-blade-system.png)

### <a name="using-the-azure-cli"></a>Uso de la CLI de Azure

Para configurar una identidad administrada mediante la CLI de Azure, deberá usar el comando `az webapp identity assign` en una aplicación existente. Tiene tres opciones para ejecutar los ejemplos de esta sección:

- Usar [Azure Cloud Shell](../cloud-shell/overview.md) desde Azure Portal.
- Use Azure Cloud Shell integrado mediante el botón "Pruébelo", situado en la esquina superior derecha de cada bloque de código.
- [Instale la versión más reciente de la CLI de Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.31 o posterior) si prefiere usar una consola de CLI local. 

Los siguientes pasos le guiarán por la creación de una aplicación web y la asignación a la misma de una identidad mediante la CLI:

1. Si usa la CLI de Azure en una consola local, lo primero que debe hacer es iniciar sesión en Azure mediante el [inicio de sesión de az](/cli/azure/reference-index#az-login). Use una cuenta asociada a la suscripción de Azure en la que desea implementar la aplicación:

    ```azurecli-interactive
    az login
    ```
2. Cree una aplicación web mediante la CLI. Para más ejemplos de cómo utilizar la CLI con App Service, consulte los [ejemplos de la CLI de App Service](../app-service/samples-cli.md):

    ```azurecli-interactive
    az group create --name myResourceGroup --location westus
    az appservice plan create --name myPlan --resource-group myResourceGroup --sku S1
    az webapp create --name myApp --resource-group myResourceGroup --plan myPlan
    ```

3. Ejecute el comando `identity assign` para crear la identidad de esta aplicación:

    ```azurecli-interactive
    az webapp identity assign --name myApp --resource-group myResourceGroup
    ```

### <a name="using-azure-powershell"></a>Uso de Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Los siguientes pasos le guiarán por la creación de una aplicación web y la asignación a la misma de una identidad mediante Azure PowerShell:

1. Si es necesario, instale Azure PowerShell con la instrucción que se encuentra en la [Guía de Azure PowerShell](/powershell/azure/overview) y, luego, ejecute `Login-AzAccount` para crear una conexión con Azure.

2. Cree una aplicación web mediante Azure PowerShell. Para ver más ejemplos de cómo utilizar Azure PowerShell con App Service, consulte los [ejemplos de PowerShell de App Service](../app-service/samples-powershell.md):

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name myResourceGroup -Location $location
    
    # Create an App Service plan in Free tier.
    New-AzAppServicePlan -Name $webappname -Location $location -ResourceGroupName myResourceGroup -Tier Free
    
    # Create a web app.
    New-AzWebApp -Name $webappname -Location $location -AppServicePlan $webappname -ResourceGroupName myResourceGroup
    ```

3. Ejecute el comando `Set-AzWebApp -AssignIdentity` para crear la identidad de esta aplicación:

    ```azurepowershell-interactive
    Set-AzWebApp -AssignIdentity $true -Name $webappname -ResourceGroupName myResourceGroup 
    ```

### <a name="using-an-azure-resource-manager-template"></a>Uso de una plantilla de Azure Resource Manager

Se puede utilizar una plantilla de Azure Resource Manager para automatizar la implementación de los recursos de Azure. Para obtener más información acerca de la implementación en App Service y Functions, vea [Automating resource deployment in App Service](../app-service/deploy-complex-application-predictably.md) (Automatización de la implementación de recursos en App Service) y [Automatización de la implementación de recursos para una aplicación de función en Azure Functions](../azure-functions/functions-infrastructure-as-code.md).

Se puede crear cualquier recurso de tipo `Microsoft.Web/sites` con una identidad mediante la inclusión de la siguiente propiedad en la definición de recursos:
```json
"identity": {
    "type": "SystemAssigned"
}    
```

> [!NOTE] 
> Una aplicación puede tener identidades asignadas por el sistema y asignadas por el usuario al mismo tiempo. En este caso, la propiedad `type` sería `SystemAssigned,UserAssigned`.

Al agregar el tipo asignado por el sistema se indica a Azure que debe crear y administrar la identidad para la aplicación.

Por ejemplo, una aplicación web podría tener el aspecto siguiente:
```json
{
    "apiVersion": "2016-08-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('appName')]",
    "location": "[resourceGroup().location]",
    "identity": {
        "type": "SystemAssigned"
    },
    "properties": {
        "name": "[variables('appName')]",
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "hostingEnvironment": "",
        "clientAffinityEnabled": false,
        "alwaysOn": true
    },
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"
    ]
}
```

Cuando se crea el sitio, tiene las siguientes propiedades adicionales:
```json
"identity": {
    "type": "SystemAssigned",
    "tenantId": "<TENANTID>",
    "principalId": "<PRINCIPALID>"
}
```

Donde `<TENANTID>` y `<PRINCIPALID>` se reemplazan por GUID. La propiedad tenantId identifica a qué inquilino AAD pertenece la identidad. El valor principalId es un identificador único para la nueva identidad de la aplicación. En AAD, la entidad de servicio tiene el mismo nombre que asignó a la instancia de App Service o Azure Functions.


## <a name="adding-a-user-assigned-identity-preview"></a>Adición de una identidad asignada por el usuario (versión preliminar)

> [!NOTE] 
> Las identidades asignadas por el usuario están actualmente en versión preliminar. Todavía no se admiten las nubes soberanas.

La creación de una aplicación con una identidad asignada por el usuario requiere que se cree la identidad y luego se agregue su identificador de recurso a la configuración de la aplicación.

### <a name="using-the-azure-portal"></a>Uso de Azure Portal

> [!NOTE] 
> Esta experiencia del portal se está implementando y puede que no esté todavía disponible en todas las regiones.

En primer lugar, tendrá que crear un recurso de identidad asignada por el usuario.

1. Cree un recurso de identidad administrada asignada por el usuario según [estas instrucciones](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity).

2. Cree una aplicación en el portal como lo haría normalmente. Navegue hasta el portal.

3. Si utiliza una aplicación de función, vaya a **Características de la plataforma**. Para otros tipos de aplicación, desplácese hacia abajo hasta el grupo **Configuración** en el panel de navegación izquierdo.

4. Seleccione **Identidad administrada**.

5. En la pestaña **Usuario asignado (versión preliminar)** , haga clic **Agregar**.

6. Busque la identidad que creó anteriormente y selecciónela. Haga clic en **Agregar**.

![Identidad administrada en App Service](media/app-service-managed-service-identity/msi-blade-user.png)

### <a name="using-an-azure-resource-manager-template"></a>Uso de una plantilla de Azure Resource Manager

Se puede utilizar una plantilla de Azure Resource Manager para automatizar la implementación de los recursos de Azure. Para obtener más información acerca de la implementación en App Service y Functions, vea [Automating resource deployment in App Service](../app-service/deploy-complex-application-predictably.md) (Automatización de la implementación de recursos en App Service) y [Automatización de la implementación de recursos para una aplicación de función en Azure Functions](../azure-functions/functions-infrastructure-as-code.md).

Se puede crear cualquier recurso de tipo `Microsoft.Web/sites` con una identidad incluyendo el siguiente bloque en la definición del recurso, que reemplaza `<RESOURCEID>` por el identificador de recurso de la identidad deseada:
```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": {}
    }
}    
```

> [!NOTE] 
> Una aplicación puede tener identidades asignadas por el sistema y asignadas por el usuario al mismo tiempo. En este caso, la propiedad `type` sería `SystemAssigned,UserAssigned`.

Al agregar el tipo asignado por el usuario se indica a Azure que debe crear y administrar la identidad para la aplicación.

Por ejemplo, una aplicación web podría tener el aspecto siguiente:
```json
{
    "apiVersion": "2016-08-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('appName')]",
    "location": "[resourceGroup().location]",
    "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
            "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]": {}
        }
    },
    "properties": {
        "name": "[variables('appName')]",
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "hostingEnvironment": "",
        "clientAffinityEnabled": false,
        "alwaysOn": true
    },
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]"
    ]
}
```

Cuando se crea el sitio, tiene las siguientes propiedades adicionales:
```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": {
            "principalId": "<PRINCIPALID>",
            "clientId": "<CLIENTID>"
        }
    }
}
```

Donde `<PRINCIPALID>` y `<CLIENTID>` se reemplazan por GUID. El valor principalId es un identificador único de la identidad que se usa para la administración de AAD. El valor clientId es un identificador único de la nueva identidad de la aplicación que se usa para especificar qué identidad utilizar durante las llamadas de runtime.


## <a name="obtaining-tokens-for-azure-resources"></a>Obtención de tokens para recursos de Azure

Una aplicación puede utilizar su identidad para obtener tokens para otros recursos protegidos por AAD, como Azure Key Vault. Estos tokens representan a la aplicación que accede al recurso, y no a un usuario específico de la aplicación. 

> [!IMPORTANT]
> Es posible que tenga que configurar el recurso de destino para permitir el acceso desde la aplicación. Por ejemplo, si se solicita un token a Key Vault, debe asegurarse de que ha agregado una directiva de acceso que incluya la identidad de la aplicación. De lo contrario, las llamadas a Key Vault se rechazarán, incluso si incluyen el token. Para más información sobre los recursos que admiten tokens de Azure Active Directory, consulte [Servicios de Azure que admiten autenticación de Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

Hay un protocolo de REST sencillo para obtener un token en App Service y Azure Functions. Para las aplicaciones de .NET, la biblioteca Microsoft.Azure.Services.AppAuthentication proporciona una abstracción sobre este protocolo y admite una experiencia de desarrollo local.

### <a name="asal"></a>Uso de la biblioteca Microsoft.Azure.Services.AppAuthentication para .NET

En el caso de aplicaciones y funciones de .NET, la manera más sencilla de trabajar con una identidad administrada es usar el paquete Microsoft.Azure.Services.AppAuthentication. Esta biblioteca también le permite probar el código localmente en la máquina de desarrollo, con su cuenta de usuario de Visual Studio, la [CLI de Azure](/cli/azure) o la autenticación integrada de Active Directory. Para obtener más información sobre las opciones de desarrollo local con esta biblioteca, consulte la [referencia de Microsoft.Azure.Services.AppAuthentication]. En esta sección se muestra cómo empezar a usar la biblioteca en su código.

1. Agregue referencias a los paquetes de NuGet [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) y a otros paquetes necesarios para la aplicación. El ejemplo siguiente también usa [Microsoft.Azure.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault).

2. Agregue el código siguiente a la aplicación, modificándolo para que tenga como destino el recurso correcto. En este ejemplo se muestran dos formas de trabajar con Azure Key Vault:

```csharp
using Microsoft.Azure.Services.AppAuthentication;
using Microsoft.Azure.KeyVault;
// ...
var azureServiceTokenProvider = new AzureServiceTokenProvider();
string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://vault.azure.net");
// OR
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
```

Para obtener más información sobre Microsoft.Azure.Services.AppAuthentication y las operaciones que expone, consulte la [referencia de Microsoft.Azure.Services.AppAuthentication] y el [ejemplo de .NET sobre App Service y KeyVault con MSI](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet).

### <a name="using-the-rest-protocol"></a>Uso del protocolo de REST

Una aplicación con una identidad administrada tiene dos variables de entorno definidas:

- MSI_ENDPOINT: dirección URL del servicio de token local.
- MSI_SECRET: encabezado que se usa para ayudar a mitigar los ataques de falsificación de solicitudes del servidor (SSRF). La plataforma se encarga de cambiarlo.

La variable **MSI_ENDPOINT** es una dirección URL local desde la que la aplicación puede solicitar tokens. Para obtener un token para un recurso, realice una solicitud HTTP GET para este punto de conexión, incluyendo los parámetros siguientes:

> |Nombre de parámetro|En|DESCRIPCIÓN|
> |-----|-----|-----|
> |resource|Consultar|El URI del recurso del recurso AAD para el que se debe obtener un token. Este podría ser uno de los [servicios de Azure que admiten la autenticación de Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) o cualquier otro URI de recurso.|
> |api-version|Consultar|La versión de la API de token que se usará. Actualmente, la única versión admitida es "2017-09-01".|
> |secret|Encabezado|El valor de la variable de entorno MSI_SECRET. Este encabezado se utiliza para ayudar a mitigar los ataques de falsificación de solicitudes del servidor (SSRF).|
> |clientid|Consultar|(Opcional) El identificador de la identidad asignada por el usuario que se usará. Si se omite, se usa la identidad asignada por el sistema.|

Una respuesta 200 OK incluye un cuerpo JSON con las siguientes propiedades:

> |Nombre de propiedad|DESCRIPCIÓN|
> |-------------|----------|
> |access_token|El token de acceso solicitado. El servicio web de llamada puede usar este token para autenticarse en el servicio web de recepción.|
> |expires_on|La hora a la que expira el token de acceso. La fecha se representa como el número de segundos desde 1970-01-01T0:0:0Z UTC hasta la fecha de expiración. Este valor se utiliza para determinar la duración de los tokens almacenados en caché.|
> |resource|El URI del identificador de la aplicación del servicio web de recepción.|
> |token_type|Indica el valor de tipo de token. El único tipo que admite Azure AD es portador. Para más información sobre los tokens de portador, consulte [The OAuth2.0 Authorization Framework: Bearer Token Usage (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt) (Marco de autorización de OAuth2.0: uso del token de portador [RFC 6750]).|

Esta respuesta es la misma que la [respuesta para la solicitud de token de acceso de servicio a servicio de AAD](../active-directory/develop/v1-oauth2-client-creds-grant-flow.md#service-to-service-access-token-response).

> [!NOTE]
> Las variables de entorno se configuran cuando el proceso se inicia por primera vez, por lo que después de habilitar una identidad administrada para la aplicación puede que necesite reiniciar la aplicación, o implementar de nuevo su código, antes de que `MSI_ENDPOINT` y `MSI_SECRET` estén disponibles en el código.

### <a name="rest-protocol-examples"></a>Ejemplos de protocolo de REST

Una solicitud de ejemplo puede tener el siguiente aspecto:

```
GET /MSI/token?resource=https://vault.azure.net&api-version=2017-09-01 HTTP/1.1
Host: localhost:4141
Secret: 853b9a84-5bfa-4b22-a3f3-0b9a43d9ad8a
```

Y una respuesta de ejemplo puede tener el siguiente aspecto:

```
HTTP/1.1 200 OK
Content-Type: application/json

{
    "access_token": "eyJ0eXAi…",
    "expires_on": "09/14/2017 00:00:00 PM +00:00",
    "resource": "https://vault.azure.net",
    "token_type": "Bearer"
}
```

### <a name="code-examples"></a>Ejemplos de código

<a name="token-csharp"></a>Para realizar esta solicitud en C#:

```csharp
public static async Task<HttpResponseMessage> GetToken(string resource, string apiversion)  {
    HttpClient client = new HttpClient();
    client.DefaultRequestHeaders.Add("Secret", Environment.GetEnvironmentVariable("MSI_SECRET"));
    return await client.GetAsync(String.Format("{0}/?resource={1}&api-version={2}", Environment.GetEnvironmentVariable("MSI_ENDPOINT"), resource, apiversion));
}
```

> [!TIP]
> Para los lenguajes. NET, también puede usar [Microsoft.Azure.Services.AppAuthentication](#asal) en lugar de crear esta solicitud personalmente.

<a name="token-js"></a>En Node.js:

```javascript
const rp = require('request-promise');
const getToken = function(resource, apiver, cb) {
    let options = {
        uri: `${process.env["MSI_ENDPOINT"]}/?resource=${resource}&api-version=${apiver}`,
        headers: {
            'Secret': process.env["MSI_SECRET"]
        }
    };
    rp(options)
        .then(cb);
}
```

<a name="token-powershell"></a>En PowerShell:

```powershell
$apiVersion = "2017-09-01"
$resourceURI = "https://<AAD-resource-URI-for-resource-to-obtain-token>"
$tokenAuthURI = $env:MSI_ENDPOINT + "?resource=$resourceURI&api-version=$apiVersion"
$tokenResponse = Invoke-RestMethod -Method Get -Headers @{"Secret"="$env:MSI_SECRET"} -Uri $tokenAuthURI
$accessToken = $tokenResponse.access_token
```

## <a name="remove"></a>Eliminación una identidad

Para quitar una identidad asignada por el sistema, deshabilite la función mediante el portal, PowerShell o la CLI de la misma forma en que se creó. Las identidades asignadas por el usuario se pueden quitar individualmente. Para quitar todas las identidades, en el protocolo de plantilla REST/ARM, esto se hace ajustando el tipo a "Ninguno":

```json
"identity": {
    "type": "None"
}
```

Al quitar una identidad asignada por el sistema de esta manera también se eliminará de AAD. Las identidades asignadas por el sistema también se quitan automáticamente de AAD cuando se elimina el recurso de la aplicación.

> [!NOTE]
> También hay una configuración de aplicación que se puede establecer, WEBSITE_DISABLE_MSI, que simplemente deshabilita el servicio de token local. Sin embargo, deja la identidad en su lugar, y las herramientas seguirán mostrando la identidad administrada como "activada" o "habilitada". Como resultado, no se recomienda el uso de esta configuración.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Acceso seguro a SQL Database mediante una identidad administrada](app-service-web-tutorial-connect-msi.md)

[Referencia de Microsoft.Azure.Services.AppAuthentication]: https://go.microsoft.com/fwlink/p/?linkid=862452
