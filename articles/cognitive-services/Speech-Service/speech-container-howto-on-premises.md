---
title: 'Uso con Kubernetes y Helm: Servicio de Voz'
titleSuffix: Azure Cognitive Services
description: Mediante el empleo de Kubernetes y Helm para definir las imágenes de contenedor de conversión de voz en texto y de texto en voz, se va a crear un paquete de Kubernetes. Este paquete se va a implementar en un clúster de Kubernetes en el entorno local.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 7/16/2019
ms.author: dapine
ms.openlocfilehash: 420ac45b7d3b5e97772b1aa712ba6b8442ac1de2
ms.sourcegitcommit: 39d95a11d5937364ca0b01d8ba099752c4128827
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/16/2019
ms.locfileid: "69562751"
---
# <a name="use-with-kubernetes-and-helm"></a>Uso con Kubernetes y Helm

Una opción para administrar los contenedores de Speech locales es usar Kubernetes y Helm. Mediante el empleo de Kubernetes y Helm para definir las imágenes de contenedor de conversión de voz en texto y de texto en voz, se va a crear un paquete de Kubernetes. Este paquete se va a implementar en un clúster de Kubernetes en el entorno local. Por último, se va a explicar cómo probar los servicios implementados y diversas opciones de configuración. Para obtener más información sobre cómo ejecutar contenedores de Docker sin orquestación de Kubernetes, vea [Instalar y ejecutar contenedores de Speech Service](speech-container-howto.md).

## <a name="prerequisites"></a>Requisitos previos

Requisitos previos para poder usar los contenedores de Voz en el entorno local:

|Obligatorio|Propósito|
|--|--|
| Cuenta de Azure | Si no tiene una suscripción a Azure, cree una [cuenta gratuita][free-azure-account] antes de empezar. |
| Acceso a Container Registry | Para incorporar las imágenes de Docker al clúster, Kubernetes necesita acceso al registro de contenedor. En primer lugar tiene que [solicitar acceso al registro de contenedor][speech-preview-access]. |
| CLI de Kubernetes | La [CLI de Kubernetes][kubernetes-cli] es necesaria para administrar las credenciales compartidas desde el registro de contenedor. Kubernetes también se necesita antes que Helm, que es el administrador de paquetes de Kubernetes. |
| CLI de Helm | Como parte de la instalación de la [CLI de Helm][helm-install], también tendrá que inicializar Helm, que instalará [Tiller][tiller-install]. |
|Recurso de Voz |Para usar estos contenedores, debe tener:<br><br>Un recurso de _Voz_ de Azure para obtener la clave de facturación asociada y el URI del punto de conexión de facturación. Ambos valores están disponibles en las páginas de claves y de introducción de **Voz** de Azure Portal y son necesarios para iniciar el contenedor.<br><br>**{API_KEY}** : clave de recurso<br><br>**{ENDPOINT_URI}** : el ejemplo de URI de punto de conexión es: `https://westus.api.cognitive.microsoft.com/sts/v1.0`|

## <a name="the-recommended-host-computer-configuration"></a>Configuración de equipo host recomendada

Vea los detalles de [Equipo host del contenedor del servicio Voz][speech-container-host-computer] como referencia. En este *gráfico de Helm* se calculan automáticamente los requisitos de CPU y memoria en función de cuántas descodificaciones (solicitudes simultáneas) especifica el usuario. Además, se ajusta en función de si las optimizaciones de entrada de audio o texto están configuradas como `enabled`. Los valores predeterminados del gráfico de Helm son dos solicitudes simultáneas y la deshabilitación de la optimización.

| Servicio | CPU o contenedor | Memoria o contenedor |
|--|--|--|
| **Voz a texto** | un descodificador requiere un mínimo de 1150 milésimas de núcleo. Si `optimizedForAudioFile` está habilitado, se requieren 1950 milésimas de núcleo. (valor predeterminado: dos descodificadores) | Requerido: 2 GB<br>Limitado:  4 GB |
| **Texto a voz** | una solicitud simultánea requiere un mínimo de 500 milésimas de núcleo. Si `optimizeForTurboMode` está habilitado, se requieren 1000 milésimas de núcleo. (valor predeterminado: dos solicitudes simultáneas) | Requerido: 1 GB<br> Limitado: 2 GB |

## <a name="connect-to-the-kubernetes-cluster"></a>Conectar al clúster de Kubernetes

