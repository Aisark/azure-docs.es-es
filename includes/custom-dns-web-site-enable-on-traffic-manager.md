---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/09/2018
ms.author: cephalin
ms.openlocfilehash: 67b9c0ba2566206b0e70db51844b21e5d5d3c261
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/23/2019
ms.locfileid: "66136259"
---
Después de que los registros de su nombre de dominio se han propagado, debería poder utilizar el explorador para comprobar que el nombre de dominio personalizado se puede utilizar para tener acceso a la aplicación web de Servicios de aplicaciones de API.

> [!NOTE]
> El CNAME puede tardar un tiempo en propagarse por el sistema DNS. Puede usar un servicio como <a href="https://www.digwebinterface.com/">https://www.digwebinterface.com/</a> para comprobar que el CNAME está disponible.
> 
> 

Si aún no lo ha agregado a la aplicación web como extremo del Administrador de tráfico, debe hacerlo para que funcione la resolución de nombres, debido a que el nombre de dominio personalizado se enruta a Administrador de tráfico. Posteriormente, el Administrador de tráfico enruta a la aplicación web. Use la información de [Adición o eliminación de extremos](../articles/traffic-manager/traffic-manager-endpoints.md) para agregar su aplicación web como un extremo en el perfil del Administrador de tráfico.

> [!NOTE]
> Si s aplicación web no aparece en la lista al agregar un extremo, compruebe que está configurada para el modo de plan **estándar** de App Service. Si desea trabajar con el Administrador de tráfico, debe utilizar el modo **estándar** para su aplicación web.
> 
> 

1. En el explorador, abra [Azure Portal](https://portal.azure.com).
2. En la pestaña **Web Apps**, haga clic en el nombre de la aplicación web, seleccione **Configuración** y elija **Dominios personalizados**.
   
    ![](./media/custom-dns-web-site/dncmntask-cname-6.png)
3. En la hoja **Dominios personalizados**, haga clic en **Agregar nombre de host**.
4. Utilice los cuadros de texto **Nombre de host** para escribir el nombre de dominio del Administrador de tráfico para realizar la asociación con esta aplicación web.
   
    ![](./media/custom-dns-web-site/dncmntask-cname-8.png)
5. Haga clic en **Validar** para guardar la configuración del nombre de dominio.
6. Al hacer clic en **Validar** , Azure iniciará el flujo de trabajo Verificación de dominio. Este flujo de trabajo comprobará la propiedad del dominio y la disponibilidad del nombre de host, e informará del éxito o del error de forma detallada y con instrucciones para solucionar el error.    
7. Tras una validación correcta, se activará el botón **Agregar nombre de host** y podrá para asignar el nombre del host. Ahora, vaya a su nombre de dominio personalizado en un explorador. Ahora verá la aplicación en ejecución con su nombre de dominio personalizado. 
   
   Una vez completada la configuración, el nombre de dominio personalizado aparecerá en la sección **nombres de dominio** de la aplicación web.

En este punto, debería poder escribir el nombre de dominio de Traffic Manager en el explorador y ver que le lleva sin problemas a la aplicación web.

