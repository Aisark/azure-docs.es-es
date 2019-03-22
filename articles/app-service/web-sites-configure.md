---
title: 'Configuración de aplicaciones: Azure App Service'
description: Cómo configurar una aplicación en Azure App Service
services: app-service\web
documentationcenter: ''
author: cephalin
manager: erikre
editor: ''
ms.assetid: 9af8a367-7d39-4399-9941-b80cbc5f39a0
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/25/2017
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: fb8dedac8b795ec127d7b4a14728d73c9397a1dd
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/25/2019
ms.locfileid: "56807412"
---
# <a name="configure-apps-in-azure-app-service"></a>Configuración de aplicaciones en Azure App Service

En este tema se explica cómo configurar una aplicación web, un back-end para dispositivos móviles o una aplicación de API con [Azure Portal].

## <a name="application-settings"></a>Configuración de la aplicación
1. En [Azure Portal], abra la hoja de la aplicación.
2. Haga clic en **Configuración de la aplicación**.

![Configuración de la aplicación][configure01]

La hoja **Configuración de la aplicación** tiene configuraciones agrupadas en varias categorías.

### <a name="general-settings"></a>Configuración general
**Versiones del marco**. Configure estas opciones si su aplicación utiliza cualquiera de estos marcos: 

* **.NET Framework**: establezca la versión de .NET Framework. 
* **PHP**: establezca la versión de PHP, o bien seleccione **DESACTIVADO** para deshabilitar PHP. 
* **Java**: seleccione la versión de Java o **DESACTIVADO** para deshabilitar Java. Utilice la opción **Contenedor web** para elegir entre las versiones Tomcat y Jetty.
* **Python**: seleccione la versión de Python o seleccione **DESACTIVADO** para deshabilitar Python.

Por razones técnicas, si se habilita Java para la aplicación, se deshabilitan las opciones .NET, PHP y Python.

<a name="platform"></a>
**Plataforma**. Seleccione si su aplicación se ejecuta en un entorno de 32 o 64 bits. El entorno de 64 bits requiere el nivel básico o estándar. Los niveles libre y compartido siempre se ejecutan en un entorno de 32 bits.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

