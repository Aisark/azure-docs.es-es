---
title: Advanced Threat Protection para Azure Cosmos DB
description: Obtenga información acerca de cómo Azure Cosmos DB proporciona cifrado de datos en reposo y cómo se implementa.
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/21/2019
ms.custom: seodec18
ms.author: memildin
author: memildin
manager: rkarlin
ms.openlocfilehash: c816c9877a9c796ee76310f2452f3505531c3018
ms.sourcegitcommit: 428fded8754fa58f20908487a81e2f278f75b5d0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/27/2019
ms.locfileid: "74555024"
---
# <a name="advanced-threat-protection-for-azure-cosmos-db"></a>Advanced Threat Protection para Azure Cosmos DB

Advanced Threat Protection para Azure Cosmos DB proporciona un nivel adicional de inteligencia de seguridad que detecta intentos poco habituales y potencialmente peligrosos de acceder a las cuentas de Azure Cosmos DB o vulnerarlas. Esta capa de protección le permite afrontar las amenazas, incluso sin necesidad de ser un experto en seguridad, e integrarlas con sistemas centrales de supervisión de seguridad.

Las alertas de seguridad se desencadenan cuando se producen anomalías en una actividad. Estas alertas de seguridad se integran en [Azure Security Center](https://azure.microsoft.com/services/security-center/) y también se envían por correo electrónico a los administradores de las suscripciones, con detalles de la actividad sospechosa y recomendaciones sobre cómo investigar y solucionar las amenazas.

> [!NOTE]
>
> * Advanced Threat Protection para Azure Cosmos DB está actualmente disponible solo para SQL API.
> * Advanced Threat Protection para Azure Cosmos DB no está disponible actualmente en Azure Government y en regiones de nube soberana.

Para una experiencia de investigación completa de las alertas de seguridad, se recomienda habilitar el [registro de diagnóstico en Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/logging), que registra las operaciones en la base de datos, incluidas las operaciones CRUD en todos los documentos, contenedores y bases de datos.

## <a name="set-up-advanced-threat-protection"></a>Configuración de Advanced Threat Protection

### <a name="set-up-atp-using-the-portal"></a>Configuración de ATP con el portal

1. Inicie Azure Portal en [https://portal.azure.com](https://portal.azure.com/).

2. En la cuenta de Azure Cosmos DB, en el menú **Configuración**, seleccione **Seguridad avanzada**.

    ![Configuración de ATP](./media/cosmos-db-advanced-threat-protection/cosmos-db-atp.png)

3. En la hoja de configuración de **Seguridad avanzada**:

    * Haga clic en la opción **Advanced Threat Protection** para configurarla en **ACTIVADA**.
    * Haga clic en **Guardar** para guardar la directiva de Protección contra amenazas avanzada nueva o actualizada.   

### <a name="set-up-atp-using-rest-api"></a>Configuración de ATP mediante la API de REST

Use comandos de la API de REST para crear, actualizar u obtener la configuración de Advanced Threat Protection para una cuenta de Azure Cosmos DB específica.

* [Creación de Advanced Threat Protection](https://go.microsoft.com/fwlink/?linkid=2099745)
* [Obtención de Advanced Threat Protection](https://go.microsoft.com/fwlink/?linkid=2099643)

### <a name="set-up-atp-using-azure-powershell"></a>Configuración de ATP con Azure PowerShell

Use los siguientes cmdlets de PowerShell:

* [Habilitación de Advanced Threat Protection](https://go.microsoft.com/fwlink/?linkid=2099607&clcid=0x409)
* [Obtención de Advanced Threat Protection](https://go.microsoft.com/fwlink/?linkid=2099608&clcid=0x409)
* [Deshabilitación de Advanced Threat Protection](https://go.microsoft.com/fwlink/?linkid=2099709&clcid=0x409)

### <a name="using-azure-resource-manager-templates"></a>Uso de plantillas del Administrador de recursos de Azure

Use una plantilla de Azure Resource Manager para configurar Cosmos DB con Advanced Threat Protection habilitada.
Para más información, consulte [Creación de una cuenta de Cosmos DB con Advanced Threat Protection](https://azure.microsoft.com/resources/templates/201-cosmosdb-advanced-threat-protection-create-account/).

### <a name="using-azure-policy"></a>Uso de Azure Policy

Use una instancia de Azure Policy para habilitar Advanced Threat Protection para Cosmos DB.

1. Inicie la página **Policy - Definitions** (Directiva - Definiciones) de Azure y busque la directiva **Deploy Advanced Threat Protection for Cosmos DB** (Implementar Advanced Threat Protection para Cosmos DB).

    ![Búsqueda de directiva](./media/cosmos-db-advanced-threat-protection/cosmos-db.png) 

1. Haga clic en la directiva **Deploy Advanced Threat Protection for Cosmos DB** (Implementar Advanced Threat Protection para Cosmos DB) y, a continuación, haga clic en **Assign** (Asignar).

    ![Selección de suscripción o grupo](./media/cosmos-db-advanced-threat-protection/cosmos-db-atp-policy.png)


1. En el campo **Scope** (Ámbito), haga clic en los tres puntos, seleccione una suscripción de Azure o un grupo de recursos y, después, haga clic en **Select** (Seleccionar).

    ![Página de definiciones de directiva](./media/cosmos-db-advanced-threat-protection/cosmos-db-atp-details.png)


1. Escriba los demás parámetros y haga clic en **Assign** (Asignar).

## <a name="manage-atp-security-alerts"></a>Administración de alertas de seguridad de ATP

Cuando se producen anomalías en la actividad de Azure Cosmos DB, se desencadena una alerta de seguridad con información sobre el evento de seguridad sospechoso. 

 Desde Azure Security Center, se pueden revisar y administrar las [alertas de seguridad](../security-center/security-center-alerts-overview.md) actuales.  Haga clic en una alerta específica en [Security Center](https://ms.portal.azure.com/#blade/Microsoft_Azure_Security/SecurityMenuBlade/0) para ver las posibles causas y las medidas recomendadas para investigar y mitigar la potencial amenaza. En la imagen siguiente se muestra un ejemplo de los detalles de la alerta proporcionados en Security Center.

 ![Detalles de la amenaza](./media/cosmos-db-advanced-threat-protection/cosmos-db-alert-details.png)

También se envía una notificación por correo electrónico con los detalles de la alerta y las medidas recomendadas. En la imagen siguiente se muestra un ejemplo de un correo electrónico de alerta.

 ![Detalles de alertas](./media/cosmos-db-advanced-threat-protection/cosmos-db-alert.png)

## <a name="cosmos-db-atp-alerts"></a>Alertas de ATP de Cosmos DB

 Para ver una lista de las alertas generadas al supervisar cuentas de Azure Cosmos DB, consulte la sección [Alertas de Cosmos DB](../security-center/security-center-alerts-data-services.md#cosmos-db) en la documentación de Security Center.

## <a name="next-steps"></a>Pasos siguientes

* Más información sobre el [Registro de diagnóstico de Azure Cosmos DB](monitor-cosmos-db.md#diagnostic-settings).
* Más información acerca de [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
