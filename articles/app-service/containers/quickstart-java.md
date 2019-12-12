---
title: 'Inicio rápido: Creación de una aplicación Java en Linux'
description: Para empezar a trabajar con aplicaciones de Linux en Azure App Service, debe implementar su primera aplicación de Java en un contenedor Linux en App Service.
keywords: azure, app service, aplicación web, linux, java, maven, inicio rápido
author: msangapu-msft
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.devlang: Java
ms.topic: quickstart
ms.date: 03/27/2019
ms.custom: mvc, seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 64d70390b3563e17bbe0c70ed426dcda81b50198
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/05/2019
ms.locfileid: "74872747"
---
# <a name="quickstart-create-a-java-app-on-azure-app-service-on-linux"></a>Inicio rápido: Creación de una aplicación de Java en Azure App Service en Linux

[App Service en Linux](app-service-linux-intro.md) proporciona un servicio de hospedaje web muy escalable y con aplicación automática de revisiones que usa el sistema operativo Linux. En este inicio rápido se muestra cómo usar la [CLI de Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) con el [complemento Maven para Azure App Service](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin) para implementar un archivo web (WAR) de Java en el sistema operativo Linux.

> [!NOTE]
>
> También se puede hacer lo mismo con IDE populares, como IntelliJ y Eclipse. Consulte nuestros documentos similares en [Inicio rápido de Azure Toolkit for IntelliJ](/java/azure/intellij/azure-toolkit-for-intellij-create-hello-world-web-app) o [Inicio rápido de Azure Toolkit for Eclipse](/java/azure/eclipse/azure-toolkit-for-eclipse-create-hello-world-web-app).
>
![Aplicación de ejemplo que se ejecuta en Azure App Service](media/quickstart-java/java-hello-world-in-browser-azure-app-service.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-java-app"></a>Creación de una aplicación Java

Ejecute el siguiente comando de Maven en el símbolo del sistema de Cloud Shell para crear una aplicación denominada `helloworld`:

```bash
mvn archetype:generate "-DgroupId=example.demo" "-DartifactId=helloworld" "-DarchetypeArtifactId=maven-archetype-webapp"
```

## <a name="configure-the-maven-plugin"></a>Configuración del complemento Maven

El proceso de implementación en Azure App Service usa las credenciales de cuenta desde la CLI de Azure. [Inicie sesión con la CLI de Azure](/cli/azure/authenticate-azure-cli?view=azure-cli-latest) antes de continuar.

```azurecli
az login
```

Luego puede configurar la implementación, ejecutar el comando de Maven en el símbolo del sistema y usar las configuraciones predeterminadas presionando **ENTRAR** hasta que aparezca el mensaje **Confirm (Y/N)** [Confirmar (S/N)], donde deberá presionar **"y"** para finalizar la configuración. 
```cmd
mvn com.microsoft.azure:azure-webapp-maven-plugin:1.8.0:config
```
Así es un proceso de ejemplo:

```cmd
~@Azure:~/helloworld$ mvn com.microsoft.azure:azure-webapp-maven-plugin:1.8.0:config
[INFO] Scanning for projects...
[INFO]
[INFO] ----------------------< example.demo:helloworld >-----------------------
[INFO] Building helloworld Maven Webapp 1.0-SNAPSHOT
[INFO] --------------------------------[ war ]---------------------------------
[INFO]
[INFO] --- azure-webapp-maven-plugin:1.8.0:config (default-cli) @ helloworld ---
[WARNING] The plugin may not work if you change the os of an existing webapp.
Define value for OS(Default: Linux):
1. linux [*]
2. windows
3. docker
Enter index to use:
Define value for javaVersion(Default: jre8):
1. Java 11
2. Java 8 [*]
Enter index to use:
Define value for runtimeStack(Default: TOMCAT 8.5):
1. TOMCAT 9.0
2. TOMCAT 8.5 [*]
3. WILDFLY 14
Enter index to use:
Please confirm webapp properties
AppName : helloworld-1558400876966
ResourceGroup : helloworld-1558400876966-rg
Region : westeurope
PricingTier : Premium_P1V2
OS : Linux
RuntimeStack : TOMCAT 8.5-jre8
Deploy to slot : false
Confirm (Y/N)? : Y
```

> [!NOTE]
> En este artículo solo se trabaja con aplicaciones Java empaquetadas en archivos WAR. El complemento también admite aplicaciones web JAR, visite [Implementación de un archivo JAR de SE de Java en App Service en Linux](https://docs.microsoft.com/java/azure/spring-framework/deploy-spring-boot-java-app-with-maven-plugin?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) para probarlo.

Vaya nuevamente a `pom.xml` para ver si se actualizó la configuración del complemento. Puede modificar otras configuraciones de App Service directamente en el archivo POM si es necesario. Algunas configuraciones comunes se muestran a continuación:

 Propiedad | Obligatorio | DESCRIPCIÓN | Versión
---|---|---|---
`<schemaVersion>` | false | Especifique la versión del esquema de configuración. Los valores admitidos son: `v1`, `v2`. | 1.5.2
`<resourceGroup>` | true | Grupo de recursos de Azure para la aplicación web. | 0.1.0+
`<appName>` | true | El nombre de la aplicación web. | 0.1.0+
[`<region>`](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme#region) | true | Especifica la región donde se hospedará la aplicación web. El valor predeterminado es **westeurope**. Todas las regiones válidas en la sección [Regiones admitidas](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme#region). | 0.1.0+
[`<pricingTier>`](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme##pricingtier) | false | El plan de tarifa de la aplicación web. El valor predeterminado es **P1V2**.| 0.1.0+
[`<runtime>`](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme#runtimesetting) | true | La configuración del entorno en tiempo de ejecución. Puede ver los detalles [aquí](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme#runtimesetting). | 0.1.0+
[`<deployment>`](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme#deploymentsetting) | true | La configuración de implementación. Puede ver los detalles [aquí](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme#deploymentsetting). | 0.1.0+

> [!div class="nextstepaction"]
> [He tenido un problema](https://www.research.net/r/javae2e?tutorial=app-service-linux-quickstart&step=config)

## <a name="deploy-the-app"></a>Implementar la aplicación

Implemente la aplicación Java en Azure mediante el siguiente comando:

```bash
mvn package azure-webapp:deploy
```

Una vez que se haya completado la implementación, vaya a la aplicación implementada mediante la siguiente dirección URL en el explorador web, por ejemplo, `http://<webapp>.azurewebsites.net`. 

![Aplicación de ejemplo que se ejecuta en Azure App Service](media/quickstart-java/java-hello-world-in-browser-azure-app-service.png)

**¡Enhorabuena!** Ha implementado su primera aplicación Java en App Service en Linux.

> [!div class="nextstepaction"]
> [He tenido un problema](https://www.research.net/r/javae2e?tutorial=app-service-linux-quickstart&step=deploy)

## <a name="clean-up-resources"></a>Limpieza de recursos

En los pasos anteriores, creó recursos de Azure en un grupo de recursos. Si prevé que no va a necesitar estos recursos en el futuro, elimine el grupo de recursos del portal, para lo que debe ejecutar el siguiente comando en Cloud Shell:

```azurecli-interactive
az group delete --name <your resource group name; for example: helloworld-1558400876966-rg> --yes
```

Este comando puede tardar varios segundos en ejecutarse.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Tutorial: Aplicación Java Enterprise con PostgreSQL](tutorial-java-enterprise-postgresql-app.md)

> [!div class="nextstepaction"]
> [Configuración de una aplicación Java](configure-custom-container.md)

> [!div class="nextstepaction"]
> [CI/CD con Jenkins](/azure/jenkins/deploy-jenkins-app-service-plugin)

> [!div class="nextstepaction"]
> [Otros recursos de Azure para desarrolladores de Java](/java/azure/)

> [!div class="nextstepaction"]
> [Más información sobre los complementos de Maven para Azure](https://github.com/microsoft/azure-maven-plugins)
