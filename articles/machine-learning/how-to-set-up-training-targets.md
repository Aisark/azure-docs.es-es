---
title: Uso de los destinos de proceso para el entrenamiento de modelos
titleSuffix: Azure Machine Learning
description: Configurar los entornos de entrenamiento (destinos de proceso) del entrenamiento del modelo de Machine Learning. Es fácil cambiar entre entornos de entrenamiento. Inicie el entrenamiento de forma local. Si necesita escalar horizontalmente, cambie a un destino de proceso basado en la nube.
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 11/21/2019
ms.custom: seodec18
ms.openlocfilehash: 730436c7437d2ed77f9066f486d9716ec6d5681f
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/09/2020
ms.locfileid: "75771843"
---
# <a name="set-up-and-use-compute-targets-for-model-training"></a>Configuración y uso de destinos de proceso para el entrenamiento del modelo 
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Con Azure Machine Learning, puede entrenar el modelo en una variedad de recursos o entornos, denominados colectivamente [__destinos de proceso__](concept-azure-machine-learning-architecture.md#compute-targets). Un destino de proceso puede ser una máquina local o un recurso de nube, como una instancia de Azure Machine Learning Compute, Azure HDInsight o una máquina virtual remota.  También puede crear destinos de proceso para la implementación de modelos tal como se describe en ["Cómo y dónde implementar los modelos"](how-to-deploy-and-where.md).

Los destinos de proceso se pueden crear y administrar mediante el SDK de Azure Machine Learning, Azure Machine Learning Studio, la CLI de Azure o la extensión de VS Code de Azure Machine Learning. Si tiene destinos de proceso creados mediante cualquier otro servicio (por ejemplo, un clúster de HDInsight), para usarlos debe adjuntarlos al área de trabajo de Azure Machine Learning.
 
En este artículo, aprenderá a usar diversos destinos de proceso para el entrenamiento de modelos.  Los pasos para todos los destinos de proceso siguen el mismo flujo de trabajo:
1. __Crear__ un destino de proceso si aún no tiene uno.
2. __Asociar__ el destino de proceso al área de trabajo.
3. __Configurar__ el destino de proceso para que contenga las dependencias de paquete y entorno de Python necesarias por el script.


>[!NOTE]
> El código de este artículo se probó con la versión 1.0.74 del SDK de Azure Machine Learning.

## <a name="compute-targets-for-training"></a>Destinos de proceso para entrenamiento

Azure Machine Learning tiene distintas modalidades de soporte técnico en los diferentes destinos de proceso. Un ciclo de vida de desarrollo de modelos típico comienza con el desarrollo y la experimentación en una pequeña cantidad de datos. En esta fase, se recomienda usar un entorno local. Por ejemplo, un equipo local o una máquina virtual basada en la nube. Si escala verticalmente su entrenamiento a conjuntos de datos grandes o realiza el entrenamiento distribuido, se recomienda usar Proceso de Azure Machine Learning para crear un clúster con uno o varios nodos que se escala automáticamente cada vez que se envía una ejecución. También puede adjuntar su propio recurso de proceso, aunque el soporte técnico para varios escenarios puede variar como se detalla a continuación:

[!INCLUDE [aml-compute-target-train](../../includes/aml-compute-target-train.md)]


> [!NOTE]
> Proceso de Azure Machine Learning puede crearse como recurso persistente o crearse dinámicamente al solicitar una ejecución. La creación basada en ejecución quita el destino de proceso después de que se completa la ejecución de entrenamiento, por lo que no puede reutilizar los destinos de proceso creados de esta manera.

## <a name="whats-a-run-configuration"></a>¿En qué consiste una configuración de ejecución?

Cuando se entrena, es normal comenzar en el equipo local y después ejecutar ese script de entrenamiento en un destino de proceso diferente. Con Azure Machine Learning, puede ejecutar el script en varios destinos de proceso sin tener que cambiar el script.

Todo lo que debe hacer es definir el entorno de cada destino de proceso con una **configuración de ejecución**.  Después, cuando desee ejecutar el experimento de entrenamiento en un destino de proceso diferente, especifique la configuración de ejecución para ese proceso. Para obtener más información sobre cómo especificar un entorno y enlazarlo a la configuración de ejecución, consulte [Creación y administración de entornos para el aprendizaje y la implementación](how-to-use-environments.md).

Obtenga más información sobre cómo [enviar experimentos](#submit) al final de este artículo.

## <a name="whats-an-estimator"></a>¿Qué es un objeto "Estimator"?

Para facilitar el entrenamiento del modelo mediante plataformas populares, el SDK de Azure Machine Learning para Python proporciona una abstracción de nivel más alto alternativa, la clase "Estimator".  Esta clase le permite construir fácilmente configuraciones de ejecución. Puede crear y usar un objeto [Estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) genérico para enviar scripts de entrenamiento que usen cualquier plataforma de aprendizaje que elija, como scikit-learn. Se recomienda usar un objeto "Estimator" para el entrenamiento, ya que crea automáticamente objetos incrustados como un entorno u objetos RunConfiguration automáticamente. Si desea tener más control sobre cómo se crean estos objetos y especificar los paquetes que se deben instalar para su ejecución de experimento, siga [estos pasos](#amlcompute) para enviar sus experimentos de entrenamiento mediante un objeto RunConfiguration en un proceso de Azure Machine Learning.

En las tareas de PyTorch, TensorFlow y Chainer, Azure Machine Learning también proporciona los objetos Estimator de [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py), [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) y [Chainer](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) para simplificar el uso de estas plataformas.

Para obtener más información, consulte [Entrenamiento de modelos con Azure Machine Learning mediante un objeto Estimator](how-to-train-ml-models.md).

## <a name="whats-an-ml-pipeline"></a>¿Qué es una canalización de ML?

Con las canalizaciones de ML, puede optimizar su flujo de trabajo con sencillez y velocidad, dotarlo de portabilidad y reutilizarlo. Al crear canalizaciones mediante Azure Machine Learning, puede centrarse en lo que domina, el aprendizaje automático, en lugar de en la infraestructura y la automatización.

Las canalizaciones de ML se construyen a partir de varios **pasos**, que son unidades de cálculo diferentes en la canalización. Cada paso se puede ejecutar de forma independiente y usar recursos de proceso aislados. Esto permite que varios científicos de datos trabajen en la misma canalización al mismo tiempo sin sobrecargar los recursos de proceso y facilita el uso de diferentes tamaños o tipos de proceso para cada paso.

> [!TIP]
> Las canalizaciones de ML pueden utilizar la configuración de ejecución o los objetos "Estimator" al entrenar modelos.

Aunque las canalizaciones de ML pueden entrenar modelos, también pueden preparar los datos antes del entrenamiento e implementar los modelos después. Uno de los casos de uso principales para las canalizaciones es la puntuación por lotes. Para obtener más información, vea el artículo [Creación de canalizaciones de ML reutilizables en Azure Machine Learning Service](concept-ml-pipelines.md).

## <a name="set-up-in-python"></a>Configuración en Python

Use las secciones siguientes para configurar estos destinos de proceso:

* [Equipo local](#local)
* [Proceso de Azure Machine Learning](#amlcompute)
* [Máquinas virtuales remotas](#vm)
* [HDInsight de Azure](#hdinsight)


### <a id="local"></a>Equipo local

1. **Crear y adjuntar**: no es necesario crear o asociar un destino de proceso para utilizar el equipo local como entorno de entrenamiento.  

1. **Configurar**:  cuando se utiliza el equipo local como destino de proceso, el código de entrenamiento se ejecuta en el [entorno de desarrollo](how-to-configure-environment.md).  Si ese entorno ya tiene los paquetes Python que necesita, utilice el entorno administrado por usuarios.

 [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=run_local)]

Ahora que ha asociado el proceso y ha configurado la ejecución, el siguiente paso es [enviar la ejecución de entrenamiento](#submit).

### <a id="amlcompute"></a>Proceso de Azure Machine Learning

Proceso de Azure Machine Learning es una infraestructura de proceso administrado que permite al usuario crear fácilmente un proceso de uno o varios nodos. El proceso se crea dentro de la región de su área de trabajo y es un recurso que se puede compartir con otros usuarios del área de trabajo. El proceso se escala verticalmente de forma automática cuando se envía un trabajo y se puede colocar en una instancia de Azure Virtual Network. El proceso se ejecuta en un entorno con contenedores y empaqueta las dependencias del modelo en un [contenedor de Docker](https://www.docker.com/why-docker).

Proceso Azure Machine Learning Compute para distribuir el proceso de entrenamiento en un clúster de nodos de proceso de CPU o GPU de la nube. Para más información sobre los tamaños de máquina virtual que incluyen GPU, consulte [Tamaños de máquinas virtuales optimizadas para GPU](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu).

Proceso de Azure Machine Learning tiene límites predeterminados, como el número de núcleos que se pueden asignar. Para más información, consulte [Administración y solicitud de cuotas para recursos de Azure](https://docs.microsoft.com/azure/machine-learning/how-to-manage-quotas).


Puede crear un entorno de Proceso de Azure Machine Learning a petición al programar una ejecución, o bien como un recurso persistente.

#### <a name="run-based-creation"></a>Creación basada en ejecución

Puede crear una instancia de Proceso de Azure Machine Learning como un destino de proceso en tiempo de ejecución. El proceso se crea automáticamente para la ejecución. El proceso se elimina automáticamente una vez completada la ejecución. 

> [!IMPORTANT]
> La creación basada en ejecución de una instancia de Proceso de Azure Machine Learning está actualmente en versión preliminar. No use la creación basada en la ejecución si usa el ajuste de hiperparámetros o el aprendizaje automático automatizado. Para utilizar la optimización de hiperparámetros o el aprendizaje automático automatizado, cree en su lugar un [destino de proceso persistente](#persistent).

1.  **Crear, adjuntar y configurar**: la creación basada en la ejecución realiza todos los pasos necesarios para crear, adjuntar y configurar el destino de proceso con la configuración de ejecución.  

  [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute.py?name=run_temp_compute)]


Ahora que ha asociado el proceso y ha configurado la ejecución, el siguiente paso es [enviar la ejecución de entrenamiento](#submit).

#### <a id="persistent"></a>Proceso persistente

Se puede reutilizar una instancia de Proceso de Azure Machine Learning persistente en varios trabajos. El proceso se puede compartir con otros usuarios del área de trabajo y se conserva entre un trabajo y otro.

1. **Crear y adjuntar**: para crear un recurso de Proceso de Azure Machine Learning persistente en Python, especifique las propiedades **vm_size** y **max_nodes**. Azure Machine Learning usará valores predeterminados inteligentes para las demás propiedades. El proceso se reduce verticalmente a cero nodos cuando no se utiliza.   Se crean máquinas virtuales dedicadas para ejecutar los trabajos según sea necesario.
    
    * **vm_size**: la familia máquina virtual de los nodos creados por el Proceso de Azure Machine Learning.
    * **max_nodes**: el número máximo de nodos hasta los que se aumenta automáticamente cuando ejecuta un trabajo en Proceso de Azure Machine Learning.
    
   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=cpu_cluster)]

   Cuando cree una instancia de Proceso de Azure Machine Learning, puede configurar también varias propiedades avanzadas. Estas propiedades permiten crear un clúster persistente de tamaño fijo o dentro de una instancia existente de Azure Virtual Network de su suscripción.  Consulte la [clase AmlCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py
    ) para más información.
    
   O bien puede crear y adjuntar un recurso persistente de Proceso de Azure Machine Learning en [Azure Machine Learning Studio](#portal-create).

1. **Configurar**: Cree una configuración de ejecución para el destino de proceso persistente.

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=run_amlcompute)]

Ahora que ha asociado el proceso y ha configurado la ejecución, el siguiente paso es [enviar la ejecución de entrenamiento](#submit).


### <a id="vm"></a>Máquinas virtuales remotas

Azure Machine Learning también admite la posibilidad de que traiga su propio recurso de proceso y lo adjunte a su área de trabajo. Uno de estos tipos de recursos es una máquina virtual remota arbitraria, siempre que se pueda acceder desde Azure Machine Learning. El recurso puede ser una máquina virtual de Azure, un servidor remoto de la organización o local. En concreto, dada la dirección IP y las credenciales (nombre de usuario y contraseña o clave SSH), puede usar cualquier máquina virtual a la que se pueda acceder para las ejecuciones remotas.

Puede usar un entorno de Conda integrado en el sistema, un entorno de Python existente o un contenedor de Docker. Para realizar la ejecución en un contenedor de Docker, el motor de Docker debe estar en ejecución en la máquina virtual. Esta funcionalidad es especialmente útil cuando se desea un entorno de desarrollo o experimentación basado en la nube más flexible que un equipo local.

Utilice Azure Data Science Virtual Machine (DSVM) como máquina virtual de Azure preferida para este escenario. Esta máquina virtual es un entorno de desarrollo de IA y de ciencia de datos preconfigurados de Azure. La máquina virtual ofrece una selección de herramientas y plataformas mantenidas para el desarrollo del aprendizaje automático a lo largo de todo el ciclo de vida. Para obtener más información sobre cómo usar la DSVM con Azure Machine Learning, consulte [Configuración del entorno de desarrollo](https://docs.microsoft.com/azure/machine-learning/how-to-configure-environment#dsvm).

1. **Crear**: Cree una instancia de DSVM antes de usarla para entrenar el modelo. Para crear este recurso, consulte [Aprovisionamiento de Data Science Virtual Machine para Linux (Ubuntu)](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro).

    > [!WARNING]
    > Azure Machine Learning solo admite máquinas virtuales que ejecuten Ubuntu. Al crear una máquina virtual o elegir una máquina virtual existente, debe seleccionar una máquina virtual que use Ubuntu.

1. **Adjuntar**: Para asociar una máquina virtual existente como destino de proceso, debe proporcionar el nombre de dominio completo (FQDN), el nombre de usuario y la contraseña de la máquina virtual. En el ejemplo, reemplace \<fqdn> por el FQDN de la máquina virtual o por la dirección IP pública. Reemplace \<username> y \<password> por el nombre de usuario y contraseña de SSH para la máquina virtual.

   ```python
   from azureml.core.compute import RemoteCompute, ComputeTarget

   # Create the compute config 
   compute_target_name = "attach-dsvm"
   attach_config = RemoteCompute.attach_configuration(address = "<fqdn>",
                                                    ssh_port=22,
                                                    username='<username>',
                                                    password="<password>")

   # If you authenticate with SSH keys instead, use this code:
   #                                                  ssh_port=22,
   #                                                  username='<username>',
   #                                                  password=None,
   #                                                  private_key_file="<path-to-file>",
   #                                                  private_key_passphrase="<passphrase>")

   # Attach the compute
   compute = ComputeTarget.attach(ws, compute_target_name, attach_config)

   compute.wait_for_completion(show_output=True)
   ```

   O bien puede asociar la instancia de DSVM al área de trabajo [mediante Azure Machine Learning Studio](#portal-reuse).

1. **Configurar**: Cree una configuración de ejecución para el destino de proceso de Data Science Virtual Machine. Docker y Conda se utilizan para crear y configurar el entorno de entrenamiento en la DSVM.

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/dsvm.py?name=run_dsvm)]


Ahora que ha asociado el proceso y ha configurado la ejecución, el siguiente paso es [enviar la ejecución de entrenamiento](#submit).

### <a id="hdinsight"></a>Azure HDInsight 

Azure HDInsight es una plataforma popular para el análisis de macrodatos. La plataforma proporciona Apache Spark, que se puede usar para entrenar el modelo.

1. **Crear**:  Cree el clúster de HDInsight antes de usarlo para entrenar el modelo. Para crear un clúster de Spark en HDInsight, consulte [Creación de un clúster de Spark en HDInsight](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-jupyter-spark-sql). 

    Al crear el clúster, debe especificar un nombre de usuario SSH y una contraseña. Anote estos valores, ya que los necesitará al usar HDInsight como destino de proceso.
    
    Cuando se ha creado el clúster, conéctelo al nombre de host \<clustername>-ssh.azurehdinsight.net, donde \<clustername> es el nombre que ha proporcionado para el clúster. 

1. **Adjuntar**: Para asociar un clúster de HDInsight como destino de proceso, debe proporcionar el nombre de host, el nombre de usuario y la contraseña para el clúster de HDInsight. El ejemplo siguiente usa el SDK para asociar un clúster al área de trabajo. En el ejemplo, reemplace \<clustername> por el nombre del clúster. Reemplace \<username> y \<password> por el nombre de usuario y contraseña de SSH para el clúster.

   ```python
   from azureml.core.compute import ComputeTarget, HDInsightCompute
   from azureml.exceptions import ComputeTargetException

   try:
    # if you want to connect using SSH key instead of username/password you can provide parameters private_key_file and private_key_passphrase
    attach_config = HDInsightCompute.attach_configuration(address='<clustername>-ssh.azurehdinsight.net', 
                                                          ssh_port=22, 
                                                          username='<ssh-username>', 
                                                          password='<ssh-pwd>')
    hdi_compute = ComputeTarget.attach(workspace=ws, 
                                       name='myhdi', 
                                       attach_configuration=attach_config)

   except ComputeTargetException as e:
    print("Caught = {}".format(e.message))

   hdi_compute.wait_for_completion(show_output=True)
   ```

   O bien puede asociar el clúster de HDInsight al área de trabajo [mediante Azure Machine Learning Studio](#portal-reuse).

1. **Configurar**: Cree una configuración de ejecución para el destino de proceso de HDI. 

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/hdi.py?name=run_hdi)]


Ahora que ha asociado el proceso y ha configurado la ejecución, el siguiente paso es [enviar la ejecución de entrenamiento](#submit).


### <a id="azbatch"></a>Azure Batch 

Azure Batch se usa para ejecutar aplicaciones de informática de alto rendimiento (HPC) en paralelo y a gran escala de manera eficaz en la nube. AzureBatchStep se puede usar en una canalización de Azure Machine Learning para enviar trabajos al grupo de máquinas de Azure Batch.

Para adjuntar Azure Batch como destino de proceso, debe usar el SDK de Azure Machine Learning y proporcionar la siguiente información:

-   **Nombre de proceso de Azure Batch**: nombre descriptivo que se usará para el proceso en el área de trabajo.
-   **Nombre de cuenta de Azure Batch**: nombre de la cuenta de Azure Batch.
-   **Grupo de recursos**: grupo de recursos que contiene la cuenta de Azure Batch.

El código siguiente muestra cómo asociar Azure Batch como destino de proceso:

```python
from azureml.core.compute import ComputeTarget, BatchCompute
from azureml.exceptions import ComputeTargetException

# Name to associate with new compute in workspace
batch_compute_name = 'mybatchcompute'

# Batch account details needed to attach as compute to workspace
batch_account_name = "<batch_account_name>"  # Name of the Batch account
# Name of the resource group which contains this account
batch_resource_group = "<batch_resource_group>"

try:
    # check if the compute is already attached
    batch_compute = BatchCompute(ws, batch_compute_name)
except ComputeTargetException:
    print('Attaching Batch compute...')
    provisioning_config = BatchCompute.attach_configuration(
        resource_group=batch_resource_group, account_name=batch_account_name)
    batch_compute = ComputeTarget.attach(
        ws, batch_compute_name, provisioning_config)
    batch_compute.wait_for_completion()
    print("Provisioning state:{}".format(batch_compute.provisioning_state))
    print("Provisioning errors:{}".format(batch_compute.provisioning_errors))

print("Using Batch compute:{}".format(batch_compute.cluster_resource_id))
```

## <a name="set-up-in-azure-machine-learning-studio"></a>Configuración de Azure Machine Learning Studio

Desde Azure Machine Learning Studio, puede acceder a destinos de proceso que están asociados con el área de trabajo.  Puede usar Studio para:

* [Ver los destinos de proceso](#portal-view) asociados a un área de trabajo
* [Crear un destino de proceso](#portal-create) en el área de trabajo
* [Adjuntar un destino de proceso](#portal-reuse) que se creó fuera del área de trabajo


Después de crear un destino y de adjuntarlo al área de trabajo, lo utilizará en la configuración de ejecución con un objeto `ComputeTarget`: 

```python
from azureml.core.compute import ComputeTarget
myvm = ComputeTarget(workspace=ws, name='my-vm-name')
```

### <a id="portal-view"></a>Visualización de destinos de proceso


Para ver los destinos de proceso del área de trabajo, use los pasos siguientes:

1. Vaya a [Azure Machine Learning Studio](https://ml.azure.com).
 
1. En __Aplicaciones__, seleccione __Proceso__.

    [![Visualización de la pestaña Proceso](./media/how-to-set-up-training-targets/azure-machine-learning-service-workspace.png)](./media/how-to-set-up-training-targets/azure-machine-learning-service-workspace-expanded.png)

### <a id="portal-create"></a>Creación de un destino de proceso

Siga los pasos anteriores para ver la lista de destinos de proceso. A continuación, siga estos pasos para crear un destino de proceso: 

1. Haga clic en el signo más (+) para agregar un destino de proceso.

    ![Agregar un destino de proceso](./media/how-to-set-up-training-targets/add-compute-target.png) 

1. Escriba un nombre para el destino de proceso. 

1. Seleccione **Proceso de Machine Learning** como tipo de proceso que se usará en __Entrenamiento__. 

    >[!NOTE]
    >Proceso de Azure Machine Learning es el único recurso de proceso administrado que se puede crear en Azure Machine Learning Studio.  Todos los demás recursos de proceso se pueden asociar después de su creación.

1. Rellene el formulario. Proporcione valores para las propiedades necesarias, especialmente para **Familia de máquinas virtuales** y **Nodos máximos** que se van a utilizar para acelerar el proceso.  

1. Seleccione __Crear__.


1. Vea el estado de la operación de creación seleccionando el destino de proceso en la lista:

    ![Seleccionar un destino de proceso para ver el estado de la operación de creación](./media/how-to-set-up-training-targets/View_list.png)

1. Verá los detalles del destino de proceso: 

    ![Ver los detalles del destino de proceso](./media/how-to-set-up-training-targets/compute-target-details.png) 

### <a id="portal-reuse"></a>Adjuntar destinos de proceso

Para usar destinos de proceso creados fuera del área de trabajo de Azure Machine Learning, debe adjuntarlos. Adjuntar un destino de proceso hace que esté disponible para el área de trabajo.

Siga los pasos descritos previamente para ver la lista de destinos de proceso. Luego, siga los pasos a continuación para adjuntar un destino de proceso: 

1. Haga clic en el signo más (+) para agregar un destino de proceso. 
1. Escriba un nombre para el destino de proceso. 
1. Seleccione el tipo de proceso que va a adjuntar para __Entrenamiento__:

    > [!IMPORTANT]
    > No todos los tipos de proceso se pueden adjuntar desde Azure Machine Learning Studio. Los tipos de proceso que se pueden adjuntar actualmente para entrenamiento incluyen:
    >
    > * Una máquina virtual remota
    > * Azure Databricks (para su uso en las canalizaciones de aprendizaje automático)
    > * Azure Data Lake Analytics (para su uso en las canalizaciones de aprendizaje automático)
    > * HDInsight de Azure

1. Rellene el formulario y proporcione valores para las propiedades necesarias.

    > [!NOTE]
    > Microsoft recomienda que use claves SSH, que son más seguras que las contraseñas. Las contraseñas son vulnerables a ataques por fuerza bruta. Las claves SSH se basan en las firmas criptográficas. Para información sobre cómo crear claves SSH para usarlas con Azure Virtual Machines, consulte los siguientes documentos:
    >
    > * [Creación y uso de claves SSH en Linux o macOS](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys)
    > * [Creación y uso de claves SSH en Windows](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows)

1. Seleccione __Adjuntar__. 
1. Consulte el estado de la operación de adjuntar seleccionando el destino de proceso en la lista.

## <a name="set-up-with-cli"></a>Configuración con la CLI

Puede acceder a los destinos de proceso asociados con el área de trabajo mediante la [extensión de la CLI](reference-azure-machine-learning-cli.md) para Azure Machine Learning.  Puede usar la CLI para:

* Crear un destino de proceso administrado
* Actualizar un destino de proceso administrado
* Asociar un destino de proceso no administrado

Para más información, consulte el artículo sobre la [administración de recursos](reference-azure-machine-learning-cli.md#resource-management).

## <a name="set-up-with-vs-code"></a>Configuración con VS Code

Puede crear y administrar los destinos de proceso asociados con el área de trabajo, así como acceder a ellos, mediante la [extensión de VS Code](how-to-vscode-tools.md#create-and-manage-compute-targets) para Azure Machine Learning.

## <a id="submit"></a>Envío de una ejecución de entrenamiento mediante el SDK de Azure Machine Learning

Después de crear una configuración de ejecución, se utiliza para ejecutar el experimento.  El patrón de código para enviar una ejecución de entrenamiento es el mismo para todos los tipos de destinos de proceso:

1. Creación de un experimento para su ejecución
1. Envíe la ejecución.
1. Espere a que la ejecución se complete.

> [!IMPORTANT]
> Cuando envía la ejecución de entrenamiento, se crea una instantánea del directorio que contiene los scripts de entrenamiento y se envía al destino de proceso. También se almacena como parte del experimento del área de trabajo. Si cambia los archivos y vuelve a enviar la ejecución, solo se cargarán los archivos cambiados.
>
> Para evitar que los archivos se incluyan en la instantánea, cree un archivo [.gitignore](https://git-scm.com/docs/gitignore) o `.amlignore` en el directorio y agréguelos. El archivo `.amlignore` usa la misma sintaxis y patrones que el archivo [.gitignore](https://git-scm.com/docs/gitignore). Si ambos archivos existen, el archivo `.amlignore` tiene prioridad.
> 
> Para más información, consulte [Instantánea](concept-azure-machine-learning-architecture.md#snapshots).

### <a name="create-an-experiment"></a>Creación de un experimento

En primer lugar, cree un experimento en el área de trabajo.

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=experiment)]

### <a name="submit-the-experiment"></a>Envío del experimento

Envíe el experimento con un objeto `ScriptRunConfig`.  Este objeto incluye:

* **source_directory**: el directorio de origen que contiene el script de entrenamiento.
* **script**: Identificación del script de entrenamiento
* **run_config**: la configuración de ejecución, que a su vez define donde tendrá lugar el entrenamiento.

Por ejemplo, para usar la configuración del [destino local](#local):

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=local_submit)]

Cambie el mismo experimento para que se ejecute en un destino de proceso diferente mediante una configuración de ejecución diferente, como [amlcompute target](#amlcompute):

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=amlcompute_submit)]

> [!TIP]
> De forma predeterminada, en este ejemplo solo se usa un nodo del destino de proceso para el entrenamiento. Para usar más de un nodo, establezca el `node_count` de la configuración de ejecución en el número deseado de nodos. Por ejemplo, el código siguiente establece el número de nodos que se usan para el entrenamiento en cuatro:
>
> ```python
> src.run_config.node_count = 4
> ```

También puede:

* Enviar el experimento con un objeto `Estimator`, tal como se muestra en [Entrenamiento de modelos de aprendizaje automático con estimadores](how-to-train-ml-models.md).
* Envíe una ejecución de HyperDrive para el [ajuste de hiperparámetros](how-to-tune-hyperparameters.md).
* Enviar un experimento mediante la [extensión de VS Code](how-to-vscode-tools.md#train-and-tune-models).

Para obtener más información, consulte la documentación de [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) y [RunConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py).

## <a name="create-run-configuration-and-submit-run-using-azure-machine-learning-cli"></a>Creación de una configuración de ejecución y envío de la ejecución mediante la CLI de Azure Machine Learning

Puede usar la [CLI de Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) y la [extensión de la CLI de Machine Learning](reference-azure-machine-learning-cli.md) para crear configuraciones de ejecución y enviar esas ejecuciones a distintos destinos del proceso. En los ejemplos siguientes se supone que ya tiene un área de trabajo de Azure Machine Learning y que ha iniciado sesión en Azure mediante el comando de la CLI `az login`. 

### <a name="create-run-configuration"></a>Creación de una configuración de ejecución

La manera más sencilla de crear la configuración de ejecución es navegar por la carpeta que contiene los scripts de Python de Machine Learning y usar el comando de la CLI.

```azurecli
az ml folder attach
```

Este comando crea una subcarpeta `.azureml` que contiene archivos de configuración de ejecución de plantillas para distintos destinos de proceso. Puede copiar y editar estos archivos para personalizar la configuración; por ejemplo, para agregar paquetes de Python o cambiar la configuración de Docker.  

### <a name="structure-of-run-configuration-file"></a>Estructura del archivo de configuración de ejecución

El archivo de configuración de ejecución tiene un formato YAML y las siguientes secciones:
 * El script que se ejecutará y sus argumentos
 * El nombre del destino de proceso, ya sea "local" o el nombre de un proceso del área de trabajo.
 * Los parámetros para realizar la ejecución: el marco de trabajo, el comunicador para ejecuciones distribuidas, una duración máxima y el número de nodos de proceso.
 * La sección del entorno. Consulte [Creación y administración de entornos para el aprendizaje y la implementación](how-to-use-environments.md) para obtener más información sobre los campos de esta sección.
   * Para especificar los paquetes de Python que se van a instalar para la ejecución, cree el [archivo de entorno de CONDA](https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html#create-env-file-manually) y establezca el campo __condaDependenciesFile__.
 * Detalles del historial de ejecución para especificar la carpeta del archivo de registro y para habilitar o deshabilitar las instantáneas de la colección de salida y del historial de ejecución.
 * Detalles de configuración específicos del marco seleccionado.
 * Detalles de la referencia de datos y del almacén de datos.
 * Detalles de configuración específicos para el Proceso de Machine Learning para crear un nuevo clúster.

Vea el [archivo JSON](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json) de ejemplo para obtener un esquema runconfig completo.

### <a name="create-an-experiment"></a>Creación de un experimento

En primer lugar, cree un experimento para las ejecuciones.

```azurecli
az ml experiment create -n <experiment>
```

### <a name="script-run"></a>Ejecución de script

Para enviar una ejecución de script, ejecute un comando

```azurecli
az ml run submit-script -e <experiment> -c <runconfig> my_train.py
```

### <a name="hyperdrive-run"></a>Ejecución de HyperDrive

Puede usar HyperDrive con la CLI de Azure para realizar ejecuciones de ajuste de parámetros. En primer lugar, cree un archivo de configuración de HyperDrive con el siguiente formato. Consulte el artículo sobre el [ajuste de hiperparámetros del modelo](how-to-tune-hyperparameters.md) para obtener más información sobre los parámetros de ajuste de hiperparámetros.

```yml
# hdconfig.yml
sampling: 
    type: random # Supported options: Random, Grid, Bayesian
    parameter_space: # specify a name|expression|values tuple for each parameter.
    - name: --penalty # The name of a script parameter to generate values for.
      expression: choice # supported options: choice, randint, uniform, quniform, loguniform, qloguniform, normal, qnormal, lognormal, qlognormal
      values: [0.5, 1, 1.5] # The list of values, the number of values is dependent on the expression specified.
policy: 
    type: BanditPolicy # Supported options: BanditPolicy, MedianStoppingPolicy, TruncationSelectionPolicy, NoTerminationPolicy
    evaluation_interval: 1 # Policy properties are policy specific. See the above link for policy specific parameter details.
    slack_factor: 0.2
primary_metric_name: Accuracy # The metric used when evaluating the policy
primary_metric_goal: Maximize # Maximize|Minimize
max_total_runs: 8 # The maximum number of runs to generate
max_concurrent_runs: 2 # The number of runs that can run concurrently.
max_duration_minutes: 100 # The maximum length of time to run the experiment before cancelling.
```

Agregue este archivo junto con los archivos de configuración de ejecución. A continuación, envíe una ejecución de HyperDrive mediante:
```azurecli
az ml run submit-hyperdrive -e <experiment> -c <runconfig> --hyperdrive-configuration-name <hdconfig> my_train.py
```

Observe la sección *arguments* (argumentos) en runconfig y el *espacio de parámetros* en la configuración de HyperDrive. Contienen los argumentos de la línea de comandos que se van a pasar al script de aprendizaje. El valor de runconfig permanece igual para cada iteración, mientras que el intervalo de la configuración de HyperDrive se repite en interación. No especifique el mismo argumento en ambos archivos.

Para obtener más información sobre estos comandos de la CLI ```az ml``` y el conjunto completo de argumentos, consulte la [documentación de referencia](reference-azure-machine-learning-cli.md).

<a id="gitintegration"></a>

## <a name="git-tracking-and-integration"></a>Integración y seguimiento de Git

Cuando se inicia una ejecución de entrenamiento en la que el directorio de origen es un repositorio de GIT local, se almacena información sobre el repositorio en el historial de ejecución. Para más información, consulte [Integración de Git con Azure Machine Learning](concept-train-model-git-integration.md).

## <a name="notebook-examples"></a>Ejemplos de cuadernos

Consulte estos cuadernos para ver ejemplos de entrenamiento con varios destinos de proceso:
* [how-to-use-azureml/training](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [tutorials/img-classification-part1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Pasos siguientes

* [Tutorial: Entrenamiento de un modelo](tutorial-train-models-with-aml.md) utiliza un destino de proceso administrado para entrenar un modelo.
* Obtenga información sobre cómo [ajustar los hiperparámetros eficazmente](how-to-tune-hyperparameters.md) para crear modelos mejores.
* Cuando tenga un modelo entrenado, aprenda [cómo y dónde implementar los modelos](how-to-deploy-and-where.md).
* Consulte la referencia del SDK de la [clase RunConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py).
* [Uso de Azure Machine Learning con Azure Virtual Networks](how-to-enable-virtual-network.md)
