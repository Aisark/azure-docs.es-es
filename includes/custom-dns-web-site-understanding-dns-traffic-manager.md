---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/09/2018
ms.author: cephalin
ms.openlocfilehash: f42a97cdd74d360bc047ef561cbe626d526f9e4a
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/18/2019
ms.locfileid: "67186894"
---
El Sistema de nombres de dominio (DNS) se utiliza para realizar búsquedas en Internet. Por ejemplo, cuando escribe una dirección en el explorador o hace clic en un vínculo de una página web, utiliza DNS para traducir el dominio en una dirección IP. La dirección IP es algo parecido a la dirección de una calle, pero su sistema es algo complejo. Por ejemplo, es mucho más fácil recordar un nombre DNS como **contoso.com** que recordar una dirección IP como 192.168.1.88 o 2001:0:4137:1f67:24a2:3888:9cce:fea3.

El sistema DNS se basa en *registros*. Los registros asocian un *nombre*específico, como **contoso.com**, a una dirección IP u otro nombre DNS. Cuando una aplicación, como un explorador web, busca un nombre en DNS, encuentra el registro y utiliza aquello a lo que apunte como dirección. Si el valor al que apunta es una dirección IP, el explorador utilizará ese valor. Si apunta a otro nombre DNS, la aplicación tiene que volver a realizar una resolución. En última instancia, todas las resoluciones de nombre terminarán en una dirección IP.

Cuando se crea un sitio web de Azure, se asigna un nombre DNS automáticamente al sitio. Este nombre adquiere la forma de **&lt;nombreDelSitio&gt;.azurewebsites.net**. Cuando se agrega el sitio web como un punto de conexión de Azure Traffic Manager, se puede tener acceso al sitio web a través del dominio **&lt;suPerfilDeTrafficManager&gt;.trafficmanager.net**.

> [!NOTE]
> Cuando el sitio web está configurado como un extremo del Administrador de tráfico, utilizará la dirección **.trafficmanager.net** cuando cree los registros DNS.
> 
> Los registros CNAME solo se pueden usar con el Administrador de tráfico
> 
> 

También hay varios tipos de registros, cada uno con sus propias funciones y limitaciones, pero para los sitios web configurados como extremos del Administrador de tráfico solo nos interesa uno de ellos, los registros *CNAME* .

### <a name="cname-or-alias-record"></a>Registro de CNAME o Alias
Un registro CNAME asigna un nombre DNS *específico*, como **mail.contoso.com** o **www\.contoso.com**, a otro nombre de dominio (canónico). En el caso de Azure Websites que utilizan Traffic Manager, el nombre de dominio canónico es el nombre de dominio **&lt;miAplicación.trafficmanager.net** de su perfil de Traffic Manager. Una vez creado, el CNAME crea un alias para el nombre de dominio **&lt;miAplicación.trafficmanager.net**. La entrada de CNAME se resolverá en la dirección IP del servicio del nombre de dominio **&lt;miAplicación.trafficmanager.net** de manera automática, por lo que si la dirección IP del sitio web cambia, no tiene que realizar ninguna acción.

Una vez que el tráfico llega al Administrador de tráfico, este enruta el tráfico al sitio web, utilizando el método de equilibrio de carga para el cual está configurado. Esto es completamente transparente para los visitantes del sitio web. Ellos solo verán el nombre de dominio personalizado en el explorador.

> [!NOTE]
> Algunos registradores de dominio solo permiten asignar subdominios cuando se utiliza un registro CNAME, como **www\.contoso.com**, no nombres de raíz, como **contoso.com**. Para obtener más información acerca de los registros CNAME, consulte la documentación que proporciona el registrador, <a href="https://en.wikipedia.org/wiki/CNAME_record">la entrada de Wikipedia sobre el registro CNAME</a> o el documento <a href="https://tools.ietf.org/html/rfc1035">Nombres de dominio IETF: implementación y especificación (en inglés)</a>.

