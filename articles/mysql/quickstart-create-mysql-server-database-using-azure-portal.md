---
title: 'Inicio rápido: Creación de un servidor: Azure Portal (Azure Database for MySQL)'
description: Este artículo le ayudará a usar Azure Portal para crear rápidamente un servidor de Azure Database for MySQL de ejemplo, en unos cinco minutos.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 12/02/2019
ms.openlocfilehash: bbf2e3204cb1e703aba445822bfb699fae13a112
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/25/2019
ms.locfileid: "75454312"
---
# <a name="create-an-azure-database-for-mysql-server-by-using-the-azure-portal"></a>Creación de un servidor de Azure Database for MySQL mediante Azure Portal

Azure Database for MySQL es un servicio administrado que se usa para ejecutar, administrar y escalar bases de datos MySQL de alta disponibilidad en la nube. Esta guía de inicio rápido muestra cómo crear un servidor de Azure Database for MySQL en unos cinco minutos mediante Azure Portal.  

Si no tiene una suscripción a Azure, cree una [cuenta gratuita de Azure](https://azure.microsoft.com/free/) antes de empezar.

## <a name="sign-in-to-the-azure-portal"></a>Inicio de sesión en Azure Portal
Abra el explorador web y vaya a [Azure Portal](https://portal.azure.com/). Introduzca sus credenciales para iniciar sesión en el portal. La vista predeterminada es el panel del servicio.

## <a name="create-an-azure-database-for-mysql-server"></a>Creación de un servidor de Azure Database for MySQL
Cree un servidor de Azure Database for MySQL con un conjunto definido de [recursos de proceso y almacenamiento](./concepts-compute-unit-and-storage.md). Cree el servidor dentro en un [grupo de recursos de Azure](../azure-resource-manager/management/overview.md).

Para crear un servidor de Azure Database for MySQL, siga estos pasos:

1. En la esquina superior izquierda del portal, seleccione **Crear un recurso** (+).

2. Seleccione **Bases de datos** > **Azure Database for MySQL**. También puede escribir **MySQL** en el cuadro de búsqueda para encontrar el servicio.

   ![Opción Azure Database for MySQL](./media/quickstart-create-mysql-server-database-using-azure-portal/2_navigate-to-mysql.png)

3. Rellene el formulario del nuevo servidor con la siguiente información:
   
   ![Formulario de creación del servidor](./media/quickstart-create-mysql-server-database-using-azure-portal/4-create-form.png)

    **Configuración** | **Valor sugerido** | **Descripción del campo** 
    ---|---|---
    Nombre de servidor | Nombre de servidor único | Elija un nombre único que identifique al servidor de Azure Database for MySQL. Por ejemplo, mydemoserver. El nombre de dominio *mysql.database.azure.com* se anexa al nombre del servidor proporcionado. El nombre del servidor solo puede contener letras minúsculas, números y el carácter de guion (-). Debe contener entre 3 y 63 caracteres.
    Subscription | Su suscripción | Seleccione la suscripción de Azure que quiere usar para el servidor. Si tiene varias suscripciones, elija aquella en la que se factura el recurso.
    Resource group | *myresourcegroup* | Proporcione un nombre de grupo de recursos nuevo o existente.
    Seleccionar origen | *En blanco* | Seleccione *En blanco* para crear un nuevo servidor desde el principio. (Seleccione *Copia de seguridad* si va a crear un servidor a partir de una copia de seguridad con redundancia geográfica de un servidor existente de Azure Database for MySQL).
    Inicio de sesión de administrador de servidor | myadmin | Una cuenta de inicio de sesión para usarla al conectarse al servidor. El nombre de inicio de sesión de administrador no puede ser **azure_superuser**, **admin**, **administrator**, **root**, **guest** ni **public**.
    Contraseña | *Su elección* | Proporcione una nueva contraseña para la cuenta de administrador del servidor. Debe contener entre ocho y 128 caracteres. La contraseña debe contener caracteres de tres de las siguientes categorías: Letras del alfabeto inglés mayúsculas y minúsculas, números (0-9) y caracteres no alfanuméricos (!, $, #, %, etc.).
    Confirmar contraseña | *Su elección*| Confirme la contraseña de la cuenta de administrador.
    Location | *Región más cercana a los usuarios*| Elija la ubicación más cercana a los usuarios o a sus otras aplicaciones de Azure.
    Versión | *La versión principal más reciente*| La versión principal más reciente (a menos que tenga requisitos específicos que requieran otra versión).
    Plan de tarifa | **Uso general**, **Gen 5**, **2 núcleos virtuales**, **5 GB**, **7 días**, **Redundancia geográfica** | Configuración de los recursos de proceso, almacenamiento y copia de seguridad para el nuevo servidor. Seleccione **Plan de tarifa**. A continuación, seleccione la pestaña **Uso general**. *Gen 5*, *4 núcleos virtuales*, *100 GB* y *7 días* son los valores predeterminados de **Generación de procesos**, **Núcleos virtuales**, **Almacenamiento** y **Período de retención de copia de seguridad**. Puede dejar los controles deslizantes tal y como están. Para habilitar las copias de seguridad del servidor en el almacenamiento con redundancia geográfica, seleccione **Redundancia geográfica** en **Opciones de redundancia de copia de seguridad**. Para guardar el plan de tarifa elegido, seleccione **Aceptar**. La captura de pantalla siguiente muestra estas opciones seleccionadas.
  
   > [!NOTE]
   > Considere la posibilidad de usar el plan de tarifa Básico si menos proceso y E/S resultan adecuados para su carga de trabajo. Tenga en cuenta que los servidores que creó en el plan de tarifa Básico no se podrán escalar más adelante a De uso general u Optimizada para memoria. Consulte la [página de precios](https://azure.microsoft.com/pricing/details/mysql/) para más información.
   > 

   ![Crear servidor: ventana de plan de tarifa](./media/quickstart-create-mysql-server-database-using-azure-portal/3-pricing-tier.png)

4. Seleccione **Crear** para realizar el aprovisionamiento del servidor. El aprovisionamiento puede tardar hasta 20 minutos.
   
5. Haga clic en **Notificaciones** en la barra de herramientas (icono de campana) para supervisar el proceso de implementación.
   
   De forma predeterminada, se crean las bases de datos siguientes en el servidor: **information_schema**, **mysql**, **performance_schema** y **sys**.

## <a name="configure-a-server-level-firewall-rule"></a>Configuración de una regla de firewall de nivel de servidor

El servicio Azure Database for MySQL crea un firewall en el nivel de servidor. Evita que herramientas y aplicaciones externas se conecten al servidor o a las bases de datos de este, a menos que se cree una regla de firewall que lo abra para direcciones IP concretas. 

1. Cuando finalice la implementación, busque el servidor. Si es necesario, puede buscarlo. Por ejemplo, seleccione **Todos los recursos** en el menú de la izquierda. Luego, escriba el nombre del servidor, en el ejemplo **mydemoserver**, para buscar el servidor recién creado. Seleccione el nombre del servidor en la lista de resultados de la búsqueda. Se abrirá la página **Introducción** del servidor, que proporciona opciones para continuar la configuración.

2. En la página del servidor, seleccione **Seguridad de la conexión**.

3. En el encabezado **Reglas de firewall**, seleccione el cuadro de texto en blanco de la columna **Nombre de regla** para empezar a crear la regla de firewall. Especifique el intervalo preciso de direcciones IP de los clientes que accederán a este servidor.
   
   ![Seguridad de la conexión: reglas de firewall](./media/quickstart-create-mysql-server-database-using-azure-portal/5-firewall-2.png)



4. En la barra de herramientas superior de la página **Seguridad de la conexión**, seleccione **Guardar**. Antes de continuar espere hasta que aparezca la notificación que indica que la actualización ha finalizado correctamente. 

   > [!NOTE]
   > Las conexiones a Azure Database for MySQL se comunican a través del puerto 3306. Si intenta conectarse desde una red corporativa, es posible que no se permita el tráfico saliente a través del puerto 3306. En ese caso no podrá conectarse al servidor, salvo que el departamento de TI abra el puerto 3306.
   > 

## <a name="get-the-connection-information"></a>Obtención de la información de conexión
Para conectarse al servidor de bases de datos, es preciso utilizar el nombre completo del servidor y las credenciales de inicio de sesión de administrador. Es posible que anotara dichos valores en el artículo de la guía de inicio rápido. En caso de que no lo haya hecho, tanto el nombre del servidor como la información de inicio de sesión se pueden encontrar con facilidad en la página **Introducción** del servidor o en la página **Propiedades** de Azure Portal.

Para encontrar estos valores, siga estos pasos: 

1. Abra la página **Información general** del servidor. Tome nota del **Nombre del servidor** y del **Server admin login name** (Nombre de inicio de sesión del administrador del servidor). 

2. Desplace el cursor sobre cada campo y el icono de copiar aparece a la derecha del texto. Seleccione el icono de copiar según sea necesario para copiar los valores.

En este ejemplo, el nombre del servidor es **mydemoserver.mysql.database.azure.com** y el inicio de sesión del administrador del servidor es **myadmin\@mydemoserver**.

## <a name="connect-to-mysql-by-using-the-mysql-command-line-tool"></a>Conexión a MySQL mediante la herramienta de línea de comandos mysql
Conéctese al servidor mediante la herramienta **mysql.exe** de la línea de comandos. Puede descargarlo desde [aquí](https://dev.mysql.com/downloads/) e instalarlo en su equipo. 

1. Para conectarse a un servidor de Azure Database for MySQL con la utilidad mysql, use el siguiente formato:

    ```bash
    mysql --host <fully qualified server name> --user <server admin login name>@<server name> -p
    ```

    Por ejemplo, el siguiente comando conecta con nuestro servidor de ejemplo:

    ```bash
    mysql --host mydemoserver.mysql.database.azure.com --user myadmin@mydemoserver -p
    ```

    parámetro mysql |Valor sugerido|Descripción
    ---|---|---
    --host | *Nombre del servidor* | El valor de nombre de servidor que usó al crear el servidor de Azure Database for MySQL. El servidor de ejemplo es **mydemoserver.mysql.database.azure.com.** Use el nombre de dominio completo ( **\*.mysql.database.azure.com**) como se muestra en el ejemplo. Si no recuerda el nombre del servidor, siga los pasos de la sección anterior para obtener la información de conexión. 
    --user | *Nombre de inicio de sesión del administrador del servidor* |El nombre de usuario de inicio de sesión del administrador del servidor que suministró al crear el servidor de Azure Database for MySQL. Si no recuerda el nombre de usuario, siga los pasos de la sección anterior para obtener la información de conexión. El formato es *nombre de usuario\@nombre de servidor*.
    -p | *Espere hasta que se le pida* |Cuando se le pida, proporcione la misma contraseña que especificó cuando se creó el servidor. Tenga en cuenta que los caracteres de la contraseña escritos no se muestran en el símbolo del sistema de Bash al escribirlos. Después de que haya escrito la contraseña, presione **Entrar**.

   Una vez conectado, la utilidad de mysql muestra un símbolo del sistema de `mysql>`, donde puede escribir comandos. 

   A continuación puede encontrar un ejemplo de salida de mysql:

    ```bash
    Welcome to the MySQL monitor.  Commands end with ; or \g.
    Your MySQL connection id is 65505
    Server version: 5.6.26.0 MySQL Community Server (GPL)
    
    Copyright (c) 2000, 2017, Oracle and/or its affiliates. All rights reserved.
    
    Oracle is a registered trademark of Oracle Corporation and/or its
    affiliates. Other names may be trademarks of their respective
    owners.

    Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
    
    mysql>
    ```
    > [!TIP]
    > Si el firewall no está configurado para permitir la dirección IP de su cliente, se produce el siguiente error:
    >
    > ERROR 2003 (28000): No está permitido que el cliente con la dirección IP 123.456.789.0 acceda al servidor.
    >
    > Para resolver el error, asegúrese de que la configuración del servidor coincida con los pasos descritos en la sección "Configuración de una regla de firewall de nivel de servidor" del artículo.

4. Para asegurarse de que la conexión es funcional, ver el estado del servidor, para lo que debe escribir `status` en el símbolo del sistema de mysql > .

    ```sql
    status
    ```

   > [!TIP]
   > Para otros comandos, consulte el [capítulo 4.5.1 del Manual de referencia de MySQL 5.7](https://dev.mysql.com/doc/refman/5.7/en/mysql.html).

5.  Cree una base de datos en blanco en el símbolo del sistema de **mysql>** , para lo que debe escribir el siguiente comando:
    ```sql
    CREATE DATABASE quickstartdb;
    ```
    El comando puede tardar un rato en completarse. 

    En un servidor de Azure Database for MySQL, puede crear una o varias bases de datos. Puede optar por crear una sola base de datos por servidor para que use todos los recursos, o bien crear varias para que los compartan. No hay límite en el número de bases de datos que se pueden crear, pero varias bases de datos comparten los mismos recursos del servidor. 

6. Escriba el comando siguiente para enumerar las bases de datos en el símbolo del sistema **mysql>** :

    ```sql
    SHOW DATABASES;
    ```

7.  Escriba `\q` y presione la tecla **Entrar** tecla para cerrar la herramienta mysql. 

Ya está conectado al servidor de Azure Database for MySQL y ha creado una base de datos de usuario en blanco. En la sección siguiente encontrará un ejercicio similar. En el siguiente ejercicio se conecta al mismo servidor con otra herramienta común, MySQL Workbench.

## <a name="connect-to-the-server-by-using-the-mysql-workbench-gui-tool"></a>Conexión al servidor mediante la herramienta de interfaz gráfica de usuario MySQL Workbench
Para conectarse al servidor mediante la herramienta de interfaz gráfica de usuario MySQL Workbench, siga estos pasos:

1.  Abra la aplicación MySQL Workbench en el equipo cliente. MySQL Workbench se puede descargar e instalar desde [Download MySQL Workbench](https://dev.mysql.com/downloads/workbench/) (Descargar MySQL Workbench).

2. Cree una nueva conexión. Seleccione el icono del signo más (+) situado junto al encabezado **MySQL Connections** (Conexiones MySQL).

3. En el cuadro de diálogo **Setup New Connection** (Configurar nueva conexión), escriba la información de la conexión del servidor en la pestaña **Parameters** (Parámetros). Los valores de marcador de posición se muestran como ejemplo. Reemplace el nombre de host, el nombre de usuario y la contraseña por sus propios valores.

   ![Configurar una nueva conexión](./media/quickstart-create-mysql-server-database-using-azure-portal/setup-new-connection.png)

    |Configuración |Valor sugerido|Descripción del campo|
    |---|---|---|
     Nombre de conexión | Conexión de demostración | Una etiqueta para esta conexión. |
    Método de conexión | Estándar (TCP/IP) | Estándar (TCP/IP) es suficiente. |
    Hostname | *Nombre del servidor* | El valor de nombre de servidor que usó al crear el servidor de Azure Database for MySQL. El servidor de ejemplo es **mydemoserver.mysql.database.azure.com.** Use el nombre de dominio completo ( **\*.mysql.database.azure.com**) como se muestra en el ejemplo. Si no recuerda el nombre del servidor, siga los pasos de la sección anterior para obtener la información de conexión.|
     Port | 3306 | El puerto que se usa al conectarse al servidor de base de datos de Azure Database for MySQL. |
    Nombre de usuario |  *Nombre de inicio de sesión del administrador del servidor* | La información de inicio de sesión del administrador del servidor que especificó al crear el servidor de Azure Database for MySQL. Nuestro nombre de usuario de ejemplo es **myadmin\@mydemoserver**. Si no recuerda el nombre de usuario, siga los pasos de la sección anterior para obtener la información de conexión. El formato es *nombre de usuario\@nombre de servidor*.
    Contraseña | *Su contraseña* | Para guardar la contraseña, seleccione **Store in Vault** (Almacenar en el almacén). |

4. Seleccione **Probar conexión** para probar si todos los parámetros están configurados correctamente. Luego, seleccione **Aceptar** para guardar la conexión. 

    > [!NOTE]
    > SSL se aplica de forma predeterminada en el servidor que requiere mayor configuración para conectarse correctamente. Para más información, consulte [Configuración de la conectividad SSL en la aplicación para conectarse de forma segura a Azure Database for MySQL](./howto-configure-ssl.md). Para deshabilitar SSL para este guía de inicio rápido, vaya a Azure Portal. Luego, seleccione la página de seguridad de la conexión para deshabilitar el botón de alternancia **Aplicar conexión SSL**.

## <a name="clean-up-resources"></a>Limpieza de recursos
Hay dos forma de eliminar los recursos que ha creado en la guía de inicio rápido. Puede eliminar el [grupo de recursos de Azure](../azure-resource-manager/management/overview.md), lo que incluye todos los recursos del grupo de recursos. Si desea mantener intactos los restantes recursos, elimine solo el único recurso del servidor.

> [!TIP]
> Otras guías de inicio rápido de esta colección se basan en esta. Si tiene previsto seguir usando otras guías de inicio rápido, no elimine los recursos que ha creado en esta. Si no tiene previsto continuar, siga estos pasos para eliminar todos los recursos que ha creado en esta.
>

Para eliminar todo el grupo de recursos, incluido el servidor recién creado, siga estos pasos:

1.  Elimine el grupo de recursos en Azure Portal. En el menú de la izquierda, seleccione **Grupos de recursos** y, después, el nombre del grupo de recursos (en el ejemplo, **myresourcegroup**).

2.  En la página del grupo de recursos, seleccione **Eliminar**. Luego, escriba el nombre del grupo de recursos, en nuestro **myresourcegroup**, en el cuadro para confirmar la eliminación y seleccione **Eliminar**.

Para eliminar solo el servidor recién creado, siga estos pasos:

1. Busque el servidor en Azure Portal si no lo tiene abierto. Seleccione **Todos los recursos** en el menú de la izquierda de Azure Portal. Luego busque el servidor que ha creado.

2. En la página **Información general**, seleccione **Eliminar**. 

   ![Azure Database for MySQL: eliminar servidor](./media/quickstart-create-mysql-server-database-using-azure-portal/delete-server.png)

3. Confirme el nombre del servidor que desea eliminar y muestre las bases de datos incluidas. Escriba el nombre del servidor en el cuadro (el de nuestro ejemplo, **mydemoserver**). Seleccione **Eliminar**.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Diseño de la primera base de datos de Azure Database for MySQL](./tutorial-design-database-using-portal.md)

