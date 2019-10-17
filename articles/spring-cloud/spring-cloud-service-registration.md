---
title: Automatización del registro y la detección de servicios
description: Aprenda a automatizar la detección y el registro de servicios mediante el registro de servicios de Spring Cloud
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/05/2019
ms.author: jeconnoc
ms.openlocfilehash: 72327e116e498ce0f6881a5c585a08e56c8bf8c2
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038675"
---
# <a name="discover-and-register-your-spring-cloud-services"></a>Detección y registro de los servicios de Spring Cloud

La característica de detección de servicios es un requisito clave para una arquitectura basada en microservicios.  La configuración manual de cada cliente lleva tiempo e introduce la posibilidad de que se produzcan errores humanos.  El registro de servicios de Azure Spring Cloud resuelve este problema.  Una vez configurado, un servidor de registro de servicios controlará el registro y la detección de servicios de los microservicios de la aplicación. El servidor de registro de servicios mantiene un registro de los microservicios implementados, habilita el equilibrio de carga del lado cliente y desacopla los proveedores de servicios de los clientes sin depender del DNS.

## <a name="register-your-application-using-spring-cloud-service-registry"></a>Registro de la aplicación mediante el registro de servicios de Spring Cloud

Para que la aplicación pueda administrar el registro y la detección de servicios mediante el registro de servicios de Spring Cloud, deben incluirse varias dependencias en el archivo *pom.xml* de la aplicación.

Para empezar, agregue un repositorio de instantáneas a la sección *repository* del archivo *pom.xml*

```xml
    <repositories>
        <repository>
            <id>nexus-snapshots</id>
            <url>https://oss.sonatype.org/content/repositories/snapshots/</url>
            <snapshots>
                <enabled>true</enabled>
            </snapshots>
        </repository>
    </repositories>
```

## <a name="include-dependencies"></a>Inclusión de dependencias

A continuación se incluyen las dependencias de *spring-cloud-starter-netflix-eureka-client* y *spring-cloud-starter-azure-spring-cloud-client* en el archivo *pom.xml*

```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.1.0-SNAPSHOT</version>
    </dependency>
```

## <a name="update-the-top-level-class"></a>Actualización de la clase de nivel superior

Por último, se agrega una anotación a la clase de nivel superior de la aplicación

 ```java
    package foo.bar;

    import org.springframework.boot.SpringApplication;
    import org.springframework.cloud.client.SpringCloudApplication;

    @SpringCloudApplication
    public class DemoApplication {
        public static void main(String... args) {
            SpringApplication.run(DemoApplication.class, args);
        }
    }
 ```

El punto de conexión del servidor de registro de servicios de Spring Cloud se insertará como variable de entorno en la aplicación.  Los microservicios podrán ahora registrarse en el servidor de registro de servicios y detectarán otros microservicios dependientes.

Tenga en cuenta que los cambios pueden tardar unos minutos en propagarse del servidor a todos los microservicios.