Se espera que el equipo host tenga un clúster de Kubernetes disponible. Vea este tutorial sobre la [implementación de un clúster de Kubernetes](../../aks/tutorial-kubernetes-deploy-cluster.md) para lograr un reconocimiento conceptual de cómo implementar un clúster de Kubernetes en un equipo host.

### <a name="sharing-docker-credentials-with-the-kubernetes-cluster"></a>Uso compartido de credenciales de Docker con el clúster de Kubernetes

Para permitir que el clúster de Kubernetes `docker pull` las imágenes configuradas del registro de contenedor `containerpreview.azurecr.io`, debe transferir las credenciales de Docker al clúster. Ejecute el comando [`kubectl create`][kubectl-create] siguiente para crear un *secreto de registro de Docker* basado en las credenciales proporcionadas en el requisito previo de acceso al registro de contenedor.

Ejecute el siguiente comando desde la interfaz de la línea de comandos que prefiera. Asegúrese de reemplazar `<username>`, `<password>` y `<email-address>` por las credenciales del registro de contenedor.

```console
kubectl create secret docker-registry containerpreview \
    --docker-server=containerpreview.azurecr.io \
    --docker-username=<username> \
    --docker-password=<password> \
    --docker-email=<email-address>
```

> [!NOTE]
> Si ya tiene acceso al registro de contenedor `containerpreview.azurecr.io`, puede crear un secreto de Kubernetes con la marca genérica en su lugar. Tenga en cuenta el siguiente comando que se ejecuta en el JSON de configuración de Docker.
> ```console
>  kubectl create secret generic containerpreview \
>      --from-file=.dockerconfigjson=~/.docker/config.json \
>      --type=kubernetes.io/dockerconfigjson
> ```

El siguiente resultado se imprime en la consola una vez que el secreto se ha creado correctamente.

```console
secret "containerpreview" created
```

Para comprobar que el secreto se ha creado, ejecute [`kubectl get`][kubectl-get] con la marca `secrets`.

```console
kuberctl get secrets
```

Al ejecutar `kubectl get secrets`, se imprimen todos los secretos configurados.

```console
NAME                  TYPE                                  DATA      AGE
containerpreview      kubernetes.io/dockerconfigjson        1         30s
```

## <a name="configure-helm-chart-values-for-deployment"></a>Configuración de los valores del gráfico de Helm para la implementación

Visite [Microsoft Helm Hub][ms-helm-hub] para ver todos los gráficos de Helm disponibles públicamente que ofrece Microsoft. En Microsoft Helm Hub se encuentra el **gráfico Cognitive Services Speech On-Premises**. **Cognitive Services Speech On-Premises** es el gráfico que se va a instalar, aunque primero se debe crear un archivo `config-values.yaml` con configuraciones explícitas. Vamos a comenzar por agregar el repositorio de Microsoft a la instancia de Helm.

```console
helm repo add microsoft https://microsoft.github.io/charts/repo
```

