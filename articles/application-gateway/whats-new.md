---
title: Novedades de Azure Application Gateway
description: Obtenga información acerca de las novedades de Azure Application Gateway, como, por ejemplo, las notas de la versión más recientes, los problemas conocidos, las correcciones de errores, las funcionalidades en desuso y los próximos cambios.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: overview
ms.date: 4/30/2019
ms.author: victorh
ms.openlocfilehash: 6755825aa123b3d7d1eed23291790ff3dc477ac5
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/05/2019
ms.locfileid: "70382482"
---
# <a name="whats-new-in-azure-application-gateway"></a>Novedades de Azure Application Gateway

Azure Application Gateway se actualiza de forma continuada. Para mantenerse al día con los avances más recientes, este artículo proporciona información acerca de los elementos siguientes:

- Versiones más recientes
- Problemas conocidos
- Corrección de errores
- Funciones obsoletas

## <a name="new-features"></a>Nuevas características

|Característica  |DESCRIPCIÓN  |Fecha de adición  |
|---------|---------|---------|
|Más métricas |Hemos agregado las siguientes métricas nuevas para ayudarle a supervisar la SKU de Application Gateway v2: [Métricas relacionadas con el control de tiempo](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics#timing-metrics), estado de respuesta de back-end, bytes recibidos, bytes enviados, protocolo TLS de cliente y unidades de proceso actuales. Consulte las [Métricas compatibles con la SKU de Application Gateway V2](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics#metrics-supported-by-application-gateway-v2-sku). |Agosto de 2019 |
|Reglas personalizadas del firewall de aplicaciones web |Application Gateway WAF_v2 admite ya la creación de reglas personalizadas. Consulte [Reglas personalizadas de Application Gateway](custom-waf-rules-overview.md). |Junio de 2019 |
|Escalado automático, redundancia de zona, disponibilidad general del soporte técnico de VIP estática |Disponibilidad general de SKU v2 que admite el escalado automático, la redundancia de zona, la mejora del rendimiento, las VIP estáticas, Key Vault y la reescritura de encabezados. Vea la [documentación sobre el escalado automático de Application Gateway](application-gateway-autoscaling-zone-redundant.md). |Abril de 2019 |
|Integración de Key Vault |Application Gateway ahora admite la integración con Key Vault (en versión preliminar pública) para certificados de servidor que se adjuntan a los clientes de escucha con HTTPS habilitado. Vea [Terminación SSL con certificados de Key Vault](key-vault-certs.md). |Abril de 2019 |
|CRUD y reescrituras de encabezado     |Ya puede reescribir encabezados HTTP. Vea [Tutorial: Crear una instancia de Application Gateway y reescribir los encabezados HTTP](tutorial-http-header-rewrite-powershell.md) para más información.|Diciembre de 2018|
|Configuración de WAF y lista de exclusión     |Hemos agregado más opciones para ayudarle a configurar el WAF y reducir los falsos positivos. Consulte [Listas de exclusión y límites de tamaño de solicitud del firewall de aplicaciones web](application-gateway-waf-configuration.md) para más información.|Diciembre de 2018|
|Escalado automático, redundancia de zona y disponibilidad general del soporte técnico de VIP estática      |En la versión v2 de SKU hay muchas mejoras, como el escalado automático, un rendimiento mejorado, etc. Consulte [¿Qué es Azure Application Gateway?](overview.md) para más información.|Septiembre de 2018|
|Purga de la conexión     |Purga de conexión le permite eliminar correctamente los miembros de los grupos de back-end. Para más información, consulte: [Purga de conexión](overview.md#connection-draining).|Septiembre de 2018|
|Páginas de error personalizadas     |Con páginas de errores personalizadas, puede crear una página de errores en el mismo formato que el resto de los sitios web. Para ello, consulte [Create Application Gateway custom error pages](custom-error.md) (Creación de páginas de errores personalizadas de Application Gateway).|Septiembre de 2018|
|Mejoras en las métricas     |Puede obtener una mejor visión del estado de Application Gateway con métricas mejoradas. Para habilitar las métricas en Application Gateway, consulte [Mantenimiento del back-end, registros de diagnóstico y métricas de Application Gateway](application-gateway-diagnostics.md).|Junio de 2018|

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre Azure Application Gateway, consulte [¿Qué es Azure Application Gateway?](overview.md)
