---
ms.openlocfilehash: d001d76bca5b9a0837349b6e05b3b0a271ea3a73
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "62128529"
---
> [!NOTE]
> Puede utilizar Azure DNS para configurar un nombre DNS personalizado para Azure Web Apps. Para más información, consulte [Usar Azure DNS para proporcionar la configuración de un dominio personalizado para un servicio de Azure](../articles/dns/dns-custom-domain.md#app-service-web-apps).
>
>

Inicie sesión en el sitio web de su proveedor de dominios.

Busque la página de administración de registros DNS. Cada proveedor de dominios tiene su propia interfaz de registros DNS, así que consulte la documentación del proveedor. Busque áreas del sitio etiquetadas como **Nombre de dominio**, **DNS** o **Administración del servidor del nombres**. 

A menudo, se puede encontrar la página de registros DNS al ver la información de la cuenta y al buscar un vínculo, como **Mis dominios**. Vaya a dicha página y, después, busque un vínculo que se llame algo así como **Zone file**, **DNS Records** o **Advanced configuration**.

La captura de pantalla siguiente es un ejemplo de página de registros DNS:

![Página de registros DNS de ejemplo](./media/app-service-web-access-dns-records-no-h/example-record-ui.png)

En la captura de pantalla de ejemplo, seleccione **Agregar** para crear un registro. Algunos proveedores tienen diferentes vínculos para agregar diferentes tipos de registros. De nuevo, consulte la documentación del proveedor.

> [!NOTE]
> En el caso de ciertos proveedores, como GoDaddy, los cambios en los registros DNS no entran en vigor hasta que se selecciona un vínculo **Guardar cambios** independiente. 
