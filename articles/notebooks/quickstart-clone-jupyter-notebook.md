---
title: Clonación de un cuaderno de Jupyter Notebook desde GitHub con Azure Notebooks (versión preliminar)
description: Clone rápidamente un cuaderno de Jupyter Notebook desde un repositorio de GitHub y ejecútelo en su cuenta de Azure Notebooks.
ms.topic: quickstart
ms.date: 12/04/2018
ms.openlocfilehash: 8aa88008ece170a5eed7ab491e3318aed5168923
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/03/2020
ms.locfileid: "75647108"
---
# <a name="quickstart-clone-a-notebook-in-azure-notebooks-preview"></a>Inicio rápido: Clonación de un cuaderno en Azure Notebooks (versión preliminar)

Muchos desarrolladores y científicos de datos almacenan los cuadernos en [repositorios de GitHub](https://github.com), un servicio gratuito que proporciona control de versiones y almacenamiento para muchos tipos de proyectos diferentes. GitHub a menudo se usa como un medio de colaboración en cuadernos de Jupyter Notebook que se ejecutan localmente. En tales casos, cada colaborador mantiene una copia local del repositorio y ejecuta los cuadernos desde esa copia.

La clonación crea una copia de un cuaderno de GitHub en la cuenta de Azure Notebooks en su lugar. Este clon es independiente de su repositorio original; los cambios solo se almacenan en la cuenta de Azure Notebooks y no afectan al original. Dado que el clon se encuentra en la nube, puede compartir el proyecto con otros colaboradores que no necesitan realizar ninguna copia local o incluso que tengan Jupyter instalado en sus propios equipos. También puede clonar un cuaderno simplemente como un punto inicial para un proyecto de su propiedad o para obtener archivos de datos.

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

## <a name="clone-azure-cognitive-services-notebooks"></a>Clonación de cuadernos de Azure Cognitive Services

1. Vaya a [Azure Notebooks](https://notebooks.azure.com) e inicie sesión. (Para más información, consulte [Quickstart - Sign in to Azure Notebooks](quickstart-sign-in-azure-notebooks.md) [Inicio rápido: Inicio de sesión en Azure Notebooks]).

1. En la página del perfil público, seleccione **My Projects** (Mis proyectos) en la parte superior de la página:

    ![Vínculo Mis proyectos en la parte superior de la ventana del explorador](media/quickstarts/my-projects-link.png)

1. En la página **Mis proyectos**, seleccione el botón de flecha arriba (método abreviado de teclado: U; el botón aparece como **Upload GitHub Repo** (Cargar repositorio de GitHub) cuando la ventana del explorador es lo suficientemente ancha):

    ![Comando Upload GitHub Repo en la página Mis proyectos](media/quickstarts/upload-github-repo-command.png)

1. En la opción **Upload GitHub Repository** que aparece, escriba o especifique los siguientes detalles y, a continuación, seleccione **Importar**:

   - **Repositorio de GitHub**: Microsoft/cognitive-services-notebooks (este nombre clona los cuadernos de Jupyter Notebook para Azure Cognitive Services en [https://github.com/Microsoft/cognitive-services-notebooks](https://github.com/Microsoft/cognitive-services-notebooks)).
   - **Clone recursively** (Clonar de forma recursiva): (desactivado)
   - **Project name** (Nombre del proyecto): Clon de Cognitive Services
   - **Identificador del proyecto**: cognitive-services-clone
   - **Público**: (desactivado)

     ![Ventana emergente Upload GitHub Repo para recopilar información del repositorio](media/quickstarts/upload-github-repo-popup.png)

1. Sea paciente mientras se completa el proceso; la clonación de un repositorio puede tardar unos minutos.

1. Una vez finalizada la clonación, Azure Notebooks le lleva al nuevo proyecto, donde podrá ver las copias de todos los archivos.

    [![](media/quickstarts/completed-clone.png "View of a completed clone")](media/quickstarts/completed-clone.png#lightbox)

## <a name="share-a-notebook"></a>Uso compartido de un cuaderno

1. Para compartir la copia del proyecto clonado, use el control **Share** o consiga un vínculo, código HTML o Markdown que contenga el vínculo, o bien cree un mensaje de correo electrónico con el vínculo:

    ![Comando para compartir el proyecto](media/quickstarts/share-project-command.png)

1. Dado que se ha desactivado la opción **Público** al clonar el proyecto, el clon es privado. Para realizar la copia pública, seleccione **Configuración del proyecto**, establezca la opción **Proyecto público** en la ventana emergente y, a continuación, seleccione **Guardar**.

1. Seleccione un cuaderno en el proyecto para ejecutarlo. Cada cuaderno del repositorio de Azure Cognitive Services, por ejemplo, es su propio inicio rápido independiente. La imagen siguiente muestra el resultado del uso del cuaderno BingImageSearchAPI después de agregar una clave de suscripción de la API de Cognitive Services y cambiar el término de búsqueda "perritos" a "conejitos":

    ![Ejecución del cuaderno de Jupyter Notebook clonado desde GitHub](media/quickstarts/clone-notebook-result.png)

1. Cuando haya terminado de ejecutar el cuaderno, seleccione **Archivo** > **Close and halt** (Cerrar y detener) para cerrar el cuaderno y su ventana del explorador.

1. Para compartir un cuaderno individual en el proyecto, haga clic con el botón derecho en el cuaderno y seleccione **Copiar vínculo** (método abreviado de teclado: y):

    ![Comando del menú contextual para copiar un vínculo en un cuaderno individual](media/quickstarts/copy-link-to-individual-notebook.png)

1. Para editar los archivos que no sean cuadernos, haga clic con el botón derecho en el archivo del proyecto y seleccione **Editar archivo** (método abreviado de teclado: i). La acción predeterminada, **Ejecutar** (método abreviado de teclado: r), solo muestra el contenido del archivo y no permite la edición.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Tutorial: create an run a Jupyter notebook to do linear regression](tutorial-create-run-jupyter-notebook.md) (Tutorial: Creación y ejecución de un cuaderno de Jupyter Notebook para hacer regresión lineal)
