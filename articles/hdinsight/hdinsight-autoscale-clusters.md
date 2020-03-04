---
title: Escalado automático de clústeres de Azure HDInsight
description: Use la característica de escalabilidad automática de Azure HDInsight para escalar clústeres de Apache Hadoop automáticamente.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/21/2020
ms.openlocfilehash: 6eb8f86d7bfa1c140c6422753840ded8a37ce3c4
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/26/2020
ms.locfileid: "77616091"
---
# <a name="automatically-scale-azure-hdinsight-clusters"></a>Escalado automático de clústeres de Azure HDInsight

> [!Important]
> La característica de escalado automático de Azure HDInsight se lanzó con carácter general el 7 de noviembre de 2019 para los clústeres de Spark y Hadoop, e incluía mejoras que no están disponibles en la versión preliminar de la característica. Si creó un clúster de Spark antes del 7 de noviembre de 2019 y quiere usar la característica de escalado automático en el clúster, la ruta recomendada es crear un nuevo clúster y habilitar el escalado automático en el nuevo clúster. 
>
>El escalado automático para los clústeres de Interactive Query (LLAP) y HBase todavía está en versión preliminar. El escalado automático solo está disponible en los clústeres de Spark, Hadoop, Interactive Query y HBase. 


La característica de escalabilidad automática de clústeres de Azure HDInsight escala o reduce verticalmente el número de nodos de trabajo. Actualmente no se pueden escalar otros tipos de nodos del clúster.  Durante la creación de un nuevo clúster de HDInsight, se puede establecer un número mínimo y máximo de nodos de trabajo. La escalabilidad automática luego supervisa los requisitos de recursos de la carga de análisis y escala o reduce verticalmente el número de nodos de trabajo. El uso de esta característica no tiene un cargo adicional.

## <a name="cluster-compatibility"></a>Compatibilidad con el clúster

En la tabla siguiente se describen las versiones y los tipos de clúster que son compatibles con la característica de escalabilidad automática.

| Versión | Spark | Hive | LLAP | HBase | Kafka | Storm | ML |
|---|---|---|---|---|---|---|---|
| HDInsight 3.6 sin ESP | Sí | Sí | Sí | Sí* | Sin | Sin | Sin |
| HDInsight 4.0 sin ESP | Sí | Sí | Sí | Sí* | Sin | Sin | Sin |
| HDInsight 3.6 sin ESP | Sí | Sí | Sí | Sí* | Sin | Sin | Sin |
| HDInsight 4.0 con ESP | Sí | Sí | Sí | Sí* | Sin | Sin | Sin |

\* Los clústeres de HBase solo se pueden configurar para escalado basado en programación, no basado en cargas.

## <a name="how-it-works"></a>Funcionamiento

Puede elegir el escalado basado en carga o el escalado basado en programación para el clúster de HDInsight. El escalado basado en carga cambia el número de nodos del clúster, dentro de un intervalo establecido, para garantizar la utilización de CPU óptima y minimizar el costo de ejecución.

El escalado basado en programación cambia el número de nodos del clúster en función de las condiciones que se aplican en momentos específicos. Estas condiciones escalan el clúster a un número deseado de nodos.

### <a name="metrics-monitoring"></a>Supervisión de métricas

La escalabilidad automática supervisa continuamente el clúster y recopila las métricas siguientes:

|Métrica|Descripción|
|---|---|
|Total de CPU pendiente|El número total de núcleos necesarios para iniciar la ejecución de todos los contenedores pendientes.|
|Total de memoria pendiente|La memoria total (en MB) necesaria para iniciar la ejecución de todos los contenedores pendientes.|
|Total de CPU libre|La suma de todos los núcleos sin usar en los nodos de trabajo activos.|
|Total de memoria libre|La suma de la memoria sin usar (en MB) en los nodos de trabajo activos.|
|Memoria usada por nodo|La carga en un nodo de trabajo. Un nodo de trabajo donde se usan 10 GB de memoria se considera bajo más carga que un trabajo con 2 GB de memoria usada.|
|Número de patrones de aplicación por nodo|El número de contenedores de patrones de aplicación (AM) que se ejecutan en un nodo de trabajo. Un nodo de trabajo que hospeda dos contenedores de AM se considera más importante que un nodo de trabajo que no hospeda ninguno de estos contenedores.|

Las métricas anteriores se comprueban cada 60 segundos. La escalabilidad automática toma decisiones de escalar o reducir verticalmente según estas métricas.

### <a name="load-based-cluster-scale-up"></a>Escalado vertical de clústeres basados en carga

Cuando se detectan las condiciones siguientes, Escalabilidad automática emitirá un solicitud de escalado vertical:

