---
title: Implementación de servicios de Azure API Management en varias regiones de Azure | Microsoft Docs
description: Aprenda a implementar una instancia del servicio Azure API Management en varias regiones de Azure.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2019
ms.author: apimpm
ms.openlocfilehash: d22da92355616c208c7616b4b0e8c26b7f9e7006
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "60658196"
---
# <a name="how-to-deploy-an-azure-api-management-service-instance-to-multiple-azure-regions"></a>Implementación de una instancia del servicio Azure API Management en varias regiones de Azure

Azure API Management admite la implementación en varias regiones, lo que permite a los publicadores de API distribuir un único servicio Azure API Management en el número de regiones de Azure deseado. Esto ayuda a reducir la latencia de solicitud que perciben los usuarios de API distribuidos geográficamente y, además, mejora la disponibilidad del servicio en caso de que una región se quede sin conexión.

Inicialmente, un nuevo servicio Azure API Management contiene solo una [unidad][unit] en una única región de Azure, la región primaria. Pueden agregarse otras regiones fácilmente mediante Azure Portal. El servidor de puerta de enlace de API Management se implementa en cada región y el tráfico de llamada se enruta a la puerta de enlace más cercana en términos de latencia. Cuando una región se queda sin conexión, el tráfico se redirige automáticamente a la siguiente puerta de enlace más cercana.

> [!NOTE]
> Azure API Management replica solo el componente de la puerta de enlace de API entre regiones. El componente de administración de servicio se hospeda solo en la región primaria. En caso de interrupción en la región primaria, no se pueden aplicar cambios de configuración en una instancia del servicio Azure API Management, incluidas las configuraciones y las actualizaciones de directivas.

[!INCLUDE [premium.md](../../includes/api-management-availability-premium.md)]

## <a name="add-region"></a>Implementación de una instancia del servicio Administración de API en una nueva región

> [!NOTE]
> Si todavía no ha creado una instancia del servicio API Management, consulte [Creación de una instancia del servicio API Management][Create an API Management service instance].

En Azure Portal, vaya a la página de **escala y precios** de su instancia de servicio de API Management. 

![Pestaña Escala][api-management-scale-service]

Para implementar en una nueva región, haga clic en **+ Agregar región** desde la barra de herramientas.

![Agregar región][api-management-add-region]

Seleccione la ubicación en la lista desplegable y establezca el número de unidades para el control deslizante.

![Especificar unidades][api-management-select-location-units]

Haga clic en **Agregar** para colocar la selección en la tabla de ubicaciones. 

Repita este proceso hasta que haya configurado todas las ubicaciones y haga clic en **Guardar** en la barra de herramientas para iniciar el proceso de implementación.

## <a name="remove-region"></a>Eliminación de una instancia de servicio de API Management de una ubicación

En Azure Portal, vaya a la página de **escala y precios** de su instancia de servicio de API Management. 

![Pestaña Escala][api-management-scale-service]

Para la ubicación que desee eliminar, abra el menú contextual mediante el botón **...** situado a la derecha de la tabla. Haga clic en la opción **Eliminar**.

Confirme la eliminación y haga clic en **Guardar** para aplicar los cambios.

## <a name="route-backend"> </a>Enrutamiento de las llamadas API a servicios regionales back-end

Azure API Management incluye solo una dirección URL del servicio back-end. Aunque hay instancias de Azure API Management en varias regiones, la puerta de enlace de API sigue reenviando las solicitudes al mismo servicio back-end, que se implementa en una única región. En este caso, la ganancia de rendimiento procederá solo de las respuestas en caché a la solicitud en Azure API Management de una región específica, pero también puede generar una latencia alta al ponerse en contacto con el back-end de todo el mundo.

Para aprovechar completamente la distribución geográfica de su sistema, debe tener servicios back-end implementados en las mismas regiones que las instancias de Azure API Management. A continuación, mediante directivas y la propiedad `@(context.Deployment.Region)` puede enrutar el tráfico a las instancias locales de su back-end.

