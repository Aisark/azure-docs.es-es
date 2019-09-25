---
title: 'Configuración de un servicio QnA Maker: QnA Maker'
titleSuffix: Azure Cognitive Services
description: Antes de crear alguna base de conocimiento de QnA Maker, primero debe configurar un servicio QnA Maker en Azure. Cualquiera que tenga autorización para crear recursos en una suscripción puede configurar un servicio QnA Maker.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: a2b467eed010edbb842d536bd8f6e3f4107fcea8
ms.sourcegitcommit: 909ca340773b7b6db87d3fb60d1978136d2a96b0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2019
ms.locfileid: "70984358"
---
# <a name="manage-qna-maker-resources"></a>Administración de recursos de QnA Maker

Antes de crear alguna base de conocimiento de QnA Maker, primero debe configurar un servicio QnA Maker en Azure. Cualquiera que tenga autorización para crear recursos en una suscripción puede configurar un servicio QnA Maker.

## <a name="types-of-keys-in-qna-maker"></a>Tipos de claves de QnA Maker

En el servicio QnA Maker se usan dos tipos de claves: **claves de suscripción** y **claves de punto de conexión**.

![Administración de claves](../media/qnamaker-how-to-key-management/key-management.png)

|NOMBRE|Location|Propósito|
|--|--|--|
|Subscription key|[Azure Portal](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)|estas claves se usan para acceder a las [API del servicio de administración de QnA Maker](https://go.microsoft.com/fwlink/?linkid=2092179). Estas API permiten editar las preguntas y respuestas de una base de conocimiento y publicar una base de conocimiento. Estas claves se crean al crear al mismo tiempo que los servicios QnA Maker.<br><br>Busque estas claves en el recurso **Cognitive Services** de la página **Claves**.|
|Clave de punto de conexión|[Portal de QnA Maker](http://www.qnamaker.ai)|Estas claves se usan para acceder al punto de conexión de la base de conocimiento publicado para obtener una respuesta para una pregunta de un usuario. Este punto de conexión normalmente se usa en un bot de chat o el código de la aplicación cliente que se conecta el servicio QnA Maker. Estas claves se crean al publicar una base de conocimiento de QnA Maker.<br><br>Busque estas claves en la página **Configuración del servicio**. Busque esta página en el menú del usuario de la parte superior derecha de la página, en el menú desplegable.|

## <a name="create-a-new-qna-maker-service"></a>Creación de un servicio QnA Maker

En este procedimiento se crean los recursos de Azure necesarios para administrar el contenido de la base de conocimiento. Después de completar estos pasos, encontrará las claves de _suscripción_ en la página **Claves** del recurso en Azure Portal.

1. Inicie sesión en Azure Portal y [cree un recurso de QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker).

1. Después de leer los términos y condiciones, seleccione **Crear**:

    ![Creación de un servicio QnA Maker](../media/qnamaker-how-to-setup-service/create-new-resource-button.png)

1. En **QnA Maker**, seleccione las regiones y los niveles apropiados:

    ![Creación de un nuevo servicio QnA Maker: plan de tarifa y regiones](../media/qnamaker-how-to-setup-service/enter-qnamaker-info.png)

    * En el campo **Nombre**, escriba un nombre único que identifique este servicio QnA Maker. Este nombre también identifica el punto de conexión de QnA Maker con el que se asociarán las bases de conocimiento.
    * En **Suscripción**, elija la suscripción en la que se implementará el recurso de QnA Maker.
    * Seleccione el **plan de tarifa** para los servicios de administración de QnA Maker (API de portal y administración). Vea [más detalles sobre los precios de las SKU](https://aka.ms/qnamaker-pricing).
    * Cree un **grupo de recursos** (se recomienda) para implementar este recurso de QnA Maker, o use uno existente. QnA Maker crea varios recursos de Azure. Cuando se crea un grupo de recursos que contenga estos recursos, puede encontrar, administrar y eliminar fácilmente estos recursos por su nombre.
    * Seleccione una **ubicación del grupo de recursos**.
    * Elija el **Plan de tarifa de búsqueda** del servicio Azure Search. Si la opción de nivel Gratis no está disponible (aparece atenuada), significa que ya tiene un nivel Gratis de Azure Search implementado a través de la suscripción. En ese caso, necesitará comenzar con el nivel Básico de Azure Search. Consulte los [detalles de precios de Azure Search](https://azure.microsoft.com/pricing/details/search/).
    * Elija la **ubicación de búsqueda** en la que desea que se implementen los datos de Azure Search. Las restricciones sobre dónde deben almacenarse los datos de los clientes ayudarán a determinar la ubicación elegida para Azure Search.
    * En el campo **Nombre de aplicación**, escriba el nombre de la instancia de Azure App Service.
    * De forma predeterminada, el nivel predeterminado de App Service es Estándar (S1). Puede cambiar el plan después de la creación. Más información sobre los [precios de App Service](https://azure.microsoft.com/pricing/details/app-service/).
    * Elija la **ubicación de sitio web** en la que se implementará App Service.

        > [!NOTE]
        > La **ubicación de la búsqueda** puede ser diferente de la **ubicación del sitio web**.

    * Elija si desea habilitar **Application Insights**. Si **Application Insights** está habilitado, QnA Maker recopila telemetría de tráfico, registros de chat y errores.
    * Elija la **ubicación de App Insights** en la que se implementará el recurso de Application Insights.
    * Como medidas de ahorro de los costos, puede [compartir](#share-existing-services-with-qna-maker) algunos pero no todos los recursos de Azure creados para QnA Maker. 

1. Una vez que se hayan validado todos los campos, seleccione **Crear**. El proceso puede tardar unos minutos en completarse.

1. Después de que se complete la implementación, verá que los siguientes recursos se crean en la suscripción:

   ![Recurso creado de un nuevo servicio QnA Maker](../media/qnamaker-how-to-setup-service/resources-created.png)

    El recurso con el tipo _Cognitive Services_ tiene las claves de la _suscripción_.

## <a name="find-subscription-keys-in-the-azure-portal"></a>Búsqueda de claves de suscripción en Azure Portal

Puede ver y restablecer las claves de suscripción desde Azure Portal, donde creó el recurso de QnA Maker.

1. Vaya al recurso de QnA Maker en Azure Portal y seleccione el recurso que tiene el tipo _Cognitive Services_:

    ![Lista de recursos de QnA Maker](../media/qnamaker-how-to-key-management/qnamaker-resource-list.png)

2. Vaya a **Claves**:

    ![Subscription key](../media/qnamaker-how-to-key-management/subscription-key.PNG)

## <a name="find-endpoint-keys-in-the-qna-maker-portal"></a>Búsqueda de claves de punto de conexión en el portal de QnA Maker

El punto de conexión está en la misma región que el recurso porque las claves del punto de conexión se utilizan para efectuar una llamada a la base de conocimiento.

Las claves de punto de conexión se pueden administrar desde el [portal de QnA Maker](https://qnamaker.ai).

1. Inicie sesión en el [portal de QnA Maker](https://qnamaker.ai), vaya a su perfil y seleccione **Service settings** (Configuración del servicio):

    ![Clave de punto de conexión](../media/qnamaker-how-to-key-management/Endpoint-keys.png)

2. Vea o restablezca las claves:

    ![administrador de claves de punto de conexión](../media/qnamaker-how-to-key-management/Endpoint-keys1.png)

    >[!NOTE]
    >Actualice las claves si cree que han estado en peligro. Esto puede requerir realizar los cambios correspondientes en el código del bot o de la aplicación cliente.

## <a name="share-existing-services-with-qna-maker"></a>Uso compartido de los servicios existentes con QnA Maker

QnA Maker crea varios recursos de Azure. Para simplificar la administración y beneficiarse del uso compartido de los costos, use la siguiente tabla para saber lo que puede y no puede compartir:

|Servicio|Compartir|Motivo|
|--|--|--|
|Cognitive Services|X|No es posible por diseño|
|Plan de App Service|✔|Espacio en disco fijo asignado para un plan de App Service. Si otras aplicaciones que comparten el mismo plan de App Service usan un espacio en disco significativo, la instancia de App Service en QnAMaker tendrá problemas.|
|App Service|X|No es posible por diseño|
|Application Insights|✔|Se puede compartir|
|Servicio de búsqueda|✔|1. `testkb` es un nombre reservado para el servicio QnAMaker; no lo pueden usar otros.<br>2. La asignación de sinónimo por el nombre `synonym-map` está reservada para el servicio QnAMaker.<br>3. El número de bases de conocimiento publicadas está limitado por el nivel del servicio Search. Si hay índices libres disponibles, otros servicios pueden utilizarlos.|

Más información sobre [App Service](../../../app-service/index.yml) y el [servicio Azure Search](../../../search/index.yml).

### <a name="using-a-single-search-service"></a>Uso de un servicio Search individual

Si crea un servicio QnA y sus dependencias (como, Search) en el portal, se creará el servicio Search de inmediato y se vinculará al servicio QnA Maker. Después de crear estos recursos puede actualizar el valor de App Service para usar un servicio Search existente y eliminar el que acaba de crear.

Si crea un servicio QnA mediante plantillas de Azure Resource Manager, puede crear todos los recursos y controlar la creación de App Service para usar un servicio Search existente.

## <a name="upgrade-qna-maker"></a>Actualización de QnA Maker

|Actualizar|Motivo|
|--|--|
|[Actualización](#upgrade-qna-maker-sku) de la SKU de administración de QnA Maker|Desea tener más preguntas y respuestas en la base de conocimiento.|
|[Actualización](#upgrade-app-service) de la SKU de App Service|Su base de conocimiento debe atender más solicitudes de la aplicación cliente, como un bot de chat.|
|[Actualización](#upgrade-the-azure-search-service) del servicio Azure Search|Planea tener muchas bases de conocimiento.|


### <a name="upgrade-qna-maker-sku"></a>Actualización de la SKU de QnA Maker

Cuando desee tener más preguntas y respuestas en su base de conocimiento, más allá de su plan actual, actualice el plan de tarifa del servicio de QnA Maker.

Para actualizar la SKU de administración de QnA Maker:

1. Vaya al recurso QnA Maker en Azure Portal y seleccione **Plan de tarifa**.

    ![Recurso QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource.png)

1. Elija la SKU adecuada y presione **Seleccionar**.

    ![Precios de QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-pricing-page.png)

### <a name="upgrade-app-service"></a>Actualización de App Service

 Cuando la base de conocimiento necesite atender más solicitudes de la aplicación cliente, actualice el plan de tarifa de App Service.

Puede [escalar verticalmente](https://docs.microsoft.com/azure/app-service/manage-scale-up) o escalar horizontalmente App Service.

Vaya al recurso App Service en Azure Portal y seleccione la opción **Escalar verticalmente** o **Escalar horizontalmente** según proceda.

![Escalado de App Service en QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-scale.png)

### <a name="upgrade-the-azure-search-service"></a>Actualización del servicio Azure Search

Si planea tener muchas bases de conocimiento, actualice el plan de tarifa del servicio Azure Search.

Actualmente, no puede realizar una actualización local de la SKU de Azure Search. Sin embargo, puede crear un recurso de Azure Search con la SKU deseada, restaurar los datos al nuevo recurso y luego vincularlo a la pila de QnA Maker. Para ello, siga estos pasos.

1. Cree un recurso de Azure Search en Azure Portal y elija la SKU que desee.

    ![Recurso Azure Search en QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-new.png)

1. Restaure los índices a partir del recurso Azure Search original en uno nuevo. Vea el [código de ejemplo de restauración de copia de seguridad](https://github.com/pchoudhari/QnAMakerBackupRestore).

1. Después de que se hayan restaurado los datos, vaya al nuevo recurso de Azure Search, seleccione **Keys** (Claves) y anote el **nombre** y la **clave de administrador**:

    ![Claves de Azure Search en QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-keys.png)

1. Para vincular el nuevo recurso de Azure Search a la pila de QnA Maker, vaya a la instancia de App Service en QnA Maker.

    ![Instancia de App Service en QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource-list-appservice.png)

1. Seleccione **Configuración de la aplicación**  y modifique la configuración en los campos **AzureSearchName** y **AzureSearchAdminKey** del paso 3.

    ![Valor de App Service en QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-settings.png)

1. Reinicie la instancia de App Service.

    ![Reinicie la instancia de App Service en QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="get-the-latest-runtime-updates"></a>Obtención de las últimas actualizaciones del runtime

El runtime de QnAMaker forma parte de la instancia de Azure App Service que se implementa al [crear un servicio QnAMaker](./set-up-qnamaker-service-azure.md) en Azure Portal. El sistema de tiempo de ejecución se actualiza periódicamente. La instancia de App Service en QnA Maker está en modo de actualización automática tras la versión de extensión de sitio de abril de 2019 (versión 5+). Esta actualización está diseñada para administrar el tiempo de inactividad CERO durante las actualizaciones.

Puede consultar su versión actual en https://www.qnamaker.ai/UserSettings. Si su versión es anterior a la 5.x, debe reiniciar App Service para aplicar las actualizaciones más recientes:

1. Vaya a su servicio QnAMaker (grupo de recursos) en [Azure Portal](https://portal.azure.com).

    ![Grupo de recursos de QnAMaker en Azure](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. Seleccione la instancia de App Service y abra la sección **Información general**.

    ![Instancia de App Service en QnAMaker](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

1. Reinicie App Service. El proceso de actualización debería finalizar en un par de segundos. Todas las aplicaciones o bots dependientes que usen este servicio QnAMaker dejarán de estar disponibles para los usuarios finales durante el periodo de reinicio.

    ![Reinicie la instancia de App Service en QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="management-service-region"></a>Región del servicio de administración

El servicio de administración de QnA Maker solo se usa para el portal de QnA Maker y para el procesamiento de datos inicial. Este servicio solo está disponible en la región Oeste de EE. UU. En este servicio de Oeste de EE. UU., no se almacena ningún dato de cliente.

## <a name="next-steps"></a>Pasos siguientes

Más información sobre [App Service](../../../app-service/index.yml) y el [servicio Azure Search](../../../search/index.yml).

> [!div class="nextstepaction"]
> [Crear y publicar una base de conocimiento](../Quickstarts/create-publish-knowledge-base.md)
