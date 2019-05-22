---
title: Creación de desencadenadores de ventana de saltos de tamaño constante en Azure Data Factory | Microsoft Docs
description: Obtenga información acerca de cómo crear un desencadenador en Azure Data Factory para que ejecute una canalización en una ventana de saltos de tamaño constante.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: shlo
ms.openlocfilehash: 6fbdee71ab1123c258a5191a78e38f51eb41cbab
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/06/2019
ms.locfileid: "57433236"
---
# <a name="create-a-trigger-that-runs-a-pipeline-on-a-tumbling-window"></a>Creación de un desencadenador que ejecuta una canalización en una ventana de saltos de tamaño constante
En este artículo se explica cómo crear, iniciar y supervisar un desencadenador de ventana de saltos de tamaño constante. Para obtener información general sobre los desencadenadores y los tipos compatibles, vea [Ejecución y desencadenadores de canalización](concepts-pipeline-execution-triggers.md).

Los desencadenadores de ventana de saltos de tamaño constante son un tipo de desencadenador que se activa en un intervalo de tiempo periódico a partir de una hora de inicio especificada, mientras conserva el estado. Las ventanas de saltos de tamaño constante son una serie de intervalos de tiempo de tamaño fijo, contiguos y que no se superponen. Un desencadenador de ventana de saltos de tamaño constante tiene una relación uno a uno con una canalización y solo puede hacer referencia a una única canalización.

## <a name="data-factory-ui"></a>Interfaz de usuario de Data Factory

Para crear un desencadenador de ventana de saltos de tamaño constante en Azure Portal, seleccione **Desencadenador > Ventana de saltos de tamaño constante > Siguiente**, y luego configure las propiedades que definen la ventana de saltos de tamaño constante.

![Creación de un desencadenador de ventana de saltos de tamaño constante en Azure Portal](media/how-to-create-tumbling-window-trigger/create-tumbling-window-trigger.png)

## <a name="tumbling-window-trigger-type-properties"></a>Propiedades del tipo de desencadenador de ventana de saltos de tamaño constante
Una ventana de saltos de tamaño constante tiene las siguientes propiedades del tipo de desencadenador:

```
{
    "name": "MyTriggerName",
    "properties": {
        "type": "TumblingWindowTrigger",
        "runtimeState": "<<Started/Stopped/Disabled - readonly>>",
        "typeProperties": {
            "frequency": "<<Minute/Hour>>",
            "interval": <<int>>,
            "startTime": "<<datetime>>",
            "endTime: "<<datetime – optional>>"",
            "delay": "<<timespan – optional>>",
            “maxConcurrency”: <<int>> (required, max allowed: 50),
            "retryPolicy": {
                "count": <<int - optional, default: 0>>,
                “intervalInSeconds”: <<int>>,
            }
        },
        "pipeline": {
            "pipelineReference": {
                "type": "PipelineReference",
                "referenceName": "MyPipelineName"
            },
            "parameters": {
                "parameter1": {
                    "type": "Expression",
                    "value": "@{concat('output',formatDateTime(trigger().outputs.windowStartTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                },
                "parameter2": {
                    "type": "Expression",
                    "value": "@{concat('output',formatDateTime(trigger().outputs.windowEndTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                },
                "parameter3": "https://mydemo.azurewebsites.net/api/demoapi"
            }
        }
    }
}
```

En la tabla siguiente se muestra una descripción general de los elementos JSON más importantes relacionados con la periodicidad y la programación de un desencadenador de ventana de saltos de tamaño constante:

| Elemento JSON | DESCRIPCIÓN | Type | Valores permitidos | Obligatorio |
|:--- |:--- |:--- |:--- |:--- |
| **type** | Tipo de desencadenador. El tipo es el valor fijo "TumblingWindowTrigger". | string | "TumblingWindowTrigger" | Sí |
| **runtimeState** | Estado actual del tiempo de ejecución del desencadenador.<br/>**Nota**: Este elemento es \<readOnly>. | string | "Started," "Stopped," "Disabled" | Sí |
| **frequency** | Una cadena que representa la unidad de frecuencia (minutos u horas) con que se repite el desencadenador. Si los valores de fecha **startTime** son más granulares que el valor **frequency**, las fechas **startTime** se tienen en cuenta para calcular los límites de ventana. Por ejemplo, si el valor **frequency** es cada hora y el valor **startTime** es 2017-09-01T10:10:10Z, la primera ventana es (2017-09-01T10:10:10Z, 2017-09-01T11:10:10Z). | string | "minute", "hour"  | Sí |
| **interval** | Un entero positivo que indica el intervalo para el valor **frequency**, que determina la frecuencia con la que se ejecuta el desencadenador. Por ejemplo, si **interval** es 3 y **frequency** es "hour", el desencadenador se repite cada tres horas. | Integer | Un número entero positivo. | Sí |
| **startTime**| La primera repetición, que puede ser en el pasado. El primer intervalo de desencadenador es (**startTime**, **startTime** + **interval**). | DateTime | Un valor DateTime. | Sí |
| **endTime**| La última repetición, que puede ser en el pasado. | DateTime | Un valor DateTime. | Sí |
| **delay** | La cantidad de tiempo para retrasar el inicio del procesamiento de datos de la ventana. La ejecución de la canalización se inicia después del tiempo de ejecución esperado más el tiempo de retraso establecido en **delay**. **delay** define el tiempo de espera del desencadenador antes de desencadenar una nueva ejecución. El valor de **delay** no altera el valor de **startTime** de la ventana. Por ejemplo, un valor **delay** de 00:10:00 implica un retraso de diez minutos. | TimeSpan<br/>(hh:mm:ss)  | Un valor de intervalo de tiempo donde el valor predeterminado es 00:00:00. | Sin  |
| **maxConcurrency** | Número de ejecuciones simultáneas del desencadenador que se activan para las ventanas que están listas. Por ejemplo, reponer las ejecuciones cada hora para el día de ayer genera veinticuatro ventanas. Si **maxConcurrency** = 10, los eventos del desencadenador se activan solo para las diez primeras ventanas (00:00-01:00 - 09:00-10:00). Una vez completadas las diez primeras ejecuciones de canalización desencadenadas, se activan las ejecuciones del desencadenador para las diez siguientes (10:00-11:00 - 19:00-20:00). Siguiendo con el ejemplo de **maxConcurrency** = 10, si hay diez ventanas listas, habrá también diez ejecuciones de canalización en total. Si solo hay una ventana lista, solo se producirá una ejecución de canalización. | Integer | Un número entero comprendido entre uno y cincuenta. | Sí |
| **retryPolicy: Count** | El número de reintentos antes de que la ejecución de la canalización se marque como "error".  | Integer | Un entero, donde el valor predeterminado es 0 (ningún reintento). | Sin  |
| **retryPolicy: intervalInSeconds** | El retraso entre intentos de reintentos, especificado en segundos. | Integer | El número de segundos, donde el valor predeterminado es 30. | Sin  |

### <a name="windowstart-and-windowend-system-variables"></a>Variables del sistema WindowStart y WindowEnd

Puede usar las variables del sistema **WindowStart** y **WindowEnd** del desencadenador de ventana de saltos de tamaño constante en la definición de **pipeline** (es decir, para la parte de una consulta). Pase las variables del sistema como parámetros a la canalización en la definición de **trigger**. En el ejemplo siguiente se muestra cómo pasar estas variables como parámetros:

```
{
    "name": "MyTriggerName",
    "properties": {
        "type": "TumblingWindowTrigger",
            ...
        "pipeline": {
            "pipelineReference": {
                "type": "PipelineReference",
                "referenceName": "MyPipelineName"
            },
            "parameters": {
                "MyWindowStart": {
                    "type": "Expression",
                    "value": "@{concat('output',formatDateTime(trigger().outputs.windowStartTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                },
                "MyWindowEnd": {
                    "type": "Expression",
                    "value": "@{concat('output',formatDateTime(trigger().outputs.windowEndTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                }
            }
        }
    }
}
```

Para usar los valores de las variables del sistema **WindowStart** y **WindowEnd** en la definición de la canalización, use los parámetros "MyWindowStart" y "MyWindowEnd", según corresponda.

