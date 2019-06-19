---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 04/02/2019
ms.openlocfilehash: d1c880ddc90ae3ce18dfde7e1983b45ac239de85
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/18/2019
ms.locfileid: "67186586"
---
La opción de configuración `Logging` administra la compatibilidad con el registro de ASP.NET Core del contenedor. Puede usar los mismos valores y opciones de configuración para el contenedor que los que usa para una aplicación ASP.NET Core. 

Los siguientes proveedores de registro son compatibles con el contenedor:

|Proveedor|Propósito|
|--|--|
|[Console](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#console-provider)|Proveedor de registro de `Console` de ASP.NET Core. Se admiten todos los valores predeterminados y las opciones de configuración de ASP.NET Core para este proveedor de registro.|
|[Depuración](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#debug-provider)|Proveedor de registro de `Debug` de ASP.NET Core. Se admiten todos los valores predeterminados y las opciones de configuración de ASP.NET Core para este proveedor de registro.|
|[Disco](#disk-logging)|Proveedor de registro JSON. Este proveedor de registro escribe datos de registro para el montaje de salida.|

Este comando de contenedor almacena información de registro en formato JSON en el montaje de salida:

```bash
docker run --rm -it -p 5000:5000 \
--memory 2g --cpus 1 \
--mount type=bind,src=/home/azureuser/output,target=/output \
<registry-location>/<image-name> \
Eula=accept \
Billing=<billing-endpoint> \
ApiKey=<api-key> \
Logging:Disk:Format=json
```

Este comando de contenedor muestra información de depuración, con el prefijo `dbug` mientras el contenedor se ejecuta:

```bash
docker run --rm -it -p 5000:5000 \
--memory 2g --cpus 1 \
<registry-location>/<image-name> \
Eula=accept \
Billing=<billing-endpoint> \
ApiKey=<api-key> \
Logging:Console:LogLevel:Default=Debug
```

### <a name="disk-logging"></a>Registro del disco

El proveedor de registro `Disk` admite la configuración siguiente:  

| NOMBRE | Tipo de datos | DESCRIPCIÓN |
|------|-----------|-------------|
| `Format` | Cadena | Formato de salida de los archivos de registro.<br/> **Nota:** Este valor debe establecerse en `json` para habilitar el proveedor de registro. Si se especifica este valor sin especificar también un montaje de salida al crear una instancia de un contenedor, se produce un error. |
| `MaxFileSize` | Entero | Tamaño máximo en megabytes (MB) de un archivo de registro. Cuando el tamaño del archivo de registro actual cumple este valor o lo supera, el proveedor de registro inicia un nuevo archivo de registro. Si se especifica -1, el tamaño del archivo de registro solo está limitado por el tamaño máximo de archivo, si existe, para el montaje de salida. El valor predeterminado es 1. |

Para obtener más información acerca de cómo configurar la compatibilidad con el registro de ASP.NET Core, consulte [Configuración del archivo de configuración](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1).