* El total de CPU pendiente es mayor que el total de CPU libre durante más de 3 minutos.
* El total de memoria pendiente es mayor que el total de memoria libre durante más de 3 minutos.

El servicio de HDInsight calcula cuántos nodos de trabajo nuevos se necesitan para cumplir con los requisitos actuales de CPU y memoria y, luego, emite una solicitud de escalado vertical para agregar el número de nodos necesario.

### <a name="load-based-cluster-scale-down"></a>Reducción vertical de clústeres basados en carga

Cuando se detectan las condiciones siguientes, Escalabilidad automática emitirá un solicitud de reducción vertical:

* El total de CPU pendiente es menor que el total de CPU libre durante más de 10 minutos.
* El total de memoria pendiente es menor que el total de memoria libre durante más de 10 minutos.

En función del número de contenedores de AM por nodo y de los requisitos actuales de CPU y memoria, Escalabilidad automática emite una solicitud para quitar un número determinado de nodos. El servicio detecta también qué nodos son candidatos para la eliminación en función de la ejecución del trabajo actual. En primer lugar, la operación de reducción vertical retira los nodos y, luego, los quita del clúster.

## <a name="get-started"></a>Introducción

### <a name="create-a-cluster-with-load-based-autoscaling"></a>Creación de un clúster con Escalabilidad automática basada en carga

Para habilitar la característica Escalabilidad automática con escalado basado en carga, complete estos pasos como parte del proceso de creación de clúster normal:

1. En la pestaña **Configuración y precios**, marque la casilla **Habilitar escalabilidad automática**.
1. Seleccione **Load-based** (Basada en carga) en **Tipo de escalabilidad automática**.
1. Escriba los valores deseados para estas propiedades:  

    * **Número de nodos** inicial para **Nodo de trabajo**.
    * Número **Mín.** de nodos de trabajo.
    * Número **Máx.** de nodos de trabajo.

    ![Habilitación de la escalabilidad automática basada en carga del nodo de trabajo](./media/hdinsight-autoscale-clusters/azure-portal-cluster-configuration-pricing-autoscale.png)

