---
title: 'Conexión al servidor SFTP con SSH: Azure Logic Apps'
description: Automatice las tareas que supervisan, crean, administran, envían y reciben archivos en un servidor SFTP mediante SSH y Azure Logic Apps.
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: divswa, klam, LADocs
ms.topic: article
ms.date: 06/18/2019
tags: connectors
ms.openlocfilehash: a48ba0d2d691314a1ca7c91ac7ae27b62fbb379b
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/08/2019
ms.locfileid: "73825235"
---
# <a name="monitor-create-and-manage-sftp-files-by-using-ssh-and-azure-logic-apps"></a>Supervisión, creación y administración de archivos SFTP mediante SSH y Azure Logic Apps

Para automatizar las tareas que supervisan, crean, envían y reciben archivos en un servidor [Secure File Transfer Protocol (SFTP)](https://www.ssh.com/ssh/sftp/) mediante el protocolo [Secure Shell (SSH)](https://www.ssh.com/ssh/protocol/), puede crear y automatizar los flujos de trabajo de integración mediante Azure Logic Apps y el conector SFTP-SSH. SFTP es un protocolo de red que proporciona acceso a archivos, transferencia de archivos y administración de archivos a través de cualquier flujo de datos confiable. Estas son algunas tareas de ejemplo que se pueden automatizar:

* Supervisar cuándo se agregan o cambian archivos
* Obtener, crear, copiar, cambiar el nombre, actualizar, enumerar y eliminar archivos.
* Crear carpetas.
* Obtener contenido de archivos y metadatos
* Extraer archivos en carpetas

Puede usar desencadenadores que supervisen eventos en el servidor SFTP y permitir que la salida esté disponible para otras acciones. Puede usar acciones que realicen diversas tareas en el servidor SFTP. También puede hacer que otras acciones de la aplicación lógica usen la salida de las acciones SFTP. Por ejemplo, si recupera regularmente archivos del servidor SFTP, puede enviar por correo electrónico alertas sobre esos archivos y su contenido mediante el conector Office 365 Outlook o el conector Outlook.com. Si no está familiarizado con las aplicaciones lógicas, consulte [¿Qué es Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

Para conocer las diferencias entre el conector SFTP-SSH y el conector SFTP, revise [Comparación de SFTP-SSH y SFTP](#comparison) más adelante en este tema.

## <a name="limits"></a>límites

* De forma predeterminada, las acciones SFTP-SSH pueden leer o escribir archivos de *1 GB o más pequeños*, pero solo en fragmentos de *15 MB* a la vez. Para controlar archivos de más de 15 MB, las acciones SFTP-SSH admiten [fragmentación de mensajes](../logic-apps/logic-apps-handle-large-messages.md), excepto para la acción Copiar archivo, que solo puede controlar los archivos de 15 MB. La acción **Obtener contenido de archivo** usa implícitamente la fragmentación de mensajes.

* Los desencadenadores SFTP-SSH no admiten la fragmentación. Cuando se solicita el contenido del archivo, los desencadenadores seleccionan solo los archivos que tienen un tamaño de 15 MB o menos. Para obtener archivos de más de 15 MB, siga este patrón en su lugar:

  * Use un desencadenador SFTP-SSH que devuelva las propiedades de archivo, como **Cuando se agrega o modifica un archivo (solo propiedades)** .

  * Siga el desencadenador con la acción **Obtener contenido de archivo** SFTP-SSH, que lee el archivo completo y utiliza implícitamente la fragmentación de mensajes.

<a name="comparison"></a>

## <a name="compare-sftp-ssh-versus-sftp"></a>Comparación de SFTP-SSH y SFTP

Estas son otras diferencias importantes entre el conector SFTP-SSH y el conector SFTP donde el conector SFTP-SSH tiene estas funcionalidades:

* Usa la biblioteca [SSH.NET](https://github.com/sshnet/SSH.NET), que es una biblioteca de Secure Shell (SSH) de código abierto que admite .NET.

* De forma predeterminada, las acciones SFTP-SSH pueden leer o escribir archivos de *1 GB o más pequeños*, pero solo en fragmentos de *15 MB* a la vez. Para controlar los archivos de más de 15 MB, las acciones SFTP-SSH pueden usar la [fragmentación de mensajes](../logic-apps/logic-apps-handle-large-messages.md). Para cargar archivos de gran tamaño, también necesita permisos de lectura y escritura. Sin embargo, la acción Copiar archivo admite solo los archivos de 15 MB porque esa acción no admite la fragmentación de mensajes. Los desencadenadores SFTP-SSH no admiten la fragmentación.

* Proporciona la acción **Crear carpeta**, que crea una carpeta en la ruta de acceso especificada en el servidor SFTP.

* Proporciona la acción **Cambiar nombre de archivo**, que cambia el nombre de un archivo en el servidor SFTP.

* Almacena en caché la conexión al servidor SFTP *durante hasta 1 hora*, lo que mejora el rendimiento y reduce el número de intentos de conexión al servidor. Para establecer la duración de este comportamiento de almacenamiento en caché, modifique la propiedad [**ClientAliveInterval**](https://man.openbsd.org/sshd_config#ClientAliveInterval) en la configuración de SSH del servidor SFTP.

## <a name="prerequisites"></a>Requisitos previos

* Una suscripción de Azure. Si no tiene una suscripción de Azure, [regístrese para obtener una cuenta gratuita de Azure](https://azure.microsoft.com/free/).

* La dirección del servidor SFTP y las credenciales de cuenta, que permiten que la aplicación lógica acceda a la cuenta de SFTP. También necesita acceso a una clave privada SSH y a la contraseña de clave privada SSH. Para usar la fragmentación al cargar archivos de gran tamaño, necesita permisos de lectura y escritura.

  > [!IMPORTANT]
  >
  > El conector SFTP-SSH *solo* admite estas claves privadas, formatos, algoritmos y huellas digitales:
  >
  > * **Formatos de clave privada**: claves RSA (Rivest Shamir Adleman) y DSA (Digital Signature Algorithm) en formatos OpenSSH y ssh.com. Si la clave privada tiene el formato de archivo PuTTy (.ppk), en primer lugar [convierta la clave al formato de archivo OpenSSH (.pem)](#convert-to-openssh).
  >
  > * **Algoritmos de cifrado**: DES-EDE3-CBC, DES-EDE3-CFB, DES-CBC, AES-128-CBC, AES-192-CBC y AES-256-CBC
  >
  > * **Huella digital**: MD5
  >
  > Después de agregar el desencadenador o la acción SFTP-SSH que quiera a la aplicación lógica, debe proporcionar información de conexión para el servidor SFTP. Cuando proporcione su clave privada SSH para esta conexión, ***no escriba ni edite manualmente la clave***, ya que podría provocar un error en la conexión. En su lugar, asegúrese de ***copiar la clave*** del archivo de clave privada SSH y ***pegar*** esa clave en los detalles de la conexión. 
  > Para obtener más información, consulte la sección [Conectarse a SFTP con SSH](#connect) que se detalla más adelante en este artículo.

* Conocimientos básicos acerca de [cómo crear aplicaciones lógicas](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* La aplicación lógica desde donde quiere acceder a la cuenta de SFTP. Para comenzar con un desencadenador SFTP-SSH, [cree una aplicación lógica en blanco](../logic-apps/quickstart-create-first-logic-app-workflow.md). Para usar una acción SFTP-SSH, inicie la aplicación lógica con otro desencadenador, por ejemplo, el desencadenador **Recurrence**.

## <a name="how-sftp-ssh-triggers-work"></a>Cómo funcionan los desencadenadores SFTP-SSH

El método de funcionamiento de los desencadenadores SFTP-SSH es sondear el sistema de archivos SFTP y buscar cualquier archivo que se haya cambiado desde el último sondeo. Algunas herramientas le permiten conservar la marca de tiempo cuando cambian los archivos. En estos casos, tiene que deshabilitar esta característica para que el desencadenador funcione. Estas son algunas opciones de configuración habituales:

| Cliente SFTP | . |
|-------------|--------|
| WinSCP | Vaya a **Options** > **Preferences** > **Transfer** > **Edit** > **Preserve timestamp** > **Disable** (Opciones > Preferencias > Transferir > Editar > Conservar marca de tiempo >Deshabilitar). |
| FileZilla | Vaya a **Transfer** > **Preserve timestamps of transferred files** > **Disable** (Transferir > Conservar marca de tiempo de archivos transferidos > Deshabilitar). |
|||

Cuando un desencadenador encuentra un nuevo archivo, el desencadenador comprueba que el nuevo archivo se ha escrito por completo y no parcialmente. Por ejemplo, un archivo podría tener los cambios en curso cuando el desencadenador comprueba el servidor de archivos. Para evitar devolver un archivo parcialmente escrito, el desencadenador anota la marca de tiempo del archivo que tiene cambios recientes, pero no devuelve inmediatamente dicho archivo. El desencadenador devuelve el archivo solo al volver a sondear el servidor. En ocasiones, este comportamiento puede provocar un retraso de hasta dos veces el intervalo de sondeo del desencadenador.

<a name="convert-to-openssh"></a>

## <a name="convert-putty-based-key-to-openssh"></a>Convertir la clave basada en PuTTy en OpenSSH

Si la clave privada está en formato PuTTy, que usa la extensión de nombre de archivo. ppk (clave privada de PuTTy), primero debe convertir la clave al formato OpenSSH, que usa la extensión de nombre de archivo. PEM (correo de privacidad mejorada).

### <a name="unix-based-os"></a>Sistema operativo basado en UNIX

1. Si las herramientas de PuTTy no están ya instaladas en el sistema, puede instalarlas ahora; por ejemplo:

   `sudo apt-get install -y putty`

1. Ejecute este comando, que crea un archivo que puede usar con el conector SFTP-SSH:

   `puttygen <path-to-private-key-file-in-PuTTY-format> -O private-openssh -o <path-to-private-key-file-in-OpenSSH-format>`

   Por ejemplo:

   `puttygen /tmp/sftp/my-private-key-putty.ppk -O private-openssh -o /tmp/sftp/my-private-key-openssh.pem`

### <a name="windows-os"></a>SO Windows

1. Si aún no lo ha hecho, [descargue la última herramienta de generación de PuTTY (puttygen.exe)](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html) y, a continuación, inicie la herramienta.

1. En esta pantalla, seleccione **Load** (Cargar).

   ![Seleccione "Load" (Cargar).](./media/connectors-sftp-ssh/puttygen-load.png)

1. Busque el archivo de clave privada en formato PuTTY y seleccione **Open** (Abrir).

1. En el menú **Conversions** (Conversiones), seleccione **Export OpenSSH key** (Exportar clave OpenSSH).

   ![Seleccione "Export OpenSSH key" (Exportar clave OpenSSH)](./media/connectors-sftp-ssh/export-openssh-key.png)

1. Guarde el archivo de clave privada con la extensión de nombre de archivo `.pem`.

<a name="connect"></a>

## <a name="connect-to-sftp-with-ssh"></a>Conexión a SFTP con SSH

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Inicie sesión en [Azure Portal](https://portal.azure.com) y abra la aplicación lógica en el diseñador de aplicaciones lógicas, si aún no lo ha hecho.

1. Para las aplicaciones lógicas en blanco, en el cuadro de búsqueda, escriba "sftp ssh" como filtro. En la lista de desencadenadores, seleccione el que desee.

   O bien

   Para las aplicaciones lógicas existentes, en el último paso donde desea agregar una acción, elija **Nuevo paso**. En el cuadro de búsqueda, escriba "sftp ssh" como filtro. En la lista de acciones, seleccione la que desee.

   Para agregar una acción entre un paso y otro, mueva el puntero sobre la flecha entre ellos. Elija el signo más ( **+** ) que aparece y seleccione **Agregar una acción**.

1. Proporcione la información necesaria para la conexión.

   > [!IMPORTANT]
   >
   > Al escribir la clave privada SSH en la propiedad **SSH private key**, siga estos pasos adicionales para asegurarse de proporcionar el valor completo y correcto para esta propiedad. Una clave no válida provoca un error de conexión.

   Aunque puede usar cualquier editor de texto, estos son los pasos de ejemplo que muestran cómo copiar y pegar correctamente la clave mediante Notepad.exe.

   1. Abra el archivo de clave privada SSH en un editor de texto. Estos pasos usan Bloc de notas como ejemplo.

   1. En el menú **Edición** del Bloc de notas, elija **Seleccionar todo**.

   1. Seleccione **Editar** > **Copiar**.

   1. En el desencadenador o la acción SFTP-SSH que ha agregado, pegue la clave *completa* que copió en la propiedad **SSH private key**, que admite varias líneas.  ***Asegúrese de pegar*** la clave. ***No especifique ni edite la clave manualmente***.

1. Cuando haya terminado de especificar los detalles de conexión, elija **Crear**.

1. Ahora, proporcione los detalles necesarios para el desencadenador o la acción seleccionados y continúe con la compilación del flujo de trabajo de la aplicación lógica.

## <a name="examples"></a>Ejemplos

<a name="file-added-modified"></a>

### <a name="sftp---ssh-trigger-when-a-file-is-added-or-modified"></a>SFTP - desencadenador SSH: When a file is added or modified (Cuando se agrega o modifica un archivo)

Este desencadenador inicia un flujo de trabajo de aplicación lógica cuando se agrega o se modifica un archivo en un servidor SFTP. Por ejemplo, puede agregar una condición que compruebe el contenido del archivo y obtenga el contenido en función de si cumple una condición especificada. Luego, puede agregar una acción que obtenga el contenido del archivo y lo coloque en una carpeta en el servidor SFTP.

**Ejemplo Enterprise**: Puede usar este desencadenador para supervisar nuevos archivos en una carpeta de SFTP que representan los pedidos de los clientes. Seguidamente, puede usar una acción SFTP, como **Get file content** para obtener el contenido del pedido para su posterior procesamiento y almacenar ese pedido en una base de datos de pedidos.

<a name="get-content"></a>

### <a name="sftp---ssh-action-get-content-using-path"></a>SFTP - acción SSH: Obtener contenido mediante la ruta de acceso

Esta acción obtiene el contenido de un archivo en un servidor SFTP. Por ejemplo, puede agregar el desencadenador del ejemplo anterior y una condición que debe cumplir el contenido del archivo. Si la condición es verdadera, se puede ejecutar la acción que obtiene el contenido.

## <a name="connector-reference"></a>Referencia de conectores

Para obtener detalles técnicos acerca de desencadenadores, acciones y límites, que se describen en la descripción de OpenAPI (antes Swagger) del conector, consulte la [página de referencia](/connectors/sftpconnector/) del conector.

## <a name="next-steps"></a>Pasos siguientes

* Obtenga más información sobre otros [conectores de Logic Apps](../connectors/apis-list.md)
