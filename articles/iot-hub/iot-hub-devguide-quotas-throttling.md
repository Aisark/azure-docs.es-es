---
title: Información de la limitación y las cuotas de IoT de Azure | Microsoft Docs
description: 'Guía del desarrollador: descripción de las cuotas que se aplican a IoT Hub y comportamiento esperado de limitación'
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/05/2018
ms.openlocfilehash: 14c563fda48bc7c54dcce1e1ccbfba716b6c73ad
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "60201404"
---
# <a name="reference---iot-hub-quotas-and-throttling"></a>Referencia: Cuotas y limitación de IoT Hub

## <a name="quotas-and-throttling"></a>Cuotas y limitación

Cada suscripción de Azure puede tener como máximo cincuenta centros de IoT y al menos un centro gratuito.

Cada instancia de IoT Hub se aprovisiona con un determinado número de unidades en un nivel determinado. El nivel y el número de unidades determinan la cuota diaria máxima de mensajes que puede enviar. El tamaño de mensaje que se usa para calcular la cuota diaria es 0,5 KB para un centro de nivel gratuito y de 4 KB para todos los otros niveles. Para más información, consulte [Precios de Azure IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub/).

El nivel también determina los valores de limitación que aplica Azure IoT Hub a las operaciones.

## <a name="operation-throttles"></a>Limitaciones de operación

Las limitaciones de operación son las limitaciones de velocidad que se aplican en los intervalos de minutos y están diseñadas para evitar abusos. IoT Hub intenta evitar los errores de devolución siempre que sea posible, pero inicia la devolución de `429 ThrottlingException` si se infringe la limitación durante demasiado tiempo.

En cualquier momento, puede aumentar las cuotas o las limitaciones si aumenta el número de unidades aprovisionadas en una instancia de IoT Hub.

En la tabla siguiente se muestran las limitaciones exigidas. Los valores hacen referencia a un centro individual.

| Limitación | Gratis, S1 y B1 | B2 y S2 | B3 y S3 | 
| -------- | ------- | ------- | ------- |
| Operaciones de registro de identidad (crear, recuperar, enumerar, actualizar y eliminar) | 1,67/s/unidad (100/min/unidad) | 1,67/s/unidad (100/min/unidad) | 83,33/s/unidad (5000/min/unidad) |
| Nuevas conexiones de dispositivo (este límite se aplica a la velocidad a la que se establecen _nuevas conexiones_, no al número total de conexiones) | Mayor de 100/s o 12/s/unidad <br/> Por ejemplo, dos unidades S1 son 2\*12 = 24 nuevas conexiones por segundo, pero dispone de al menos 100 nuevas conexiones por segundo entre todas las unidades. Con nueve unidades S1 tiene 108 nuevas conexiones por segundo (9\*12) entre todas las unidades. | 120 conexiones nuevas por segundo por unidad | 6000 conexiones nuevas por segundo por unidad |
| Envíos de dispositivo a nube | Mayor de 100/s o 12/s/unidad <br/> Por ejemplo, dos unidades S1 equivalen a 2\*12 = 24/s, pero tendrá al menos 100/s en todas las unidades. Con nueve unidades S1 tiene 108/s (9\*12) en las unidades. | 120/s/unidad | 6000/s/unidad |
| Envíos de nube a dispositivo<sup>1</sup> | 1,67/s/unidad (100/min/unidad) | 1,67/s/unidad (100/min/unidad) | 83,33/s/unidad (5000/min/unidad) |
| Recepciones de nube a dispositivo<sup>1</sup> <br/> (solo cuando el dispositivo usa HTTPS)| 16,67/s/unidad (1000/min/unidad) | 16,67/s/unidad (1000/min/unidad) | 833,33/s/unidad (50000/min/unidad) |
| Carga de archivos | 1,67 notificaciones de cargas de archivos/s/unidad (100/min/unidad) | 1,67 notificaciones de cargas de archivos/s/unidad (100/min/unidad) | 83,33 notificaciones de cargas de archivos/s/unidad (5000/min/unidad) |
| Métodos directos<sup>1</sup> | 160 MB/s/unidad<sup>2</sup> | 480 MB/s/unidad<sup>2</sup> | 24 MB/s/unidad<sup>2</sup> | 
| Consultas | 20/min/unidad | 20/min/unidad | 1000/min/unidad |
| Lecturas de (dispositivos y módulos) gemelos<sup>1</sup> | 100/seg. | Mayor de 100/s o 10/s/unidad | 500/s/unidad |
| Actualizaciones de (dispositivos y módulos) gemelos<sup>1</sup> | 50/seg. | Mayor de 50/s o 5/s/unidad | 250/s/unidad |
| Operaciones de trabajos<sup>1,3</sup> <br/> (crear, actualizar, enumerar, eliminar) | 1,67/s/unidad (100/min/unidad) | 1,67/s/unidad (100/min/unidad) | 83,33/s/unidad (5000/min/unidad) |
| Operaciones de dispositivo de trabajos<sup>1</sup> <br/> (actualizar gemelos, invocar método directo) | 10/s | Mayor de 10/s o 1/s/unidad | 50/s/unidad |
| Configuraciones e implementaciones perimetrales<sup>1</sup> <br/> (crear, actualizar, enumerar, eliminar) | 0,33/s/unidad (20/m/unidad) | 0,33/s/unidad (20/m/unidad) | 0,33/s/unidad (20/m/unidad) |
| Velocidad de iniciación de secuencia de dispositivos<sup>4</sup> | 5 nuevas secuencias/s | 5 nuevas secuencias/s | 5 nuevas secuencias/s |
| Número máximo de secuencias de dispositivos conectados simultáneamente<sup>4</sup> | 50 | 50 | 50 |
| Nivel máximo de transferencia de datos de secuencia de dispositivos<sup>4</sup> (volumen agregado por día) | 300 MB | 300 MB | 300 MB |


