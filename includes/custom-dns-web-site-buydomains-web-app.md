---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/09/2018
ms.author: cephalin
ms.openlocfilehash: ce949caa2b80c08f1015ee21c00197d6a95103c2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "60531096"
---
Si desea un dominio, puede comprarlo directamente en el [Portal de administración de Azure](https://portal.azure.com). Siga los pasos siguientes para adquirir nombres de dominio y asignarlos a su aplicación web.

1. En el explorador, abra el [Portal de administración de Azure](https://portal.azure.com).
2. En la pestaña **Web Apps**, haga clic en el nombre de la aplicación web, seleccione **Configuración** y, luego, seleccione **Dominios personalizados y SSL**
   
    ![](./media/custom-dns-web-site/dncmntask-cname-6.png)
3. En la hoja **Dominios personalizados y SSL**, haga clic en **Comprar dominios**.
   
    ![](./media/custom-dns-web-site/dncmntask-cname-buydomains-1.png)
4. En la hoja **Comprar dominios**, use el cuadro de texto para escribir el nombre de dominio que desea comprar. Los dominios disponibles sugeridos se mostrarán justo debajo del cuadro de texto. Seleccione el dominio que quiere comprar.
   
   ![](./media/custom-dns-web-site/dncmntask-cname-buydomains-2.png)
5. Haga clic en **Información de contacto** y rellene el formulario de información de contacto del dominio.
   
   ![](./media/custom-dns-web-site/dncmntask-cname-buydomains-3.png)
6. Haga clic en la hoja **Seleccionar** en **Comprar dominios** y verá la información de compra en la hoja **Confirmación de compra**. Si acepta los términos legales y hace clic en **Comprar**, se enviará su pedido y podrá supervisar el proceso de compra en **Notificación**.
   
   ![](./media/custom-dns-web-site/dncmntask-cname-buydomains-4.png)
   
   ![](./media/custom-dns-web-site/dncmntask-cname-buydomains-5.png)
7. Si ha solicitado correctamente un dominio, puede administrar el dominio y asignarlo a la aplicación web. Haga clic en **"..."** en el lado derecho de su dominio. Puede **Cancelar compra** o **Administrar dominio**. Haga clic en **Administrar dominio** para que podamos enlazar el **subdominio** a nuestra aplicación web en la hoja **Administrar dominio**.
   
    ![](./media/custom-dns-web-site/dncmntask-cname-buydomains-6.png)
   
    Una vez completada la configuración, el nombre de dominio personalizado aparecerá en la sección **Enlaces de nombres de host** de la aplicación web.

En este punto, debería poder escribir el nombre de dominio personalizado en el explorador y ver que le lleva sin problemas a la aplicación web.

