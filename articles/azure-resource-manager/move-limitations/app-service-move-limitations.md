---
title: Movimiento de recursos de Azure App Service
description: Use Azure Resource Manager para trasladar recursos de App Service a un nuevo grupo de recursos o a una nueva suscripción.
ms.topic: conceptual
ms.date: 07/09/2019
ms.openlocfilehash: dca9b3a8f328192683cfde586f0ccdb01e84dc16
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150902"
---
# <a name="move-guidance-for-app-service-resources"></a>Orientaciones para el traslado de recursos de App Service

Los pasos para mover recursos de App Service son diferentes en función de si los recursos se mueven dentro de una suscripción o a una nueva.

## <a name="move-in-same-subscription"></a>Traslado en la misma suscripción

Al mover una instancia de Web App _dentro de la misma suscripción_, no se pueden mover los certificados SSL de terceros. Sin embargo, puede mover una instancia de Web App al nuevo grupo de recursos sin mover su certificado de terceros y la funcionalidad SSL de la aplicación seguirá funcionando.

Si desea mover el certificado SSL junto con la instancia de Web App, siga estos pasos:

1. Elimine el certificado de terceros desde Web App, pero guarde una copia de su certificado.
2. Traslade la instancia.
3. Cargue el certificado de terceros en la instancia de Web App trasladada.

## <a name="move-across-subscriptions"></a>Traslado entre suscripciones

Al mover una instancia de Web App _entre suscripciones_, se aplican las limitaciones siguientes:

- El grupo de recursos de destino no debe tener ningún recurso de App Service. Entre los recursos de App Service se incluyen:
    - Web Apps
    - Planes de App Service
    - Certificados SSL cargados o importados
    - Entornos de App Service
- Todos los recursos de App Service del grupo de recursos se deben mover conjuntamente.
- Los recursos de App Service solo se pueden mover del grupo de recursos en el que se crearon originalmente. Si un recurso de App Service ya no está en su grupo de recursos original, muévalo a su grupo de recursos original. A continuación, mueva el recurso entre las suscripciones.

Si no recuerda el grupo de recursos original, se puede encontrar a través de un diagnóstico. Para la aplicación web, seleccione **Diagnosticar y solucionar problemas**. A continuación, seleccione **Configuración y administración**.

![Selección de diagnósticos](./media/app-service-move-limitations/select-diagnostics.png)

Seleccione **Opciones de migración**.

![Selección de opciones de migración](./media/app-service-move-limitations/select-migration.png)

Seleccione la opción de los pasos recomendados para trasladar la aplicación web.

![Selección de pasos recomendados](./media/app-service-move-limitations/recommended-steps.png)

Puede ver las acciones recomendadas que deben realizarse antes de trasladar los recursos. La información incluye el grupo de recursos original de la aplicación web.

![Recomendaciones](./media/app-service-move-limitations/recommendations.png)

## <a name="move-app-service-certificate"></a>Traslado de Azure App Service Certificate

Puede mover su instancia de App Service Certificate a un nuevo grupo de recursos o a una nueva suscripción. Si su instancia de App Service Certificate está enlazada a una aplicación web, debe seguir algunos pasos antes de trasladar los recursos a una nueva suscripción. Elimine el enlace SSL y el certificado privado de la aplicación web antes de trasladar los recursos. No es necesario eliminar la instancia de App Service Certificate, solo el certificado privado en la aplicación web.

## <a name="move-support"></a>Compatibilidad con el movimiento

Para determinar qué recursos de App Service se pueden mover, consulte el estado de compatibilidad con el traslado de:

- [Microsoft.AppService](../move-support-resources.md#microsoftappservice)
- [Microsoft.CertificateRegistration](../move-support-resources.md#microsoftcertificateregistration)
- [Microsoft.DomainRegistration](../move-support-resources.md#microsoftdomainregistration)
- [Microsoft.Web](../move-support-resources.md#microsoftweb)

## <a name="next-steps"></a>Pasos siguientes

Para ver los comandos para trasladar recursos, vea [Traslado de los recursos a un nuevo grupo de recursos o a una nueva suscripción](../resource-group-move-resources.md).