**Sockets web**. Seleccione **ACTIVADO** para habilitar el protocolo WebSocket; por ejemplo, si la aplicación utiliza [ASP.NET SignalR] o [socket.io](https://socket.io/).

<a name="alwayson"></a>
**AlwaysOn**. De forma predeterminada, las aplicaciones se descargan si están inactivas durante algún tiempo. Esto permite que el sistema conserve recursos. En el modo básico o estándar puede habilitar **Siempre disponible** para mantener el sitio cargado continuamente. Si su aplicación ejecuta WebJobs continuos o WebJobs activados mediante una expresión CRON, debe habilitar **AlwaysOn** o los trabajos web no se ejecutarán de forma fiable.

**Versión de canalización administrada**. Configura el [modo de canalización]IIS. Deje este valor en Integrado (el valor predeterminado) a no ser que tenga una aplicación web heredada que requiera una versión anterior de IIS.

**Versión HTTP**. Establézcala en **2.0** para habilitar la compatibilidad con el protocolo [HTTPS/2](https://wikipedia.org/wiki/HTTP/2). 

> [!NOTE]
> Los exploradores más modernos admiten el protocolo HTTP/2 sobre TLS únicamente, mientras que el tráfico no cifrado sigue usando HTTP/1.1. Para asegurarse de que los exploradores del cliente se conectan a la aplicación con HTTP/2, [compre un certificado App Service Certificate](web-sites-purchase-ssl-web-site.md) para el dominio personalizado de la aplicación o [enlace con un certificado de terceros](app-service-web-tutorial-custom-ssl.md).

**Afinidad ARR**. En una aplicación que se ha escalado horizontalmente a varias instancias de máquinas virtuales, las cookies de Afinidad ARR garantizan que el cliente se enruta hacia la misma instancia a lo largo de toda la duración de la sesión. Para mejorar el rendimiento de las aplicaciones sin estado, establezca esta opción en **Desactivado**.   

**Intercambio automático**. Si habilita el intercambio automático de una ranura de implementación, App Service intercambiará automáticamente la aplicación en producción cuando inserte una actualización para esa ranura. Para más información, consulte [Implementación en ranuras de ensayo para las aplicaciones en Azure App Service](deploy-staging-slots.md).

### <a name="debugging"></a>Depuración
**Depuración remota**. Habilita la depuración remota. Cuando esté habilitada, puede usar la depuración remota en Visual Studio para conectarse directamente a su aplicación. La depuración remota permanecerá habilitada durante 48 horas. 

### <a name="app-settings"></a>Configuración de la aplicación
Esta sección contiene los pares de nombre y valor que la aplicación cargará al inicio. 

* En las aplicaciones .NET, estas configuraciones se insertarán en la sección de la configuración de .NET `AppSettings` en tiempo de ejecución y reemplazará la configuración existente. 
* Para App Service en Linux o Web App for Containers, si ha anidado en su nombre la estructura de claves de JSON como `ApplicationInsights:InstrumentationKey` deberá tener `ApplicationInsights__InstrumentationKey` como nombre de clave. Por lo que tenga en cuenta que cualquier `:` deben reemplazarse por `__` (es decir, el subrayado doble).
* Las aplicaciones PHP, Python, Java y Node pueden acceder a estas configuraciones como variables de entorno en tiempo de ejecución. En cada configuración de aplicación se crean dos variables de entorno; una con el nombre especificado en el entrada de configuración de la aplicación y otra con el prefijo APPSETTING_. Ambas contienen el mismo valor.

La configuración de la aplicación siempre se cifra cuando se almacena (cifrado en reposo).

La configuración de la aplicación se puede resolver desde Key Vault mediante las [referencias de Key Vault](app-service-key-vault-references.md).

### <a name="connection-strings"></a>Cadenas de conexión
Cadenas de conexión para los recursos vinculados. 

En las aplicaciones .NET, estas cadenas de conexión se insertan en la sección `connectionStrings` de la configuración de .NET en tiempo de ejecución y reemplazan las entradas existentes en las que la clave sea igual que el nombre de la base de datos vinculada. 

En las aplicaciones PHP, Python, Java y Node, estas configuraciones estarán disponibles como variables de entorno en tiempo de ejecución, con el tipo de conexión como prefijo. Los prefijos de variable de entorno son los siguientes: 

* SQL Server: `SQLCONNSTR_`
* MySQL: `MYSQLCONNSTR_`
* SQL Database: `SQLAZURECONNSTR_`
* Personalizado: `CUSTOMCONNSTR_`

Por ejemplo, si una cadena de conexión de MySQL recibió el nombre de  `connectionstring1`, se obtendrá acceso a ella a través de la variable de entorno `MYSQLCONNSTR_connectionString1`.

Las cadenas de conexión siempre se cifran cuando se almacenan (cifrado en reposo).

Las cadenas de conexión se pueden resolver desde Key Vault mediante las [referencias de Key Vault](app-service-key-vault-references.md).

### <a name="default-documents"></a>Documentos predeterminados
El documento predeterminado es la página web que se muestra en la dirección URL raíz de un sitio web.  Se usa el primer archivo coincidente en la lista. 

Es posible que las aplicaciones utilicen módulos que enruten basándose en la URL, en lugar de ofrecer funcionalidad a contenido estático, en cuyo caso no existe realmente un documento predeterminado.    

### <a name="handler-mappings"></a>Asignaciones de controlador
Utilice esta zona para agregar procesadores de script personalizados para controlar solicitudes de extensiones de archivo específicas. 

* **Extensión**. La extensión de archivo que se va a gestionar, por ejemplo, *.php o handler.fcgi. 
* **Ruta de acceso del procesador de script**. La ruta absoluta del procesador de script. El procesador de script procesará las solicitudes a archivos que coincidan con esta extensión de archivo. Utilice la ruta de acceso `D:\home\site\wwwroot` para hacer referencia al directorio raíz de la aplicación.
* **Argumentos adicionales**. Argumentos opcionales de la línea de comandos para el procesador de script 

### <a name="virtual-applications-and-directories"></a>Directorios y aplicaciones virtuales
Para configurar las aplicaciones y los directorios virtuales, especifique cada directorio virtual y su ruta de acceso física correspondiente en relación con la raíz del sitio web. De manera opcional, puede activar la casilla **Aplicación** para marcar un directorio virtual como una aplicación.

## <a name="enabling-diagnostic-logs"></a>Habilitación de registros de diagnóstico
Para habilitar registros de diagnóstico:

1. En la hoja de la aplicación, haga clic en **Todas las configuraciones**.
2. Haga clic en **Registros de diagnóstico**. 

Opciones para escribir registros de diagnóstico de una aplicación web que admita el registro: 

* **Registro de aplicaciones**. Escribe registros de aplicación en el sistema de archivos. El registro dura un período de 12 horas. 

**Nivel**. Cuando el registro de aplicaciones está habilitado, esta opción especifica la cantidad de información que se registrará (Error, Advertencia, Información o Detalle).

**Registro del servidor web**. Los registros se guardan con formato de archivo de registro W3C extendido. 

**Mensajes de error detallados**. Guarda archivos .htm de mensajes de error detallados. Los archivos se guardan en /LogFiles/DetailedErrors. 

**Seguimiento de solicitudes erróneas**. Registra solicitudes erróneas en archivos XML. Los archivos se guardan en /LogFiles/W3SVC*xxx*, donde xxx es un identificador único. Esta carpeta contiene un archivo XSL y uno o varios archivos XML. Asegúrese de descargar el archivo XSL porque proporciona funcionalidad para dar formato y filtrar los contenidos de los archivos XML.

Para ver los archivos de registro, debe crear las credenciales FTP, de la forma siguiente:

1. En la hoja de la aplicación, haga clic en **Todas las configuraciones**.
2. Haga clic en **Credenciales de implementación**.
3. Escriba un nombre de usuario y una contraseña.
4. Haga clic en **Save**(Guardar).

![Configurar credenciales de implementación][configure03]

El nombre de usuario de FTP completo es "app\nombreusuario", donde *app* es el nombre de su aplicación. El nombre de usuario se indica en la hoja de la aplicación, en **Essentials**.

![Credenciales de implementación de FTP][configure02]

## <a name="other-configuration-tasks"></a>Otras tareas de configuración
### <a name="ssl"></a>SSL
En modo básico o estándar puede cargar certificados SSL para un dominio personalizado. Para más información, consulte [Habilitación de HTTPS para una aplicación](app-service-web-tutorial-custom-ssl.md). 

Para ver los certificados cargados, haga clic en **Toda la configuración** > **Dominios personalizados y SSL**.

### <a name="domain-names"></a>Nombres de dominio
Agregue nombres de dominio personalizados para su aplicación. Para más información, consulte [Configuración de un nombre de dominio personalizado para una aplicación en Azure App Service](app-service-web-tutorial-custom-domain.md).

Para ver los nombres de dominios, haga clic en **Toda la configuración** > **Dominios personalizados y SSL**.

### <a name="deployments"></a>Implementaciones
* Configure la implementación continua. Consulte [Uso de Git para implementar aplicaciones en Azure App Service](deploy-local-git.md).
* Ranuras de implementación. Consulte [Implementación de entornos de ensayo para Azure App Service].

Para ver las ranuras de implementación, haga clic en **Toda la configuración** > **Ranuras de implementación**.

### <a name="monitoring"></a>Supervisión
En modo estándar o básico, pruebe la disponibilidad de los puntos de conexión HTTP o HTTPS desde ubicaciones geodistribuidas. Una prueba de supervisión da error si el código de respuesta HTTP es un error (4xx o 5xx) o si la respuesta se retrasa más de 30 segundos. Un extremo se considera disponible si sus pruebas de supervisión se realizan correctamente desde todas las ubicaciones especificadas. 

Para obtener más información, consulte [Cómo supervisar el estado del punto de conexión de web].

## <a name="next-steps"></a>Pasos siguientes
* [Configuración de un nombre de dominio personalizado en Azure App Service]
* [Habilitación de HTTPS para una aplicación en Azure App Service]
* [Escalado de una aplicación en Azure App Service]
* [Aspectos básicos de supervisión en Azure App Service]
* [Cambiar la configuración applicationHost.config con applicationHost.xdt](https://github.com/projectkudu/kudu/wiki/Xdt-transform-samples)

<!-- URL List -->

[ASP.NET SignalR]: https://www.asp.net/signalr
[Azure Portal]: https://portal.azure.com/
[Configuración de un nombre de dominio personalizado en Azure App Service]: ./app-service-web-tutorial-custom-domain.md
[Implementación de entornos de ensayo para Azure App Service]: ./deploy-staging-slots.md
[Habilitación de HTTPS para una aplicación en Azure App Service]: ./app-service-web-tutorial-custom-ssl.md
[Cómo supervisar el estado del punto de conexión de web]: https://go.microsoft.com/fwLink/?LinkID=279906
[Aspectos básicos de supervisión en Azure App Service]: ./web-sites-monitor.md
[modo de canalización]: https://www.iis.net/learn/get-started/introduction-to-iis/introduction-to-iis-architecture#Application
[Escalado de una aplicación en Azure App Service]: ./web-sites-scale.md

<!-- IMG List -->

[configure01]: ./media/web-sites-configure/configure01.png
[configure02]: ./media/web-sites-configure/configure02.png
[configure03]: ./media/web-sites-configure/configure03.png
