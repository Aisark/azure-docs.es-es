---
title: Depuración y solución de problemas de ParallelRunStep
titleSuffix: Azure Machine Learning
description: Depure y solucione los problemas de ParallelRunStep en las canalizaciones de aprendizaje automático del SDK de Azure Machine Learning para Python. Aprenda sobre los errores comunes en el desarrollo de las canalizaciones, y consulte sugerencias que le ayudarán a depurar los scripts antes y durante la ejecución remota.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: trbye, jmartens, larryfr, vaidyas
ms.author: trmccorm
author: tmccrmck
ms.date: 01/15/2020
ms.openlocfilehash: ca50d70965d5edc4e31606e542ddf163fe3b0741
ms.sourcegitcommit: 5bbe87cf121bf99184cc9840c7a07385f0d128ae
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/16/2020
ms.locfileid: "76122929"
---
# <a name="debug-and-troubleshoot-parallelrunstep"></a>Depuración y solución de problemas de ParallelRunStep
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

En este artículo aprenderá a depurar y resolver los problemas de la clase [ParallelRunStep](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps.parallel_run_step.parallelrunstep?view=azure-ml-py) con el [SDK de Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).

## <a name="testing-scripts-locally"></a>Prueba de scripts de forma local

Consulte la [sección Prueba de scripts de forma local](how-to-debug-pipelines.md#testing-scripts-locally) en el artículo sobre canalizaciones de aprendizaje automático. La instancia de ParallelRunStep se ejecuta como un paso en las canalizaciones de Machine Learning, por lo que la misma respuesta se aplica en ambos casos.

## <a name="debugging-scripts-from-remote-context"></a>Depuración de scripts desde un contexto remoto

La transición de la depuración de un script de puntuación de forma local, a la depuración de un script de puntuación en una canalización real puede resultar difícil. Para información sobre cómo buscar los registros en el portal, consulte la [sección de canalizaciones de aprendizaje automático en la depuración de scripts desde un contexto remoto](how-to-debug-pipelines.md#debugging-scripts-from-remote-context). La información de esa sección también se aplica a una ejecución de pasos en paralelo.

Por ejemplo, el archivo de registro `70_driver_log.txt` contiene información del controlador que inicia el código de pasos de ejecución en paralelo.

Dada la naturaleza distribuida de los trabajos de ejecución en paralelo, hay registros de distintos orígenes. Sin embargo, se crean dos archivos consolidados que proporcionan información de alto nivel:

- `~/logs/overview.txt`: Este archivo proporciona información de alto nivel sobre el número de minilotes (también conocidos como tareas) que se han creado hasta el momento y el número de minilotes que se han procesado hasta ahora. En este extremo, se muestra el resultado del trabajo. Si se produjo un error en el trabajo, se mostrará el mensaje de error y dónde se debe iniciar la solución de problemas.

- `~/logs/sys/master.txt`: Este archivo proporciona la vista del nodo principal (también conocido como orquestador) del trabajo en ejecución. Incluye la creación de tareas, la supervisión del progreso y el resultado de la ejecución.

Los registros generados a partir del script de entrada mediante EntryScript.logger y las instrucciones de impresión se encuentran en los siguientes archivos:

- `~/logs/user/<ip_address>/Process-*.txt`: Este archivo contiene los registros escritos desde entry_script mediante EntryScript.logger. También contiene la instrucción print (stdout) de entry_script.

Cuando necesite comprender en detalle cómo ejecuta cada nodo el script de puntuación, examine los registros de proceso individuales para cada nodo. Los registros de proceso se pueden encontrar en la carpeta `sys/worker`, agrupados por nodos de trabajo:

- `~/logs/sys/worker/<ip_address>/Process-*.txt`: Este archivo proporciona información detallada sobre cada uno de los minilotes a medida que un trabajo lo recoge o lo completa. Para cada minilote, este archivo incluye:

    - La dirección IP y el PID del proceso de trabajo. 
    - El número total de elementos, el número de elementos procesados correctamente y el número de elementos con errores.
    - Hora de inicio, duración, tiempo de proceso y tiempo del método de ejecución.

También puede encontrar información sobre el uso de recursos de los procesos para cada trabajo. Esta información está en formato CSV y se encuentra en `~/logs/sys/perf/<ip_address>/`. En el caso de un nodo individual, los archivos de trabajo estarán disponibles en `~logs/sys/perf`. Por ejemplo, al comprobar la utilización de recursos, examine los siguientes archivos:

- `Process-*.csv`: Uso de recursos de proceso por trabajo. 
- `sys.csv`: Por registro de nodo.

### <a name="how-do-i-log-from-my-user-script-from-a-remote-context"></a>¿Cómo me puedo registrar desde mi script de usuario en un contexto remoto?
Puede obtener un registrador de EntryScript, como se muestra en el código de ejemplo siguiente, para que los registros aparezcan en la carpeta **logs/user** del portal.

**Uso del registrador por un script de entrada de ejemplo:**
```python
from entry_script import EntryScript

def init():
    """ Initialize the node."""
    entry_script = EntryScript()
    logger = entry_script.logger
    logger.debug("This will show up in files under logs/user on the Azure portal.")


def run(mini_batch):
    """ Accept and return the list back."""
    # This class is in singleton pattern and will return same instance as the one in init()
    entry_script = EntryScript()
    logger = entry_script.logger
    logger.debug(f"{__file__}: {mini_batch}.")
    ...

    return mini_batch
```

### <a name="how-could-i-pass-a-side-input-such-as-a-file-or-files-containing-a-lookup-table-to-all-my-workers"></a>¿Cómo puedo pasar una entrada lateral, como un archivo o archivos que contienen una tabla de búsqueda, a todos los trabajadores?

Construya un objeto [Dataset](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py) que contenga la entrada lateral y realice el registro con su área de trabajo. Una vez que pueda acceder a él en su script inferencia [por ejemplo, en el método init()] como sigue:

```python
from azureml.core.run import Run
from azureml.core.dataset import Dataset

ws = Run.get_context().experiment.workspace
lookup_ds = Dataset.get_by_name(ws, "<registered-name>")
lookup_ds.download(target_path='.', overwrite=True)
```

## <a name="next-steps"></a>Pasos siguientes

* Consulte la referencia del SDK para obtener ayuda con el paquete [azureml-contrib-pipeline-step](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps?view=azure-ml-py) y la [documentación](https://docs.microsoft.com/python/api/azureml-contrib-pipeline-steps/azureml.contrib.pipeline.steps.parallelrunstep?view=azure-ml-py) para la clase ParallelRunStep.

* Consulte el [tutorial avanzado](tutorial-pipeline-batch-scoring-classification.md) sobre el uso de canalizaciones con un paso de ejecución en paralelo.
