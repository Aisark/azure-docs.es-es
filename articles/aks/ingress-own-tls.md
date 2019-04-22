---
title: Uso de sus propios certificados TLS para la entrada con el clúster de Azure Kubernetes Service (AKS)
description: Aprenda a instalar y configurar un controlador de entrada NGINX que use sus propios certificados en un clúster de Azure Kubernetes Service (AKS).
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 03/27/2019
ms.author: iainfou
ms.openlocfilehash: e20f881d740c5d5b73c23c933ceb3d6f19e78ef9
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/17/2019
ms.locfileid: "59683858"
---
# <a name="create-an-https-ingress-controller-and-use-your-own-tls-certificates-on-azure-kubernetes-service-aks"></a>Creación de un controlador de entrada HTTPS y uso de sus propios certificados TLS en Azure Kubernetes Service (AKS)

Un controlador de entrada es un software que proporciona el proxy inverso, el enrutamiento del tráfico configurable y la terminación de TLS para los servicios de Kubernetes. Los recursos de entrada de Kubernetes se usan para configurar las reglas de entrada y las rutas de los distintos servicios de Kubernetes. Mediante reglas de entrada y un controlador de entrada, se puede usar una sola dirección IP para enrutar el tráfico a varios servicios en un clúster de Kubernetes.

En este artículo se muestra cómo implementar el [controlador de entrada NGINX][nginx-ingress] en un clúster de Azure Kubernetes Service (AKS). Generará sus propios certificados y creará un secreto de Kubernetes para su uso con la ruta de entrada. Por último, en el clúster de AKS se ejecutan dos aplicaciones, a las que se puede acceder con una sola dirección IP.

También puede:

- [Crear un controlador de entrada básico con conectividad de red externa][aks-ingress-basic]
- [Habilitar el complemento de enrutamiento de aplicación HTTP][aks-http-app-routing]
- [Crear un controlador de entrada que usa una red privada interna y una dirección IP][aks-ingress-internal]
- Crear un controlador de entrada que use Let's Encrypt para generar certificados TLS de forma automática [con una dirección IP pública dinámica][aks-ingress-tls] o [con una dirección IP pública estática][aks-ingress-static-tls]

## <a name="before-you-begin"></a>Antes de empezar

En este artículo se usa Helm para instalar el controlador de entrada NGINX y una aplicación web de ejemplo. Debe tener Helm inicializado dentro del clúster de AKS y usar una cuenta de servicio para Tiller. Asegúrese de que está usando la versión más reciente de Helm. Para obtener instrucciones de actualización, vea la [documentación de instalación de Helm][helm-install]. Para más información sobre cómo configurar y usar Helm, consulte el artículo sobre la [instalación de aplicaciones con Helm en Azure Kubernetes Service (AKS)][use-helm].

En este artículo también requiere que se está ejecutando la CLI de Azure versión 2.0.61 o posterior. Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Crear un controlador de entrada

Para crear el controlador de entrada, use `Helm` para instalar *nginx-ingress*. Para obtener redundancia adicional, se implementan dos réplicas de los controladores de entrada NGINX con el parámetro `--set controller.replicaCount`. Para sacar el máximo provecho de las réplicas en ejecución del controlador de entrada, asegúrese de que hay más de un nodo en el clúster de AKS.

> [!TIP]
> En el ejemplo siguiente se crea un espacio de nombres de Kubernetes para los recursos de entrada denominado *entrada basic*. Especifique un espacio de nombres para su propio entorno según sea necesario. Si el clúster de AKS no está habilitado de RBAC, agregue `--set rbac.create=false` a los comandos de Helm.

```console
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Use Helm to deploy an NGINX ingress controller
helm install stable/nginx-ingress --namespace ingress-basic --set controller.replicaCount=2
```

Durante la instalación se crea una dirección IP pública de Azure para el controlador de entrada. Esta dirección IP pública es estática durante el período de vida del controlador de entrada. Si elimina el controlador de entrada, se pierde la asignación de dirección IP pública. Si después crea un controlador de entrada adicional, se asigna una dirección IP pública nueva. Si quiere conservar el uso de la dirección IP pública, en su lugar puede [crear un controlador de entrada con una dirección IP pública estática][aks-ingress-static-tls].

