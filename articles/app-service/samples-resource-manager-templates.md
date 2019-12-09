---
title: Ejemplos de plantillas de Azure Resource Manager
description: Busque ejemplos de plantillas de Azure Resource Manager para algunos de los escenarios de App Service habituales. Aprenda a automatizar las tareas de administración o implementación de App Service.
author: tfitzmac
tags: azure-service-management
ms.topic: sample
ms.date: 01/04/2019
ms.author: tomfitz
ms.custom: mvc
ms.openlocfilehash: 58871035eac89931dce0c1cd289cf3fab97c0c3e
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/02/2019
ms.locfileid: "74688411"
---
# <a name="azure-resource-manager-templates-for-app-service"></a>Plantillas de Azure Resource Manager para App Service

En la tabla siguiente se incluyen vínculos a plantillas de Azure Resource Manager para Azure App Service. Para obtener recomendaciones sobre cómo evitar errores habituales al crear plantillas de aplicaciones, consulte [Guía de implementación de aplicaciones mediante plantillas de Azure Resource Manager](deploy-resource-manager-template.md).

Para obtener información sobre la sintaxis de JSON y las propiedades para los recursos de App Services, consulte [Tipos de recursos Microsoft.Web ](/azure/templates/microsoft.web/allversions).

| | |
|-|-|
|**Implementación de una aplicación**||
| [App Service plan and basic Linux app](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-basic-linux) (Plan de App Service y aplicación básica para Linux) | Implementa una aplicación de App Service configurada para Linux. |
| [App Service plan and basic Windows web app](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-basic-windows) (Plan de App Service y aplicación web básica para Windows) | Implementa una aplicación de App Service configurada para Windows. |
| [App linked to a GitHub repository](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-github-deploy) (Aplicación vinculada a un repositorio de GitHub)| Implementa una aplicación de App Service que extrae código de GitHub. |
| [App with custom deployment slots](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-custom-deployment-slots) (Aplicación con ranuras de implementación personalizados)| Implementa una aplicación de App Service con ranuras o entornos de implementación personalizados. |
|**Configuración de una aplicación**||
| [App certificate from Key Vault](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-certificate-from-key-vault) (Certificado de aplicación de Key Vault)| Implementa un certificado de aplicación de App Service desde un secreto de Azure Key Vault y lo utiliza para el enlace de SSL. |
| [App with a custom domain](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-custom-domain) (Aplicación con un dominio personalizado)| Implementa una aplicación de App Service con un nombre de host personalizado. |
| [App with a custom domain and SSL](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-custom-domain-and-ssl) (Aplicación con un dominio personalizado y SSL)| Implementa una aplicación de App Service con un nombre de host personalizado y obtiene un certificado de aplicación de Key Vault para el enlace de SSL. |
| [App with a GoLang extension](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-with-golang) (Aplicación con una extensión de GoLang)| Implementa una aplicación de App Service con la extensión de sitio de Golang. A partir de ese momento se pueden ejecutar en Azure aplicaciones web desarrolladas en Golang. |
| [App with Java 8 and Tomcat 8](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-java-tomcat) (Aplicación con Java 8 y Tomcat 8)| Implementa una aplicación de App Service con Java 8 y Tomcat 8 habilitados. A continuación, puede ejecutar aplicaciones Java en Azure. |
|**Linux app with connected resources** (Aplicación Linux con recursos conectados)||
| [App on Linux with MySQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-linux-managed-mysql) (Aplicación en Linux con MySQL) | Implementa una aplicación de App Service en Linux con Azure Database for MySQL. |
| [App on Linux with PostgreSQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-linux-managed-postgresql) (Aplicación en Linux con PostgreSQL) | Implementa una aplicación de App Service en Linux con Azure Database for PostgreSQL. |
|**App with connected resources** (Aplicación con recursos conectados)||
| [App with MySQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-managed-mysql) (Aplicación con MySQL)| Implementa una aplicación de App Service en Windows con Azure Database for MySQL. |
| [App with PostgreSQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-managed-postgresql) (Aplicación con PostgreSQL)| Implementa una aplicación de App Service en Windows con Azure Database for PostgreSQL. |
| [App with a SQL database](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database) (Aplicación con un base de datos SQL)| Implementa una aplicación de App Service y una base de datos SQL en el nivel de servicio Básico. |
| [App with a Blob storage connection](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-blob-connection) (Aplicación con una conexión a Azure Blob Storage)| Implementa una aplicación de App Service con una cadena de conexión de Azure Blob Storage. Luego, puede usar Blob Storage desde la aplicación. |
| [App with an Azure Cache for Redis](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-with-redis-cache) (Aplicación con una instancia de Azure Redis Cache)| Implementa una aplicación de App Service con una instancia de Azure Redis Cache. |
|**App Service Environment para PowerApps**||
| [Create an App Service environment v2](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-asev2-create) (Creación de un entorno de App Service v2) | Crea un entorno de App Service v2 en la red virtual. |
| [Create an App Service environment v2 with an ILB address](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-asev2-ilb-create/) (Creación de un entorno de App Service v2 con una dirección ILB) | Crea un entorno de App Service v2 en la red virtual con una dirección de equilibrador de carga interno privado. |
| [Configure the default SSL certificate for an ILB App Service environment or an ILB App Service environment v2](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-ase-ilb-configure-default-ssl) (Configuración del certificado SSL predeterminado para un entorno de App Service con ILB o un entorno de App Service v2 con ILB) | Configura el certificado SSL predeterminado para un entorno de App Service con ILB o de App Service v2 con ILB. |
| | |
