---
title: Precios de Azure Security Center
description: 'Azure Security Center se ofrece en dos modos: con y sin Azure Defender.'
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 4d1364cd-7847-425a-bb3a-722cb0779f78
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/13/2020
ms.author: memildin
ms.openlocfilehash: 0b6a9d081d2b81124b0cae15e81e4b233f7824bb
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/17/2020
ms.locfileid: "97629347"
---
# <a name="pricing-of-azure-security-center"></a>Precios de Azure Security Center
Azure Security Center ofrece una administración de seguridad unificada y una protección contra amenazas avanzada para las cargas de trabajo que se ejecutan en Azure, de forma local y en otras nubes. Ofrece visibilidad y control sobre las cargas de trabajo de nube híbrida, defensas activas que reducen la exposición a las amenazas y una detección inteligente, lo que le permitirá mantenerse al día de los ciberriesgos, que se expanden rápidamente.


## <a name="free-option-vs-azure-defender-enabled"></a>Opción gratis frente a Azure Defender habilitado

Security Center se ofrece en dos modos:

- **Azure Defender DESACTIVADO** (Gratis): Security Center sin Azure Defender está habilitado en todas las suscripciones de Azure la primera vez que visite el panel de Azure Security Center en Azure Portal, o si se habilitó mediante programación a través de una API. Este modo gratis proporciona recomendaciones de directivas de seguridad, de evaluación continua de la seguridad y de seguridad que requieren acción para que pueda proteger sus recursos de Azure.

