---
title: 'Azure Front Door Service: Reescritura de direcciones URL | Microsoft Docs'
description: Este artículo le ayudará a comprender cómo Azure Front Door Service realiza la reescritura de direcciones URL para las rutas, si se ha configurado.
services: front-door
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: dc2126276e3e8e0d35ce8ed1f835544386659eff
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "60736195"
---
# <a name="url-rewrite-custom-forwarding-path"></a>Reescritura de direcciones URL (ruta de acceso de reenvío personalizada)
Azure Front Door Service admite la reescritura de direcciones URL al permitirle configurar una **ruta de acceso de reenvío personalizada** opcional que se usará al construir la solicitud para reenviar al back-end. De forma predeterminada, si no se proporciona ninguna ruta de reenvío personalizada, Front Door copiará la ruta de acceso de la dirección URL entrante en la dirección URL usada en la solicitud reenviada. El encabezado host usado en la solicitud reenviada se configura para el back-end seleccionado. Lea [Encabezado de host de back-end](front-door-backend-pool.md#hostheader) para más información sobre lo que hace y cómo configurarlo.

La ventaja más importante de la reescritura de direcciones URL mediante la ruta de acceso de reenvío personalizada es que esta copia cualquier parte de la ruta de acceso entrante que coincida con una ruta de acceso de comodín en la ruta de acceso de reenvío (estos segmentos aparecen en **verde** en el siguiente ejemplo):
</br>
![Reescritura de direcciones URL de Azure Front Door Service][1]

## <a name="url-rewrite-example"></a>Ejemplo de reescritura de direcciones URL
Considere la posibilidad de una regla de enrutamiento con los siguientes hosts de front-end y rutas de acceso configuradas:

| Hosts      | Rutas de acceso       |
|------------|-------------|
| www\.contoso.com | /\*         |
|            | /foo        |
|            | /foo/\*     |
|            | /foo/bar/\* |

La primera columna de la tabla siguiente muestra ejemplos de solicitudes entrantes y la segunda muestra cuál sería la ruta coincidente "más específica".  La tercera y las siguientes columnas de la primera fila de la tabla son ejemplos de **rutas de acceso de reenvío personalizadas** configuradas. El resto de filas de esas columnas representan ejemplos de lo que sería la ruta de acceso de solicitud reenviada si coincidiera con la solicitud de esa fila.

Por ejemplo, si leemos en la segunda fila, dice que para la solicitud entrante `www.contoso.com/sub`, si la ruta de acceso de reenvío personalizada fuera `/`, la ruta de acceso reenviada sería `/sub`. Si la ruta de reenvío personalizada fuera `/fwd/`, la ruta de acceso reenviada sería `/fwd/sub`. Y así sucesivamente, para las restantes columnas. Las partes **destacadas** de las rutas de acceso siguientes representan las porciones que forman parte de la coincidencia de caracteres comodín.


| Solicitud entrante       | Ruta de acceso de coincidencia más específica | /          | /fwd/          | /foo/          | /foo/bar/          |
|------------------------|--------------------------|------------|----------------|----------------|--------------------|
| www\.contoso.com/            | /\*                      | /          | /fwd/          | /foo/          | /foo/bar/          |
| www\.contoso.com/**sub**     | /\*                      | /**sub**   | /fwd/**sub**   | /foo/**sub**   | /foo/bar/**sub**   |
| www\.contoso.com/**a/b/c**   | /\*                      | /**a/b/c** | /fwd/**a/b/c** | /foo/**a/b/c** | /foo/bar/**a/b/c** |
| www\.contoso.com/foo         | /foo                     | /          | /fwd/          | /foo/          | /foo/bar/          |
| www\.contoso.com/foo/        | /foo/\*                  | /          | /fwd/          | /foo/          | /foo/bar/          |
| www\.contoso.com/foo/**bar** | /foo/\*                  | /**bar**   | /fwd/**bar**   | /foo/**bar**   | /foo/bar/**bar**   |


## <a name="optional-settings"></a>Configuración opcional
Hay valores opcionales adicionales que también puede especificar para cualquier configuración de regla de enrutamiento:

* **Configuración de caché**: si está deshabilitada o no se ha especificado, las solicitudes que coincidan con esta regla de enrutamiento no intentarán usar el contenido almacenado en caché y, en su lugar, lo capturarán siempre desde el back-end. Obtenga más información sobre [Almacenamiento en caché con Front Door](front-door-caching.md).



## <a name="next-steps"></a>Pasos siguientes

- Aprenda a [crear una instancia de Front Door](quickstart-create-front-door.md).
- Más información acerca de cómo [funciona Front Door](front-door-routing-architecture.md).

<!--Image references-->
[1]: ./media/front-door-url-rewrite/front-door-url-rewrite-example.jpg