---
title: Tutorial para solicitar Microsoft Azure Data Box Disk | Microsoft Docs
description: Use este tutorial para aprender a registrarse y solicitar Azure Data Box Disk para importar datos en Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.date: 02/27/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: 67f4eb5383452a81ba288f5fe611242259217951
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/05/2019
ms.locfileid: "57404905"
---
# <a name="tutorial-order-an-azure-data-box-disk"></a>Tutorial: Solicitud de Azure Data Box Disk

Azure Data Box Disk es una solución en la nube híbrida que permite importar datos locales en Azure de una forma rápida, fácil y confiable. Transfiera los datos a discos de estado sólido (SSD) suministrados por Microsoft y envíe los discos de vuelta. Luego, dichos datos se cargan en Azure.

En este tutorial se describe cómo se puede solicitar Azure Data Box Disk. En este tutorial, obtendrá información sobre lo siguiente:

> [!div class="checklist"]
> * Solicitar Data Box Disk
> * Hacer un seguimiento del pedido
> * Cancelar el pedido

## <a name="prerequisites"></a>Requisitos previos

Antes de implementar, complete los siguientes requisitos previos de configuración para el servicio Data Box y para Data Box Disk.

### <a name="for-service"></a>Para el servicio

Antes de comenzar, asegúrese de que:
- Tiene una cuenta de almacenamiento de Microsoft Azure con credenciales de acceso.
- Asegúrese de que la suscripción que utilice para el servicio Data Box sea de uno de los siguientes tipos:
    - Contrato Enterprise (EA) de Microsoft. Más información acerca de [las suscripciones de EA](https://azure.microsoft.com/pricing/enterprise-agreement/).
    - Proveedor de soluciones en la nube (CSP). Más información acerca del [programa Azure CSP](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview).
- Asegúrese de que tiene acceso de propietario o colaborador a la suscripción para crear un pedido de Data Box.

### <a name="for-device"></a>Para el dispositivo

Antes de comenzar, asegúrese de que:
- Tiene un equipo cliente disponible desde el que puede copiar los datos. El equipo cliente debe:
    - Ejecutar un [sistema operativo admitido](data-box-disk-system-requirements.md#supported-operating-systems-for-clients).
    - Disponer de otro [software necesario](data-box-disk-system-requirements.md#other-required-software-for-windows-clients) instalado si es un cliente de Windows.  

## <a name="order-data-box-disk"></a>Solicitud de Data Box Disk

Para solicitar Data Box Disk, siga estos pasos en [Azure Portal](https://aka.ms/azuredataboxfromdiskdocs).

1. En la esquina superior izquierda del portal, haga clic en **+ Crear un recurso**y busque *Azure Data Box*. Haga clic en **Azure Data Box**.
    
   ![Busque Azure Data Box 1](media/data-box-disk-deploy-ordered/search-data-box11.png)

2. Haga clic en **Create**(Crear).

3. Compruebe si el servicio Data Box está disponible en su región. Escriba o seleccione la siguiente información y haga clic en **Aplicar**.

    ![Seleccione la opción Data Box Disk](media/data-box-disk-deploy-ordered/select-data-box-sku-1.png)

    |Configuración|Valor|
    |---|---|
    |Subscription|Seleccione la suscripción para la que se habilita el servicio Data Box.<br> La suscripción está vinculada a la cuenta de facturación. |
    |Tipo de transferencia| Importar en Azure|
    |País de origen | Seleccione el país en que residen los datos actualmente.|
    |Región de Azure de destino|Seleccione la región de Azure a la que desea transferir los datos.|

  
5.  Seleccione **Data Box Disk**. La capacidad máxima de la solución para un solo pedido de cinco discos es 35 TB. Para tamaños de datos mayores puede crear varios pedidos.

     ![Seleccione la opción Data Box Disk](media/data-box-disk-deploy-ordered/select-data-box-sku-zoom.png)

6.  En **Order** (Pedido), especifique el valor de **Order details** (Detalles del pedido). Escriba o seleccione la siguiente información.

    |Configuración|Valor|
    |---|---|
    |NOMBRE|Especifique un nombre descriptivo para hacer un seguimiento del pedido.<br> El nombre puede tener entre 3 y 24 caracteres que pueden ser letras, números y guiones. <br> El nombre debe empezar y terminar con una letra o un número. |
    |Grupos de recursos| Uso uno existente o cree uno nuevo. <br> Un grupo de recursos es un contenedor lógico para los recursos que se pueden administrar o implementar conjuntamente. |
    |Región de Azure de destino| Seleccione la región de la cuenta de almacenamiento.<br> Actualmente, se admiten cuentas de almacenamiento de todas las regiones de EE.UU., Europa Occidental, Europa del Norte, Canadá y Australia. |
    |Tamaño de datos estimado, en TB| Escriba una estimación, en TB. <br>Según el tamaño de los datos, Microsoft envía un número adecuado de SSD de 8 TB (7 TB de capacidad utilizable). <br>La capacidad máxima utilizable de cinco discos 35 TB. |
    |Clave de paso de disco| Si marca **Usar la clave personalizada en lugar de la clave de paso generada por Azure**, proporcione la clave de paso de disco. <br> Proporcione una clave alfanumérica de entre 12 y 32 caracteres de longitud que tenga al menos un carácter numérico y un carácter especial. Solo se permiten los caracteres especiales `@?_+`. <br> Puede elegir omitir esta opción y usar la clave de paso generada por Azure para desbloquear los discos.|
    |Destino de almacenamiento     | Elija una cuenta de almacenamiento o discos administrados, o ambos. <br> En función de la región de Azure especificada, seleccione una cuenta de almacenamiento en la lista filtrada de una cuenta de almacenamiento existente. Data Box se puede vincular con un máximo de diez cuentas de almacenamiento. <br> También puede crear una **cuenta de uso general v1**, una **cuenta de uso general v2** o una **cuenta de Blob Storage**. <br>No puede utilizar cuentas de almacenamiento que tengan reglas configuradas. Las cuentas de almacenamiento deben **permitir el acceso desde todas las redes** en la sección de firewalls y redes virtuales.|

    Si usa la cuenta de almacenamiento como el destino de almacenamiento, consulte la siguiente captura de pantalla:

    ![Solicitud de Data Box Disk para la cuenta de almacenamiento](media/data-box-disk-deploy-ordered/order-storage-account.png)

    Si usa Data Box Disk para crear discos administrados desde los discos duros virtuales locales, también deberá proporcionar la siguiente información:

    |Configuración  |Valor  |
    |---------|---------|
    |Grupos de recursos     | Cree un grupo de recursos si pretende crear discos administrados desde los discos duros virtuales locales. Use un grupo de recursos existentes solo si se creó para la solicitud de Data Box Disk para discos administrados realizada por el servicio Data Box. <br> Solo se admite un grupo de recursos.|

    ![Solicitud de Data Box Disk para discos administrados](media/data-box-disk-deploy-ordered/order-managed-disks.png)

    La cuenta de almacenamiento especificada para los discos administrados se usa como una cuenta de almacenamiento provisional. El servicio Data Box carga los discos duros virtuales en la cuenta de almacenamiento provisional y, a continuación, los convierte en discos administrados y los mueve a los grupos de recursos. Para más información, vea [Comprobación de la carga de datos en Azure](data-box-disk-deploy-picked-up.md#verify-data-upload-to-azure).

13. Haga clic en **Next**.

    ![Especificar detalles del pedido](media/data-box-disk-deploy-ordered/data-box-order-details.png)

14. En la pestaña **Dirección de envío**, escriba su nombre y apellidos, el nombre y la dirección postal de la empresa y un número de teléfono válido. Haga clic en **Validar la dirección**. El servicio valida la dirección de envío para conocer la disponibilidad del servicio. Si el servicio está disponible para la dirección de envío especificada, recibirá una notificación al respecto. 

    ![Especificar dirección de envío](media/data-box-disk-deploy-ordered/data-box-shipping-address.png)
15. En **Detalles de la notificación**, especifique las direcciones de correo electrónico. El servicio envía notificaciones por correo electrónico si se produce cualquier actualización en el estado del pedido a las direcciones de correo electrónico especificadas. 

    Es aconsejable usar un correo electrónico de grupo, con el fin de que siga recibiendo notificaciones aunque algún administrador deje el grupo.

16. Examine el **resumen** de la información relacionada con el pedido, el contacto, la notificación y los términos de privacidad. Active la casilla correspondiente a contrato acuerdo con los términos de privacidad.

17. Haga clic en **Pedido**. El pedido tarda unos minutos en crearse.

 
## <a name="track-the-order"></a>Seguimiento del pedido

Tras realizar el pedido, puede hacer un seguimiento del estado del mismo desde Azure Portal. Vaya al pedido y, después, vaya a **Información general** para ver el estado. El portal muestra el trabajo en estado **Ordered** (Pedido).

![Estado Pedido de Data Box Disk](media/data-box-disk-deploy-ordered/data-box-portal-ordered.png) 

Si los discos no están disponibles, recibirá una notificación. Si los discos están disponibles, Microsoft los identifica para su envío y prepara el paquete. Durante la preparación de los discos, se producen las siguientes acciones:

- Con los discos se usa el cifrado BitLocker AES-128.  
- Los discos se bloquean para evitar el acceso no autorizado a ellos.
- Durante este proceso se genera la clave de paso que desbloquea los discos.

Una vez que se completa la preparación del disco, el portal muestra el pedido en estado **Processed** (Procesado).

Luego, Microsoft prepara y envía los discos a través de un operador regional. Tras su envío recibirá un número de seguimiento. El portal muestra el orden en estado **Dispatched** (Enviado).

## <a name="cancel-the-order"></a>Cancelar el pedido

Para cancelar el pedido, en Azure Portal, vaya a **Información general** y haga clic en **Cancelar** en la barra de comandos.

La cancelación sólo se puede realizar una vez que se han pedido los discos y el pedido se está procesando para el envío. Una vez que el pedido se procese, no se podrá cancelar.

![Cancelar un pedido](media/data-box-disk-deploy-ordered/cancel-order1.png)

Para eliminar un pedido cancelado, vaya a **Información general** y haga clic en **Eliminar** desde la barra de comandos.


## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha obtenido información acerca de varios temas relacionados con Azure Data Box, como:

> [!div class="checklist"]
> * Solicitud de Data Box Disk
> * Seguimiento del pedido
> * Cancelar el pedido

En el siguiente tutorial aprenderá a configurar Data Box Disk.

> [!div class="nextstepaction"]
> [Configuración de Azure Data Box Disk](./data-box-disk-deploy-set-up.md)
