---
title: 'Uso de un certificado SSL en el código: Azure App Service | Microsoft Docs'
description: Obtenga información sobre cómo utilizar certificados de cliente para conectarse a recursos remotos que los necesiten.
services: app-service
documentationcenter: ''
author: cephalin
manager: gwallace
editor: ''
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: cephalin
ms.reviewer: yutlin
ms.custom: seodec18
ms.openlocfilehash: 1546ded1977e1e26792189e1d992d106d3d77ef2
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/21/2019
ms.locfileid: "74271277"
---
# <a name="use-an-ssl-certificate-in-your-code-in-azure-app-service"></a>Uso de un certificado SSL en el código en Azure App Service

En el código de la aplicación, puede acceder a los [certificados públicos o privados que agregue a App Service](configure-ssl-certificate.md). El código de la aplicación puede actuar como un cliente y acceder a un servicio externo que requiere autenticación de certificado, o puede que tenga que realizar tareas criptográficas. Esta guía muestra cómo utilizar certificados públicos o privados en el código de aplicación.

Este enfoque para el uso de certificados en el código usa la funcionalidad SSL de App Service, que requiere que la aplicación tenga el nivel **Básico** u otro superior. Si la aplicación está en los niveles **Gratis** o **Compartido**, puede [incluir el archivo de certificado en el repositorio de la aplicación](#load-certificate-from-file).

Cuando permite que App Service administre los certificados SSL, puede mantener por separado los certificados y el código de la aplicación y proteger así la información confidencial.

## <a name="prerequisites"></a>Requisitos previos

Para completar esta guía paso a paso, debe:

- [Crear una aplicación de App Service](/azure/app-service/)
- [Agregar un certificado a su aplicación](configure-ssl-certificate.md)

## <a name="find-the-thumbprint"></a>Busque la huella digital

En <a href="https://portal.azure.com" target="_blank">Azure Portal</a>, en el menú de la izquierda, seleccione **App Services** >  **\<nombre-de-aplicación>** .

En el panel izquierdo de la aplicación, seleccione **Configuración de TLS/SSL** y, posteriormente, seleccione **Certificados de clave privada (.pfx)** o **Certificados de clave pública (.cer)** .

Encuentre el certificado que desea utilizar y copie la huella digital.

![Copie la huella digital del certificado](./media/configure-ssl-certificate/create-free-cert-finished.png)

## <a name="make-the-certificate-accessible"></a>Que el certificado sea accesible

Para acceder a un certificado en el código de aplicación, agregue su huella digital a la opción de la aplicación `WEBSITE_LOAD_CERTIFICATES`; para ello, ejecute el siguiente comando en <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_CERTIFICATES=<comma-separated-certificate-thumbprints>
```

Para que todos los certificados sean accesibles, establezca el valor en `*`.

## <a name="load-certificate-in-windows-apps"></a>Carga de certificado en aplicaciones de Windows

El valor `WEBSITE_LOAD_CERTIFICATES` de la aplicación permite que los certificados especificados estén accesibles para la aplicación hospedada de Windows en el almacén de certificados de Windows; la ubicación depende del [plan de tarifa ](overview-hosting-plans.md):

- Plan **Aislado**: en [Equipo local\Mi](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores). 
- El resto de los planes: en [Usuario actual\Mi](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores).

En el código C#, se accede al certificado mediante su huella digital. El código siguiente carga un certificado con la huella digital `E661583E8FABEF4C0BEF694CBC41C28FB81CD870`.

```csharp
using System;
using System.Security.Cryptography.X509Certificates;

...
X509Store certStore = new X509Store(StoreName.My, StoreLocation.CurrentUser);
certStore.Open(OpenFlags.ReadOnly);
X509Certificate2Collection certCollection = certStore.Certificates.Find(
                            X509FindType.FindByThumbprint,
                            // Replace below with your certificate's thumbprint
                            "E661583E8FABEF4C0BEF694CBC41C28FB81CD870",
                            false);
// Get the first cert with the thumbprint
if (certCollection.Count > 0)
{
    X509Certificate2 cert = certCollection[0];
    // Use certificate
    Console.WriteLine(cert.FriendlyName);
}
certStore.Close();
...
```

En el código Java, se accede al certificado desde el almacén "Windows-MY" mediante el campo Nombre común del firmante (consulte [Public key certificate](https://en.wikipedia.org/wiki/Public_key_certificate) [Certificado de clave pública]). En el código siguiente se muestra cómo se carga un certificado de clave privada:

```java
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.bind.annotation.RequestMapping;
import java.security.KeyStore;
import java.security.cert.Certificate;
import java.security.PrivateKey;

...
KeyStore ks = KeyStore.getInstance("Windows-MY");
ks.load(null, null); 
Certificate cert = ks.getCertificate("<subject-cn>");
PrivateKey privKey = (PrivateKey) ks.getKey("<subject-cn>", ("<password>").toCharArray());

// Use the certificate and key
...
```

Para los lenguajes que no son compatibles o no ofrecen suficiente compatibilidad con el almacén de certificados de Windows, consulte [Carga del certificado desde el archivo](#load-certificate-from-file).

## <a name="load-certificate-in-linux-apps"></a>Carga del certificado en aplicaciones Linux

El valor `WEBSITE_LOAD_CERTIFICATES` de la aplicación permite que los certificados especificados estén accesibles para las aplicaciones hospedadas en Linux (incluidas las aplicaciones de contenedor personalizadas) como archivos. Los archivos se encuentran en los siguientes directorios:

- Certificados privados: `/var/ssl/private` (archivos `.p12`)
- Certificados públicos: `/var/ssl/certs` (archivos `.der`)

Los nombres de los archivos de certificado son las huellas digitales del certificado. En el siguiente código C# se muestra cómo se carga un certificado público en una aplicación Linux.

```csharp
using System;
using System.Security.Cryptography.X509Certificates;

...
var bytes = System.IO.File.ReadAllBytes("/var/ssl/certs/<thumbprint>.der");
var cert = new X509Certificate2(bytes);

// Use the loaded certificate
```

Para ver cómo se carga un certificado SSL desde un archivo en Node.js, PHP, Python, Java o Ruby, consulte la documentación del lenguaje o la plataforma web correspondientes.

## <a name="load-certificate-from-file"></a>Carga del certificado desde el archivo

Si tiene que cargar un archivo de certificado manualmente, es mejor hacerlo mediante [FTPS](deploy-ftp.md) en lugar de [Git](deploy-local-git.md), por ejemplo. Debe mantener los datos confidenciales, como un certificado privado, fuera del control del código fuente.

> [!NOTE]
> ASP.NET y ASP.NET Core en Windows deben acceder al almacén de certificados, incluso si el certificado se carga desde un archivo. Para cargar un archivo de certificado en una aplicación de Windows .NET, cargue el perfil de usuario actual con el siguiente comando en <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>:
>
> ```azurecli-interactive
> az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_USER_PROFILE=1
> ```
>
> Este enfoque para el uso de certificados en el código usa la funcionalidad SSL de App Service, que requiere que la aplicación tenga el nivel **Básico** u otro superior.

En el siguiente ejemplo de C# se carga un certificado público desde una ruta de acceso relativa de la aplicación:

```csharp
using System;
using System.Security.Cryptography.X509Certificates;

...
var bytes = System.IO.File.ReadAllBytes("~/<relative-path-to-cert-file>");
var cert = new X509Certificate2(bytes);

// Use the loaded certificate
```

Para ver cómo se carga un certificado SSL desde un archivo en Node.js, PHP, Python, Java o Ruby, consulte la documentación del lenguaje o la plataforma web correspondientes.

## <a name="more-resources"></a>Más recursos

* [Protección de un nombre DNS personalizado con un enlace SSL](configure-ssl-bindings.md)
* [Aplicación de HTTPS](configure-ssl-bindings.md#enforce-https)
* [Aplicación de TLS 1.1 y 1.2](configure-ssl-bindings.md#enforce-tls-versions)
* [Preguntas más frecuentes: Certificados de App Service](https://docs.microsoft.com/azure/app-service/faq-configuration-and-management/)