### <a name="execution-order-of-windows-in-a-backfill-scenario"></a>Orden de ejecución de ventanas en un escenario de reposición
Cuando hay varias ventanas abiertas para su ejecución (especialmente en un escenario de reposición), el orden de ejecución de las ventanas es determinante, del intervalo más antiguo al más nuevo. Actualmente, no se puede modificar este comportamiento.

### <a name="existing-triggerresource-elements"></a>Elementos TriggerResource existentes
Los siguientes puntos se aplican a elementos **TriggerResource** existentes:

* Si el valor del elemento **frequency** o el tamaño de la ventana del desencadenador cambia, *no* se restablece el estado de las ventanas que ya se han procesado. El desencadenador sigue activando las ventanas a partir de la última ventana que ejecutó con el uso del nuevo tamaño de ventana.
* Si el valor del elemento **endTime** del desencadenador cambia (se agrega o se actualiza), *no* se restablece el estado de las ventanas que ya se han procesado. El desencadenador respeta el nuevo valor de **endTime**. Si el nuevo valor de **endTime** es anterior a las ventanas que ya se han ejecutado, el desencadenador se detiene. En caso contrario, el desencadenador se detiene cuando se encuentra el nuevo valor de **endTime**.

## <a name="sample-for-azure-powershell"></a>Ejemplo para Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

En esta sección se muestra cómo usar Azure PowerShell para crear, iniciar y supervisar un desencadenador.

1. Cree un archivo JSON llamado **MyTrigger.json** en la carpeta C:\ADFv2QuickStartPSH\ con el siguiente contenido:

    > [!IMPORTANT]
    > Antes de guardar el archivo JSON, establezca el valor del elemento **startTime** en la hora UTC actual. Establezca el valor del elemento **endTime** en una hora anterior a la hora UTC actual.

    ```json
    {
      "name": "PerfTWTrigger",
      "properties": {
        "type": "TumblingWindowTrigger",
        "typeProperties": {
          "frequency": "Minute",
          "interval": "15",
          "startTime": "2017-09-08T05:30:00Z",
          "delay": "00:00:01",
          "retryPolicy": {
            "count": 2,
            "intervalInSeconds": 30
          },
          "maxConcurrency": 50
        },
        "pipeline": {
          "pipelineReference": {
            "type": "PipelineReference",
            "referenceName": "DynamicsToBlobPerfPipeline"
          },
          "parameters": {
            "windowStart": "@trigger().outputs.windowStartTime",
            "windowEnd": "@trigger().outputs.windowEndTime"
          }
        },
        "runtimeState": "Started"
      }
    }
    ```

2. Crear un desencadenador mediante el **conjunto AzDataFactoryV2Trigger** cmdlet:

    ```powershell
    Set-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger" -DefinitionFile "C:\ADFv2QuickStartPSH\MyTrigger.json"
    ```
    
3. Confirme que el estado del desencadenador es **detenido** utilizando el **Get AzDataFactoryV2Trigger** cmdlet:

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

4. Inicie el desencadenador mediante el **inicio AzDataFactoryV2Trigger** cmdlet:

    ```powershell
    Start-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

5. Confirme que el estado del desencadenador es **iniciado** utilizando el **Get AzDataFactoryV2Trigger** cmdlet:

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

6. Obtenga ejecuciones del desencadenador en Azure PowerShell mediante el uso de la **Get AzDataFactoryV2TriggerRun** cmdlet. Para obtener información sobre cómo se ejecuta el desencadenador, ejecute el siguiente comando periódicamente. Actualice los valores **TriggerRunStartedAfter** y **TriggerRunStartedBefore** para que coincidan con los valores de la definición del desencadenador:

    ```powershell
    Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "MyTrigger" -TriggerRunStartedAfter "2017-12-08T00:00:00" -TriggerRunStartedBefore "2017-12-08T01:00:00"
    ```
    
Para supervisar las ejecuciones del desencadenador o de la canalización en Azure Portal, consulte la sección sobre la [supervisión de ejecuciones de canalización](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).

## <a name="next-steps"></a>Pasos siguientes
Para obtener información detallada acerca de los desencadenadores, consulte el artículo [Pipeline execution and triggers](concepts-pipeline-execution-triggers.md#triggers) (Ejecución de canalizaciones y desencadenadores).
