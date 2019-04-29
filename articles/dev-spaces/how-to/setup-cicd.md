---
title: Uso de CI/CD con Azure Dev Spaces
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: DrEsteban
ms.author: stevenry
ms.date: 12/17/2018
ms.topic: conceptual
manager: yuvalm
description: Desarrollo rápido de Kubernetes con contenedores y microservicios en Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, contenedores
ms.openlocfilehash: 983af0dd75e6ae62630c85d04ac3819c7e260439
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "60687374"
---
# <a name="use-cicd-with-azure-dev-spaces"></a>Uso de CI/CD con Azure Dev Spaces

En este artículo se le guía a través de la implementación continua y la integración continua (CI/CD) en Azure Kubernetes Service (AKS) con Dev Spaces habilitado. CI/CD en AKS permite que las actualizaciones de aplicaciones se implementen automáticamente cada vez que se inserta código confirmado en el repositorio de origen. El hecho de usar CI/CD junto con un clúster habilitado para Dev Spaces resulta útil porque puede mantener actualizada una línea base de la aplicación para que el equipo trabaje con ella.

![Diagrama de CI/CD de ejemplo](../media/common/ci-cd-simple.png)

Aunque en este artículo se le guiará por Azure DevOps, son de aplicación los mismos conceptos a sistemas de CI/CD como Jenkins, TeamCity, etc.

