---
title: Configuración y acceso a los registros del servidor en Azure Database for PostgreSQL con un único servidor desde Azure Portal
description: En este artículo se describe cómo configurar los registros de servidor de Azure Database for PostgreSQL con un único servidor, y acceder a ellos, mediante Azure Portal.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: c77e708e14d34545754ca38095aedb63ff0172a1
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/03/2019
ms.locfileid: "71841523"
---
# <a name="configure-and-access-azure-database-for-postgresql---single-server-logs-from-the-azure-portal"></a>Configuración y acceso a los registros de Azure Database for PostgreSQL con un único servidor desde Azure Portal

Puede configurar, enumerar y descargar los [registros de Azure Database for PostgreSQL](concepts-server-logs.md) de Azure Portal.

## <a name="prerequisites"></a>Requisitos previos
Los pasos descritos en este artículo requieren que tenga un [servidor de Azure Database for PostgreSQL](quickstart-create-server-database-portal.md).

## <a name="configure-logging"></a>registro
Configure el acceso a los registros de consulta y los registros de errores. 

1. Inicie sesión en el [Azure Portal](https://portal.azure.com/).

2. Seleccione su servidor de Azure Database for PostgreSQL.

3. En la sección **Supervisión** de la barra lateral, seleccione **Registros de servidor**. 

   ![Captura de pantalla de las opciones de registros del servidor](./media/howto-configure-server-logs-in-portal/1-select-server-logs-configure.png)

4. Seleccione **Haga clic aquí para habilitar los registros y configurar los parámetros** para ver los parámetros del servidor.

5. Cambie los parámetros que necesita ajustar. Todos los cambios que realice en esta sesión se resaltan en color púrpura.

   Después de cambiar los parámetros, seleccione **Guardar**. O bien puede descartar los cambios. 

   ![Captura de pantalla de las opciones de los parámetros del servidor](./media/howto-configure-server-logs-in-portal/3-save-discard.png)

Desde la página **Parámetros de servidor**, puede volver a la lista de los registros cerrando la página.

## <a name="view-list-and-download-logs"></a>Visualización de lista y descarga de registros
Una vez que comienza el registro, puede ver una lista de los registros disponibles y descargar archivos de registro individuales. 

1. Abra Azure Portal.

2. Seleccione su servidor de Azure Database for PostgreSQL.

3. En la sección **Supervisión** de la barra lateral, seleccione **Registros de servidor**. La página muestra una lista de los archivos de registro.

   ![Captura de pantalla de la página Registros de servidor con una lista de registros resaltados](./media/howto-configure-server-logs-in-portal/4-server-logs-list.png)

   > [!TIP]
   > La convención de nomenclatura del registro es **postgresql-aaaa-mm-dd_hh0000.log**. La fecha y hora que se utilizan en el nombre de archivo indican el momento en que se emitió el registro. Los archivos de registro rotan cada hora o 100 MB, lo que ocurra primero.

4. Si es necesario, utilice el cuadro de búsqueda para encontrar rápidamente un registro específico en función de la fecha y hora. La búsqueda se encuentra en el nombre del registro.

   ![Captura de pantalla de la página Registros de servidor, con el cuadro de búsqueda y los resultados resaltados](./media/howto-configure-server-logs-in-portal/5-search.png)

5. Para descargar los archivos de registro individuales, seleccione el icono de flecha hacia abajo que hay junto a cada archivo de registro en la fila de tabla.

   ![Captura de pantalla de la página Registros de servidor, con el icono de flecha hacia abajo resaltado](./media/howto-configure-server-logs-in-portal/6-download.png)

## <a name="next-steps"></a>Pasos siguientes
- Consulte [Access server logs in CLI](howto-configure-server-logs-using-cli.md) (Acceso a los registros de servidor mediante la CLI) para más información acerca de cómo descargar registros mediante programación.
- Obtenga más información sobre los [registros de servidor](concepts-server-logs.md) en Azure Database for PostgreSQL. 
- Para más información sobre las definiciones de parámetros y el registro de PostgreSQL, consulte la documentación de PostgreSQL sobre [informes y registro de errores](https://www.postgresql.org/docs/current/static/runtime-config-logging.html).

