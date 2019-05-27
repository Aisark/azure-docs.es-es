---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: e087a1db008422aeec8fd4e073a7476eebe4d54b
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/23/2019
ms.locfileid: "66140617"
---
1. Inicie sesión en [Azure Portal].
2. Seleccione **+NUEVO** > **Web y móvil** > **Aplicación móvil** y, después, proporcione un nombre para el back-end de Mobile Apps.
3. En **Grupo de recursos**, seleccione un grupo de recursos existente o cree uno nuevo (con el mismo nombre que su aplicación). 
4. Para **Plan de App Service**, se selecciona el plan predeterminado (en el [plan Estándar](https://azure.microsoft.com/pricing/details/app-service/)). También puede seleccionar otro plan o [crear uno](../articles/app-service/app-service-plan-manage.md#create-an-app-service-plan). 

   La configuración del plan de App Service determina la [ubicación, las características, el costo y los recursos de proceso](https://azure.microsoft.com/pricing/details/app-service/) asociados con la aplicación. Para más información acerca de los planes de App Service y cómo crear un nuevo plan en un plan de tarifa diferente en la ubicación deseada, consulte [Introducción detallada a los planes de Azure App Service](../articles/app-service/overview-hosting-plans.md).
   
5. Seleccione **Crear**. Este paso crea el back-end de Mobile Apps. 
6. En el panel **Configuración** del nuevo back-end de Mobile Apps, seleccione **Inicio rápido** > la plataforma de aplicaciones cliente > **Conectar a una base de datos**. 
   
   ![Selecciones para conectarse a una base de datos](./media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-data-connection.png)
7. En el panel **Agregar la conexión de datos**, haga clic en **SQL Database** > **Crear una base de datos nueva**. Escriba el nombre de la base de datos, elija un plan de tarifa y, después, seleccione **Servidor**. Puede reutilizar esta nueva base de datos. Si ya tiene una base de datos en la misma ubicación, puede elegir **Usar base de datos existente**. No se recomienda el uso de una base de datos en una ubicación diferente debido a los costos de ancho de banda y a una mayor latencia.
   
   ![Selección de una base de datos](./media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-db.png)
8. En el panel **Nuevo servidor**, escriba un nombre de servidor único en el cuadro **Nombre del servidor**, proporcione un inicio de sesión y una contraseña, active **Permitir que los servicios de Azure accedan al servidor** y seleccione **Aceptar**. Este paso creará la nueva base de datos.
9. De nuevo en el panel **Agregar conexión de datos**, seleccione **Cadena de conexión**, escriba los valores de inicio de sesión y contraseña para la base de datos y seleccione **Aceptar**. 

   Espere unos minutos para que se implemente la base de datos correctamente antes de continuar.

<!-- URLs. -->
[Azure Portal]: https://portal.azure.com/
