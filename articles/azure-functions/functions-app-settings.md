---
title: Referencia de configuración de aplicación para Azure Functions
description: Documentación de referencia para la configuración de la aplicación de Azure Functions o de variables de entorno.
ms.topic: conceptual
ms.date: 09/22/2018
ms.openlocfilehash: 1c7f5f9f8f6f198c5fe74baa613306732fa9b55b
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/10/2019
ms.locfileid: "74977274"
---
# <a name="app-settings-reference-for-azure-functions"></a>Referencia de configuración de aplicación para Azure Functions

La configuración de la aplicación en una aplicación de función contiene opciones de configuración global que afectan a todas las funciones de dicha aplicación. Cuando se ejecuta localmente, se accede a esta configuración como [variables de entorno](functions-run-local.md#local-settings-file) locales. Este artículo incluye una lista de las opciones de configuración disponibles en las aplicaciones de funciones.

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

Hay otras opciones de configuración global en el archivo [host.json](functions-host-json.md) y en [local.settings.json](functions-run-local.md#local-settings-file).

## <a name="appinsights_instrumentationkey"></a>APPINSIGHTS_INSTRUMENTATIONKEY

Clave de instrumentación de Application Insights si usa dicho servicio. Consulte [Supervisar Azure Functions](functions-monitoring.md).

|Clave|Valor de ejemplo|
|---|------------|
|APPINSIGHTS_INSTRUMENTATIONKEY|5dbdd5e9-af77-484b-9032-64f83bb83bb|

## <a name="azure_functions_environment"></a>AZURE_FUNCTIONS_ENVIRONMENT

En la versión 2.x y posteriores del entorno en tiempo de ejecución de Functions, se configura el comportamiento de la aplicación en función del entorno en tiempo de ejecución. Este valor se [lee durante la inicialización](https://github.com/Azure/azure-functions-host/blob/dev/src/WebJobs.Script.WebHost/Program.cs#L43). Puede establecer `AZURE_FUNCTIONS_ENVIRONMENT` en cualquier valor, pero se admiten [tres valores](/dotnet/api/microsoft.aspnetcore.hosting.environmentname): [desarrollo](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.development), [almacenamiento provisional](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.staging) y [producción](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.production). Cuando no se ha establecido `AZURE_FUNCTIONS_ENVIRONMENT`, el valor predeterminado es `Development` en un entorno local, y `Production` en Azure. Esta configuración debe usarse en lugar de `ASPNETCORE_ENVIRONMENT` para establecer el entorno de ejecución. 

## <a name="azurewebjobsdashboard"></a>AzureWebJobsDashboard

Cadena de conexión de la cuenta de almacenamiento opcional para almacenar los registros y mostrarlos en la pestaña **Monitor** del portal. La cuenta de almacenamiento debe ser una de uso general que admite blobs, colas y tablas. Consulte [Almacenamiento de la cuenta](functions-infrastructure-as-code.md#storage-account) y [Requisitos de almacenamiento de la cuenta](functions-create-function-app-portal.md#storage-account-requirements).

|Clave|Valor de ejemplo|
|---|------------|
|AzureWebJobsDashboard|DefaultEndpointsProtocol=https;AccountName=[nombre];AccountKey=[clave]|

> [!TIP]
> Por motivos de rendimiento y experiencia, se recomienda utilizar APPINSIGHTS_INSTRUMENTATIONKEY y App Insights para la supervisión, en lugar de AzureWebJobsDashboard.

## <a name="azurewebjobsdisablehomepage"></a>AzureWebJobsDisableHomepage

`true` significa deshabilitar la página de aterrizaje predeterminada que se muestra para la dirección URL raíz de una aplicación de función. El valor predeterminado es `false`.

|Clave|Valor de ejemplo|
|---|------------|
|AzureWebJobsDisableHomepage|true|

Cuando esta configuración de la aplicación se omite o se establece en `false`, una página similar a la del siguiente ejemplo se muestra en respuesta a la dirección URL `<functionappname>.azurewebsites.net`.

![Página de aterrizaje de la aplicación de función](media/functions-app-settings/function-app-landing-page.png)

## <a name="azurewebjobsdotnetreleasecompilation"></a>AzureWebJobsDotNetReleaseCompilation

`true` significa utilizar el modo de versión cuando se compila código .NET y `false` significa utilizar el modo de depuración. El valor predeterminado es `true`.

|Clave|Valor de ejemplo|
|---|------------|
|AzureWebJobsDotNetReleaseCompilation|true|

## <a name="azurewebjobsfeatureflags"></a>AzureWebJobsFeatureFlags

Una lista delimitada por comas de características de la versión beta que se van a habilitar. Las características de la versión beta habilitadas por estas marcas no están listas para la producción, pero se pueden habilitar para su uso experimental antes de su publicación.

|Clave|Valor de ejemplo|
|---|------------|
|AzureWebJobsFeatureFlags|feature1,feature2|

## <a name="azurewebjobssecretstoragetype"></a>AzureWebJobsSecretStorageType

Especifica el repositorio o el proveedor que se utilizará para el almacenamiento de claves. Actualmente, los repositorios admitidos son almacenamiento de blobs ("Blob") y sistema de archivos local ("Files"). El valor predeterminado es blob en la versión 2 y sistema de archivos en la versión 1.

|Clave|Valor de ejemplo|
|---|------------|
|AzureWebJobsSecretStorageType|Archivos|

## <a name="azurewebjobsstorage"></a>AzureWebJobsStorage

El tiempo de ejecución de Azure Functions usa esta cadena de conexión de la cuenta de almacenamiento para todas las funciones, salvo en el caso de las desencadenadas de HTTP. La cuenta de almacenamiento debe ser una de uso general que admite blobs, colas y tablas. Consulte [Almacenamiento de la cuenta](functions-infrastructure-as-code.md#storage-account) y [Requisitos de almacenamiento de la cuenta](functions-create-function-app-portal.md#storage-account-requirements).

|Clave|Valor de ejemplo|
|---|------------|
|AzureWebJobsStorage|DefaultEndpointsProtocol=https;AccountName=[nombre];AccountKey=[clave]|

## <a name="azurewebjobs_typescriptpath"></a>AzureWebJobs_TypeScriptPath

Ruta de acceso al compilador que se usa para TypeScript. Le permite reemplazar el valor predeterminado si lo necesita.

|Clave|Valor de ejemplo|
|---|------------|
|AzureWebJobs_TypeScriptPath|%HOME%\typescript|

## <a name="function_app_edit_mode"></a>MODO DE\_EDICIÓN DE\_LA APLICACIÓN DE\_FUNCIÓN

Determina si está habilitada la edición en Azure Portal. Los valores válidos son "readwrite" y "readonly".

|Clave|Valor de ejemplo|
|---|------------|
|MODO DE\_EDICIÓN DE\_LA APLICACIÓN DE\_FUNCIÓN|readonly|

## <a name="functions_extension_version"></a>VERSIÓN DE LA \_EXTENSIÓN\_ DE FUNCTIONS

La versión del tiempo de ejecución de Functions para usar en esta aplicación de función. Una tilde con la versión principal significa utilizar la versión más reciente de esa versión principal (por ejemplo, "~2"). Cuando haya disponibles versiones nuevas de la misma versión principal, se instalarán automáticamente en la aplicación de función. Para anclar la aplicación a una versión específica, use el número completo de la versión (por ejemplo, "2.0.12345"). El valor predeterminado es "~2". Un valor de `~1` ancla la aplicación a la versión 1.x del tiempo de ejecución.

|Clave|Valor de ejemplo|
|---|------------|
|VERSIÓN DE LA \_EXTENSIÓN\_ DE FUNCTIONS|~2|

## <a name="functions_worker_process_count"></a>FUNCTIONS\_WORKER\_PROCESS\_COUNT

Especifica el número máximo de procesos de trabajo de lenguaje, con un valor predeterminado de `1`. El valor máximo permitido es `10`. Las invocaciones de función se distribuyen uniformemente entre los procesos de trabajo de lenguaje. Los procesos de trabajo de lenguaje se generan cada diez segundos hasta que se alcanza el número establecido por FUNCTIONS\_WORKER\_PROCESS\_COUNT. El uso de varios procesos de trabajo de lenguaje no es el mismo que el [escalado](functions-scale.md). Considere la posibilidad de usar esta opción cuando la carga de trabajo tenga una combinación de invocaciones vinculadas a la E/S y vinculadas a la CPU. Esta configuración se aplica a todos los lenguajes que no son .NET.

|Clave|Valor de ejemplo|
|---|------------|
|FUNCTIONS\_WORKER\_PROCESS\_COUNT|2|


## <a name="functions_worker_runtime"></a>FUNCTIONS\_WORKER\_RUNTIME

Tiempo de ejecución del trabajo del lenguaje que se cargará en la aplicación de función.  Se corresponderá con el lenguaje usado en la aplicación (por ejemplo, "dotnet"). Para las funciones en varios lenguajes deberá publicarlas en varias aplicaciones, cada una con un valor de tiempo de ejecución de trabajo correspondiente.  Los valores válidos son `dotnet` (C#/F#), `node` (JavaScript/TypeScript), `java` (Java), `powershell` (PowerShell) y `python` (Python).

|Clave|Valor de ejemplo|
|---|------------|
|FUNCTIONS\_WORKER\_RUNTIME|dotnet|

## <a name="website_contentazurefileconnectionstring"></a>WEBSITE_CONTENTAZUREFILECONNECTIONSTRING

Solo para los planes de consumo y Premium. Cadena de conexión para la cuenta de almacenamiento donde se almacenan el código de aplicación de función y la configuración. Consulte [Creación de una aplicación de función](functions-infrastructure-as-code.md#create-a-function-app).

|Clave|Valor de ejemplo|
|---|------------|
|WEBSITE_CONTENTAZUREFILECONNECTIONSTRING|DefaultEndpointsProtocol=https;AccountName=[nombre];AccountKey=[clave]|

## <a name="website_contentshare"></a>WEBSITE\_CONTENTSHARE

Solo para los planes de consumo y Premium. Ruta de acceso del archivo para el código de la aplicación de función y la configuración. Se usa con WEBSITE_CONTENTAZUREFILECONNECTIONSTRING. El valor predeterminado es una cadena única que comienza con el nombre de aplicación de función. Consulte [Creación de una aplicación de función](functions-infrastructure-as-code.md#create-a-function-app).

|Clave|Valor de ejemplo|
|---|------------|
|WEBSITE_CONTENTSHARE|functionapp091999e2|

## <a name="website_max_dynamic_application_scale_out"></a>ESCALABILIDAD\_HORIZONTAL\_MÁXIMA\_DE LA\_APLICACIÓN\_DINÁMICA

Número máximo de instancias al que se puede escalar horizontalmente la aplicación de función. El valor predeterminado es sin límite.

> [!NOTE]
> Este ajuste es una característica en vista previa, que solo es confiable si se establece en un valor <= 5

|Clave|Valor de ejemplo|
|---|------------|
|ESCALABILIDAD\_HORIZONTAL\_MÁXIMA\_DE LA\_APLICACIÓN\_DINÁMICA|5|

## <a name="website_node_default_version"></a>DEFAULT_VERSION\_DEL NODO\_DEL SITIO WEB

_Solo Windows._  
Establece la versión de Node.js que se usará al ejecutar la aplicación de funciones en Windows. Debe usar una tilde (~) para que el entorno de ejecución use la última versión disponible de la versión principal de destino. Por ejemplo, cuando se establece en `~10`, se usa la versión más reciente de Node.js 10. Cuando se especifica una versión principal con una tilde, no es necesario actualizar manualmente la versión secundaria. 

|Clave|Valor de ejemplo|
|---|------------|
|DEFAULT_VERSION\_DEL NODO\_DEL SITIO WEB|~10|

## <a name="website_run_from_package"></a>WEBSITE\_RUN\_FROM\_PACKAGE

Permite que la aplicación de función se ejecute desde un archivo de paquete montado.

|Clave|Valor de ejemplo|
|---|------------|
|WEBSITE\_RUN\_FROM\_PACKAGE|1|

Los valores válidos son una dirección URL que se resuelve en la ubicación de un archivo de paquete de implementación o `1`. Cuando se establece en `1`, el paquete debe estar en la carpeta `d:\home\data\SitePackages`. Cuando se usa la implementación de ZIP con esta configuración, el paquete se carga automáticamente en esta ubicación. En la versión preliminar, este ajuste se denomina `WEBSITE_RUN_FROM_ZIP`. Para más información, vea [Run your functions from a package file](run-functions-from-deployment-package.md) (Ejecución de Azure Functions desde un archivo de paquete).

## <a name="azure_function_proxy_disable_local_call"></a>AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL

De forma predeterminada, las proxies de Functions usarán accesos directos para enviar llamadas de API desde servidores proxy directamente a funciones en la misma instancia de Function App, en lugar de crear una nueva solicitud HTTP. Esta configuración le permite deshabilitar este comportamiento.

|Clave|Valor|DESCRIPCIÓN|
|-|-|-|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|true|Las llamadas con una dirección URL de back-end que señala a una instancia de Function App local ya no se enviarán directamente a la función y, en su lugar, se dirigirán al front-end HTTP para la instancia de Function App.|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|false|Este es el valor predeterminado. Las llamadas con una dirección URL de back-end que señale a una instancia de Function App local se reenviarán directamente a esa función.|


## <a name="azure_function_proxy_backend_url_decode_slashes"></a>AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES

Esta opción de configuración controla si se descodifica %2F como barras diagonales en los parámetros de ruta cuando se insertan en la URL del back-end. 

|Clave|Valor|DESCRIPCIÓN|
|-|-|-|
|AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES|true|Los parámetros de ruta con barras diagonales codificadas los descodificarán. `example.com/api%2ftest` se convertirá en `example.com/api/test`|
|AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES|false|Este es el comportamiento predeterminado. Todos los parámetros de ruta se pasarán sin cambios|

### <a name="example"></a>Ejemplo

Este es un ejemplo de proxies.json en una aplicación de función en la dirección URL myfunction.com

```JSON
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "root": {
            "matchCondition": {
                "route": "/{*all}"
            },
            "backendUri": "example.com/{all}"
        }
    }
}
```
|Descodificación de URL|Entrada|Output|
|-|-|-|
|true|myfunction.com/test%2fapi|example.com/test/api
|false|myfunction.com/test%2fapi|example.com/test%2fapi|


## <a name="next-steps"></a>Pasos siguientes

[Obtenga información acerca de cómo actualizar la configuración de la aplicación](functions-how-to-use-azure-function-app-settings.md#settings)

[Consulte la configuración global en el archivo host.json](functions-host-json.md)

[Consulte otros valores de aplicación para aplicaciones de App Service](https://github.com/projectkudu/kudu/wiki/Configurable-settings)
