---
title: Configurar y acceder a los registros del servidor para Azure Database for PostgreSQL con un único servidor en Azure Portal
description: En este artículo se describe cómo configurar los registros de servidor de Azure Database for PostgreSQL con un único servidor, y acceder a ellos, mediante Azure Portal.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 13a8c1ee4b7ca114211f93245d74866e5aa5d52c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "65067448"
---
# <a name="configure-and-access-azure-database-for-postgresql---single-server-logs-in-the-azure-portal"></a>Configurar y acceder a los registros de Azure Database for PostgreSQL con un único servidor en Azure Portal

Puede configurar, enumerar y descargar los [registros de Azure Database for PostgreSQL](concepts-server-logs.md) de Azure Portal.

## <a name="prerequisites"></a>Requisitos previos
Para seguir esta guía, necesitará:
- [Un servidor de Azure Database for PostgreSQL](quickstart-create-server-database-portal.md)

## <a name="configure-logging"></a>registro
Configure el acceso a los registros de consulta y los registros de errores. 

1. Inicie sesión en el [Azure Portal](https://portal.azure.com/).

2. Seleccione su servidor de Azure Database for PostgreSQL.

3. En la sección **Supervisión** de la barra lateral, seleccione **Registros de servidor**. 

   ![Seleccione Registros de servidor y después "Haga clic aquí para habilitar..."](./media/howto-configure-server-logs-in-portal/1-select-server-logs-configure.png)

4. Seleccione el título **Haga clic aquí para habilitar los registros y configurar los parámetros** para ver los parámetros del servidor.

5. Cambie los parámetros que necesita ajustar. Todos los cambios que realice en esta sesión se resaltan en color púrpura.

   Cuando haya cambiado los parámetros, haga clic en **Guardar**. O bien puede **descartar** los cambios. 

   ![Lista exhaustiva de parámetros con los cambios que se van a guardar o descartar](./media/howto-configure-server-logs-in-portal/3-save-discard.png)

6. Para volver a la lista de registros, haga clic en el **botón Cerrar** (icono de X) en la página **Parámetros de servidor**.

## <a name="view-list-and-download-logs"></a>Visualización de lista y descarga de registros
Una vez que comience el registro, puede ver una lista de registros disponibles y descargar archivos de registro individuales en el panel Registros de servidor. 

1. Abra Azure Portal.

2. Seleccione su servidor de Azure Database for PostgreSQL.

3. En la sección **Supervisión** de la barra lateral, seleccione **Registros de servidor**. En la página se muestra una lista de los archivos de registro, de esta forma:

   ![Lista de registros del servidor](./media/howto-configure-server-logs-in-portal/4-server-logs-list.png)

   > [!TIP]
   > La convención de nomenclatura del registro es **postgresql-aaaa-mm-dd_hh0000.log**. La fecha y hora que se utilizan en el nombre de archivo es el momento en que se emitió el registro. Los archivos de registro rotan cada hora o 100 MB de tamaño, lo que ocurra primero.

4. Si es necesario, utilice el **cuadro de búsqueda** para reducir rápidamente a un registro específico en función de la fecha y hora. La búsqueda se encuentra en el nombre del registro.

   ![Ejemplo de búsqueda de los nombres de registro](./media/howto-configure-server-logs-in-portal/5-search.png)

5. Descargue los archivos de registro individuales con el botón **Descargar** (icono de flecha hacia abajo) junto a cada archivo de registro en la fila de tabla, como se muestra:

   ![Haga clic en el icono de descarga](./media/howto-configure-server-logs-in-portal/6-download.png)

## <a name="next-steps"></a>Pasos siguientes
- Consulte [Configure and access server logs using Azure CLI](howto-configure-server-logs-using-cli.md) (Configuración y acceso a los registros del servidor con la CLI de Azure) para más información acerca de cómo descargar registros mediante programación.
- Aprenda más sobre los [registros del servidor](concepts-server-logs.md) en Azure Database for PostgreSQL. 
- Para más información sobre las definiciones de parámetros y el registro de PostgreSQL, consulte la documentación de PostgreSQL sobre [informes y registro de errores](https://www.postgresql.org/docs/current/static/runtime-config-logging.html).

