---
title: Configuración de aplicaciones de Node.js
description: Aprenda a configurar un contenedor de Node.js precompilado para la aplicación. En este artículo se muestran las tareas de configuración más comunes.
ms.devlang: nodejs
ms.topic: article
ms.date: 03/28/2019
ms.openlocfilehash: 6cf60472307a378d2fd4258a9777152344a11ded
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/02/2019
ms.locfileid: "74670268"
---
# <a name="configure-a-linux-nodejs-app-for-azure-app-service"></a>Configurar una aplicación de Node.js para Azure App Service

Las aplicaciones de Node.js deben implementarse con todas las dependencias de NPM. El motor de implementación de App Service (Kudu) ejecuta `npm install --production` automáticamente al implementar un [repositorio de Git](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) o un [paquete Zip](../deploy-zip.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) con los procesos de compilación activos. Sin embargo, si implementa los archivos con [FTP/S](../deploy-ftp.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json), deberá cargar manualmente los paquetes necesarios.

En esta guía se incluyen conceptos clave e instrucciones para los desarrolladores de Node.js que usan un contenedor Linux integrado en App Service. Si nunca ha usado Azure App Service, siga primero la [guía de inicio rápido de Node.js](quickstart-nodejs.md) y el [tutorial de Node.js con MongoDB](tutorial-nodejs-mongodb-app.md).

## <a name="show-nodejs-version"></a>Mostrar la versión de Node.js