El número inicial de nodos de trabajo debe estar comprendido entre los valores mínimo y máximo, ambos inclusives. Este valor define el tamaño inicial del clúster durante su creación. El número mínimo de nodos de trabajo debe establecerse en tres o más. El escalado del clúster a menos de tres nodos puede hacer que se quede atascado en el modo seguro debido a una replicación de archivos insuficiente.  Para obtener más información, consulte [Bloqueo en modo seguro](./hdinsight-scaling-best-practices.md#getting-stuck-in-safe-mode).

### <a name="create-a-cluster-with-schedule-based-autoscaling"></a>Creación de un clúster con Escalabilidad automática basada en programación

Para habilitar la característica Escalabilidad automática con escalado basado en programación, complete estos pasos como parte del proceso de creación de clúster normal:

1. En la pestaña **Configuración y precios**, active la casilla **Habilitar escalabilidad automática**.
1. Especifique el **Número de nodos** para **Nodo de trabajo**, que controla el límite para escalar verticalmente el clúster.
1. Seleccione la opción **Schedule-based** (Basada en programación) en **Tipo de escalabilidad automática**.
1. Seleccione **Configurar** para abrir la ventana **Configuración de la escalabilidad automática**.
1. Seleccione la zona horaria y haga clic en **+ Agregar condición**.
1. Seleccione los días de la semana en que se deberá aplicar la condición nueva.
1. Edite la hora en que debería aplicarse la condición y el número de nodos a los que se debe escalar el clúster.
1. Si es necesario, agregue más condiciones.

    ![Habilitación de la creación basada en programación del nodo de trabajo](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-schedule-creation.png)

El número de nodos debe estar entre 3 y el número máximo de nodos de trabajo especificado antes de agregar condiciones.

### <a name="final-creation-steps"></a>Pasos finales de creación

Tanto para el escalado basado en carga como el basado en programación, seleccione el tipo de máquina virtual de los nodos de trabajo al seleccionar una máquina virtual en la lista desplegable de **Tamaño del nodo**. Después de elegir el tipo de máquina virtual para cada tipo de nodo, puede ver el intervalo de costo estimado para todo el clúster. Ajuste los tipos de máquina virtual que se ajusten a su presupuesto.

![Habilitación de la escalabilidad automática del tamaño del nodo basada en la programación del nodo de trabajo](./media/hdinsight-autoscale-clusters/azure-portal-cluster-configuration-pricing-vmsize.png)

La suscripción tiene una cuota de capacidad para cada región. El número total de núcleos de los nodos principales junto con el número máximo de nodos de trabajo no puede superar la cuota de capacidad. Sin embargo, esta cuota tiene un límite flexible; sencillamente puede crear una incidencia de soporte técnico en cualquier momento para que la aumenten.

> [!Note]  
> Si se supera el límite de cuota de núcleos total, recibirá un mensaje de error que dice "the maximum node exceeded the available cores in this region, please choose another region or contact the support to increase the quota" (El nodo máximo superó los núcleos disponibles en esta región, elija otra región o póngase en contacto con soporte técnico para aumentar la cuota).

Para obtener más información sobre la creación de clústeres de HDInsight con Azure Portal, consulte [Crear clústeres basados en Linux en HDInsight con Azure Portal](hdinsight-hadoop-create-linux-clusters-portal.md).  

### <a name="create-a-cluster-with-a-resource-manager-template"></a>Creación de un clúster con una plantilla del Administrador de recursos

#### <a name="load-based-autoscaling"></a>Escalado automático basado en carga

Para crear un clúster de HDInsight con el escalado automático basado en carga de una plantilla de Azure Resource Manager, agregue un nodo `autoscale` a la sección `computeProfile` > `workernode` con las propiedades `minInstanceCount` y `maxInstanceCount`, tal como se muestra en el fragmento de código JSON a continuación.

```json
{
  "name": "workernode",
  "targetInstanceCount": 4,
  "autoscale": {
      "capacity": {
          "minInstanceCount": 3,
          "maxInstanceCount": 10
      }
  },
  "hardwareProfile": {
      "vmSize": "Standard_D13_V2"
  },
  "osProfile": {
      "linuxOperatingSystemProfile": {
          "username": "[parameters('sshUserName')]",
          "password": "[parameters('sshPassword')]"
      }
  },
  "virtualNetworkProfile": null,
  "scriptActions": []
}
```

Para más información sobre cómo crear clústeres con plantillas de Resource Manager, consulte [Creación de clústeres de Apache Hadoop basados en Windows en HDInsight mediante plantillas de Azure Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md).  

#### <a name="schedule-based-autoscaling"></a>Escalado automático basado en programación

Para crear un clúster de HDInsight con el escalado automático basado en programación de una plantilla de Azure Resource Manager, agregue un nodo `autoscale` a la sección `computeProfile` > `workernode`. El nodo `autoscale` contiene una `recurrence` que tiene una `timezone` y una `schedule` que describe cuándo se aplicará el cambio.

```json
{
  "autoscale": {
    "recurrence": {
      "timeZone": "Pacific Standard Time",
      "schedule": [
        {
          "days": [
            "Monday",
            "Tuesday",
            "Wednesday",
            "Thursday",
            "Friday"
          ],
          "timeAndCapacity": {
            "time": "11:00",
            "minInstanceCount": 10,
            "maxInstanceCount": 10
          }
        },
      ]
    }
  },
  "name": "workernode",
  "targetInstanceCount": 4,
}
```

### <a name="enable-and-disable-autoscale-for-a-running-cluster"></a>Habilitación y deshabilitación de escalabilidad automática para un clúster en ejecución

#### <a name="using-the-azure-portal"></a>Uso de Azure Portal

Para habilitar la escalabilidad automática en un clúster en ejecución, seleccione **Tamaño del clúster** en **Configuración**. Después, seleccione **Habilitar escalado automático**. Seleccione el tipo de escalabilidad automática que quiere y especifique las opciones de escalado basado en carga o basado en programación. Por último, seleccione **Guardar**.

![Habilitación de la escalabilidad automática de la ejecución del clúster basada en programación del nodo de trabajo](./media/hdinsight-autoscale-clusters/azure-portal-settings-autoscale.png)

#### <a name="using-the-rest-api"></a>Uso de la API de REST

Para habilitar o deshabilitar el escalado automático en un clúster en ejecución mediante la API REST, realice una solicitud POST al punto de conexión de escalado automático, tal como se muestra en el siguiente fragmento de código:

```
https://management.azure.com/subscriptions/{subscription Id}/resourceGroups/{resourceGroup Name}/providers/Microsoft.HDInsight/clusters/{CLUSTERNAME}/roles/workernode/autoscale?api-version=2018-06-01-preview
```

Use los parámetros adecuados en la carga de solicitud. La carga de JSON siguiente podría usarse para habilitar el escalado automático. Use la carga `{autoscale: null}` para deshabilitar el escalado automático.

```json
{ autoscale: { capacity: { minInstanceCount: 3, maxInstanceCount: 2 } } }
```

Vea la sección anterior sobre [cómo habilitar el escalado automático basado en la carga](#load-based-autoscaling) para obtener una descripción completa de todos los parámetros de carga.

## <a name="best-practices"></a>Procedimientos recomendados

### <a name="choosing-load-based-or-schedule-based-scaling"></a>Elección del escalado basado en carga o basado en programación

Tenga en cuenta estos factores antes de tomar una decisión con respecto al modo que va a elegir:

* Habilite la escalabilidad automática durante la creación del clúster.
* El número mínimo de nodos debe ser tres.
* Varianza de carga: si la carga del clúster sigue un patrón coherente a horas específicas en días específicos. Si no es así, la programación basada en carga es una mejor opción.
* Requisitos de SLA: el escalado de escalabilidad automática es reactivo en lugar de predictivo. ¿Habrá una demora suficiente entre el momento en que la carga empieza a aumentar y el momento en que el clúster tiene que alcanzar su tamaño de destino? Si los requisitos de SLA son estrictos y la carga es un patrón fijo conocido, la mejor opción es "basado en programación".

### <a name="consider-the-latency-of-scale-up-or-scale-down-operations"></a>Consideración de la latencia de las operaciones de escalado o reducción verticales

Una operación de escalado puede tardar entre 10 y 20 minutos en completarse. Cuando configure una programación personalizada, planee esta demora. Por ejemplo, si necesita que el tamaño del clúster sea 20 a las 9:00 a.m., establezca el desencadenador de programación más temprano, a eso de las 8:30 a.m., para que la operación de escalado se complete antes de las 9:00 a.m.

### <a name="preparation-for-scaling-down"></a>Preparación para la reducción vertical

Durante el proceso de reducción vertical de un clúster, la característica Escalabilidad automática retirará los nodos para cumplir con el tamaño de destino. Si hay tareas en ejecución en esos nodos, Escalabilidad automática esperará hasta que se completen las tareas. Como cada nodo de trabajo también tiene un rol en HDFS, los datos temporales se desplazarán a los nodos restantes. Por tanto, debe asegurarse de que haya espacio suficiente en los nodos restantes para hospedar todos los datos temporales.

Los trabajos en ejecución se seguirán ejecutando y se completarán. Los trabajos pendientes esperarán ser programados como normales con menos nodos de trabajo disponibles.

### <a name="minimum-cluster-size"></a>Tamaño mínimo del clúster

No reduzca verticalmente el clúster a menos de tres nodos. El escalado del clúster a menos de tres nodos puede hacer que se quede atascado en el modo seguro debido a una replicación de archivos insuficiente.  Para obtener más información, consulte [Bloqueo en modo seguro](./hdinsight-scaling-best-practices.md#getting-stuck-in-safe-mode).

## <a name="monitoring"></a>Supervisión

### <a name="cluster-status"></a>Estado del clúster

El estado del clúster que aparece en Azure Portal puede ayudarlo a supervisar las actividades de escalabilidad automática.

![Habilitación de la escalabilidad automática del estado del clúster basada en la carga del nodo de trabajo](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-cluster-status.png)

Todos los mensajes de estado del clúster que podría ver se explican en la lista siguiente.

| Estado del clúster | Descripción |
|---|---|
| En ejecución | El clúster funciona normalmente. Todas las actividades de Escalabilidad automática anteriores se completaron correctamente. |
| Actualizando  | La configuración de Escalabilidad automática del clúster se está actualizando.  |
| Configuración de HDInsight  | Una operación de escalado o de reducción verticales de un clúster está en curso.  |
| Error de actualización  | HDInsight encontró problemas durante la actualización de la configuración de Escalabilidad automática. Los clientes pueden elegir si reintentan la actualización o deshabilitan la escalabilidad automática.  |
| Error  | Error en el clúster, que no se puede usar. Elimine este clúster y cree uno nuevo.  |

Para ver el número actual de nodos en el clúster, vaya al gráfico **Tamaño del clúster** en la página de **Información general** del clúster, o bien seleccione **Tamaño del clúster** en **Configuración**.

### <a name="operation-history"></a>Historial de operaciones

Puede ver el historial de escalado y reducción verticales del clúster como parte de las métricas del clúster. También puede enumerar todas las acciones de escalado durante el último día, semana u otro período de tiempo.

Seleccione **Métricas** en **Supervisión**. Luego, seleccione en **Agregar métrica** y **Número de trabajos activos** en la lista desplegable **Métrica**. Seleccione el botón que se encuentra en la esquina superior derecha para cambiar el intervalo de tiempo.

![Habilitación de la escalabilidad automática de la métrica basada en programación del nodo de trabajo](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-chart-metric.png)

## <a name="next-steps"></a>Pasos siguientes

Obtenga información sobre los procedimientos recomendados para el escalado manual de clústeres en los [procedimientos recomendados de escalado](hdinsight-scaling-best-practices.md)
