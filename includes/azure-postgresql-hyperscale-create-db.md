---
title: archivo de inclusión
description: archivo de inclusión
author: jonels-msft
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: include
ms.date: 05/14/2019
ms.author: jonels
ms.custom: include file
ms.openlocfilehash: c07e352288d7dc1d0bf198fd74c8baaded3a2d23
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/18/2019
ms.locfileid: "67186558"
---
Si no tiene una suscripción a Azure, cree una cuenta [gratuita](https://azure.microsoft.com/free/) antes de empezar.

## <a name="sign-in-to-the-azure-portal"></a>Inicio de sesión en Azure Portal

Inicie sesión en el [Azure Portal](https://portal.azure.com).

## <a name="create-an-azure-database-for-postgresql---hyperscale-citus"></a>Creación de una instancia de Azure Database for PostgreSQL: Hiperescala (Citus)

Para crear un servidor de Azure Database for PostgreSQL, siga estos pasos:
1. Haga clic en **Crear un recurso** de la esquina superior izquierda de Azure Portal.
2. En la página **Nuevo**, seleccione **Bases de datos** y, en la página **Bases de datos**, seleccione **Azure Database for PostgreSQL**.
3. Para la opción de implementación, haga clic en el botón **Crear** en **Hyperscale (Citus) server group - PREVIEW** (Grupo de servidores Hiperescala (Citus): VERSIÓN PRELIMINAR).
4. Rellene el formulario del nuevo servidor con la siguiente información:
   - Grupo de recursos: haga clic en el vínculo **Crear nuevo** que está debajo de este cuadro de texto para este campo. Escriba un nombre como **myresourcegroup**.
   - Nombre del grupo de servidores: escriba un nombre exclusivo para el nuevo grupo de servidores, que también se usará para un subdominio de servidor.
   - Nombre de usuario de administrador: actualmente, se necesita el valor **citus** y no se puede cambiar.
   - Contraseña: tiene que tener al menos ocho caracteres de las tres categorías siguientes: letras mayúsculas del alfabeto inglés, letras minúsculas del alfabeto inglés, números (0-9) y caracteres no alfanuméricos (!, $, # o %, entre otros)
   - Ubicación: use la ubicación más cercana a los usuarios para que puedan acceder de la forma más rápida posible a los datos.

   > [!IMPORTANT]
   > Se precisa la contraseña de administrador del servidor que especifique aquí para iniciar sesión en el servidor y sus bases de datos. Recuerde o grabe esta información para su uso posterior.

5. Haga clic en **Configurar grupo de servidores**. No modifique los valores de esa sección y haga clic en **Guardar**.
6. Haga clic en **Revisar y crear**  y luego en **Crear** para aprovisionar el servidor. El aprovisionamiento tarda unos minutos.
7. La página irá a la supervisión de la implementación. Cuando el estado activo cambia de **La implementación está en curso** a **Se completó la implementación**, haga clic en el elemento de menú **Salidas** que se encuentra a la izquierda de la página.
8. La página de resultados incluirá un nombre de host de coordinación junto a un botón para copiar el valor en el Portapapeles. Anote esta información para usarla más adelante.

## <a name="configure-a-server-level-firewall-rule"></a>Configuración de una regla de firewall de nivel de servidor

El servicio Azure Database for PostgreSQL Hiperescala (Citus) (versión preliminar) usa un firewall en el nivel de servidor. De manera predeterminada, el firewall impide que todas las herramientas y aplicaciones externas se conecten al nodo de coordinación y a cualquier base de datos interna. Tenemos que agregar una regla para abrir el firewall en un intervalo específico de direcciones IP.

1. Desde la sección **Salidas** donde anteriormente copió el nombre de host del nodo de coordinación, haga clic en Atrás para volver al elemento de menú **Información general**.

2. Busque el nombre del grupo de servidores de la implementación y haga clic en él. (El nombre del grupo de servidores *no* tendrá un sufijo. Los elementos con nombres que terminan en, por ejemplo, "-c", "-w0" o "-w1" no son el grupo de servidores).

3. Haga clic en **Firewall** en **Seguridad**, en el menú de la izquierda.

4. Haga clic en el vínculo **+ Agregar regla de firewall para la dirección IP del cliente actual**.

5. Por último, haga clic en el botón **Guardar**.

   > [!NOTE]
   > El servidor Azure PostgreSQL se comunica a través de puerto 5432. Si intenta conectarse desde una red corporativa, es posible que el firewall de la red no permita el tráfico saliente a través del puerto 5432. En ese caso, no puede conectarse al servidor de Azure SQL Database, salvo que el departamento de TI abra el puerto 5432.
   >

## <a name="connect-to-the-database-using-psql"></a>Conexión a la base de datos mediante psql

Al crear el servidor de Azure Database for PostgreSQL, también se crea la base de datos predeterminada denominada **citus**. Para conectarse al servidor de bases de datos, necesita una cadena de conexión y la contraseña de administrador.

1. Obtenga la cadena de conexión. En la página del grupo de servidores, haga clic en el elemento de menú **Cadenas de conexión**. (Se encuentra en **Configuración**). Busque la cadena marcada como **C++ (libpq)** . Tendrá el formato:

   ```
   host=hostname.postgres.database.azure.com port=5432 dbname=citus user=citus password={your_password} sslmode=require
   ```

   Copie la cadena. Deberá reemplazar "{su\_contraseña}" por la contraseña administrativa que eligió anteriormente. El sistema no almacena la contraseña de texto no cifrado y, por lo tanto, no puede mostrarla en la cadena de conexión.

2. Abra una ventana del terminal en el equipo local.

3. En el símbolo de sistema, conéctese al servidor de Azure Database for PostgreSQL con la utilidad [psql](https://www.postgresql.org/docs/current/app-psql.html). Pase la cadena de conexión entre comillas, asegurándose de que contiene la contraseña:
   ```bash
   psql "{connection_string}"
   ```

   Por ejemplo, el siguiente comando se conecta al nodo de coordinación del grupo de servidores **mydemoserver**:

   ```bash
   psql "host=mydemoserver-c.postgres.database.azure.com port=5432 dbname=citus user=citus password={your_password} sslmode=require"
   ```