Luego vamos a configurar los valores del gráfico de Helm. Copie y pegue el siguiente YAML en un archivo denominado `config-values.yaml`. Para obtener más información sobre la personalización del **gráfico de Helm Cognitive Services Speech On-Premises**, vea [Personalizar gráficos de Helm](#customize-helm-charts). Reemplace los comentarios `# {ENDPOINT_URI}` y `# {API_KEY}` por sus propios valores.

```yaml
# These settings are deployment specific and users can provide customizations

# speech-to-text configurations
speechToText:
  enabled: true
  numberOfConcurrentRequest: 3
  optimizeForAudioFile: true
  image:
    registry: containerpreview.azurecr.io
    repository: microsoft/cognitive-services-speech-to-text
    tag: latest
    pullSecrets:
      - containerpreview # Or an existing secret
    args:
      eula: accept
      billing: # {ENDPOINT_URI}
      apikey: # {API_KEY}

# text-to-speech configurations
textToSpeech:
  enabled: true
  numberOfConcurrentRequest: 3
  optimizeForTurboMode: true
  image:
    registry: containerpreview.azurecr.io
    repository: microsoft/cognitive-services-text-to-speech
    tag: latest
    pullSecrets:
      - containerpreview # Or an existing secret
    args:
      eula: accept
      billing: # {ENDPOINT_URI}
      apikey: # {API_KEY}
```

> [!IMPORTANT]
> Si no se proporcionan los valores `billing` y `apikey`, los servicios expiran pasados 15 minutos. Del mismo modo, se produce un error de comprobación, ya que los servicios no están disponibles.

### <a name="the-kubernetes-package-helm-chart"></a>Paquete de Kubernetes (gráfico de Helm)

El *gráfico de Helm* contiene la configuración de las imágenes de Docker que se van a extraer del registro de contenedor `containerpreview.azurecr.io`.

> Un [gráfico de Helm][helm-charts] es una colección de archivos que describen un conjunto relacionado de recursos de Kubernetes. Un solo gráfico se podría usar para implementar algo sencillo, como un pod almacenado en memoria, o complejo, como una pila de aplicación web completa con servidores HTTP, bases de datos, memorias caché, etc.

Los *gráficos de Helm* proporcionados extraen las imágenes de Docker del servicio Voz, los servicios de conversión de texto en voz y de voz en texto desde el registro de contenedor `containerpreview.azurecr.io`.

## <a name="install-the-helm-chart-on-the-kubernetes-cluster"></a>Instalación del gráfico de Helm en el clúster de Kubernetes

Para instalar el *gráfico de Helm*, se debe ejecutar el comando [`helm install`][helm-install-cmd], reemplazando `<config-values.yaml>` por el argumento de ruta de acceso y nombre de archivo adecuado. El gráfico de Helm `microsoft/cognitive-services-speech-onpremise` al que se hace referencia a continuación está disponible en [Microsoft Helm Hub aquí][ms-helm-hub-speech-chart].

```console
helm install microsoft/cognitive-services-speech-onpremise \
    --version 0.1.0 \
    --values <config-values.yaml> \
    --name onprem-speech
```

Este es el resultado de ejemplo que se puede ver tras una ejecución de instalación correcta:

```console
NAME:   onprem-speech
LAST DEPLOYED: Tue Jul  2 12:51:42 2019
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/Pod(related)
NAME                             READY  STATUS             RESTARTS  AGE
speech-to-text-7664f5f465-87w2d  0/1    Pending            0         0s
speech-to-text-7664f5f465-klbr8  0/1    ContainerCreating  0         0s
text-to-speech-56f8fb685b-4jtzh  0/1    ContainerCreating  0         0s
text-to-speech-56f8fb685b-frwxf  0/1    Pending            0         0s

==> v1/Service
NAME            TYPE          CLUSTER-IP    EXTERNAL-IP  PORT(S)       AGE
speech-to-text  LoadBalancer  10.0.252.106  <pending>    80:31811/TCP  1s
text-to-speech  LoadBalancer  10.0.125.187  <pending>    80:31247/TCP  0s

==> v1beta1/PodDisruptionBudget
NAME                                MIN AVAILABLE  MAX UNAVAILABLE  ALLOWED DISRUPTIONS  AGE
speech-to-text-poddisruptionbudget  N/A            20%              0                    1s
text-to-speech-poddisruptionbudget  N/A            20%              0                    1s

==> v1beta2/Deployment
NAME            READY  UP-TO-DATE  AVAILABLE  AGE
speech-to-text  0/2    2           0          0s
text-to-speech  0/2    2           0          0s

==> v2beta2/HorizontalPodAutoscaler
NAME                       REFERENCE                  TARGETS        MINPODS  MAXPODS  REPLICAS  AGE
speech-to-text-autoscaler  Deployment/speech-to-text  <unknown>/50%  2        10       0         0s
text-to-speech-autoscaler  Deployment/text-to-speech  <unknown>/50%  2        10       0         0s


NOTES:
cognitive-services-speech-onpremise has been installed!
Release is named onprem-speech
```

La implementación de Kubernetes puede tardar varios minutos en completarse. Para confirmar que los pods y los servicios se han implementado correctamente y están disponibles, ejecute el siguiente comando:

```console
kubectl get all
```

Debería ver algo parecido al resultado siguiente:

```console
NAME                                  READY     STATUS    RESTARTS   AGE
pod/speech-to-text-7664f5f465-87w2d   1/1       Running   0          34m
pod/speech-to-text-7664f5f465-klbr8   1/1       Running   0          34m
pod/text-to-speech-56f8fb685b-4jtzh   1/1       Running   0          34m
pod/text-to-speech-56f8fb685b-frwxf   1/1       Running   0          34m

NAME                     TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)        AGE
service/kubernetes       ClusterIP      10.0.0.1       <none>           443/TCP        3h
service/speech-to-text   LoadBalancer   10.0.252.106   52.162.123.151   80:31811/TCP   34m
service/text-to-speech   LoadBalancer   10.0.125.187   65.52.233.162    80:31247/TCP   34m

NAME                             DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/speech-to-text   2         2         2            2           34m
deployment.apps/text-to-speech   2         2         2            2           34m

NAME                                        DESIRED   CURRENT   READY     AGE
replicaset.apps/speech-to-text-7664f5f465   2         2         2         34m
replicaset.apps/text-to-speech-56f8fb685b   2         2         2         34m

NAME                                                            REFERENCE                   TARGETS   MINPODS   MAXPODS   REPLICAS   AGE
horizontalpodautoscaler.autoscaling/speech-to-text-autoscaler   Deployment/speech-to-text   1%/50%    2         10        2          34m
horizontalpodautoscaler.autoscaling/text-to-speech-autoscaler   Deployment/text-to-speech   0%/50%    2         10        2          34m
```

### <a name="verify-helm-deployment-with-helm-tests"></a>Comprobación de la implementación de Helm con pruebas de Helm

Los gráficos de Helm instalados definen *pruebas de Helm*, que resultan cómodas para la comprobación. Estas pruebas validan la disponibilidad del servicio. Para comprobar los servicios de conversión de **voz en texto** y de **texto en voz**, se ejecuta el comando [Helm test][helm-test].

```console
helm test onprem-speech
```

> [!IMPORTANT]
> Se produce un error en estas pruebas si el estado de POD no es `Running` o si la implementación no aparece en la columna `AVAILABLE`. Tenga paciencia, ya que esto puede tardar más de diez minutos en completarse.

Estas pruebas generan distintos resultados de estado:

```console
RUNNING: speech-to-text-readiness-test
PASSED: speech-to-text-readiness-test
RUNNING: text-to-speech-readiness-test
PASSED: text-to-speech-readiness-test
```

Como alternativa a la ejecución de *pruebas de Helm*, puede recopilar las direcciones *IP externas* y los puertos correspondientes desde el comando `kubectl get all`. Con la dirección IP y el puerto, abra un explorador web y vaya a `http://<external-ip>:<port>:/swagger/index.html` para ver las páginas de Swagger de API.

## <a name="customize-helm-charts"></a>Personalizar gráficos de Helm

Los gráficos de Helm son jerárquicos. La jerarquía permite la herencia de gráficos y, además, proporciona el concepto de especificidad, que consiste en que los valores más específicos invalidan las reglas heredadas.

[!INCLUDE [Speech umbrella-helm-chart-config](includes/speech-umbrella-helm-chart-config.md)]

[!INCLUDE [Speech-to-Text Helm Chart Config](includes/speech-to-text-chart-config.md)]

[!INCLUDE [Text-to-Speech Helm Chart Config](includes/text-to-speech-chart-config.md)]

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre la instalación de aplicaciones con Helm en Azure Kubernetes Service (AKS), [visite esta página][installing-helm-apps-in-aks].

> [!div class="nextstepaction"]
> [Contenedores de Cognitive Services][cog-svcs-containers]

<!-- LINKS - external -->
[free-azure-account]: https://azure.microsoft.com/free
[git-download]: https://git-scm.com/downloads
[azure-cli]: https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest
[docker-engine]: https://www.docker.com/products/docker-engine
[kubernetes-cli]: https://kubernetes.io/docs/tasks/tools/install-kubectl
[helm-install]: https://helm.sh/docs/using_helm/#installing-helm
[helm-install-cmd]: https://helm.sh/docs/helm/#helm-install
[tiller-install]: https://helm.sh/docs/install/#installing-tiller
[helm-charts]: https://helm.sh/docs/developing_charts
[speech-preview-access]: https://aka.ms/speechcontainerspreview
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[helm-test]: https://helm.sh/docs/helm/#helm-test
[ms-helm-hub]: https://hub.helm.sh/charts/microsoft
[ms-helm-hub-speech-chart]: https://hub.helm.sh/charts/microsoft/cognitive-services-speech-onpremise

<!-- LINKS - internal -->
[speech-container-host-computer]: speech-container-howto.md#the-host-computer
[installing-helm-apps-in-aks]: ../../aks/kubernetes-helm.md
[cog-svcs-containers]: ../cognitive-services-container-support.md