- **Azure Defender ACTIVADO**: la habilitación de Azure Defender amplía las capacidades del nivel Gratis a las cargas de trabajo que se ejecutan en nubes privadas y otras nubes públicas, lo que ofrece una administración de seguridad unificada y protección contra amenazas en todas sus cargas de trabajo de nube híbrida. Algunas de las principales características de Azure Defender:

    - **Microsoft Defender for Endpoint**: Azure Defender para servidores incluye [Microsoft Defender for Endpoint](https://www.microsoft.com/microsoft-365/security/endpoint-defender) para una completa detección y respuesta de puntos de conexión (EDR). Obtenga más información sobre las ventajas de usar Microsoft Defender for Endpoint junto con Azure Defender en [Uso de la solución EDR integrada de Security Center](security-center-wdatp.md).
    - **Detección de vulnerabilidades de máquinas virtuales y registros de contenedor**: implemente fácilmente un escáner en todas las máquinas virtuales que proporciona la solución más avanzada del sector para la administración de vulnerabilidades. Vea, investigue y corrija los hallazgos directamente dentro de Security Center. 
    - **Seguridad híbrida**: Obtenga una vista unificada de la seguridad de todas sus cargas de trabajo locales y en la nube. Aplique directivas de seguridad y evalúe constantemente la seguridad de las cargas de trabajo de nube híbrida para garantizar el cumplimiento normativo con los estándares de seguridad. Recopile, busque y analice datos de seguridad de varios orígenes, incluidos firewalls y otras soluciones de partners.
    - **Alertas de protección contra amenazas**: el análisis de comportamiento avanzado y Microsoft Intelligent Security Graph proporcionan una ventaja frente a los ataques cibernéticos en evolución. La funcionalidad integrada de análisis del comportamiento y aprendizaje automático puede identificar ataques y vulnerabilidades de seguridad de día cero. Supervise las redes, las máquinas y los servicios en la nube para detectar ataques entrantes y actividad posterior a una infracción de seguridad. Optimice la investigación con herramientas interactivas e inteligencia de amenazas contextual.
    - **Controles de acceso y de aplicación**: bloquee el malware y otras aplicaciones no deseadas aplicando recomendaciones de basadas en el aprendizaje automático, adaptadas a sus cargas de trabajo específicas, para crear listas de permitidos y denegados. Reduzca la superficie de la red que está expuesta a ataques mediante un acceso Just-In-Time controlado a los puertos de administración de las VM de Azure. Los controles de acceso y de aplicación reducen drásticamente la exposición a ataques por fuerza bruta y otros tipos de ataque de red.
    - **Características de seguridad de contenedor**: Aproveche la administración de vulnerabilidades y la protección contra amenazas en tiempo real en los entornos en contenedores. Al habilitar **Azure Defender para registros de contenedor**, pueden pasar hasta 12 horas para que se habiliten todas las características. Los cargos se basan en el número de imágenes de contenedor únicas insertadas en el registro conectado. Una vez que se haya analizado una imagen, no se le cobrará de nuevo a menos que se modifique e inserte una vez más.
    - **Protección contra amenazas con amplitud para recursos conectados al entorno de Azure**: Azure Defender incluye protección contra amenazas con amplitud nativa de Azure para los servicios de Azure comunes a todos los recursos: Azure Resource Manager, Azure DNS, capa de red de Azure y Azure Key Vault. Azure Defender tiene una visibilidad exclusiva de la capa de administración de Azure y la capa de Azure DNS, y, por tanto, puede proteger los recursos en la nube que están conectados a esas capas.


## <a name="try-azure-defender-free-for-30-days"></a>Prueba de Azure Defender gratis durante 30 días
Azure Defender es gratis durante los primeros 30 días. Después de 30 días, si decide continuar usando el servicio, empezaremos a cobrar automáticamente el uso.

## <a name="enable-azure-defender"></a>Habilitación de Azure Defender
Puede proteger toda una suscripción de Azure con Azure Defender y todos los recursos de la suscripción heredarán las protecciones.

Para habilitar Azure Defender:

1. En el menú principal de Security Center, seleccione **Precios y configuración**.
1. Seleccione la suscripción que quiere actualizar.
1. Seleccione **Azure Defender está activado** para actualizar.
1. Seleccione **Guardar**.

A continuación se muestra la página de precios de una suscripción de ejemplo. Observará que cada plan de Azure Defender tiene un precio independiente y se puede activar o desactivar de forma independiente.

:::image type="content" source="./media/security-center-pricing/pricing-tier-page.png" alt-text="Página de precios de Security Center en el portal":::

> [!NOTE]
> Para habilitar todas las características de Security Center, incluidas las funcionalidades de protección contra amenazas, debe habilitar Azure Defender en la suscripción que contenga las cargas de trabajo aplicables. La habilitación en el nivel de área de trabajo no habilita el acceso a VM Just-In-Time, los controles de aplicación adaptables ni las detecciones de red para los recursos de Azure. Además, los únicos planes de Azure Defender disponibles en el nivel de área de trabajo son Azure Defender para servidores y Azure Defender para servidores SQL en máquinas.
>
> Puede habilitar **Azure Defender para cuentas de Storage** en el nivel de suscripción o de recursos.
> Puede habilitar **Azure Defender para SQL** en el nivel de suscripción o de recursos.
> Puede habilitar la protección contra amenazas para **Azure Database for MariaDB/MySQL/PostgreSQL** solo en el nivel de recursos.


## <a name="faq---pricing-and-billing"></a>Preguntas frecuentes sobre precios y facturación 

- [¿Cómo se puede realizar un seguimiento de las personas de mi organización que habilitaron cambios de Azure Defender en Azure Security Center?](#how-can-i-track-who-in-my-organization-enabled-azure-defender-changes-in-security-center)
- [¿Cuáles son los planes que ofrece Security Center?](#what-are-the-plans-offered-by-security-center)
- [¿Cómo habilito Azure Defender para mi suscripción?](#how-do-i-enable-azure-defender-for-my-subscription)
- [¿Puedo habilitar Azure Defender para servidores en un subconjunto de servidores de mi suscripción?](#can-i-enable-azure-defender-for-servers-on-a-subset-of-servers-in-my-subscription)
- [Mi suscripción tiene Azure Defender para servidores habilitado, ¿pagaré por los servidores que no estén en ejecución?](#my-subscription-has-azure-defender-for-servers-enabled-do-i-pay-for-not-running-servers)
- [¿Se me cobrará por máquinas sin el agente de Log Analytics instalado?](#will-i-be-charged-for-machines-without-the-log-analytics-agent-installed)
- [Si un agente de Log Analytics envía notificaciones a diversas áreas de trabajo, ¿se me cobrará varias veces?](#if-a-log-analytics-agent-reports-to-multiple-workspaces-will-i-be-charged-twice)
- [Si un agente de Log Analytics envía notificaciones a varias áreas de trabajo, ¿la ingesta de datos gratuita de 500 MB estará disponible en todas ellas?](#if-a-log-analytics-agent-reports-to-multiple-workspaces-is-the-500-mb-free-data-ingestion-available-on-all-of-them)
- [¿Se calcula la ingesta de 500 MB de datos gratis para todo el área de trabajo o estrictamente por máquina?](#is-the-500-mb-free-data-ingestion-calculated-for-an-entire-workspace-or-strictly-per-machine)

### <a name="how-can-i-track-who-in-my-organization-enabled-azure-defender-changes-in-security-center"></a>¿Cómo se puede realizar un seguimiento de las personas de mi organización que habilitaron cambios de Azure Defender en Security Center?
Las suscripciones de Azure pueden tener varios administradores con permisos para cambiar la configuración de precios. Para averiguar qué usuario realizó un cambio, use el registro de actividad de Azure.

:::image type="content" source="media/security-center-pricing/logged-change-to-pricing.png" alt-text="Registro de actividad de Azure en el que se muestra un evento de cambio de precio":::

Si la información del usuario no aparece en la columna **Evento iniciado por**, explore el JSON del evento para ver los detalles pertinentes.

:::image type="content" source="media/security-center-pricing/tracking-pricing-changes-in-activity-log.png" alt-text="Explorador JSON del registro de actividad de Azure":::


### <a name="what-are-the-plans-offered-by-security-center"></a>¿Cuáles son los planes que ofrece Security Center? 
Security Center tiene dos ofertas: 

- Azure Security Center gratis 
- Azure Defender  

### <a name="how-do-i-enable-azure-defender-for-my-subscription"></a>¿Cómo habilito Azure Defender para mi suscripción? 
Puede usar cualquiera de los siguientes métodos para habilitar Azure Defender para su suscripción: 

|Método  |Instrucciones  |
|---------|---------|
|Páginas de Azure Security Center en Azure Portal|[Habilitación de Azure Defender](#enable-azure-defender)|
|API DE REST|[API de precios](/rest/api/securitycenter/pricings)|
|Azure CLI|[az security pricing](/cli/azure/security/pricing)|
|PowerShell|[Set-AzSecurityPricing](/powershell/module/az.security/set-azsecuritypricing)|
|Azure Policy|[Precios de conjunto](https://github.com/Azure/Azure-Security-Center/blob/master/Pricing%20%26%20Settings/ARM%20Templates/Set-ASC-Bundle-Pricing.json)|
|||

### <a name="can-i-enable-azure-defender-for-servers-on-a-subset-of-servers-in-my-subscription"></a>¿Puedo habilitar Azure Defender para servidores en un subconjunto de servidores de mi suscripción?
No. Al habilitar [Azure Defender para servidores](defender-for-servers-introduction.md) en una suscripción, Azure Defender protegerá todos los servidores de la suscripción. 

Una alternativa es habilitar Azure Defender para servidores en el nivel de área de trabajo de Log Analytics. Si lo hace, solo se protegerán y facturarán los servidores que envían notificaciones a esa área de trabajo. Sin embargo, varias funcionalidades no estarán disponibles, como el acceso a máquina virtual Just-in-Time, las detecciones de red, el cumplimiento normativo, la protección de red adaptable y los controles de aplicaciones adaptables, entre otras. 


### <a name="my-subscription-has-azure-defender-for-servers-enabled-do-i-pay-for-not-running-servers"></a>Mi suscripción tiene Azure Defender para servidores habilitado, ¿pagaré por los servidores que no estén en ejecución? 
No. Al habilitar [Azure Defender para servidores](defender-for-servers-introduction.md) en una suscripción, se le facturará por horas solo por los servidores en ejecución. No se le cobrará por ningún servidor mientras esté desactivado. 

> [!TIP]
> Esto también es aplicable a los otros tipos de recursos protegidos por Security Center. 

### <a name="will-i-be-charged-for-machines-without-the-log-analytics-agent-installed"></a>¿Se me cobrará por máquinas sin el agente de Log Analytics instalado?
Sí. Al habilitar [Azure Defender para servidores](defender-for-servers-introduction.md) en una suscripción, las máquinas de esa suscripción obtienen una serie de protecciones incluso si no ha instalado el agente de Log Analytics.

### <a name="if-a-log-analytics-agent-reports-to-multiple-workspaces-will-i-be-charged-twice"></a>Si un agente de Log Analytics envía notificaciones a diversas áreas de trabajo, ¿se me cobrará varias veces? 
Sí. Si ha configurado el agente de Log Analytics para enviar datos a dos o más áreas de trabajo de Log Analytics diferentes (hospedaje múltiple), se le cobrará por cada área de trabajo que tenga instaladas soluciones de seguridad o antimalware. 

### <a name="if-a-log-analytics-agent-reports-to-multiple-workspaces-is-the-500-mb-free-data-ingestion-available-on-all-of-them"></a>Si un agente de Log Analytics envía notificaciones a varias áreas de trabajo, ¿la ingesta de datos gratuita de 500 MB estará disponible en todas ellas?
Sí. Si ha configurado el agente de Log Analytics para enviar datos a dos o más áreas de trabajo de Log Analytics diferentes (hospedaje múltiple), obtendrá una ingesta de datos gratuita de 500 MB. Se calcula por nodo, por área de trabajo notificada y por día, y está disponible para cada área de trabajo que tenga instaladas soluciones de seguridad o antimalware. Se le cobrarán los datos ingeridos por encima de 500 MB.

### <a name="is-the-500-mb-free-data-ingestion-calculated-for-an-entire-workspace-or-strictly-per-machine"></a>¿Se calcula la ingesta de 500 MB de datos gratis para todo el área de trabajo o estrictamente por máquina?
Obtendrá una ingesta de 500 MB de datos gratis al día para cada una de las máquinas conectadas al área de trabajo. Específicamente para tipos de datos de seguridad recopilados directamente por Azure Security Center.

Estos datos son una tasa diaria promediada en todos los nodos. Por tanto, aunque algunas máquinas envíen 100 MB y otras 800 MB, si el total no supera el límite gratuito de **[número de máquinas] x 500 MB**, no se le cobrará ningún cargo adicional.

## <a name="next-steps"></a>Pasos siguientes
En este artículo se explican las opciones de precios de Security Center. Para obtener material relacionado, consulte:

- [Optimización de los costos de la carga de trabajo de Azure](https://azure.microsoft.com/blog/how-to-optimize-your-azure-workload-costs/)
- [Detalles de precios en la moneda de su elección y según su región](https://azure.microsoft.com/pricing/details/security-center/)
- Quizá quiera administrar los costos y limitar la cantidad de datos recopilados para una solución mediante la limitación a un conjunto determinado de agentes. La [selección de destino de solución](../azure-monitor/insights/solution-targeting.md) permite aplicar un ámbito a la solución y tener como destino un subconjunto de equipos en el área de trabajo. Si usa la selección de destino de solución, Security Center muestra el área de trabajo como si no tuviera una solución.