Para mostrar la versión actual de Node.js, ejecute el siguiente comando en [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Para mostrar todas las versiones compatibles de Node.js, ejecute el siguiente comando en [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep NODE
```

## <a name="set-nodejs-version"></a>Establecer la versión de Node.js

Para establecer la aplicación en una [versión compatible de Node.js](#show-nodejs-version), ejecute el siguiente comando en [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "NODE|10.14"
```

Esta configuración especifica la versión de Node.js que usa, tanto en tiempo de ejecución como durante la restauración automatizada de paquetes en Kudu.

> [!NOTE]
> Debe establecer la versión de Node.js en el archivo `package.json` del proyecto. El motor de implementación se ejecuta en un contenedor independiente que contiene todas las versiones compatibles de Node.js.

## <a name="configure-nodejs-server"></a>Configurar el servidor Node.js

Los contenedores de Node.js se suministran con [PM2](https://pm2.keymetrics.io/), un administrador de procesos de producción. Puede configurar la aplicación para que se inicie con PM2, con NPM o con un comando personalizado.

- [Ejecutar un comando personalizado](#run-custom-command)
- [Ejecutar npm start](#run-npm-start)
- [Ejecutar con PM2](#run-with-pm2)

### <a name="run-custom-command"></a>Ejecutar un comando personalizado

App Service puede iniciar la aplicación con un comando personalizado, por ejemplo, un archivo ejecutable como *run.sh*. Por ejemplo, para ejecutar `npm run start:prod`, ejecute el siguiente comando en [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "npm run start:prod"
```

### <a name="run-npm-start"></a>Ejecutar npm start

Para iniciar la aplicación con `npm start`, asegúrese de que haya un script `start` en el archivo *package.json*. Por ejemplo:

```json
{
  ...
  "scripts": {
    "start": "gulp",
    ...
  },
  ...
}
```

Para utilizar un archivo *package.json* personalizado en el proyecto, ejecute el siguiente comando en [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<filename>.json"
```

### <a name="run-with-pm2"></a>Ejecutar con PM2

El contenedor inicia automáticamente la aplicación con PM2 cuando se encuentra uno de los archivos comunes de Node.js en el proyecto:

- *bin/www*
- *server.js*
- *app.js*
- *index.js*
- *hostingstart.js*
- Uno de los siguientes [archivos PM2](https://pm2.keymetrics.io/docs/usage/application-declaration/#process-file): *process.json* y *ecosystem.config.js*

También puede configurar un archivo de inicio personalizado con las siguientes extensiones:

- Un archivo *.js*
- Un [archivo PM2](https://pm2.keymetrics.io/docs/usage/application-declaration/#process-file) con la extensión *.json*, *. config.js*, *.yaml* o *.yml*

Para agregar un archivo de inicio personalizado, ejecute el siguiente comando en [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<filname-with-extension>"
```

## <a name="debug-remotely"></a>Depuración remota

> [!NOTE]
> La depuración remota se encuentra actualmente en versión preliminar.

Puede depurar la aplicación de Node.js de manera remota en [Visual Studio Code](https://code.visualstudio.com/) si la configura para [ejecutar con PM2](#run-with-pm2), excepto cuando se ejecuta con una extensión *.config.js, *.yml o *.yaml*.

En la mayoría de los casos, no es necesaria ninguna configuración adicional para la aplicación. Si la aplicación se ejecuta con un archivo *process.json* (predeterminado o personalizado), debe tener una `script` propiedad en la raíz JSON. Por ejemplo:

```json
{
  "name"        : "worker",
  "script"      : "./index.js",
  ...
}
```

Para configurar Visual Studio Code para la depuración remota, instale la [extensión App Service](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice). Siga las instrucciones que aparecen en la página de la extensión e inicie sesión en Azure en Visual Studio Code.

En el Explorador de Azure, busque la aplicación que desea depurar, haga clic en ella y seleccione **Start Remote Debugging** (Iniciar depuración remota). Haga clic en **Sí** para habilitarla para la aplicación. App Service inicia un proxy de túnel y conecta el depurador. A continuación, puede realizar solicitudes a la aplicación y ver al depurador detenerse en los puntos de interrupción.

Una vez que finalice la depuración, detenga el depurador seleccionando **Desconectar**. Cuando se le solicite, debe hacer clic en **Sí** para deshabilitar la depuración remota. Para deshabilitarla más adelante, haga clic en su aplicación de nuevo en el explorador de Azure y seleccione **Disable Remote Debugging** (Deshabilitar la depuración remota).

## <a name="access-environment-variables"></a>Acceso a variables de entorno

En App Service, puede [establecer la configuración de la aplicación](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) fuera del código de la aplicación. Luego puede acceder a ellas mediante el patrón estándar de Node.js. Por ejemplo, para acceder a una configuración de una aplicación denominada `NODE_ENV`, use el código siguiente:

```javascript
process.env.NODE_ENV
```

## <a name="run-gruntbowergulp"></a>Ejecutar Grunt, Bower o Gulp

De forma predeterminada, Kudu ejecuta `npm install --production` cuando reconoce que se implementa una aplicación Node.js. Si la aplicación requiere alguna de las herramientas de automatización más populares, como Grunt, Bower o Gulp, deberá suministrar un [script de implementación personalizado](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script) para ejecutarla.

Para habilitar que el repositorio ejecute estas herramientas, deberá agregarlas a las dependencias en el archivo *package.json.* Por ejemplo:

```json
"dependencies": {
  "bower": "^1.7.9",
  "grunt": "^1.0.1",
  "gulp": "^3.9.1",
  ...
}
```

Desde una ventana de terminal local, cambie el directorio a la raíz del repositorio y ejecute los siguientes comandos:

```bash
npm install kuduscript -g
kuduscript --node --scriptType bash --suppressPrompt
```

La raíz del repositorio ahora tiene dos archivos adicionales: *.deployment* y *deploy.sh*.

Abra *deploy.sh* y busque la sección `Deployment`, que tiene el siguiente aspecto:

```bash
##################################################################################################################################
# Deployment
# ----------
```

En esta sección termina con la ejecución de `npm install --production`. Agregue la sección de código que necesita para ejecutar la herramienta necesaria *al final* de la sección `Deployment`:

- [Bower](#bower)
- [Gulp](#gulp)
- [Grunt](#grunt)

Consulte un [ejemplo en la muestra de MEAN.js](https://github.com/Azure-Samples/meanjs/blob/master/deploy.sh#L112-L135), donde el script de implementación también ejecuta un comando `npm install` personalizado.

### <a name="bower"></a>Bower

Este fragmento de código ejecuta `bower install`.

```bash
if [ -e "$DEPLOYMENT_TARGET/bower.json" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/bower install
  exitWithMessageOnError "bower failed"
  cd - > /dev/null
fi
```

### <a name="gulp"></a>Gulp

Este fragmento de código ejecuta `gulp imagemin`.

```bash
if [ -e "$DEPLOYMENT_TARGET/gulpfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/gulp imagemin
  exitWithMessageOnError "gulp failed"
  cd - > /dev/null
fi
```

### <a name="grunt"></a>Grunt

Este fragmento de código ejecuta `grunt`.

```bash
if [ -e "$DEPLOYMENT_TARGET/Gruntfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/grunt
  exitWithMessageOnError "Grunt failed"
  cd - > /dev/null
fi
```

## <a name="detect-https-session"></a>Detección de sesión de HTTPS

En App Service, la [terminación de SSL](https://wikipedia.org/wiki/TLS_termination_proxy) se produce en los equilibradores de carga de red, por lo que todas las solicitudes HTTPS llegan a su aplicación en forma de solicitudes HTTP sin cifrar. Si su aplicación lógica necesita comprobar si las solicitudes de usuario están cifradas, inspeccione el encabezado `X-Forwarded-Proto`.

Los marcos web más usados le permiten acceder a la información de `X-Forwarded-*` en el patrón de aplicación estándar. En [Express](https://expressjs.com/), puede usar [trust proxy](https://expressjs.com/guide/behind-proxies.html). Por ejemplo:

```javascript
app.set('trust proxy', 1)
...
if (req.secure) {
  // Do something when HTTPS is used
}
```

## <a name="access-diagnostic-logs"></a>Acceso a los registros de diagnóstico

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>Abrir sesión SSH en el explorador

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="troubleshooting"></a>solución de problemas

Cuando una aplicación de Node.js en funcionamiento se comporta de manera diferente en App Service o genera errores, intente lo siguiente:

- [Acceso a la secuencia de registros](#access-diagnostic-logs).
- Pruebe la aplicación localmente en modo de producción. App Service ejecuta las aplicaciones de Node.js en el modo de producción, por lo que deberá asegurarse de que el proyecto funciona según lo previsto en modo de producción localmente. Por ejemplo:
    - En función de su archivo *package.json*, pueden instalarse distintos paquetes para el modo de producción (`dependencies` frente a `devDependencies`).
    - Algunos marcos web pueden implementar archivos estáticos de forma diferente en modo de producción.
    - Algunos marcos web pueden usar scripts de inicio personalizados cuando se ejecutan en modo de producción.
- Ejecute la aplicación en App Service en el modo de desarrollo. Por ejemplo, en [MEAN.js](https://meanjs.org/), puede establecer la aplicación en modo de desarrollo en tiempo de ejecución [estableciendo la configuración de aplicación `NODE_ENV`](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings).

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Tutorial: Aplicación Node.js con MongoDB](tutorial-nodejs-mongodb-app.md)

> [!div class="nextstepaction"]
> [P+F sobre App Service en Linux](app-service-linux-faq.md)