## <a name="prerequisites"></a>Requisitos previos
* [Clúster de Azure Kubernetes Service (AKS) con Azure Dev Spaces habilitado](../get-started-netcore.md)
* [CLI de Azure Dev Spaces instalada](upgrade-tools.md)
* [Organización de Azure DevOps con un proyecto](https://docs.microsoft.com/azure/devops/user-guide/sign-up-invite-teammates?view=vsts)
* [Azure Container Registry (ACR)](../../container-registry/container-registry-get-started-azure-cli.md)
    * Detalles disponibles de una [cuenta de administrador](../../container-registry/container-registry-authentication.md#admin-account) de Azure Container Registry
* [Autorización al clúster de AKS para extraer de Azure Container Registry](../../container-registry/container-registry-auth-aks.md)

## <a name="download-sample-code"></a>Descarga de código de ejemplo
Por motivos de tiempo, vamos a crear una bifurcación de nuestro repositorio con código de ejemplo de GitHub. Vaya a https://github.com/Azure/dev-spaces y seleccione **Bifurcación**. Una vez completado el proceso de bifurcación, **clone** su versión local bifurcada del repositorio. De forma predeterminada, se extraerá del repositorio la rama _maestra_, pero hemos incluido para ahorrar tiempo algunos cambios en la rama _azds_updates_, que también debería haberse transferido durante la bifurcación. La rama _azds_updates_ contiene actualizaciones que le pedimos que realizara manualmente en las secciones del tutorial de Dev Spaces, así como algunos archivos JSON y YAML creados previamente para simplificar la implementación del sistema de CI/CD. Puede usar un comando como `git checkout -b azds_updates origin/azds_updates` para extraer la rama _azds_updates_ del repositorio local.

## <a name="dev-spaces-setup"></a>Configuración de Dev Spaces
Cree un espacio llamado _dev_ con el comando `azds space select`. El espacio _dev_ usará la canalización de CI/CD para insertar los cambios de código. También se usará para crear _espacios secundarios_ basados en _dev_.

```cmd
azds space select -n dev
```

Cuando se le pida que seleccione un espacio de desarrollo principal, seleccione _\<ninguno\>_.

Una vez creado el espacio de desarrollo, deberá determinar el sufijo de host. Use el `azds show-context` comando para mostrar el sufijo de host del controlador de entrada de Azure Dev espacios.

```cmd
$ azds show-context
Name   ResourceGroup    DevSpace  HostSuffix
-----  ---------------  --------  ----------------------
MyAKS  MyResourceGroup  dev       fedcba098.eus.azds.io
```

En el ejemplo anterior, el sufijo de host es _fedcba098.eus.azds.io_. Este valor se usa más adelante al crear la definición de versión.

El espacio _dev_ siempre contendrá el estado más reciente del repositorio, una línea base, para que los desarrolladores puedan crear _espacios secundarios_ a partir de _dev_ para probar sus cambios aislados en el contexto de la aplicación de mayor tamaño. Este concepto se trata con más detalle en los tutoriales de Dev Spaces.

## <a name="creating-the-build-definition"></a>Creación de la definición de compilación
Abra el proyecto de equipo de Azure DevOps en un explorador web y vaya a la sección _Canalizaciones_. En primer lugar, haga clic en la foto de perfil de la esquina superior derecha del sitio de Azure DevOps, abra el panel de características en versión preliminar y deshabilite la _nueva experiencia de creación de canalizaciones de YAML_:

![Apertura del panel de características en versión preliminar](../media/common/preview-feature-open.png)

La opción para deshabilitar:

![Opción Nueva experiencia de creación de canalizaciones de YAML](../media/common/yaml-pipeline-preview-feature.png)

> [!Note]
> La característica en versión preliminar _Nueva experiencia de creación de canalizaciones de YAML_de Azure DevOps entra en conflicto con la creación de canalizaciones de compilación predefinidas en este momento. Debe deshabilitarla por ahora para implementar nuestra canalización de compilación predefinida.

En la rama _azds_updates_ hemos incluido una sencilla instancia de [YAML de canalización de Azure](https://docs.microsoft.com/azure/devops/pipelines/yaml-schema?view=vsts&tabs=schema) que define los pasos de compilación necesarios para *mywebapi* y *webfrontend*.

Dependiendo del lenguaje que haya elegido, instancia de YAML de canalización se habrá insertado en una ruta de acceso similar a: `samples/dotnetcore/getting-started/azure-pipelines.dotnetcore.yml`

Para crear una canalización a partir de este archivo:
1. En la página principal del proyecto de DevOps, vaya a las canalizaciones > compilaciones.
1. Seleccione la opción para crear un **New** canalización de compilación.
1. Seleccione **GitHub** autorizar con su cuenta de GitHub como el origen, si es necesario y, a continuación, seleccione el _azds_updates_ Bifurcar desde la versión del repositorio sampleapp dev espacios bifurcada.
1. Seleccione **configuración como código**, o **YAML**, como la plantilla.
1. Ahora verá una página de configuración para la canalización de compilación. Como se mencionó anteriormente, vaya a la ruta de acceso específicos del idioma para el **ruta del archivo YAML** utilizando el **...**  botón. Por ejemplo, `samples/dotnetcore/getting-started/azure-pipelines.dotnet.yml`.
1. Vaya a la **Variables** ficha.
1. Agregue manualmente _dockerId_ como variable, que es el nombre de usuario de su [cuenta de administrador de Azure Container Registry](../../container-registry/container-registry-authentication.md#admin-account). (Se menciona en los requisitos previos del artículo).
1. Agregue manualmente _dockerPassword_ como variable, que es la contraseña de su [cuenta de administrador de Azure Container Registry](../../container-registry/container-registry-authentication.md#admin-account). No olvide especificar _dockerPassword_ como secreto (seleccionando el icono de candado) por motivos de seguridad.
1. Seleccione **guardar y poner en cola**.

Ahora tiene una solución de CI que compilará automáticamente *mywebapi* y *webfrontend* para cualquier actualización que se inserte en la rama _azds_updates_ de la bifurcación de GitHub. Puede comprobar las imágenes de Docker se han insertado navegar al portal de Azure, seleccionando el registro de contenedor de Azure y examinar el **repositorios** ficha. Puede tardar varios minutos para las imágenes generar y aparecen en el registro de contenedor.

![Repositorios de Azure Container Registry](../media/common/ci-cd-images-verify.png)

## <a name="creating-the-release-definition"></a>Creación de la definición de versión

1. En la página principal del proyecto de DevOps, vaya a Canalizaciones > Versiones.
1. Si está trabajando en un nuevo proyecto de DevOps que aún no contiene una definición de versión, deberá crear primero una definición de versión vacía para continuar. La opción de importación no se muestra en la interfaz de usuario hasta que tenga una definición de versión existente.
1. En el lado izquierdo, haga clic en el **+ nuevo** botón y, después, haga clic en **importar una canalización**.
1. Haga clic en **examinar** y seleccione `samples/release.json` desde el proyecto.
1. Haga clic en **OK**. Observe que el panel de la canalización se ha cargado con la página de edición de la definición de versión. Fíjese también en que hay algunos iconos de advertencia de color rojo que indican los detalles específicos del clúster que todavía se deben configurar.
1. A la izquierda del panel Canalización, haga clic en **Agregar un artefacto**.
1. En el **origen** lista desplegable, seleccione la compilación de canalización que creó anteriormente.
1. Para el **versión predeterminada**, elija **más reciente de la rama de compilación canalización predeterminada con etiquetas**.
1. Deje **etiquetas** vacía.
1. Establezca el **Alias de origen** en `drop`. El **alias de origen** valor se utiliza por las tareas de versión predefinidas, por lo que debe establecerse.
1. Haga clic en **Agregar**.
1. Ahora haga clic en el icono de rayo en el origen del artefacto `drop` recién creado, como se muestra a continuación:

    ![Configuración de la implementación continua del artefacto de versión](../media/common/release-artifact-cd-setup.png)
1. Habilitar la **desencadenador de implementación continua**.
1. Mantenga el mouse sobre el **tareas** junto a la pestaña **canalización** y haga clic en _dev_ para editar el _dev_ almacenar provisionalmente las tareas.
1. Comprobar **Azure Resource Manager** está seleccionado en **tipo de conexión.** y verá los tres controles de lista desplegable resaltados en rojo: ![Configuración de la definición de versión](../media/common/release-setup-tasks.png)
1. Seleccione la suscripción de Azure que se usa con espacios de desarrollo de Azure. Es posible que también tenga que hacer clic en **Authorize**.
1. Seleccione el grupo de recursos y el clúster que se usa con espacios de desarrollo de Azure.
1. Haga clic en **trabajo del agente**.
1. Seleccione **hospedado Ubuntu 1604** en **grupo de agentes**.
1. Mantenga el mouse sobre el **tareas** selector en la parte superior, haga clic en _prod_ para editar el _prod_ almacenar provisionalmente las tareas.
1. Comprobar **Azure Resource Manager** está seleccionado en **tipo de conexión.** y seleccione la suscripción de Azure, el grupo de recursos y el clúster que usa con espacios de desarrollo de Azure.
1. Haga clic en **trabajo del agente**.
1. Seleccione **hospedado Ubuntu 1604** en **grupo de agentes**.
1. Haga clic en el **Variables** tab para actualizar las variables para su lanzamiento.
1. Actualice el valor de **DevSpacesHostSuffix** desde **UPDATE_ME** a su sufijo de host. El sufijo de host se muestra cuando ejecutó el `azds show-context` comando anteriormente.
1. Haga clic en **Guardar** en la esquina superior derecha y, luego, en **Aceptar**.
1. Haga clic en **+ Versión** (junto al botón Guardar) y en **Crear una versión**.
1. En **artefactos**, compruebe que la compilación más reciente de la canalización de compilación está seleccionada.
1. Haga clic en **Create**(Crear).

Ahora se iniciará un proceso de versiones automatizado, que implementa los gráficos *mywebapi* y *webfrontend* en su clúster de Kubernetes en el espacio de nivel superior _dev_. Puede supervisar el progreso de la versión en el portal web de DevOps de Azure:

1. Navegue hasta la **versiones** sección **canalizaciones**.
1. Haga clic en la canalización de versiones para la aplicación de ejemplo.
1. Haga clic en el nombre de la versión más reciente.
1. Mantenga el mouse sobre **dev** cuadro bajo **fases** y haga clic en **registros**.

La versión se realiza cuando se completan todas las tareas.

> [!TIP]
> Si se produce un error en la versión con un mensaje de error como *UPGRADE FAILED: timed out waiting for the condition* (ERROR EN LA ACTUALIZACIÓN: se agotó el tiempo de espera para la condición), intente inspeccionar los pods del clúster [mediante el panel de Kubernetes](../../aks/kubernetes-dashboard.md). Si ve que se producen errores al iniciar los pods, con mensajes de error como *Failed to pull image "azdsexample.azurecr.io/mywebapi:122": rpc error: code = Unknown desc = Error response from daemon: Get https://azdsexample.azurecr.io/v2/mywebapi/manifests/122: unauthorized: authentication required* (No se puede extraer imagen 'azdsexample.azurecr.io/mywebapi:122': error rpc: código = desc desconocido = error de respuesta de demonio: Obtener https://azdsexample.azurecr.io/v2/mywebapi/manifests/122: no autorizado:se requiere autenticación), es posible que el clúster no tenga autorización para extraer de Azure Container Registry. Asegúrese de que ha completado el requisito previo [Autorización al clúster de AKS para extraer de Azure Container Registry](../../container-registry/container-registry-auth-aks.md).

Ahora dispone de una canalización de CI/CD completamente automatizada para su bifurcación de GitHub de las aplicaciones de ejemplo de Dev Spaces. Cada vez que confirme e inserte el código, la canalización de compilación compilará e insertará las imágenes *mywebapi* y *webfrontend* en la instancia de ACR personalizada. A continuación, la canalización de versión implementará el gráfico de Helm para cada aplicación en el espacio _dev_ en el clúster habilitado para Dev Spaces.

## <a name="accessing-your-dev-services"></a>Acceso a sus servicios de _dev_
Después de la implementación, la versión _dev_ de *webfrontend* puede obtenerse con una dirección URL pública, como: `http://dev.webfrontend.fedcba098.eus.azds.io`. Puede encontrar esta dirección URL mediante la ejecución de la `azds list-uri` comando: 

```cmd
$ azds list-uris

Uri                                           Status
--------------------------------------------  ---------
http://dev.webfrontend.fedcba098.eus.azds.io  Available
```

## <a name="deploying-to-production"></a>Implementación en producción

Para promover manualmente una versión particular a _prod_ con el sistema de CI/CD creado en este tutorial:
1. Navegue hasta la **versiones** sección **canalizaciones**.
1. Haga clic en la canalización de versiones para la aplicación de ejemplo.
1. Haga clic en el nombre de la versión más reciente.
1. Mantenga el mouse sobre el **prod** cuadro bajo **fases** y haga clic en **implementar**.
    ![Promover a producción](../media/common/prod-promote.png)
1. Mantenga el mouse sobre **prod** cuadro nuevamente en **fases** y haga clic en **registros**.

La versión se realiza cuando se completan todas las tareas.

El _prod_ fase de la canalización de CI/CD utiliza un equilibrador de carga en lugar del controlador Ingress de espacios de desarrollo para proporcionar acceso a _prod_ servicios. Los servicios implementan en el _prod_ fase son accesibles como direcciones IP en lugar de nombres DNS. En un entorno de producción, puede crear su propio controlador de entrada para hospedar los servicios según su propia configuración de DNS.

Para determinar la dirección IP del servicio webfrontend, haga clic en el **imprimir IP público de webfrontend** paso para expandir la salida del registro. Use la dirección IP que se muestra en el registro de salida para tener acceso a la **webfrontend** aplicación.

```cmd
...
2019-02-25T22:53:02.3237187Z webfrontend can be accessed at http://52.170.231.44
2019-02-25T22:53:02.3320366Z ##[section]Finishing: Print webfrontend public IP
...
```

## <a name="dev-spaces-instrumentation-in-production"></a>Instrumentación de Dev Spaces en producción
Aunque la instrumentación de Dev Spaces se ha desarrollado para _no_ interponerse en el funcionamiento normal de la aplicación, se recomienda ejecutar las cargas de trabajo de producción en un espacio de nombres de Kubernetes que no esté habilitado con Dev Spaces. El uso de este tipo de espacio de nombres de Kubernetes implica que debe crear el espacio de nombres de producción mediante la CLI de `kubectl`, o permitir que el sistema de CI/CD lo cree durante la primera implementación de Helm. _Seleccionar_ o bien crear un espacio con las herramientas de Dev Spaces agregará instrumentación de Dev Spaces a ese espacio de nombres.

Este es un ejemplo de estructura de espacio de nombres que admite el desarrollo de características, el entorno "dev" _y_ producción, todo en un único clúster de Kubernetes:

![Ejemplo de estructura de espacio de nombres](../media/common/cicd-namespaces.png)

> [!Tip]
> Si ya ha creado un espacio de `prod`, y simplemente desea excluirlo de la instrumentación de Dev Spaces (¡sin eliminarlo!), puede hacerlo con el siguiente comando de la CLI de Dev Spaces:
>
> `azds space remove -n prod --no-delete`
>
> Es posible que deba eliminar todos los pods del espacio de nombres de `prod` una vez hecho esto para que puedan volver a crearse sin instrumentación de Dev Spaces.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Obtenga información sobre el desarrollo en equipo con Azure Dev Spaces](../team-development-netcore.md)