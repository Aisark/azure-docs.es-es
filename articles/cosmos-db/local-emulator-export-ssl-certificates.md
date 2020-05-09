---
title: Exportación de los certificados del Emulador de Azure Cosmos DB
description: Al desarrollar en lenguajes y tiempos de ejecución que no usan el almacén de certificados de Windows, debe exportar y administrar los certificados TLS/SSL. Esta publicación proporciona instrucciones detalladas para ello.
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
author: deborahc
ms.author: dech
ms.openlocfilehash: c72dbf24df850d8b0f7e5f26a873b78f5664c9e0
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/28/2020
ms.locfileid: "82200945"
---
# <a name="export-the-azure-cosmos-db-emulator-certificates-for-use-with-java-python-and-nodejs"></a>Exportación de los certificados del emulador de Azure Cosmos DB para su uso con Java, Python y Node.js

[**Descarga del Emulador**](https://aka.ms/cosmosdb-emulator)

El emulador de Azure Cosmos DB proporciona un entorno local que emula el servicio Azure Cosmos DB con fines de desarrollo, incluido su uso de conexiones TLS/SSL. En esta publicación se muestra cómo exportar los certificados TLS/SSL para su uso en lenguajes y entornos de ejecución que no se integran con el almacén de certificados de Windows, como Java, que utiliza su propio [almacén de certificados](https://docs.oracle.com/cd/E19830-01/819-4712/ablqw/index.html), Python, que usa [contenedores de sockets](https://docs.python.org/2/library/ssl.html) y Node.js, que usa [tlsSocket](https://nodejs.org/api/tls.html#tls_tls_connect_options_callback). Puede leer más sobre el emulador en el artículo [Uso del emulador de Azure Cosmos DB para desarrollo y pruebas](./local-emulator.md).

En este tutorial se describen las tareas siguientes:

> [!div class="checklist"]
> * Rotación de certificados
> * Exportación de certificados TLS/SSL
> * Obtener información sobre cómo usar el certificado en Java, Python y Node.js

## <a name="certification-rotation"></a>Rotación de certificación

Los certificados del emulador local de Azure Cosmos DB se generan la primera vez que se ejecuta el emulador. Hay dos certificados. Uno se utiliza para la conexión con el emulador local y otro para la administración de secretos en el emulador. El certificado que desea exportar es el certificado de conexión con el nombre descriptivo "DocumentDBEmulatorCertificate".

Ambos certificados se pueden regenerar; para ello, haga clic en **Restablecer datos** tal y como se muestra a continuación desde el emulador de Azure Cosmos DB que se ejecuta en la Bandeja de Windows. Si regenera los certificados y los tiene instalados en el almacén de certificados de Java o los usa en otro lugar, deberá actualizarlos; en caso contrario, la aplicación ya no se conectará al emulador local.

![Restablecimiento de datos en el emulador local de Azure Cosmos DB](./media/local-emulator-export-ssl-certificates/database-local-emulator-reset-data.png)

## <a name="how-to-export-the-azure-cosmos-db-tlsssl-certificate"></a>Exportación del certificado TLS/SSL de Azure Cosmos DB

1. Inicie el administrador de certificados de Windows; para ello, ejecute certlm.msc, vaya a la carpeta Personal-> Certificados y abra el certificado con el nombre descriptivo **DocumentDbEmulatorCertificate**.

    ![Paso 1 de exportación de un emulador local de Azure Cosmos DB](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-1.png)

2. Haga clic en **Detalles** y, luego, en **Aceptar**.

    ![Paso 2 de exportación de un emulador local de Azure Cosmos DB](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-2.png)

3. Haga clic en **Copiar en archivo...** .

    ![Paso 3 de exportación de un emulador local de Azure Cosmos DB](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-3.png)

4. Haga clic en **Next**.

    ![Paso 4 de exportación de un emulador local de Azure Cosmos DB](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-4.png)

5. Haga clic en **No exportar la clave privada** y, luego, haga clic en **Siguiente**.

    ![Paso 5 de exportación de un emulador local de Azure Cosmos DB](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-5.png)

6. Haga clic en **X.509 codificado base 64 (. CER)** y luego en **Siguiente**.

    ![Paso 6 de exportación de un emulador local de Azure Cosmos DB](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-6.png)

7. Asigne un nombre al certificado. En este caso, **documentdbemulatorcert** y, luego, haga clic en **Siguiente**.

    ![Paso 7 de exportación de un emulador local de Azure Cosmos DB](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-7.png)

8. Haga clic en **Finalizar**

    ![Paso 8 de exportación de un emulador local de Azure Cosmos DB](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-8.png)

## <a name="how-to-use-the-certificate-in-java"></a>Uso del certificado en Java

Al ejecutar aplicaciones de Java o MongoDB que utilizan el cliente de Java, es más fácil instalar el certificado en el almacén de certificados predeterminado de Java que pasar las marcas `-Djavax.net.ssl.trustStore=<keystore> -Djavax.net.ssl.trustStorePassword="<password>"`. Por ejemplo, la aplicación de demostración de Java (`https://localhost:8081/_explorer/index.html`) incluida depende del almacén de certificados predeterminado.

Siga las instrucciones de la publicación [Incorporación de un certificado al almacén de certificados CA de Java](https://docs.microsoft.com/azure/java-add-certificate-ca-store) para importar el certificado X.509 en el almacén de certificados de Java. Tenga en cuenta que trabajará en el directorio %JAVA_HOME% al ejecutar keytool.

Una vez que el certificado TLS/SSL "CosmosDBEmulatorCertificate" está instalado en la aplicación, debe poder conectarse y utilizar el emulador de Azure Cosmos DB local. Si sigue teniendo problemas, puede seguir los pasos indicados en el artículo [Debugging SSL/TLS Connections](https://docs.oracle.com/javase/7/docs/technotes/guides/security/jsse/ReadDebug.html) (Depuración de conexiones SSL/TLS). Es muy probable que el certificado no esté instalado en el almacén %JAVA_HOME%/jre/lib/security/cacerts. Por ejemplo, si tiene varias versiones instaladas de Java, es probable que la aplicación esté utilizando un almacén de certificados CA diferente del actualizado.

## <a name="how-to-use-the-certificate-in-python"></a>Uso del certificado en Python

De forma predeterminada, el [SDK de Python (versión 2.0.0 o posterior)](sql-api-sdk-python.md) para la API de SQL no probará ni usará el certificado TLS/SSL al conectarse con el emulador local. Sin embargo, si quiere usar la validación de TLS, puede seguir los ejemplos de la documentación de los [contenedores de sockets de Python](https://docs.python.org/2/library/ssl.html).

## <a name="how-to-use-the-certificate-in-nodejs"></a>Uso del certificado en Node.js

De forma predeterminada, el [SDK de Node.js (versión 1.10.1 o posterior)](sql-api-sdk-node.md) para la API de SQL no probará ni usará el certificado TLS/SSL al conectarse con el emulador local. Sin embargo, si desea usar la validación de TLS, puede seguir los ejemplos de la [documentación de Node.js](https://nodejs.org/api/tls.html#tls_tls_connect_options_callback).

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha hecho lo siguiente:

> [!div class="checklist"]
> * Ha alternado certificados
> * Ha exportado el certificado TLS/SSL
> * Ha aprendido a usar el certificado en Java, Python y Node.js

Ahora puede continuar con la sección de conceptos para obtener más información sobre Azure Cosmos DB. 

> [!div class="nextstepaction"]
>[Niveles de coherencia de datos optimizables en Azure Cosmos DB](../cosmos-db/consistency-levels.md)
