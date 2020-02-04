---
title: Admisión de protocolos para encabezados HTTP en Azure Front Door Service | Microsoft Docs
description: En este artículo se describen los protocolos de encabezado HTTP que admite Front Door Service.
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: 7c77527b7300c1149e96c94a4dbe122da226ac6d
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720442"
---
# <a name="protocol-support-for-http-headers-in-azure-front-door-service"></a>Admisión de protocolos para encabezados HTTP en Azure Front Door Service
Este artículo describe el protocolo que admite el servicio Front Door Service con partes de la ruta de acceso de llamada (consulte la imagen). Las secciones siguientes proporcionan más información acerca de los encabezados HTTP admitidos por el servicio Front Door Service.

![Protocolo de encabezados HTTP de Azure Front Door Service][1]

>[!IMPORTANT]
>Front Door Service no certifica ningún encabezado HTTP que no se documente aquí.

## <a name="client-to-front-door-service"></a>Cliente para Front Door Service
Front Door Service acepta la mayoría de los encabezados de la solicitud entrante sin tener que modificarlos. Algunos encabezados reservados se quitan de la solicitud entrante si se envían, incluidos los encabezados con el prefijo X-FD-*.

## <a name="front-door-service-to-backend"></a>Front Door Service para back-end

Front Door Service incluye encabezados de una solicitud entrante a menos que se quite debido a las restricciones. Front Door también agrega los encabezados siguientes:

| Encabezado  | Ejemplo y descripción |
| ------------- | ------------- |
| Via |  Via: 1.1 Azure </br> Front Door agrega la versión del cliente HTTP seguida de *Azure* como valor del encabezado Via. Esto indica la versión HTTP del cliente y que Front Door era un destinatario intermedio de la solicitud entre el cliente y el servidor back-end.  |
| X-Azure-ClientIP | X-Azure-ClientIP: 127.0.0.1 </br> Representa la dirección IP de cliente asociada con la solicitud que se está procesando. Por ejemplo, una solicitud procedente de un servidor proxy puede agregar el encabezado X-Forwarded-For para indicar la dirección IP del autor de la llamada original. |
| X-Azure-SocketIP |  X-Azure-SocketIP: 127.0.0.1 </br> Representa la dirección IP del socket asociada con la conexión TCP de la que se originó la solicitud actual. Una dirección IP del cliente de la solicitud podría no ser igual a su dirección IP de socket porque se puede sobrescribir arbitrariamente por un usuario.|
| X-Azure-Ref |  X-Azure-Ref: 0zxV+XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz </br> Una cadena de referencia única que identifica una solicitud atendida por Front Door. Se usa para buscar los registros de acceso y es esencial para la solución de problemas.|
| X-Azure-RequestChain |  X-Azure-RequestChain: hops=1 </br> Un encabezado que Front Door usa para detectar bucles de solicitudes y los usuarios no deben generar ninguna dependencia de él. |
| X-Forwarded-For | X-Forwarded-For: 127.0.0.1 </br> A menudo, el campo de encabezado X-Forwarded-For (XFF) identifica la dirección IP de origen de un cliente que se conecta a un servidor web a través de un equilibrador de carga o proxy HTTP. Si hay un encabezado XFF existente, Front Door le anexa la dirección IP de socket de cliente o agrega el encabezado XFF con la dirección IP de socket de cliente. |
| X-Forwarded-Host | X-Forwarded-Host: contoso.azurefd.net </br> El campo de encabezado HTTP X-Forwarded-Host es un método común utilizado para identificar el host original solicitado por el cliente en el encabezado de solicitud HTTP del host. Esto es porque el nombre de host de Front Door puede diferir del servidor back-end que controla la solicitud. |
| X-Forwarded-Proto | X-Forwarded-Proto: http </br> El campo de encabezado HTTP X-Forwarded-Proto a menudo se usa para identificar el protocolo de origen de una solicitud HTTP porque Front Door, según la configuración, podría comunicarse con el back-end a través de HTTPS. Esto es cierto incluso si la solicitud para el proxy inverso es HTTP. |
| X-FD-HealthProbe | El campo de encabezado HTTP X-FD-HealthProbe se usa para identificar el sondeo de estado de Front Door. Si este encabezado se establece en 1, la solicitud es el sondeo de estado. Puede usarse cuando desee establecer un acceso estricto desde una instancia determinada de Front Door con el campo de encabezado X-Forwarded-Host. |

## <a name="front-door-service-to-client"></a>Front Door Service para cliente

Los encabezados enviados a Front Door desde el back-end se pasan también al cliente. Los siguientes son los encabezados que se envían desde Front Door a los clientes.

| Encabezado  | Ejemplo |
| ------------- | ------------- |
| X-Azure-Ref |  *X-Azure-Ref: 0zxV+XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz* </br> Esta es una cadena de referencia única que identifica una solicitud atendida por Front Door. Es esencial para solucionar problemas, ya que se utiliza para buscar los registros de acceso.|

## <a name="next-steps"></a>Pasos siguientes

- [Cree una instancia de Front Door](quickstart-create-front-door.md)
- Información acerca de cómo [funciona Front Door](front-door-routing-architecture.md)

<!--Image references-->
[1]: ./media/front-door-http-headers-protocol/front-door-protocol-summary.png
