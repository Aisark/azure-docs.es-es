---
title: Descarga del SDK de Azure para PHP
description: Obtenga información acerca de cómo descargar e instalar el SDK de Azure para PHP.
documentationcenter: php
services: app-service\web
author: allclark
manager: douge
editor: ''
ms.assetid: bac355ac-4c25-42f4-8273-c5112eafa8d4
ms.service: app-service-web
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 06/01/2016
ms.author: allclark;yaqiyang
ms.openlocfilehash: 4f2c242e27d8a0ed6687eb4a3510bbce6e07694f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61457641"
---
# <a name="download-the-azure-sdk-for-php"></a>Descarga del SDK de Azure para PHP

## <a name="overview"></a>Información general

El SDK de Azure para PHP incluye componentes que le permiten desarrollar, implementar y administrar aplicaciones PHP para Azure. Específicamente, el SDK de Azure para PHP incluye lo siguiente:

* **Las bibliotecas de clientes PHP para Azure**. Estas bibliotecas de clases proporcionan una interfaz para tener acceso a características de Azure, como los servicios de administración de datos y los servicios en la nube.
* **La interfaz de la línea de comandos de Azure para Mac, Linux y Windows (CLIC de Azure)**. Este es un conjunto de herramientas que sirve para implementar y administrar servicios de Azure, como Azure Websites y Azure Virtual Machines. La interfaz de la línea de comandos de Azure funciona en cualquier plataforma, incluidas las plataformas Mac, Linux y Windows.
* **Azure PowerShell (solo Windows)**. Este es un conjunto de cmdlets de PowerShell para implementar y administrar servicios de Azure, como Cloud Services y Virtual Machines.
* **Los emuladores de Azure (solo Windows)**. Los emuladores de proceso y almacenamiento son emuladores locales de los servicios en la nube y los servicios de administración de datos que le permiten probar localmente una aplicación. Los emuladores de Azure solo se ejecutan en Windows.

Las secciones que vienen a continuación describen cómo descargar e instalar los componentes descritos.

En las instrucciones de este tema se da por hecho que tiene [PHP][install-php] instalado.

> [!NOTE]
> Debe tener instalado PHP 5.5 o superior con el fin de utilizar las bibliotecas de clientes PHP para Azure.
>
>

## <a name="php-client-libraries-for-azure"></a>Bibliotecas de clientes PHP para Azure

Las bibliotecas de clientes PHP para Azure proporcionan una interfaz para tener acceso a características de Azure, como los servicios de administración de datos y los servicios en la nube, desde cualquier sistema operativo. Estas bibliotecas se pueden instalar mediante el Compositor.

Si desea obtener información sobre el uso de las bibliotecas de clientes PHP para Azure, consulte [Uso del servicio BLOB][blob-service], [Uso del servicio Tabla][table-service] y [Uso del servicio Cola][queue-service].

### <a name="install-via-composer"></a>Instalación mediante el compositor

1. [Instalación de Git][install-git] En Windows, también tendrá que agregar el archivo ejecutable Git a la variable de entorno PATH.

2. Cree un archivo con el nombre **composer.json** en la raíz del proyecto y agréguele el código siguiente:

        {
            "require": {
                "microsoft/windowsazure": "^0.4"
            }
        }

3. Descargue **[composer.phar][composer-phar]** en la raíz del proyecto.

4. Abra un símbolo del sistema y ejecute esto en la raíz del proyecto

        php composer.phar install

## <a name="azure-powershell-and-azure-emulators"></a>Azure PowerShell y emuladores de Azure

Azure PowerShell es un conjunto de cmdlets de PowerShell para implementar y administrar servicios de Azure (como Cloud Services y Virtual Machines). Los emuladores de Azure son emuladores de servicios en la nube y servicios de administración de datos que le permiten probar localmente una aplicación. Estos componentes solo son compatibles con Windows.

La manera recomendada de instalar Azure PowerShell y los emuladores de Azure es utilizar el [instalador de plataforma web de Microsoft][download-wpi]. Observe que también puede elegir instalar otros componentes de desarrollo, como PHP, SQL Server, los controladores de Microsoft para SQL Server para PHP y WebMatrix.

Para obtener más información sobre el uso de Azure PowerShell, consulte [Cómo instalar y configurar Azure PowerShell][powershell-tools].

## <a name="azure-cli"></a>Azure CLI

La interfaz de la línea de comandos de Azure es un conjunto de herramientas que sirve para implementar y administrar servicios de Azure, como Azure Websites y Azure Virtual Machines. Para obtener información acerca de cómo instalar la CLI de Azure, consulte [Instalar la CLI de Azure](cli-install-nodejs.md).

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información, consulte el [Centro para desarrolladores de PHP](https://azure.microsoft.com/develop/php/).

[install-php]: http://www.php.net/manual/en/install.php
[composer-github]: https://github.com/composer/composer
[composer-phar]: https://getcomposer.org/composer.phar
[nodejs-org]: https://nodejs.org/
[install-node-linux]: https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager
[download-wpi]: https://go.microsoft.com/fwlink/?LinkId=253447
[mac-installer]: https://go.microsoft.com/fwlink/?LinkId=252249
[blob-service]: https://go.microsoft.com/fwlink/?LinkId=252714
[table-service]: https://go.microsoft.com/fwlink/?LinkId=252715
[queue-service]: https://go.microsoft.com/fwlink/?LinkId=252716
[azure cli]: https://go.microsoft.com/fwlink/?LinkId=252717
[powershell-tools]: https://go.microsoft.com/fwlink/?LinkId=252718
[php-sdk-github]: https://go.microsoft.com/fwlink/?LinkId=252719
[install-git]: https://git-scm.com/book/en/Getting-Started-Installing-Git
