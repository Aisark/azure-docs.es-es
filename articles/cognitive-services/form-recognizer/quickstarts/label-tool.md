---
title: 'Inicio rápido: Etiquetado de formularios, entrenamiento de un modelo y análisis de un formulario mediante la herramienta de etiquetado de ejemplo: Form Recognizer'
titleSuffix: Azure Cognitive Services
description: En este inicio rápido, utilizará la herramienta de etiquetado de ejemplo Form Recognizer para etiquetar manualmente documentos de formularios. A continuación, entrenará un modelo personalizado con los documentos etiquetados y lo empleará para extraer pares clave-valor.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 11/14/2019
ms.author: pafarley
ms.openlocfilehash: 8ab673c1a268f5ab663e8f423dd9b60cdfde14ab
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/11/2020
ms.locfileid: "77118371"
---
# <a name="train-a-form-recognizer-model-with-labels-using-the-sample-labeling-tool"></a>Entrenamiento de un modelo de Form Recognizer con etiquetas mediante la herramienta de etiquetado de ejemplo

En este inicio rápido, usará la API REST de Form Recognizer con la herramienta de etiquetado de ejemplo para entrenar un modelo personalizado con datos etiquetados manualmente. Consulte la sección [Entrenamiento con etiquetas](../overview.md#train-with-labels) de la introducción para más información acerca de esta característica.

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

## <a name="prerequisites"></a>Prerrequisitos

Para completar este inicio rápido, debe cumplir los siguientes requisitos:

- Un conjunto de al menos seis formularios del mismo tipo. Usará estos datos para entrenar el modelo y probar un formulario. En este inicio rápido puede usar un [conjunto de datos de ejemplo](https://go.microsoft.com/fwlink/?linkid=2090451). Cargue los archivos de entrenamiento en la raíz de un contenedor de almacenamiento de blobs de una cuenta de Azure Storage.

## <a name="set-up-the-sample-labeling-tool"></a>Configuración de la herramienta de etiquetado de ejemplo

Usará el motor de Docker para ejecutar la herramienta de etiquetado de ejemplo. Siga estos pasos para configurar el contenedor de Docker. Para conocer los principios básicos de Docker y de los contenedores, consulte [Introducción a Docker](https://docs.docker.com/engine/docker-overview/).
1. En primer lugar, instale Docker en un equipo host. El equipo host puede ser el equipo local ([Windows](https://docs.docker.com/docker-for-windows/), [MacOS](https://docs.docker.com/docker-for-mac/)o [Linux](https://docs.docker.com/install/)). O bien, puede usar un servicio de hospedaje de Docker en Azure, como [Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/index), [Azure Container Instances](https://docs.microsoft.com/azure/container-instances/index) o un clúster de Kubernetes [implementado en Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-solution-template-kubernetes-deploy?view=azs-1910). El equipo host debe cumplir los siguientes requisitos de hardware:

    | Contenedor | Mínima | Recomendado|
    |:--|:--|:--|
    |Herramienta de etiquetado de ejemplo|2 núcleos, 4 GB de memoria|4 núcleos, 8 GB de memoria|
    
1. Obtenga el contenedor de la herramienta de etiquetado de ejemplo con el comando `docker pull`.
    ```
    docker pull mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool
    ```
1. Ahora ya está listo para ejecutar el contenedor con `docker run`.
    ```
    docker run -it -p 3000:80 mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool eula=accept
    ```

   Este comando hará que la herramienta de etiquetado de ejemplo esté disponible mediante un explorador web. Vaya a [http://localhost:3000](http://localhost:3000).

> [!NOTE]
> También puede etiquetar documentos y entrenar modelos mediante la API REST de Form Recognizer. Para entrenar y analizar con la API REST, consulte [Entrenamiento con etiquetas mediante la API REST y Python](./python-labeled-data.md).

## <a name="set-up-input-data"></a>Configuración de datos de entrada

En primer lugar, asegúrese de que todos los documentos de entrenamiento tienen el mismo formato. Si tiene formularios en varios formatos, organícelos en subcarpetas basadas en un formato común. Al entrenar, deberá dirigir la API a una subcarpeta.

### <a name="configure-cross-domain-resource-sharing-cors"></a>Configuración del uso compartido de recursos entre dominios (CORS)

Habilite CORS en la cuenta de almacenamiento. Seleccione la cuenta de almacenamiento en Azure Portal y haga clic en la pestaña **CORS** del panel izquierdo. En la línea inferior, rellene los valores siguientes. Después, haga clic en **Guardar** en la parte superior.

* Orígenes permitidos = * 
* Métodos permitidos = \[seleccionar todos\]
* Encabezados permitidos = *
* Encabezados expuestos = * 
* Antigüedad máxima = 200

> [!div class="mx-imgBorder"]
> ![Configuración de CORS en Azure Portal](../media/label-tool/cors-setup.png)

## <a name="connect-to-the-sample-labeling-tool"></a>Conexión a la herramienta de etiquetado de ejemplo

La herramienta de etiquetado de ejemplo se conecta a un origen (en el que se encuentran los formularios originales) y a un destino (la ubicación a la que se exportan las etiquetas creadas y los datos de salida).

Las conexiones se pueden configurar y compartir entre proyectos. Usan un modelo extensible de proveedores, por lo que puede agregar fácilmente nuevos proveedores de origen y destino.

Para crear una nueva conexión, haga clic en el icono **Nuevas conexiones** (un enchufe) en la barra de navegación izquierda.

Rellene los campos con los siguientes valores:

* **Nombre para mostrar**: el nombre para mostrar de la conexión.
* **Descripción**: la descripción del proyecto.
* **Dirección URL de SAS**: la dirección URL de la firma de acceso compartido (SAS) del contenedor de Azure Blob Storage. Para recuperar la dirección URL de SAS, abra el Explorador de Microsoft Azure Storage, haga clic con el botón derecho en el contenedor y seleccione **Get shared access signature** (Obtener firma de acceso compartido). Establezca la hora de expiración en alguna hora posterior a la hora en la que utilizará el servicio. Asegúrese de que están seleccionados los permisos de **lectura**, **escritura**, **eliminación** y **enumeración** y haga clic en **Crear**. A continuación, copie el valor en la sección **URL**. Debe tener el formato `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.

![Valores de conexión de la herramienta de etiquetado de ejemplo](../media/label-tool/connections.png)

## <a name="create-a-new-project"></a>Creación de un nuevo proyecto

En la herramienta de etiquetado de ejemplo, los proyectos almacenan las configuraciones y los valores. Cree un nuevo proyecto y rellene los campos con los siguientes valores:

* **Nombre para mostrar**: el nombre para mostrar del proyecto
* **Token de seguridad**: algunos valores de configuración del proyecto pueden incluir valores confidenciales, como claves de API u otros secretos compartidos. Cada proyecto generará un token de seguridad que se puede usar para cifrar o descifrar los valores de configuración confidenciales del proyecto. Los tokens de seguridad se encuentran en Configuración de la aplicación. Para acceder a ella, haga clic en el icono de engranaje situado en la esquina inferior de la barra de navegación izquierda.
* **Conexión de origen**: la conexión de Azure Blob Storage que creó en el paso anterior que le gustaría usar para este proyecto.
* **Ruta de acceso a la carpeta** (opcional): si los formularios de origen se encuentran en una carpeta en el contenedor de blobs, especifique aquí el nombre de la carpeta.
* **URI de servicio del servicio Form Recognizer**: la dirección URL del punto de conexión de Form Recognizer.
* **Clave de API**: la clave de suscripción de Form Recognizer.
* **Descripción** (opcional): descripción del proyecto.

![Página de nuevo proyecto en la herramienta de etiquetado de ejemplo](../media/label-tool/new-project.png)

## <a name="label-your-forms"></a>Etiquetado de formularios

Al crear o abrir un proyecto, se abrirá la ventana principal del editor de etiquetas. El editor de etiquetas consta de tres partes:

* Un panel de vista previa de tamaño variable que contiene una lista desplazable de formularios de la conexión de origen.
* El panel principal del editor que permite aplicar etiquetas.
* El panel del editor de etiquetas que permite a los usuarios modificar, bloquear, reordenar y eliminar etiquetas. 

### <a name="identify-text-elements"></a>Identificación de elementos de texto

Haga clic en **Run OCR on all files** (Ejecutar OCR en todos los archivos) en el panel izquierdo para obtener la información de diseño del texto de cada documento. La herramienta de etiquetado dibujará los cuadros de límite alrededor de cada elemento de texto.

### <a name="apply-labels-to-text"></a>Aplicación de etiquetas a texto

A continuación, creará etiquetas y las aplicará a los elementos de texto que desea que reconozca el modelo.

1. Primero, use el panel del editor de etiquetas para crear las etiquetas (etiquetas) que le gustaría identificar.
1. En el editor principal, haga clic y arrastre para seleccionar una o varias palabras de los elementos de texto resaltados.

    > [!NOTE]
    > Actualmente, no se puede seleccionar texto que abarque varias páginas.
1. Haga clic en la etiqueta que desea aplicar o presione la tecla correspondiente del teclado. Solo se puede aplicar una etiqueta a cada elemento de texto seleccionado y cada etiqueta solo se puede aplicar una vez por página.

    > [!TIP]
    > Las teclas numéricas se asignan como teclas de acceso rápido para las diez primeras etiquetas. Puede volver a ordenar las etiquetas con los iconos de flecha arriba y abajo del panel del editor de etiquetas.

Siga los pasos anteriores para etiquetar cinco de los formularios y, después, continúe con el paso siguiente.

![Ventana principal del editor de la herramienta de etiquetado de ejemplo](../media/label-tool/main-editor.png)


## <a name="train-a-custom-model"></a>Entrenamiento de un modelo personalizado

Haga clic en el icono Entrenar (el vagón de tren) en el panel izquierdo para abrir la página de entrenamiento. A continuación, haga clic en el botón **Entrenar** para empezar a entrenar el modelo. Una vez completado el proceso de entrenamiento, verá la siguiente información:

* **Id. del modelo**: el identificador del modelo que se ha creado y entrenado. Cada llamada de entrenamiento crea un nuevo modelo con su propio identificador. Copie esta cadena en una ubicación segura; la necesitará si desea realizar llamadas de predicción mediante la API REST.
* **Precisión media**: el promedio de precisión del modelo. Puede mejorar la precisión del modelo mediante el etiquetado de formularios adicionales y el entrenamiento de nuevo para crear un nuevo modelo. Se recomienda empezar por etiquetar cinco formularios y agregar más formularios según sea necesario.
* La lista de etiquetas y la precisión estimada por etiqueta.

![Vista de entrenamiento](../media/label-tool/train-screen.png)

Una vez finalizado el entrenamiento, examine el valor de **Precisión media**. Si es bajo, debe agregar más documentos de entrada y repetir los pasos anteriores. Los documentos que ya ha etiquetado permanecerán en el índice del proyecto.

> [!TIP]
> También puede ejecutar el proceso de entrenamiento con una llamada a la API REST. Para aprender a hacerlo, consulte [Entrenamiento con etiquetas mediante Python](./python-labeled-data.md).

## <a name="analyze-a-form"></a>Análisis de un formulario

Haga clic en el icono Predecir (rectángulos) de la izquierda para probar el modelo. Cargue un documento de formulario que no haya utilizado en el proceso de entrenamiento. A continuación, haga clic en el botón **Predecir** de la derecha para obtener las predicciones de clave y valor del formulario. La herramienta aplicará etiquetas en los cuadros de límite e informará de la confianza de cada etiqueta.

> [!TIP]
> También puede ejecutar la API de análisis con una llamada a REST. Para aprender a hacerlo, consulte [Entrenamiento con etiquetas mediante Python](./python-labeled-data.md).

## <a name="improve-results"></a>Mejora de los resultados

Según la precisión notificada, puede que desee realizar un entrenamiento adicional para mejorar el modelo. Después de haber realizado una predicción, examine los valores de confianza de cada una de las etiquetas aplicadas. Si el valor de precisión media del entrenamiento es alto, pero las puntuaciones de confianza son bajas (o los resultados son inexactos), debe agregar el archivo que se utilizó para la predicción al conjunto de entrenamiento, etiquetarlo y realizar el entrenamiento de nuevo.

La precisión media notificada, las puntuaciones de confianza y la precisión real pueden ser incoherentes si los documentos que se analizan son diferentes de los usados en el entrenamiento. Tenga en cuenta que algunos documentos tienen un aspecto similar cuando los ven los usuarios, pero pueden parecer distintos para el modelo de IA. Por ejemplo, puede realizar el entrenamiento con un tipo de formulario que tiene dos variantes, en el que el conjunto de entrenamiento consta de un 20 % perteneciente a la variante A y un 80 % a la variante B. Durante la predicción, es probable que las puntuaciones de confianza de los documentos de la variante A sean menores.

## <a name="save-a-project-and-resume-later"></a>Guardar un proyecto y reanudarlo más tarde

Para reanudar el proyecto en otro momento o en otro explorador, debe guardar el token de seguridad del proyecto y volver a escribirlo más tarde. 

### <a name="get-project-credentials"></a>Obtención de las credenciales del proyecto
Vaya a la página de configuración del proyecto (icono de control deslizante) y anote el nombre del token de seguridad. A continuación, vaya a la configuración de la aplicación (icono de engranaje), que muestra todos los tokens de seguridad de la instancia actual del explorador. Busque el token de seguridad del proyecto y copie su nombre y valor de clave en una ubicación segura.

### <a name="restore-project-credentials"></a>Restauración de las credenciales del proyecto
Si desea reanudar el proyecto, primero debe crear una conexión al mismo contenedor de almacenamiento de blobs. Para ello, siga los pasos anteriores. A continuación, vaya a la página de configuración de la aplicación (icono de engranaje) y compruebe si el token de seguridad del proyecto está allí. Si no es así, agregue un nuevo token de seguridad y copie el nombre y la clave del token del paso anterior. Después, haga clic en Guardar configuración. 

### <a name="resume-a-project"></a>Reanudación de un proyecto
Por último, vaya a la página principal (icono de casa) y haga clic en Open Cloud Project (Abrir proyecto en la nube). Después, seleccione la conexión a Blob Storage y seleccione el archivo *.vott* del proyecto. La aplicación cargará todos los valores del proyecto ya que tiene el token de seguridad.

## <a name="next-steps"></a>Pasos siguientes

En este inicio rápido, ha aprendido a utilizar la herramienta de etiquetado de ejemplo Form Recognizer para entrenar un modelo con datos etiquetados manualmente. Si desea integrar la herramienta de etiquetado en su propia aplicación, use las API REST que se ocupan del entrenamiento de datos etiquetados.

> [!div class="nextstepaction"]
> [Entrenamiento con etiquetas mediante Python](./python-labeled-data.md)