Para obtener la dirección IP pública, use el comando `kubectl get service`. La asignación de la dirección IP al servicio puede tardar hasta un minuto.

```
$ kubectl get service -l app=nginx-ingress --namespace ingress-basic

NAME                                          TYPE           CLUSTER-IP    EXTERNAL-IP    PORT(S)                      AGE
virulent-seal-nginx-ingress-controller        LoadBalancer   10.0.48.240   40.87.46.190   80:31159/TCP,443:30657/TCP   7m
virulent-seal-nginx-ingress-default-backend   ClusterIP      10.0.50.5     <none>         80/TCP                       7m
```

Anote esta dirección IP pública, ya que se utiliza en el último paso para probar la implementación.

Todavía no se creó ninguna regla de entrada. Si navega a la dirección IP pública, se muestra la página 404 predeterminada del controlador de entrada NGINX.

## <a name="generate-tls-certificates"></a>Generación de certificados TLS

En este artículo, vamos a generar un certificado autofirmado con `openssl`. Para su uso en producción, debe solicitar un certificado de confianza firmado mediante un proveedor o su propia entidad de certificación (CA). En el paso siguiente, generará un *secreto* de Kubernetes utilizando el certificado TLS y la clave privada que ha generado OpenSSL.

En el ejemplo siguiente, se genera un certificado X509 RSA de 2048 bits válido durante 365 días denominado *aks-ingress-tls.crt*. El archivo de clave privada se denomina *aks-ingress-tls.key*. Un secreto de Kubernetes TLS requiere ambos archivos.

Este artículo utiliza el nombre común del asunto *demo.azure.com*, que no es necesario cambiar. Para su uso en producción, especifique los valores de su propia organización para el parámetro `-subj`:

```console
openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
    -out aks-ingress-tls.crt \
    -keyout aks-ingress-tls.key \
    -subj "/CN=demo.azure.com/O=aks-ingress-tls"
```

## <a name="create-kubernetes-secret-for-the-tls-certificate"></a>Creación de un secreto de Kubernetes para el certificado TLS

Para permitir que Kubernetes use el certificado TLS y la clave privada para el controlador de entrada, debe crear y usar un secreto. El secreto se define una vez y usa el certificado y el archivo de clave que creó en el paso anterior. A continuación, hará referencia a este secreto al definir las rutas de entrada.

En el ejemplo siguiente se crea un secreto llamado *aks-ingress-tls*:

```console
kubectl create secret tls aks-ingress-tls \
    --namespace ingress-basic \
    --key aks-ingress-tls.key \
    --cert aks-ingress-tls.crt
```

## <a name="run-demo-applications"></a>Ejecución de aplicaciones de demostración

Se han configurado un controlador de entrada y un secreto con su certificado. Ahora vamos a ejecutar dos aplicaciones de demostración en el clúster de AKS. En este ejemplo, Helm se usa para implementar dos instancias de una aplicación "Hola mundo" sencilla.

Antes de instalar los gráficos de Helm de ejemplo, agregue el repositorio de ejemplos de Azure al entorno de Helm como se indica a continuación:

```console
helm repo add azure-samples https://azure-samples.github.io/helm-charts/
```

Cree la primera aplicación de demostración desde un gráfico de Helm con el comando siguiente:

```console
helm install azure-samples/aks-helloworld --namespace ingress-basic
```

A continuación, instale una segunda instancia de la aplicación de demostración. Para esta segunda instancia, se especifica un nuevo título de manera que las dos aplicaciones se distingan visualmente. También se especifica un nombre de servicio único:

```console
helm install azure-samples/aks-helloworld \
    --namespace ingress-basic \
    --set title="AKS Ingress Demo" \
    --set serviceName="ingress-demo"
```

## <a name="create-an-ingress-route"></a>Creación de una ruta de entrada

