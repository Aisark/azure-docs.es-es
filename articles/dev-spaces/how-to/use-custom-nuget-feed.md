---
title: Uso de una fuente NuGet personalizada en Azure Dev Spaces
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 07/17/2019
ms.topic: conceptual
description: Use una fuente NuGet personalizada para acceder a paquetes NuGet y utilizarlos en Azure Dev Spaces.
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, contenedores
manager: gwallace
ms.openlocfilehash: 44a87491d276e09e1fa8fed3f5e6803648c3e4a2
ms.sourcegitcommit: 770b060438122f090ab90d81e3ff2f023455213b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/17/2019
ms.locfileid: "68305402"
---
#  <a name="use-a-custom-nuget-feed-in-an-azure-dev-space"></a>Uso de una fuente NuGet personalizada en Azure Dev Spaces

Una fuente NuGet proporciona una manera cómoda de incluir orígenes de paquetes en un proyecto. Azure Dev Spaces necesita acceder a esta fuente para que las dependencias se instalen correctamente en el contenedor de Docker.

## <a name="set-up-a-nuget-feed"></a>Configuración de una fuente NuGet

Agregue una [referencia de paquete](https://docs.microsoft.com/nuget/consume-packages/package-references-in-project-files) para la dependencia en el archivo `*.csproj` bajo el nodo `PackageReference`. Por ejemplo:

```xml
<ItemGroup>
    <!-- ... -->
    <PackageReference Include="Contoso.Utility.UsefulStuff" Version="3.6.0" />
    <!-- ... -->
</ItemGroup>
```

Cree un archivo [NuGet.Config](https://docs.microsoft.com/nuget/reference/nuget-config-file) en la carpeta del proyecto y defina las secciones `packageSources` y `packageSourceCredentials` de la fuente de NuGet. La sección `packageSources` contiene la dirección URL de la fuente, que debe ser de acceso público. `packageSourceCredentials` se corresponde con las credenciales para acceder a la fuente. Por ejemplo:

```xml
<packageSources>
    <add key="Contoso" value="https://contoso.com/packages/" />
</packageSources>

<packageSourceCredentials>
    <Contoso>
        <add key="Username" value="user@contoso.com" />
        <add key="ClearTextPassword" value="33f!!lloppa" />
    </Contoso>
</packageSourceCredentials>
```

Actualice su instancia de Dockerfiles para copiar el archivo `NuGet.Config` en la imagen. Por ejemplo:

```console
COPY ["<project folder>/NuGet.Config", "./NuGet.Config"]
```

> [!TIP]
> En Windows, `NuGet.Config`, `Nuget.Config` y `nuget.config` funcionan como nombres de archivo válidos. En Linux, solo `NuGet.Config` es un nombre de archivo válido para este archivo. Como Azure Dev Spaces usa Docker y Linux, este archivo se debe denominar `NuGet.Config`. Puede corregir los nombres manualmente o mediante la ejecución de `dotnet restore --configfile nuget.config`.


Si usa GIT, no debe tener las credenciales de la fuente de NuGet en el control de versiones. Agregue `NuGet.Config` a `.gitignore` para el proyecto, a fin de que el archivo `NuGet.Config` no se agregue al control de versiones. Azure Dev Spaces necesitará este archivo durante el proceso de compilación de la imagen de contenedor, pero de forma predeterminada, respeta las reglas definidas en `.gitignore` y `.dockerignore` durante la sincronización. Para cambiar el valor predeterminado y permitir que Azure Dev Spaces sincronice el archivo `NuGet.Config`, actualice el archivo `azds.yaml`:

```yaml
build:
useGitIgnore: true
ignore:
- “!NuGet.Config”
```

Si no usa GIT, puede omitir este paso.

La próxima vez que ejecute `azds up` o presione `F5` en Visual Studio Code o Visual Studio, Azure Dev Spaces sincronizará el archivo `NuGet.Config` que se usa para instalar las dependencias de paquete.

## <a name="next-steps"></a>Pasos siguientes

Obtenga más información sobre [NuGet y cómo funciona](https://docs.microsoft.com/nuget/what-is-nuget).