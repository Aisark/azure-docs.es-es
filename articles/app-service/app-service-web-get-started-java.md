---
title: 'Creación de una aplicación web de Java en Windows: Azure App Service'
description: En este inicio rápido, implementará su primera aplicación Hola mundo de Java en Azure App Service en Windows en cuestión de minutos.
keywords: azure, app service, web app, windows, java, maven, quickstart
services: app-service\web
documentationcenter: ''
author: msangapu-msft
manager: jeconnoc
editor: ''
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: quickstart
ms.date: 05/29/2019
ms.author: jafreebe
ms.custom: mvc
ms.openlocfilehash: 0ca50bae0748570932c7a4cc3bb10cde17c940f5
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/07/2019
ms.locfileid: "67617705"
---
# <a name="quickstart-create-a-java-app-in-app-service"></a>Inicio rápido: Creación de una aplicación de Java en App Service

> [!NOTE]
> En este artículo se implementa una aplicación en App Service en Windows. Para implementarla en App Service en _Linux_, consulte [Creación de una aplicación web de java en Linux](./containers/quickstart-java.md).
>

[Azure App Service](overview.md) proporciona un servicio de hospedaje web muy escalable y con aplicación de revisiones de un modo automático.  En este inicio rápido se muestra cómo usar la [CLI de Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) con el [complemento Maven para Azure App Service](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin) para implementar un archivo web (WAR) de Java.

> [!NOTE]
> También se puede hacer lo mismo con IDE populares, como IntelliJ y Eclipse. Consulte nuestros documentos similares en [Inicio rápido de Azure Toolkit for IntelliJ](/java/azure/intellij/azure-toolkit-for-intellij-create-hello-world-web-app) o [Inicio rápido de Azure Toolkit for Eclipse](/java/azure/eclipse/azure-toolkit-for-eclipse-create-hello-world-web-app).
>
![Aplicación de ejemplo que se ejecuta en Azure](./media/app-service-web-get-started-java/java-hello-world-in-browser.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-java-app"></a>Creación de una aplicación Java

Ejecute el siguiente comando de Maven en el símbolo del sistema de Cloud Shell para crear una aplicación denominada `helloworld`:

```bash
mvn archetype:generate -DgroupId=example.demo -DartifactId=helloworld -DarchetypeArtifactId=maven-archetype-webapp
```

## <a name="configure-the-maven-plugin"></a>Configuración del complemento Maven

Para implementar desde Maven, use el editor de código de Cloud Shell para abrir el archivo de proyecto `pom.xml` en el directorio `helloworld`. 

```bash
code pom.xml
```

Luego agregue la siguiente definición del complemento al elemento `<build>` del archivo `pom.xml`.

```xml
<plugins>
    <!--*************************************************-->
    <!-- Deploy to Tomcat in App Service Windows         -->
    <!--*************************************************-->
    <plugin>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-webapp-maven-plugin</artifactId>
        <version>1.6.0</version>
        <configuration>
            <!-- Specify v2 schema -->
            <schemaVersion>v2</schemaVersion>
            <!-- App information -->
            <subscriptionId>${SUBSCRIPTION_ID}</subscriptionId>
            <resourceGroup>${RESOURCEGROUP_NAME}</resourceGroup>
            <appName>${WEBAPP_NAME}</appName>
            <region>${REGION}</region>
            <!-- Java Runtime Stack for App Service on Windows-->
            <runtime>
                <os>windows</os>
                <javaVersion>1.8</javaVersion>
                <webContainer>tomcat 9.0</webContainer>
            </runtime>
            <deployment>
                <resources>
                    <resource>
                        <directory>${project.basedir}/target</directory>
                        <includes>
                            <include>*.war</include>
                        </includes>
                    </resource>
                </resources>
            </deployment>
        </configuration>
    </plugin>
</plugins>
```

> [!NOTE]
> En este artículo solo se trabaja con aplicaciones Java empaquetadas en archivos WAR. El complemento también admite aplicaciones web JAR, visite [Implementación de un archivo JAR de SE de Java en App Service en Linux](https://docs.microsoft.com/java/azure/spring-framework/deploy-spring-boot-java-app-with-maven-plugin?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) para probarlo.


Actualice los siguientes marcadores de posición en la configuración del complemento:

| Marcador de posición | DESCRIPCIÓN |
| ----------- | ----------- |
| `SUBSCRIPTION_ID` | Id. único de la suscripción en la que quiere implementar la aplicación. El id. de la suscripción predeterminada se puede encontrar en Cloud Shell o en la CLI mediante el comando `az account show`. Para todas las suscripciones disponibles, use el comando `az account list`.|
| `RESOURCEGROUP_NAME` | Nombre del nuevo grupo de recursos en el que se va a crear la aplicación. Al colocar todos los recursos de una aplicación en un grupo, puede administrarlos juntos. Por ejemplo, si elimina el grupo de recursos también se eliminarán todos los recursos asociados con la aplicación. Actualice este valor con un nombre único de un nuevo grupo de recursos, por ejemplo, *myResourceGroup*. Este nombre lo utilizará para limpiar todos los recursos de Azure en una sección posterior. |
| `WEBAPP_NAME` | El nombre de la aplicación formará parte del nombre de host de la aplicación si se ha implementado en Azure (WEBAPP_NAME.azurewebsites.net). Actualice este valor con un nombre único para la nueva aplicación de App Service, que hospedará la aplicación Java, por ejemplo *contoso*. |
| `REGION` | Una región de Azure donde se hospeda la aplicación, por ejemplo, *westus2*. Puede obtener una lista de regiones en Cloud Shell o la CLI mediante el comando `az account list-locations`. |

## <a name="deploy-the-app"></a>Implementar la aplicación

Implemente la aplicación Java en Azure mediante el siguiente comando:

```bash
mvn package azure-webapp:deploy
```

Una vez que se haya completado la implementación, vaya a la aplicación implementada mediante la siguiente dirección URL en el explorador web, por ejemplo, `http://<webapp>.azurewebsites.net/`.

![Aplicación de ejemplo que se ejecuta en Azure](./media/app-service-web-get-started-java/java-hello-world-in-browser.png)

**¡Enhorabuena!** Ha implementado su primera aplicación de Java en App Service en Windows.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Recursos de Azure para desarrolladores de Java](/java/azure/)

> [!div class="nextstepaction"]
> [Asignación de un dominio personalizado](app-service-web-tutorial-custom-domain.md)