Ambas aplicaciones se ejecutan ahora en el clúster de Kubernetes, sin embargo, están configuradas con un servicio de tipo `ClusterIP`. Por lo tanto, no se puede acceder a ellas desde Internet. Para que estén disponibles de manera pública, cree un recurso de entrada de Kubernetes. El recurso de entrada configura las reglas de enrutamiento del tráfico a una de las dos aplicaciones.

En el ejemplo siguiente, el tráfico a la dirección `https://demo.azure.com/` se enruta al servicio denominado `aks-helloworld`. El tráfico a la dirección `https://demo.azure.com/hello-world-two` se enruta al servicio `ingress-demo`. En este artículo, no es necesario cambiar los nombres de host de demostración. Para su uso en producción, proporcione los nombres especificados como parte del proceso de solicitud y generación del certificado.

> [!TIP]
> Si el nombre de host especificado durante el proceso de solicitud de certificado (el nombre CN) no coincide con el host definido en la ruta de entrada, el controlador de entrada muestra un *certificado falso de controlador de entrada de Kubernetes*. Asegúrese de que los nombres de host de la ruta de entrada y el certificado coinciden.

La sección *tls* indica la ruta de entrada para usar el secreto llamado *aks-ingress-tls* para el host *demo.azure.com*. De nuevo, para su uso en producción, especifique su propia dirección de host.

Cree un archivo denominado `hello-world-ingress.yaml` y cópielo en el ejemplo siguiente de YAML.

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  namespace: ingress-basic
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  tls:
  - hosts:
    - demo.azure.com
    secretName: aks-ingress-tls
  rules:
  - host: demo.azure.com
    http:
      paths:
      - path: /
        backend:
          serviceName: aks-helloworld
          servicePort: 80
      - path: /hello-world-two
        backend:
          serviceName: ingress-demo
          servicePort: 80
```

Cree el recurso de entrada con el comando `kubectl apply -f hello-world-ingress.yaml`.

```
$ kubectl apply -f hello-world-ingress.yaml

ingress.extensions/hello-world-ingress created
```

## <a name="test-the-ingress-configuration"></a>Prueba de la configuración de entrada

Para probar los certificados con nuestro host *demo.azure.com* falso, utilice `curl` y especifique el parámetro *--resolver*. Este parámetro le permite asignar el nombre *demo.azure.com* a la dirección IP pública de su controlador de entrada. Especifique la dirección IP pública de su propio controlador de entrada, como se muestra en el ejemplo siguiente:

```
curl -v -k --resolve demo.azure.com:443:40.87.46.190 https://demo.azure.com
```

No proporcionó ninguna ruta de acceso adicional con la dirección, por lo que el controlador de entrada se establece de manera predeterminada en la ruta */*. Se devuelve la primera aplicación de demostración, tal como se muestra en la siguiente salida de ejemplo reducido:

```
$ curl -v -k --resolve demo.azure.com:443:40.87.46.190 https://demo.azure.com

[...]
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <link rel="stylesheet" type="text/css" href="/static/default.css">
    <title>Welcome to Azure Kubernetes Service (AKS)</title>
[...]
```

El parámetro *-v* de nuestro comando `curl` genera información detallada, incluyendo el certificado TLS recibido. A mitad de la salida de cURL, puede comprobar que se ha utilizado su propio certificado TLS. El parámetro *-k* continúa cargando la página, aunque se esté usando un certificado autofirmado. En el siguiente ejemplo se muestra el certificado *issuer: CN=demo.azure.com; O=aks-ingress-tls* que se ha utilizado:

```
[...]
* Server certificate:
*  subject: CN=demo.azure.com; O=aks-ingress-tls
*  start date: Oct 22 22:13:54 2018 GMT
*  expire date: Oct 22 22:13:54 2019 GMT
*  issuer: CN=demo.azure.com; O=aks-ingress-tls
*  SSL certificate verify result: self signed certificate (18), continuing anyway.
[...]
```

A continuación, agregue la ruta de acceso */hello-world-two* a la dirección, como `https://demo.azure.com/hello-world-two`. Se devuelve la segunda aplicación de demostración con el título personalizado, tal como se muestra en la siguiente salida de ejemplo reducido:

```
$ curl -v -k --resolve demo.azure.com:443:137.117.36.18 https://demo.azure.com/hello-world-two

[...]
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <link rel="stylesheet" type="text/css" href="/static/default.css">
    <title>AKS Ingress Demo</title>
[...]
```

## <a name="clean-up-resources"></a>Limpieza de recursos

En este artículo, se usa Helm para instalar los componentes de entrada y las aplicaciones de ejemplo. Al implementar un gráfico de Helm, se crean algunos recursos de Kubernetes. Estos recursos incluyen pods, implementaciones y servicios. Para limpiar estos recursos, puede eliminar el espacio de nombres de ejemplo completo, o los recursos individuales.

### <a name="delete-the-sample-namespace-and-all-resources"></a>Eliminar el espacio de nombres de ejemplo y todos los recursos

Para eliminar el espacio de nombres de ejemplo completo, use el `kubectl delete` comando y especifique el nombre del espacio de nombres. Se eliminan todos los recursos en el espacio de nombres.

```console
kubectl delete namespace ingress-basic
```

A continuación, quite el repositorio de Helm para la aplicación hello world AKS:

```console
helm repo remove azure-samples
```

### <a name="delete-resources-individually"></a>Eliminar recursos individualmente

Como alternativa, un enfoque más granular consiste en eliminar los recursos individuales que se creó. Lista lo Helm se publica con el `helm list` comando. Busque los gráficos denominados *nginx-ingress* y *aks-helloworld*, tal y como se muestra en la salida del ejemplo siguiente:

```
$ helm list

NAME            REVISION    UPDATED                     STATUS      CHART                   APP VERSION NAMESPACE
virulent-seal   1           Tue Oct 23 16:37:24 2018    DEPLOYED    nginx-ingress-0.22.1    0.15.0      kube-system
billowing-guppy 1           Tue Oct 23 16:41:38 2018    DEPLOYED    aks-helloworld-0.1.0                default
listless-quokka 1           Tue Oct 23 16:41:30 2018    DEPLOYED    aks-helloworld-0.1.0                default
```

Elimine las versiones con el comando `helm delete`. En el ejemplo siguiente se elimina la implementación de entrada NGINX y las dos aplicaciones Hola mundo de AKS de ejemplo.

```
$ helm delete virulent-seal billowing-guppy listless-quokka

release "virulent-seal" deleted
release "billowing-guppy" deleted
release "listless-quokka" deleted
```

A continuación, elimine el repositorio de Helm para la aplicación Hola mundo de AKS:

```console
helm repo remove azure-samples
```

Elimine la ruta de entrada que dirige el tráfico a las aplicaciones de ejemplo:

```console
kubectl delete -f hello-world-ingress.yaml
```

Eliminar el certificado de secreto:

```console
kubectl delete secret aks-ingress-tls
```

Por último, puede eliminar el propio espacio de nombres. Use el `kubectl delete` comando y especifique el nombre del espacio de nombres:

```console
kubectl delete namespace ingress-basic
```

## <a name="next-steps"></a>Pasos siguientes

En este artículo se incluyen algunos componentes externos a AKS. Para más información sobre estos componentes, consulte las siguientes páginas del proyecto:

- [CLI de Helm][helm-cli]
- [Controlador de entrada NGINX][nginx-ingress]

También puede:

- [Crear un controlador de entrada básico con conectividad de red externa][aks-ingress-basic]
- [Habilitar el complemento de enrutamiento de aplicación HTTP][aks-http-app-routing]
- [Crear un controlador de entrada que usa una red privada interna y una dirección IP][aks-ingress-internal]
- Crear un controlador de entrada que use Let's Encrypt para generar certificados TLS de forma automática [con una dirección IP pública dinámica][aks-ingress-tls] o [con una dirección IP pública estática][aks-ingress-static-tls]

<!-- LINKS - external -->
[helm-cli]: https://docs.microsoft.com/azure/aks/kubernetes-helm
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx
[helm-install]: https://docs.helm.sh/using_helm/#installing-helm

<!-- LINKS - internal -->
[use-helm]: kubernetes-helm.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[aks-ingress-internal]: ingress-internal-ip.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-ingress-basic]: ingress-basic.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-tls]: ingress-tls.md
