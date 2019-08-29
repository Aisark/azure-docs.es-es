---
title: 'Introducción a la caché local: Azure App Service | Microsoft Docs'
description: En este artículo se describe cómo habilitar, cambiar el tamaño y consultar el estado de la característica de caché local de Azure App Service
services: app-service
documentationcenter: app-service
author: cephalin
manager: jpconnock
editor: ''
tags: optional
keywords: ''
ms.assetid: e34d405e-c5d4-46ad-9b26-2a1eda86ce80
ms.service: app-service
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/04/2016
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 9102d6f3ce3be44107268419517dc9ebe434ac7a
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/28/2019
ms.locfileid: "70098457"
---
# <a name="azure-app-service-local-cache-overview"></a>Información general de caché local de Azure App Service

> [!NOTE]
> La caché local no se admite en aplicaciones de función o en aplicaciones de App Service en contenedores, como es el caso de [App Service en Linux](containers/app-service-linux-intro.md).


El contenido de Azure App Service se almacena en Azure Storage y surge de manera duradera como un recurso compartido de contenido. Este diseño está pensado para trabajar con una amplia gama de aplicaciones y tiene los siguientes atributos:  

* El contenido se comparte entre varias instancias de máquina virtual de la aplicación.
* El contenido es duradero y se puede modificar mediante la ejecución de las aplicaciones.
* Los archivos de registro y los archivos de datos de diagnóstico están disponibles bajo la misma carpeta de contenido compartida.
* La publicación de nuevo contenido directamente actualiza la carpeta de contenido Puede ver inmediatamente el mismo contenido a través del sitio web de SCM y la aplicación en ejecución (normalmente, algunas tecnologías como ASP.NET, llevan a cabo un reinicio de la aplicación en algunos cambios de archivo para obtener el contenido más reciente).

Si bien muchas aplicaciones usan una o la totalidad de estas características, algunas aplicaciones solo necesitan un almacén de contenido de solo lectura de gran rendimiento desde donde se puedan ejecutar con alta disponibilidad. Estas aplicaciones se pueden beneficiar de una instancia de VM de una caché local específica.

La característica de caché local de Azure App Service proporciona una vista de rol web del contenido. Este contenido es una caché de escritura temporal del contenido de almacenamiento que se creó de forma asincrónica durante el inicio en el sitio. Cuando la memoria caché está preparada, el sitio cambia para ejecutarse con el contenido almacenado en caché. Las aplicaciones que se ejecutan en la caché local tienen los siguientes beneficios:

* Son inmunes a las latencias que se producen cuando tienen acceso al contenido en Azure Storage.
* Son inmunes a las actualizaciones planeadas o a los tiempos de inactividad no planeados y cualquier otra interrupción con Azure Storage que se produzca en los servidores que atienden el recurso compartido del contenido.
* Tienen menos reinicios de aplicación debido a cambios en el recurso compartido de almacenamiento.

