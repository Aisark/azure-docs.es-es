---
title: Habilitación de SSL en un grupo de contenedores
description: Crear un punto de conexión SSL o TLS para un grupo de contenedores que se ejecute en Azure Container Instances
ms.topic: article
ms.date: 04/03/2019
ms.openlocfilehash: 7578ad6f8c451694a90dde00b74bf2e8c6c61109
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/25/2019
ms.locfileid: "74483478"
---
# <a name="enable-an-ssl-endpoint-in-a-container-group"></a>Habilitar un punto de conexión SSL en un grupo de contenedores

En este artículo se muestra cómo crear un [grupo de contenedores](container-instances-container-groups.md) con un contenedor de aplicaciones y un contenedor sidecar que ejecuten un proveedor de SSL. Al configurar un grupo de contenedores con un punto de conexión SSL independiente, habilita las conexiones SSL para su aplicación sin cambiar el código de la aplicación.

Va a configurar un grupo de contenedores formado por dos contenedores:
* Un contenedor de aplicaciones que ejecute una aplicación web sencilla mediante la imagen pública [aci-helloworld](https://hub.docker.com/_/microsoft-azuredocs-aci-helloworld) de Microsoft. 
* Un contenedor sidecar que ejecute la imagen pública [Nginx](https://hub.docker.com/_/nginx), configurada para usar SSL. 

En este ejemplo, el grupo de contenedores solo expone el puerto 443 para Nginx con su dirección IP pública. Nginx enruta las solicitudes HTTPS a la aplicación web complementaria, que escucha internamente en el puerto 80. Puede adaptar el ejemplo para las aplicaciones de contenedor que escuchan en otros puertos.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Puede usar Azure Cloud Shell o una instalación local de la CLI de Azure para completar este artículo. Si desea usarlos de forma local, se recomienda la versión 2.0.55 o posterior. Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, vea [Instalación de la CLI de Azure](/cli/azure/install-azure-cli).

## <a name="create-a-self-signed-certificate"></a>Creación de un certificado autofirmado

Para configurar Nginx como un proveedor de SSL, necesita un certificado SSL. En este artículo se muestra cómo crear y configurar un certificado SSL autofirmado. Para escenarios de producción, debe obtener un certificado de una entidad de certificación.

Para crear un certificado SSL autofirmado, utilice la herramienta [OpenSSL](https://www.openssl.org/), disponible en Azure Cloud Shell y muchas distribuciones de Linux, o una herramienta de cliente comparable en su sistema operativo.

En primer lugar, cree una solicitud de certificado (archivo .csr) en un directorio de trabajo local:

```console
openssl req -new -newkey rsa:2048 -nodes -keyout ssl.key -out ssl.csr
```

Siga las indicaciones para agregar la información de identificación. En “Nombre común”, escriba el nombre de host asociado al certificado. Cuando se le solicite una contraseña, presione Entrar sin escribir, para no tener que introducir una contraseña.

Ejecute el siguiente comando para crear el certificado autofirmado (archivo .crt) desde la solicitud de certificado. Por ejemplo:

```console
openssl x509 -req -days 365 -in ssl.csr -signkey ssl.key -out ssl.crt
```

Ahora debería ver tres archivos en el directorio: la solicitud de certificado (`ssl.csr`), la clave privada (`ssl.key`) y el certificado autofirmado (`ssl.crt`). Usará `ssl.key` y `ssl.crt` en los pasos posteriores.

## <a name="configure-nginx-to-use-ssl"></a>Configurar Nginx para usar SSL

### <a name="create-nginx-configuration-file"></a>Crear el archivo de configuración de Nginx

En esta sección, creará un archivo de configuración para Nginx con el fin de usar SSL. Para empezar, copie el siguiente texto en un nuevo archivo denominado `nginx.conf`. En Azure Cloud Shell, puede usar Visual Studio Code para crear el archivo en el directorio de trabajo:

```console
code nginx.conf
```

En `location`, establezca `proxy_pass` con el puerto correcto para la aplicación. En este ejemplo, se establece el puerto 80 para el contenedor `aci-helloworld`.

```console
# nginx Configuration File
# https://wiki.nginx.org/Configuration

# Run as a less privileged user for security reasons.
user nginx;

worker_processes auto;

events {
  worker_connections 1024;
}

pid        /var/run/nginx.pid;

http {

    #Redirect to https, using 307 instead of 301 to preserve post data

    server {
        listen [::]:443 ssl;
        listen 443 ssl;

        server_name localhost;

        # Protect against the BEAST attack by not using SSLv3 at all. If you need to support older browsers (IE6) you may need to add
        # SSLv3 to the list of protocols below.
        ssl_protocols              TLSv1 TLSv1.1 TLSv1.2;

        # Ciphers set to best allow protection from Beast, while providing forwarding secrecy, as defined by Mozilla - https://wiki.mozilla.org/Security/Server_Side_TLS#Nginx
        ssl_ciphers                ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-AES256-GCM-SHA384:DHE-RSA-AES128-GCM-SHA256:DHE-DSS-AES128-GCM-SHA256:kEDH+AESGCM:ECDHE-RSA-AES128-SHA256:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA:ECDHE-ECDSA-AES128-SHA:ECDHE-RSA-AES256-SHA384:ECDHE-ECDSA-AES256-SHA384:ECDHE-RSA-AES256-SHA:ECDHE-ECDSA-AES256-SHA:DHE-RSA-AES128-SHA256:DHE-RSA-AES128-SHA:DHE-DSS-AES128-SHA256:DHE-RSA-AES256-SHA256:DHE-DSS-AES256-SHA:DHE-RSA-AES256-SHA:AES128-GCM-SHA256:AES256-GCM-SHA384:ECDHE-RSA-RC4-SHA:ECDHE-ECDSA-RC4-SHA:AES128:AES256:RC4-SHA:HIGH:!aNULL:!eNULL:!EXPORT:!DES:!3DES:!MD5:!PSK;
        ssl_prefer_server_ciphers  on;

        # Optimize SSL by caching session parameters for 10 minutes. This cuts down on the number of expensive SSL handshakes.
        # The handshake is the most CPU-intensive operation, and by default it is re-negotiated on every new/parallel connection.
        # By enabling a cache (of type "shared between all Nginx workers"), we tell the client to re-use the already negotiated state.
        # Further optimization can be achieved by raising keepalive_timeout, but that shouldn't be done unless you serve primarily HTTPS.
        ssl_session_cache    shared:SSL:10m; # a 1mb cache can hold about 4000 sessions, so we can hold 40000 sessions
        ssl_session_timeout  24h;


        # Use a higher keepalive timeout to reduce the need for repeated handshakes
        keepalive_timeout 300; # up from 75 secs default

        # remember the certificate for a year and automatically connect to HTTPS
        add_header Strict-Transport-Security 'max-age=31536000; includeSubDomains';

        ssl_certificate      /etc/nginx/ssl.crt;
        ssl_certificate_key  /etc/nginx/ssl.key;

        location / {
            proxy_pass http://localhost:80; # TODO: replace port if app listens on port other than 80
            
            proxy_set_header Connection "";
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $remote_addr;
        }
    }
}
```

### <a name="base64-encode-secrets-and-configuration-file"></a>Archivo de configuración y secretos con codificación Base64

Base64 codifica el archivo de configuración de Nginx, el certificado SSL y la clave SSL. En la sección siguiente, escriba el contenido codificado en un archivo YAML que se use para implementar el grupo de contenedores.

```console
cat nginx.conf | base64 -w 0 > base64-nginx.conf
cat ssl.crt | base64 -w 0 > base64-ssl.crt
cat ssl.key | base64 -w 0 > base64-ssl.key
```

## <a name="deploy-container-group"></a>Implementar un grupo de contenedores

Ahora implemente el grupo de contenedores especificando las configuraciones de contenedor en un [archivo YAML](container-instances-multi-container-yaml.md).

### <a name="create-yaml-file"></a>Crear un archivo YAML

Copie el siguiente código YAML en un nuevo archivo denominado `deploy-aci.yaml`. En Azure Cloud Shell, puede usar Visual Studio Code para crear el archivo en el directorio de trabajo:

```console
code deploy-aci.yaml
```

Escriba el contenido de los archivos con codificación Base64 donde se indique en `secret`. Por ejemplo, aplique `cat` en cada uno de los archivos con codificación Base64 para ver su contenido. Durante la implementación, estos archivos se agregan a un [volumen secreto](container-instances-volume-secret.md) del grupo de contenedores. En este ejemplo, el volumen secreto se monta en el contenedor de Nginx.

```YAML
api-version: 2018-10-01
location: westus
name: app-with-ssl
properties:
  containers:
  - name: nginx-with-ssl
    properties:
      image: nginx
      ports:
      - port: 443
        protocol: TCP
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      volumeMounts:
      - name: nginx-config
        mountPath: /etc/nginx
  - name: my-app
    properties:
      image: mcr.microsoft.com/azuredocs/aci-helloworld
      ports:
      - port: 80
        protocol: TCP
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
  volumes:
  - secret:
      ssl.crt: <Enter contents of base64-ssl.crt here>
      ssl.key: <Enter contents of base64-ssl.key here>
      nginx.conf: <Enter contents of base64-nginx.conf here>
    name: nginx-config
  ipAddress:
    ports:
    - port: 443
      protocol: TCP
    type: Public
  osType: Linux
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

### <a name="deploy-the-container-group"></a>Implementación del grupo de contenedores

Cree un grupo de recursos con el comando [az group create](/cli/azure/group#az-group-create):

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Implemente el grupo de contenedores con el comando [az container create](/cli/azure/container#az-container-create) y pase el archivo YAML como un argumento.

```azurecli
az container create --resource-group <myResourceGroup> --file deploy-aci.yaml
```

### <a name="view-deployment-state"></a>Visualización del estado de la implementación

Para ver el estado de la implementación, use el siguiente comando [az container show](/cli/azure/container#az-container-show):

```azurecli
az container show --resource-group <myResourceGroup> --name app-with-ssl --output table
```

En una implementación correcta, el resultado es similar al siguiente:

```console
Name          ResourceGroup    Status    Image                                                    IP:ports             Network    CPU/Memory       OsType    Location
------------  ---------------  --------  -------------------------------------------------------  -------------------  ---------  ---------------  --------  ----------
app-with-ssl  myresourcegroup  Running   mcr.microsoft.com/azuredocs/nginx, aci-helloworld        52.157.22.76:443     Public     1.0 core/1.5 gb  Linux     westus
```

## <a name="verify-ssl-connection"></a>Comprobar la conexión SSL

Para ver la aplicación en ejecución, vaya a su dirección IP en el explorador. Por ejemplo, la dirección IP que se muestra en este ejemplo es `52.157.22.76`. Debe usar `https://<IP-ADDRESS>` para ver la aplicación en ejecución, debido a la configuración del servidor de Nginx. Los intentos de conectarse con `http://<IP-ADDRESS>` producirán un error.

![Captura de pantalla del explorador que muestra una aplicación en ejecución en una instancia de contenedor de Azure](./media/container-instances-container-group-ssl/aci-app-ssl-browser.png)

> [!NOTE]
> Como en este ejemplo se usa un certificado autofirmado y no uno procedente de una entidad de certificación, el explorador muestra una advertencia de seguridad al conectarse al sitio a través de HTTPS. Este comportamiento es normal.
>

## <a name="next-steps"></a>Pasos siguientes

En este artículo hemos hablado de cómo configurar un contenedor Nginx para habilitar las conexiones SSL con una aplicación web en ejecución en el grupo de contenedores. Puede adaptar este ejemplo a aplicaciones que escuchen en puertos distintos al puerto 80. También puede actualizar el archivo de configuración de Nginx para que redirija automáticamente las conexiones del servidor del puerto 80 (HTTP) con el fin de usar HTTPS.

Aunque en este artículo se usa Nginx en sidecar, puede usar otro proveedor de SSL, como [Caddy](https://caddyserver.com/).

Otra opción para habilitar SSL en un grupo de contenedores es implementar el grupo en una [red virtual de Azure](container-instances-vnet.md) con una [puerta de enlace de aplicación de Azure](../application-gateway/overview.md). La puerta de enlace se puede configurar como punto de conexión SSL. Vea un ejemplo de [plantilla de implementación](https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-wordpress-vnet) que puede adaptar para habilitar la terminación de SSL en la puerta de enlace.