1. Vaya a la instancia de Azure API Management y haga clic en **API** en el menú izquierdo.
2. Seleccione la API que desee.
3. Haga clic en el **Editor de código** de la flecha desplegable de **Procesamiento de entrada**.

    ![Editor de código de API](./media/api-management-howto-deploy-multi-region/api-management-api-code-editor.png)

4. Use `set-backend` en combinación con directivas `choose` condicionales para construir una directiva de enrutamiento adecuada en la sección `<inbound> </inbound>` del archivo.

    Por ejemplo, el siguiente archivo XML sería válido para las regiones Oeste de Estados Unidos y Asia oriental:

    ```xml
    <policies>
        <inbound>
            <base />
            <choose>
                <when condition="@("West US".Equals(context.Deployment.Region, StringComparison.OrdinalIgnoreCase))">
                    <set-backend-service base-url="http://contoso-us.com/" />
                </when>
                <when condition="@("East Asia".Equals(context.Deployment.Region, StringComparison.OrdinalIgnoreCase))">
                    <set-backend-service base-url="http://contoso-asia.com/" />
                </when>
                <otherwise>
                    <set-backend-service base-url="http://contoso-other.com/" />
                </otherwise>
            </choose>
        </inbound>
        <backend>
            <base />
        </backend>
        <outbound>
            <base />
        </outbound>
        <on-error>
            <base />
        </on-error>
    </policies>
    ```

> [!TIP]
> También puede adelantar sus servicios de back-end con [Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/), dirigir las llamadas de las API a Traffic Manager y dejar que resuelva el enrutamiento automáticamente.

## <a name="custom-routing"> </a>Uso del enrutamiento personalizado a puertas de enlace regionales de API Management

API Management enruta las solicitudes a una *puerta de enlace* regional en función de la [latencia más baja](../traffic-manager/traffic-manager-routing-methods.md#performance). Aunque no es posible invalidar esta configuración en API Management, puede usar su propia instancia de Traffic Manager con reglas de enrutamiento personalizadas.

1. Cree su propio perfil de [Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/).
1. Si usa un dominio personalizado, [úselo con Traffic Manager](../traffic-manager/traffic-manager-point-internet-domain.md) y no con el servicio API Management.
1. [Configuración de los puntos de conexión regionales de API Management en Traffic Manager](../traffic-manager/traffic-manager-manage-endpoints.md). Los puntos de conexión regionales siguen el patrón de dirección URL de `https://<service-name>-<region>-01.regional.azure-api.net`, por ejemplo `https://contoso-westus2-01.regional.azure-api.net`.
1. [Configure los puntos de conexión de estado regional de API Management en Traffic Manager](../traffic-manager/traffic-manager-monitoring.md). Los puntos de conexión de estado regional siguen el patrón de dirección URL de `https://<service-name>-<region>-01.regional.azure-api.net/status-0123456789abcdef`, por ejemplo `https://contoso-westus2-01.regional.azure-api.net/status-0123456789abcdef`.
1. Especifique [el método de enrutamiento](../traffic-manager/traffic-manager-routing-methods.md) de Traffic Manager.


[api-management-management-console]: ./media/api-management-howto-deploy-multi-region/api-management-management-console.png

[api-management-scale-service]: ./media/api-management-howto-deploy-multi-region/api-management-scale-service.png
[api-management-add-region]: ./media/api-management-howto-deploy-multi-region/api-management-add-region.png
[api-management-select-location-units]: ./media/api-management-howto-deploy-multi-region/api-management-select-location-units.png
[api-management-remove-region]: ./media/api-management-howto-deploy-multi-region/api-management-remove-region.png

[Create an API Management service instance]: get-started-create-service-instance.md
[Get started with Azure API Management]: get-started-create-service-instance.md

[Deploy an API Management service instance to a new region]: #add-region
[Delete an API Management service instance from a region]: #remove-region

[unit]: https://azure.microsoft.com/pricing/details/api-management/
[Premium]: https://azure.microsoft.com/pricing/details/api-management/