## <a name="how-the-local-cache-changes-the-behavior-of-app-service"></a>Cómo la memoria caché local cambia el comportamiento de App Service
* _D:\home_ apunta a la memoria caché local, que se crea en la instancia de la VM cuando se inicia la aplicación. _D:\local_ sigue apuntando al almacenamiento específico de la VM temporal.
* La memoria caché local contiene una copia única de las carpetas _/site_ y _/siteextensions_ del almacén de contenido compartido, en _D:\home\site_ y _D:\home\siteextensions_, respectivamente. Los archivos se copian en la memoria caché local cuando se inicia la aplicación. El tamaño de la memoria caché local para cada aplicación se limita a 300 MB de forma predeterminada, pero se puede aumentar a hasta 2 GB.
* La caché local es de lectura y escritura. Sin embargo, se descartará cualquier modificación cuando la aplicación mueva máquinas virtuales o se reinicie. No use la memoria caché local para las aplicaciones que almacenan datos críticos en el almacén de contenido.
* _D:\home\LogFiles_ y _D:\home\Data_ contienen archivos de registro y datos de la aplicación. Las dos subcarpetas se almacenan localmente en la instancia de la VM y se copian periódicamente en el almacén de contenido compartido. Las aplicaciones pueden conservar los archivos de registro y los datos al escribirlos en estas carpetas. Sin embargo, la copia en el almacén de contenido compartido es de mejor esfuerzo, por lo que es posible que los archivos de registro y datos se pierdan debido a un bloqueo repentino de una instancia de la VM.
* Las [secuencias de registro](troubleshoot-diagnostic-logs.md#streamlogs) se ve afectada por la copia de mejor esfuerzo. Esto podría provocar un retraso de hasta un minuto en los registros en secuencia.
* En el almacén de contenido compartido, hay un cambio en la estructura de las carpetas _LogFiles_ y _Data_ para las aplicaciones que usan la memoria caché local. Ahora contienen subcarpetas que siguen el patrón de nomenclatura de "identificador único" + marca de tiempo. Cada una de las subcarpetas corresponde a una instancia de VM en donde la aplicación se está ejecutando o se ha ejecutado.
* Las demás carpetas en _D:\home_ permanecen en la memoria caché local y no se copian en el almacén de contenido compartido.
* La implementación de aplicaciones a través de cualquier método admitido publica directamente en el almacén de contenido compartido duradero. Para actualizar las carpetas _D:\home\site_ y _D:\home\siteextensions_ en la memoria caché local, se debe reiniciar la aplicación. Para que el ciclo de vida avance sin problemas, consulte la información que aparece más adelante en este artículo.
* La vista de contenido predeterminado del sitio de SCM sigue siendo la del almacén de contenido compartido.

## <a name="enable-local-cache-in-app-service"></a>Habilitación de la caché local en App Service
Para configurar la caché local, se usa una combinación de configuraciones de aplicación reservadas. Puede configurar estos ajustes de la aplicación con los métodos siguientes:

* [Azure Portal](#Configure-Local-Cache-Portal)
* [Administrador de recursos de Azure](#Configure-Local-Cache-ARM)

### <a name="configure-local-cache-by-using-the-azure-portal"></a>Configuración de la caché local mediante el Portal de Azure
<a name="Configure-Local-Cache-Portal"></a>

Use esta configuración de aplicación para habilitar la caché local en función de la aplicación web: `WEBSITE_LOCAL_CACHE_OPTION` = `Always`  

![Configuración de la aplicación en Azure Portal: caché local](media/app-service-local-cache-overview/app-service-local-cache-configure-portal.png)

### <a name="configure-local-cache-by-using-azure-resource-manager"></a>Configuración de la caché local mediante Azure Resource Manager
<a name="Configure-Local-Cache-ARM"></a>

```json

...

{
    "apiVersion": "2015-08-01",
    "type": "config",
    "name": "appsettings",
    "dependsOn": [
        "[resourceId('Microsoft.Web/sites/', variables('siteName'))]"
    ],

    "properties": {
        "WEBSITE_LOCAL_CACHE_OPTION": "Always",
        "WEBSITE_LOCAL_CACHE_SIZEINMB": "300"
    }
}

...
```

## <a name="change-the-size-setting-in-local-cache"></a>Cambio de la configuración de tamaño en la caché local
El tamaño predeterminado de la caché local es **300 MB**. Esto incluye las carpetas /site y /siteextensions que se copian desde el almacén de contenido así como en cualquier registro y carpeta de datos creados localmente. Para aumentar este límites, use la configuración de aplicación `WEBSITE_LOCAL_CACHE_SIZEINMB`. Puede aumentar el tamaño hasta **2 GB** (2000 MB) por aplicación.

## <a name="best-practices-for-using-app-service-local-cache"></a>Prácticas recomendadas para usar la caché local de App Service
Se recomienda que use la caché local conjuntamente con la característica [Entornos de ensayo](../app-service/deploy-staging-slots.md) .

* Agregue la configuración de aplicación *temporal* `WEBSITE_LOCAL_CACHE_OPTION` con el valor `Always` a la ranura **Producción**. Si usa `WEBSITE_LOCAL_CACHE_SIZEINMB`, agréguela también como una configuración temporal a su ranura de producción.
* Cree una ranura de **ensayo** y haga la publicación en su ranura de ensayo. Habitualmente, no define la ranura de ensayo para usar la caché local para habilitar un ciclo de vida de compilación, implementación y prueba sin problemas si obtiene los beneficios de la caché local para la ranura de producción.
* Pruebe el sitio en la ranura de ensayo.  
* Una vez que esté preparado, emita una [operación de intercambio](../app-service/deploy-staging-slots.md#Swap) entre las ranuras de ensayo y producción.  
* La configuración temporal incluye un nombre y se adhiere a una ranura. Por tanto, cuando la ranura de ensayo se intercambia por producción, hereda la configuración de aplicación de la caché local. La ranura de producción recién intercambiada se ejecutará en la caché local después de unos minutos y se calentará como parte del calentamiento de la ranura después del intercambio. Por tanto, cuando se completa el intercambio de ranura, la ranura de producción del usuario se ejecuta en la caché local.

## <a name="frequently-asked-questions-faq"></a>Preguntas más frecuentes
### <a name="how-can-i-tell-if-local-cache-applies-to-my-app"></a>¿Cómo puedo saber si la caché local se aplica a mi aplicación?
Si la aplicación necesita un almacén de contenido confiable y de alto rendimiento, y no usa el almacén de contenido para escribir datos críticos en tiempo de ejecución y tiene un tamaño inferior a 2 GB, la respuesta es sí. Para obtener el tamaño total de las carpetas /site y /siteextensions, puede utilizar la extensión del sitio de "uso de disco de las aplicaciones web de Azure".

### <a name="how-can-i-tell-if-my-site-has-switched-to-using-local-cache"></a>¿Cómo puedo saber si mi sitio ha cambiado para usar caché local?
Si usa la característica de caché local con entornos de ensayo, la operación de intercambio no se completa hasta que la memoria caché local se haya calentado. Para comprobar si el sitio se ejecuta en caché local, puede comprobar la variable de entorno del proceso de trabajo `WEBSITE_LOCALCACHE_READY`. Siga las instrucciones de la página de la [variable de entorno del proceso de trabajo](https://github.com/projectkudu/kudu/wiki/Process-Threads-list-and-minidump-gcdump-diagsession#process-environment-variable) para tener acceso a la variable de entorno del proceso de trabajo en varias instancias.  

### <a name="i-just-published-new-changes-but-my-app-does-not-seem-to-have-them-why"></a>Acabo de publicar nuevos cambios, pero mi aplicación no parece tenerlos. ¿Por qué?
Si la aplicación usa caché local, debe reiniciar el sitio para obtener los cambios más recientes. ¿No desea publicar los cambios en un sitio de producción? Consulte las opciones de ranura en la sección anterior sobre prácticas recomendadas.

### <a name="where-are-my-logs"></a>¿Dónde están mis registros?
Con la caché local, los registros y las carpetas de datos tienen un aspecto diferente. Sin embargo, la estructura de las subcarpetas permanece igual, excepto en que están anidadas en una subcarpeta con el formato "identificador de VM único" + marca de tiempo.

### <a name="i-have-local-cache-enabled-but-my--app-still-gets-restarted-why-is-that-i-thought-local-cache-helped-with-frequent-app-restarts"></a>Tengo habilitada la caché local, pero mi aplicación todavía se reinicia. ¿Por qué ocurre esto? Pensé que la memoria caché local que iba a ayudar con los reinicios de aplicación frecuentes.
La caché local ayuda a evitar que la aplicación relacionada con el almacenamiento se reinicie. Con todo, la aplicación todavía puede sufrir reinicios durante actualizaciones de infraestructura de la VM planeadas. Los reinicios totales de aplicación que experimenta con la caché local habilitada deben ser menos.

### <a name="does-local-cache-exclude-any-directories-from-being-copied-to-the-faster-local-drive"></a>¿La caché local evita que los directorios se copien en la unidad local con mayor rapidez?
Como parte del paso que copia el contenido de almacenamiento, se excluye cualquier carpeta que se denomine "repositorio". Esto es útil con escenarios donde el contenido del sitio puede contener un repositorio de control de código fuente que es posible que no sea necesario en el funcionamiento rutinario de la aplicación. 
