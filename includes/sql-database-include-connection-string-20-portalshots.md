---
title: Obtenga la cadena de conexión del portal de Azure
description: Obtenga la cadena de conexión del portal de Azure
keywords: conexión sql, cadena de conexión
services: sql-database
author: dalechen
manager: craigg
ms.service: sql-database
ms.custom: develop apps
ms.topic: include
ms.date: 07/13/2018
ms.author: ninarn
ms.openlocfilehash: 6ead2e0ea326b5c3f2e76e7aa9cc4ab3c50d4154
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/18/2019
ms.locfileid: "67186395"
---
### <a name="obtain-the-connection-string-from-the-azure-portal"></a>Obtenga la cadena de conexión del portal de Azure
Utilice [Azure Portal](https://portal.azure.com/) para obtener la cadena de conexión necesaria para que su programa cliente interactúe con Azure SQL Database.

1. Seleccione **Todos los servicios** > **Bases de datos SQL**.

2. Escriba el nombre de la base de datos en el cuadro de texto de filtro situado en la esquina superior izquierda de la hoja **Bases de datos SQL**.

3. Seleccione la fila correspondiente a la base de datos.

4. Cuando aparezca la hoja de su base de datos, para una mayor comodidad visual puede seleccionar los botones **Minimizar** para contraer las hojas que utilizó para examinar y filtrar la base de datos.

5. En la hoja de la base de datos, seleccione **Mostrar cadenas de conexión de la base de datos**.

6. Copie la cadena de conexión adecuada. Por ejemplo, si piensa usar la biblioteca de conexiones de ADO.NET, copie la cadena adecuada de la pestaña **ADO.NET**.

    ![Copie la cadena de conexión ADO correspondiente a la base de datos][20-CopyAdoConnectionString]

7. Edite la cadena de conexión según sea necesario. Por ejemplo, inserte su contraseña en la cadena de conexión o quite "@&lt;nombre del servidor&gt;" del nombre de usuario si el nombre de servidor o el nombre de usuario es demasiado largo.

8. En un formato u otro, pegue la información de la cadena de conexión en el código del programa cliente.

Para obtener más información, consulte [Cadenas de conexión y archivos de configuración](https://msdn.microsoft.com/library/ms254494.aspx).

<!-- Image references. -->



[20-CopyAdoConnectionString]: ./media/sql-database-include-connection-string-20-portalshots/connqry-connstr-b.png


<!--
These three includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-connection-string-20-portalshots.md
includes/sql-database-include-connection-string-30-compare.md
includes/sql-database-include-connection-string-40-config.md
-->