<sup>1</sup>Esta característica no está disponible en el nivel básico de IoT Hub. Para más información, consulte [Elección de la instancia de IoT Hub correcta](iot-hub-scaling.md). <br/><sup>2</sup>limitación de tamaño del medidor es de 4 KB. <br/><sup>3</sup>Solo puede tener un trabajo activo de importación o exportación de dispositivos a la vez. <br/><sup>4</sup>Las secuencias de dispositivo IoT Hub solo están disponibles para los SKU de S1, S2, S3 y F1.

La limitación de las *conexiones de dispositivo* determina la velocidad a la que se pueden establecer nuevas conexiones de dispositivo con una instancia de IoT Hub. La limitación de las *conexiones de dispositivo* no controla el número máximo de dispositivos conectados a la vez. La limitación de *conexiones de dispositivo* depende del número de unidades aprovisionadas para el centro de IoT.

Por ejemplo, si compra una sola unidad S1, tendrá una limitación de 100 conexiones por segundo. Por lo tanto, para conectar 100 000 dispositivos, se tarda al menos 1000 segundos (aproximadamente 16 minutos). Sin embargo, puede tener el mismo número de dispositivos conectados al mismo tiempo que de dispositivos registrados en el registro de identidad.

Consulte la entrada de blog [IoT Hub throttling and you](https://azure.microsoft.com/blog/iot-hub-throttling-and-you/) (Limitación de IoT Hub) para ver una explicación detallada del comportamiento de limitación de IoT Hub.

> [!IMPORTANT]
> Las operaciones de registro de identidad están diseñadas para usarse en tiempo de ejecución en escenarios de administración y aprovisionamiento de dispositivos. La lectura o actualización de un gran número de identidades de dispositivo se realiza mediante [trabajos de importación y exportación](iot-hub-devguide-identity-registry.md#import-and-export-device-identities).
> 
> 

## <a name="other-limits"></a>Otros límites

IoT Hub exige otros límites operativos:

| Operación | Límite |
| --------- | ----- |
| URI de carga de archivos | 10000 URI de SAS pueden estar fuera para una cuenta de almacenamiento al mismo tiempo. <br/>  10 URI/dispositivo de SAS puede estar fuera al mismo tiempo. |
| Trabajos<sup>1</sup> | El historial de trabajos se conserva durante 30 días como máximo. <br/> El número máximo de trabajos simultáneos es 1 para gratuitos y S1, 5 para S2 y 10 para S3. |
| Puntos de conexión adicionales | Los centros de SKU de pago pueden tener 10 puntos de conexión adicionales. Los centros de SKU gratis pueden tener un punto de conexión adicional. |
| Reglas de enrutamiento de mensajes | Los centros de SKU de pago pueden tener 100 reglas de enrutamiento. Los centros de SKU gratis pueden tener cinco reglas de enrutamiento. |
| Mensajería de un dispositivo a la nube | Tamaño máximo del mensaje 256 KB |
| Mensajería de la nube a un dispositivo<sup>1</sup> | Tamaño máximo del mensaje 64 KB. El número máximo de mensajes pendientes para la entrega es 50. |
| Método directo<sup>1</sup> | El tamaño de carga máximo del método directo es de 128 KB. |
| Configuraciones automáticas de dispositivos<sup>1</sup> | 100 configuraciones por centro de SKU de pago. 20 configuraciones por centro de SKU gratis. |
| Implementaciones automáticas de Edge<sup>1</sup> | 20 módulos por implementación. 100 implementaciones por centro de SKU de pago. 20 implementaciones por centro de SKU gratis. |
| Gemelos<sup>1</sup> | El tamaño máximo por sección de gemelos (etiquetas, propiedades deseadas y propiedades notificadas) es de 8 KB. |

<sup>1</sup>Esta característica no está disponible en el nivel básico de IoT Hub. Para más información, consulte [Elección de la instancia de IoT Hub correcta](iot-hub-scaling.md).

> [!NOTE]
> Actualmente, el número máximo de dispositivos que pueden conectarse a un solo centro de IoT es de 1 000 000. Si quiere aumentar este límite, póngase en contacto con el [soporte técnico de Microsoft](https://azure.microsoft.com/support/options/).

## <a name="latency"></a>Latencia
IoT Hub se esfuerza por proporcionar una latencia baja para todas las operaciones. Pero debido a las condiciones de la red y otros factores impredecibles no puede garantizar una latencia máxima. Cuando diseñe la solución, debería:

* Evitar realizar suposiciones sobre la latencia máxima de cualquier operación de IoT Hub.
* Aprovisionar el IoT Hub en la región de Azure más cercana a los dispositivos.
* Considere la posibilidad de usar Azure IoT Edge para realizar operaciones sensibles a la latencia en el dispositivo o en una puerta de enlace próxima al dispositivo.

Muchas unidades de IoT Hub afectan a la limitación, tal como se ha descrito anteriormente, pero no proporcionan ventajas ni garantías de latencia adicionales.

Si ve aumentos inesperados de la latencia de operación, póngase en contacto con el [Soporte técnico de Microsoft](https://azure.microsoft.com/support/options/).

## <a name="next-steps"></a>Pasos siguientes

Otros temas de referencia en la Guía del desarrollador de IoT Hub son:

* [Puntos de conexión de IoT Hub](iot-hub-devguide-endpoints.md)
