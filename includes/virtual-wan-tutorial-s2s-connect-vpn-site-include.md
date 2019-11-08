---
title: archivo de inclusión
description: archivo de inclusión
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 11/04/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: bcc4af837cc28a18345e49cd39ddf88d24e44dd8
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2019
ms.locfileid: "73488848"
---
1. Seleccione **Conectar sitios de VPN** para abrir la página **Conectar sitios**.

    ![conectarse](./media/virtual-wan-tutorial-connect-vpn-site-include/connect.png "conectar")

   Rellene los siguientes campos:

   * Escriba una clave previamente compartida. Si no especifica una clave, Azure generará una automáticamente.
   * Seleccione el protocolo y la configuración de IPsec. Consulte los detalles de IPSec predeterminados o personalizados (put the link to the page)
   * Seleccione la opción adecuada para **Propagar la ruta predeterminada**. La opción **Habilitar** permite que el centro de conectividad propague una ruta predeterminada aprendida a esta conexión. Esta marca habilita la propagación de la ruta predeterminada a una conexión, solamente si el centro de Virtual WAN ya ha aprendido la ruta predeterminada como resultado de la implementación de un firewall en el centro, o en caso de que otro sitio conectado tenga habilitada la tunelización forzada. La ruta predeterminada no se origina en el centro de conectividad de Virtual WAN.

2. Seleccione **Conectar**.
3. En unos minutos, el sitio mostrará la conexión y el estado de conectividad.

   ![status](./media/virtual-wan-tutorial-connect-vpn-site-include/status.png "status")

   **Estado de la conexión:** Este es el estado del recurso de Azure para la conexión que conecta el sitio VPN a la puerta de enlace de VPN del centro de conectividad de Azure. Una vez que esta operación de plano de control se realiza correctamente, la puerta de enlace de VPN de Azure y el dispositivo VPN local procederán a establecer la conectividad.

   **Estado de conectividad:** Este es el estado de conectividad real (ruta de acceso a datos) entre la puerta de enlace de VPN de Azure en el centro de conectividad y el sitio VPN. Puede mostrar cualquiera de los siguientes estados:

    * **Desconocido**: Este estado suele observarse si los sistemas de back-end están trabajando en una transición a otro estado.
    * **Conectando**: La puerta de enlace de VPN de Azure está intentando ponerse en contacto con el sitio VPN local.
    * **Conectado**: Se ha establecido la conectividad entre la puerta de enlace de VPN de Azure y el sitio VPN local.
    * **Desconectado**: Este estado aparece si, por cualquier motivo (local o en Azure), se desconectó la conexión.
4. Dentro de un sitio VPN de centro de conectividad también puede hacer lo siguiente: 

   * Editar o eliminar la conexión VPN.
   * Eliminar el sitio en Azure Portal.
   * Descargar una configuración específica de la rama para más información acerca del lado de Azure mediante el menú contextual (...) situado junto al sitio. Si desea descargar la configuración de todos los sitios conectados en el centro de conectividad, seleccione **Descargar la configuración de VPN** en el menú superior.