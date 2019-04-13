---
title: Configuración de la CLI de Azure Service Fabric Mesh | Microsoft Docs
description: Aprenda a configurar la CLI de Azure Service Fabric Mesh.
services: service-fabric-mesh
keywords: ''
author: dkkapur
ms.author: dekapur
ms.date: 11/28/2018
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: c716ae0a2bb30e7e8eb249a1d230097efc0d3795
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/12/2019
ms.locfileid: "59521023"
---
# <a name="set-up-service-fabric-mesh-cli"></a>Configuración de la CLI de Service Fabric Mesh
La interfaz de la línea de comandos (CLI) de Service Fabric Mesh es necesaria para implementar y administrar recursos de Azure Service Fabric Mesh. 

Hay tres tipos de interfaces de la línea de comandos que se pueden utilizar y que se resumen en la siguiente tabla. 

| Módulo de la CLI | Entorno de destino |  DESCRIPCIÓN | 
|---|---|---|
| az mesh | Azure Service Fabric Mesh | La CLI principal que le permite implementar las aplicaciones y administrar recursos en el entorno de Azure Service Fabric Mesh. 
| sfctl | Clústeres locales | CLI de Service Fabric que permite la implementación y la prueba de los recursos de Service Fabric frente a los clústeres locales.  
| CLI de Maven | Clústeres locales y Azure Service Fabric Mesh | Un contenedor alrededor de `az mesh` y `sfctl` que permite a los desarrolladores de Java que se usará una experiencia familiar de línea de comandos para la experiencia de desarrollo local y Azure.  

En la versión preliminar, la CLI de Azure Service Fabric Mesh está escrita como una extensión de la CLI de Azure. Puede instalarla en Azure Cloud Shell o como una instalación local de la CLI de Azure. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

## <a name="install-the-azure-service-fabric-mesh-cli"></a>Instalación de la CLI de Azure Service Fabric Mesh
1. Debe instalar la versión 2.0.43 de la CLI de Azure, o cualquier versión posterior. Ejecute `az --version` para encontrar la versión. Para instalar o actualizar a la última versión de la CLI, consulte [Instalación de la CLI de Azure][azure-cli-install].

2. Instale el módulo de extensión de la CLI de Azure Service Fabric Mesh mediante el siguiente comando. 

    ```azurecli-interactive
    az extension add --name mesh
    ```

3. Actualice un módulo de la CLI de Azure Service Fabric Mesh mediante el comando siguiente.

    ```azurecli-interactive
    az extension update --name mesh
    ```

## <a name="install-the-service-fabric-cli-sfctl"></a>Instalación de la CLI de Service Fabric (sfctl) 

Siga las instrucciones de [Configuración de la CLI de Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli). El módulo **sfctl** puede utilizarse para la implementación de aplicaciones basadas en el modelo de recursos contra clústeres de Service Fabric en su máquina local. 

## <a name="install-the-maven-cli"></a>Instalación de la CLI de Maven 

Para poder usar la CLI de Maven, se debe instalar lo siguiente en la máquina: 

* [Java](https://www.azul.com/downloads/zulu/)
* [Maven](https://maven.apache.org/download.cgi)
* [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
* CLI de Azure Mesh (az mesh): para seleccione el destino en Azure Service Fabric Mesh 
* SFCTL (sfctl): para dirigirse a los clústeres locales 

La CLI de Maven para Service Fabric sigue en versión preliminar. 

Para usar el complemento de Maven en la aplicación Java de Maven, agrega el siguiente fragmento al archivo pom.xml:

```XML
<project>
  ...
  <build>
    ...
    <plugins>
      ...
      <plugin>
        <groupId>com.microsoft.azure</groupId>
          <artifactId>azure-sfmesh-maven-plugin</artifactId>
          <version>0.1.0</version>
          <configuration>
            ...
          </configuration>
      </plugin>
    </plugins>
  </build>
</project>
```

Lea la sección [Referencia de la CLI de Maven](service-fabric-mesh-reference-maven.md) para obtener más información sobre el uso.

## <a name="next-steps"></a>Pasos siguientes

También puede configurar su [entorno de desarrollo de Windows](service-fabric-mesh-howto-setup-developer-environment-sdk.md).

Busque respuestas a [preguntas y problemas comunes](service-fabric-mesh-faq.md).

[azure-cli-install]: /cli/azure/install-azure-cli
