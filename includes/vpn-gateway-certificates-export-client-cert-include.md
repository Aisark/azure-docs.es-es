---
title: archivo de inclusión
description: archivo de inclusión
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: b657d54c3ebbe5afc20fc98c1348bb783410df60
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "60764014"
---
Al generar un certificado de cliente, se instala automáticamente en el equipo que usó para generarlo. Si desea instalar el certificado de cliente en otro equipo cliente, debe exportar el certificado de cliente que ha generado.

1. Para exportar un certificado de cliente, abra **Administrar certificados de usuario**. De forma predeterminada, los certificados de cliente que ha generado se encuentran en "Certificates - Current User\Personal\Certificates". Haga clic con el botón derecho en el certificado de cliente que desee exportar y haga clic en **Todas las tareas** y en **Exportar** para abrir el **Asistente para exportar certificados**.

   ![Exportación](./media/vpn-gateway-certificates-export-client-cert-include/export.png)
2. En Asistente para exportar certificados, haga clic en **Siguiente** para continuar.

   ![Pasos siguientes](./media/vpn-gateway-certificates-export-client-cert-include/next.png)
3. Seleccione **Exportar la clave privada** y, después, haga clic en **Siguiente**.

   ![exportar clave privada](./media/vpn-gateway-certificates-export-client-cert-include/privatekeyexport.png)
4. En la página **Formato de archivo de exportación**, deje seleccionados los valores predeterminados. Asegúrese de que **Incluir todos los certificados en la ruta de certificación si es posible** esté seleccionada. Esta opción también exporta la información del certificado raíz que se requiere para una autenticación correcta del cliente. Sin ella, se produce un error de autenticación del cliente porque este no tiene el certificado raíz de confianza. A continuación, haga clic en **Siguiente**.

   ![exportar formato de archivo](./media/vpn-gateway-certificates-export-client-cert-include/includeallcerts.png)
5. En la página **Seguridad** , debe proteger la clave privada. Si decide usar una contraseña, asegúrese de anotarla o de recordar la contraseña que estableció para este certificado. A continuación, haga clic en **Siguiente**.

   ![security](./media/vpn-gateway-certificates-export-client-cert-include/security.png)
6. En **Archivo que se va a exportar**, haga clic en **Examinar** para ir a la ubicación a la que desea exportar el certificado. En **Nombre de archivo**, asígnele un nombre al archivo de certificado. A continuación, haga clic en **Siguiente**.

   ![archivo para la exportación](./media/vpn-gateway-certificates-export-client-cert-include/filetoexport.png)
7. Haga clic en **Finalizar** para exportar el certificado.

   ![finalizar](./media/vpn-gateway-certificates-export-client-cert-include/finish.png)