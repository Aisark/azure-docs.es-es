---
title: 'Capacidad de prueba: Comunicación del servicio | Microsoft Docs'
description: La comunicación entre servicios es un punto crítico de integración de una aplicación de Service Fabric. En este artículo se describen las consideraciones de diseño y las técnicas de prueba.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: 017557df-fb59-4e4a-a65d-2732f29255b8
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 529c8d74b6e0a63a7969f31d5b5e8073ecb79411
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/29/2019
ms.locfileid: "58665753"
---
# <a name="service-fabric-testability-scenarios-service-communication"></a>Escenarios de testability de Service Fabric: Comunicación de servicio
Los microservicios y los estilos de arquitectura orientados a servicios emergen naturalmente en Service Fabric de Azure. En estos tipos de arquitecturas distribuidas, las aplicaciones de microservicio divididas en componentes suelen constar de varios servicios que necesitan comunicarse entre sí. Incluso en los casos más simples, por lo general dispone al menos de un servicio web sin estado y de un servicio de almacenamiento de datos con estado que necesitan comunicarse.

La comunicación entre servicios es un punto crítico de integración de una aplicación, ya que cada servicio expone una API remota a otros servicios. El trabajo con un conjunto de límites de API que implican E/S requiere generalmente tomar algunas precauciones y realizar una buena cantidad de pruebas y validaciones.

Existen numerosas consideraciones que deben tenerse en cuenta cuando estos límites de servicio se conectan entre sí en un sistema distribuido:

* *Protocolo de transporte*. ¿Se utilizará HTTP para una mayor interoperabilidad, o bien un protocolo binario personalizado para el máximo rendimiento?
* *Control de errores*. ¿Cómo se controlarán los errores transitorios y permanentes? ¿Qué sucederá cuando un servicio se mueva a un nodo diferente?
* *Tiempos de espera y latencia*. En las aplicaciones de varios niveles, ¿cómo controlará cada capa de servicio la latencia a través de la pila hasta el usuario?

Tanto si utiliza uno de los componentes de comunicación de servicio integrados que aporta Service Fabric como si crea uno propio, la prueba de las interacciones entre los servicios es fundamental para garantizar la resistencia de la aplicación.

## <a name="prepare-for-services-to-move"></a>Preparación para el desplazamiento de los servicios
Las instancias de los servicios pueden desplazarse con el tiempo. Esto ocurre especialmente cuando se configuran con métricas de carga para el equilibrio óptimo personalizado de los recursos. Service Fabric mueve las instancias de servicio para maximizar su disponibilidad incluso durante las actualizaciones, las conmutaciones por error, el escalado horizontal y otras situaciones diversas que se producen durante el ciclo de vida de un sistema distribuido.

Dado que los servicios se desplazan en el clúster, hay dos escenarios que los clientes y otros servicios deben estar preparados para controlar al comunicarse con un servicio:

* La instancia de servicio o la réplica de partición se han movido desde la última comunicación con ellas. Esto forma parte normal del ciclo de vida de un servicio y se espera que suceda mientras dure la vigencia de la aplicación.
* La instancia de servicio o la réplica de partición están en un proceso de movimiento. Aunque la conmutación por error de un servicio de un nodo a otro se realiza rápidamente en Service Fabric, puede haber un retraso en la disponibilidad si el componente de comunicación del servicio tarda en iniciarse.

El control correcto de estas situaciones es importante para lograr que un sistema funcione sin problemas. Para ello, tenga en cuenta que:

* Todos los servicios con los que se puede conectar tienen una *dirección* en la que escucha (por ejemplo, HTTP o WebSockets ). Cuando una instancia del servicio o una partición se desplaza, cambia el punto de conexión de su dirección. (Se mueve a otro nodo con una dirección IP diferente). Si utiliza los componentes de comunicación integrados, ellos se ocuparán de volver a resolver las direcciones del servicio.
* Puede haber un aumento temporal de latencia del servicio mientras la instancia de servicio inicia de nuevo su agente de escucha. Esto depende de la rapidez con que el servicio abra el agente de escucha después de mover la instancia de servicio.
* Las conexiones existentes deben cerrarse y volverse a abrir después de que el servicio se haya abierto en un nuevo nodo. Un cierre o un reinicio estables del nodo permiten que se produzca un cierre estable de las conexiones existentes.

