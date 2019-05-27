---
title: 'Autenticación de servicio a servicio: SDK de .NET con Azure Data Lake Storage Gen1 en Azure Active Directory | Microsoft Docs'
description: Aprenda a realizar la autenticación de servicio a servicio con Azure Data Lake Storage Gen1 mediante Azure Active Directory usando el SDK de .NET.
services: data-lake-store
documentationcenter: ''
author: twooley
manager: cgronlun
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 96c496ef67e26a3079577bf52e9d019d963467b8
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/20/2019
ms.locfileid: "65915844"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-net-sdk"></a>Autenticación de servicio a servicio con Azure Data Lake Storage Gen1 mediante el SDK de .NET
> [!div class="op_single_selector"]
> * [Uso de Java](data-lake-store-service-to-service-authenticate-java.md)
> * [Uso de SDK de .NET](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [Uso de Python](data-lake-store-service-to-service-authenticate-python.md)
> * [Uso de la API de REST](data-lake-store-service-to-service-authenticate-rest-api.md)
>
>

En este artículo, aprenderá a usar el SDK de .NET para realizar la autenticación de servicio a servicio con Azure Data Lake Storage Gen1. Para la autenticación del usuario final con Data Lake Storage Gen1 mediante el SDK de .NET, vea [End-user authentication with Data Lake Storage Gen1 using .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md) (Autenticación del usuario final con Data Lake Storage Gen1 mediante el SDK de .NET).

## <a name="prerequisites"></a>Requisitos previos
* **Visual Studio 2013 o versiones posteriores**. Las instrucciones siguientes usan Visual Studio de 2019.

* **Una suscripción de Azure**. Consulte [Obtención de una versión de evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/).

* **Cree una aplicación "web" de Azure Active Directory**. Debe haber completado los pasos descritos en [Service-to-service authentication with Data Lake Storage Gen1 using Azure Active Directory](data-lake-store-service-to-service-authenticate-using-active-directory.md) (Autenticación entre servicios con Data Lake Storage Gen1 mediante Azure Active Directory).

## <a name="create-a-net-application"></a>Creación de una aplicación .NET
1. En Visual Studio, seleccione el **archivo** menú, **New**y, a continuación, **proyecto**.
2. Elija **aplicación de consola (.NET Framework)** y, a continuación, seleccione **siguiente**.
3. En **nombre del proyecto**, escriba `CreateADLApplication`y, a continuación, seleccione **crear**.

4. Agregue los paquetes NuGet al proyecto.

   1. Haga clic con el botón derecho en el Explorador de soluciones y haga clic en **Administrar paquetes de NuGet**.
   2. En la pestaña **Administrador de paquetes NuGet**, asegúrese de que la opción **Origen del paquete** esté establecida en **nuget.org** y que esté activada la casilla **Incluir versión preliminar**.
   3. Busque e instale los siguientes paquetes NuGet:

      * `Microsoft.Azure.Management.DataLake.Store` - En este tutorial se usa v2.1.3 (versión preliminar).
      * `Microsoft.Rest.ClientRuntime.Azure.Authentication` - En este tutorial se usa v2.2.12.

        ![Incorporación de un origen de NuGet](./media/data-lake-store-get-started-net-sdk/data-lake-store-install-nuget-package.png "Creación de una cuenta de Azure Data Lake")
   4. Cierre el **Administrador de paquetes NuGet**.

5. Abra **Program.cs**, elimine el código existente e incluya las siguientes instrucciones para agregar referencias a espacios de nombres.

```csharp
using System;
using System.IO;
using System.Linq;
using System.Text;
using System.Threading;
using System.Collections.Generic;
using System.Security.Cryptography.X509Certificates; // Required only if you are using an Azure AD application created with certificates

using Microsoft.Rest;
using Microsoft.Rest.Azure.Authentication;
using Microsoft.Azure.Management.DataLake.Store;
using Microsoft.Azure.Management.DataLake.Store.Models;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

## <a name="service-to-service-authentication-with-client-secret"></a>Autenticación entre servicios con el secreto de cliente
Agregue este fragmento de código a su aplicación cliente .NET. Reemplace los valores de marcador de posición por los valores recuperados de una aplicación web de Azure AD (se enumera como un requisito previo). Este fragmento de código le permite autenticar la aplicación de **forma no interactiva** con Data Lake Storage Gen1 mediante la clave o secreto de cliente para la aplicación web de Azure AD.

```csharp
private static void Main(string[] args)
{
    // Service principal / application authentication with client secret / key
    // Use the client ID of an existing AAD "Web App" application.
    string TENANT = "<AAD-directory-domain>";
    string CLIENTID = "<AAD_WEB_APP_CLIENT_ID>";
    System.Uri ARM_TOKEN_AUDIENCE = new System.Uri(@"https://management.core.windows.net/");
    System.Uri ADL_TOKEN_AUDIENCE = new System.Uri(@"https://datalake.azure.net/");
    string secret_key = "<AAD_WEB_APP_SECRET_KEY>";
    var armCreds = GetCreds_SPI_SecretKey(TENANT, ARM_TOKEN_AUDIENCE, CLIENTID, secret_key);
    var adlCreds = GetCreds_SPI_SecretKey(TENANT, ADL_TOKEN_AUDIENCE, CLIENTID, secret_key);
}
```

El fragmento de código anterior usa la función auxiliar `GetCreds_SPI_SecretKey`. El código de esta función auxiliar está disponible [aquí en GitHub](https://github.com/Azure-Samples/data-lake-analytics-dotnet-auth-options#getcreds_spi_secretkey).

## <a name="service-to-service-authentication-with-certificate"></a>Autenticación entre servicios con un certificado

Agregue este fragmento de código a su aplicación cliente .NET. Reemplace los valores de marcador de posición por los valores recuperados de una aplicación web de Azure AD (se enumera como un requisito previo). Este fragmento de código le permite autenticar su aplicación **de forma no interactiva** con Data Lake Storage Gen1 usando el certificado para una aplicación web de Azure AD. Para obtener instrucciones acerca de cómo crear una aplicación de Azure AD, consulte [Creación de una entidad de servicio con un certificado autofirmado](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate).

```csharp
private static void Main(string[] args)
{
    // Service principal / application authentication with certificate
    // Use the client ID and certificate of an existing AAD "Web App" application.
    string TENANT = "<AAD-directory-domain>";
    string CLIENTID = "<AAD_WEB_APP_CLIENT_ID>";
    System.Uri ARM_TOKEN_AUDIENCE = new System.Uri(@"https://management.core.windows.net/");
    System.Uri ADL_TOKEN_AUDIENCE = new System.Uri(@"https://datalake.azure.net/");
    var cert = new X509Certificate2(@"d:\cert.pfx", "<certpassword>");
    var armCreds = GetCreds_SPI_Cert(TENANT, ARM_TOKEN_AUDIENCE, CLIENTID, cert);
    var adlCreds = GetCreds_SPI_Cert(TENANT, ADL_TOKEN_AUDIENCE, CLIENTID, cert);
}
```

El fragmento de código anterior usa la función auxiliar `GetCreds_SPI_Cert`. El código de esta función auxiliar está disponible [aquí en GitHub](https://github.com/Azure-Samples/data-lake-analytics-dotnet-auth-options#getcreds_spi_cert).

## <a name="next-steps"></a>Pasos siguientes
En este artículo, aprendió a usar la autenticación de servicio a servicio con Data Lake Storage Gen1 mediante el SDK de .NET. Ahora puede consultar los siguientes artículos que tratan sobre cómo usar el SDK de .NET con Data Lake Storage Gen1.

* [Operaciones de administración de cuentas en Data Lake Storage Gen1 con el SDK de .NET](data-lake-store-get-started-net-sdk.md)
* [Operaciones de datos en Data Lake Storage Gen1 mediante el SDK de .NET](data-lake-store-data-operations-net-sdk.md)