### <a name="test-it-move-service-instances"></a>Probarlo: Mover las instancias de servicio
Mediante las herramientas de capacidad de prueba de Service Fabric, es posible crear un escenario de prueba para probar estas situaciones de maneras diferentes:

1. Mover la réplica principal de un servicio con estado.
   
    La réplica principal de una partición de servicio con estado se pueden mover por diversos motivos. Utilice la siguiente línea para establecer como objetivo la réplica principal de una partición específica y ver cómo los servicios reaccionan al movimiento de manera controlada.
   
    ```powershell
   
    PS > Move-ServiceFabricPrimaryReplica -PartitionId 6faa4ffa-521a-44e9-8351-dfca0f7e0466 -ServiceName fabric:/MyApplication/MyService
   
    ```
2. Detener un nodo.
   
    Cuando se detiene un nodo, Service Fabric mueve todas las instancias de servicio o las particiones incluidas en ese nodo a uno de los otros nodos disponibles del clúster. Utilice lo siguiente para probar un situación en la que se pierde un nodo del clúster y se deben mover todas las instancias de servicio y las réplicas de dicho nodo.
   
    Puede detener un nodo con el cmdlet **Stop-ServiceFabricNode** de PowerShell:
   
    ```powershell
   
    PS > Stop-ServiceFabricNode -NodeName Node_1
   
    ```

## <a name="maintain-service-availability"></a>Mantener la disponibilidad del servicio
Como plataforma, Service Fabric está diseñado para ofrecer una alta disponibilidad de los servicios. Sin embargo, los problemas de infraestructura subyacentes pueden provocar que no exista disponibilidad en casos extremos. Es importante probar también estos escenarios.

Los servicios con estado utilizan un sistema basado en cuórum para la replicación de estado, lo que permite ofrecer una alta disponibilidad. Esto significa que debe haber un cuórum de réplicas disponible para realizar operaciones de escritura. En casos excepcionales, como en un error generalizado de hardware, puede que no esté disponible el cuórum de réplicas. En estos casos, no podrá realizar operaciones de escritura, pero sí podrá realizar operaciones de lectura.

### <a name="test-it-write-operation-unavailability"></a>Probarlo: No disponibilidad de la operación de escritura
Mediante las herramientas de capacidad de prueba de Service Fabric, puede insertar un error que provoca la pérdida de cuórum como prueba. Aunque una situación de este tipo es poco habitual, es importante que los clientes y los servicios que dependen de un servicio con estado estén preparados para controlar situaciones en las que no pueden realizar solicitudes de escritura al mismo. También es importante que el propio servicio con estado sea consciente de esta posibilidad y pueda comunicarla correctamente a los autores de llamadas.

Puede provocar la pérdida de cuórum con el cmdlet **Invoke-ServiceFabricPartitionQuorumLoss** de PowerShell:

```powershell

PS > Invoke-ServiceFabricPartitionQuorumLoss -ServiceName fabric:/Myapplication/MyService -QuorumLossMode QuorumReplicas -QuorumLossDurationInSeconds 20

```

En este ejemplo, `QuorumLossMode` se establece en `QuorumReplicas` para indicar que queremos provocar la pérdida de quórum sin desconectar todas las réplicas. De este modo, las operaciones de lectura sigan siendo posibles. Para probar un escenario en el que no esté disponible una partición completa, puede establecer este modificador en `AllReplicas`.

## <a name="next-steps"></a>Pasos siguientes
[Más información sobre las acciones de capacidad de prueba](service-fabric-testability-actions.md)

[Más información sobre los escenarios de capacidad de prueba](service-fabric-testability-scenarios.md)

