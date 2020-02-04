---
title: Prevención de costos inesperados y administración de la facturación en Azure
description: Obtenga información acerca de cómo evitar cargos inesperados en la factura de Azure. Use las características de administración y seguimiento de costos en su cuenta de Azure.
author: bandersmsft
manager: amberb
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: a78edc29ef85430dc8af57e60dfcba1f2e46413e
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/31/2020
ms.locfileid: "75985500"
---
# <a name="prevent-unexpected-charges-with-azure-billing-and-cost-management"></a>Prevención de cargos inesperados con la administración de costos y facturación de Azure

Al suscribirse a Azure, hay varias cosas que puede hacer para obtener una idea más clara de los gastos:

- Obtenga los costos estimados antes de agregar servicios mediante una [calculadora de precios](https://azure.microsoft.com/pricing/calculator/), la hoja de precios de Azure o al agregar servicios en Azure Portal.
- Supervise los costos con [presupuestos](../costs/tutorial-acm-create-budgets.md), [alertas ](../costs/cost-mgt-alerts-monitor-usage-spending.md) y [análisis de costos](../costs/quick-acm-cost-analysis.md).
- Revise los cargos de la factura comparándolos con [archivos de uso detallados](download-azure-invoice-daily-usage-date.md).
- Integre los datos de facturación y de costos en su propio sistema de generación de informes mediante las API de [facturación](https://docs.microsoft.com/rest/api/billing/) y [consumo](https://docs.microsoft.com/rest/api/consumption/).
- Use recursos y herramientas adicionales para los clientes de Contrato Enterprise (EA), Proveedor de soluciones en la nube (CSP) y Patrocinio de Azure.
- Utilice [algunos de los servicios más populares de Azure de forma gratuita durante 12 meses](create-free-services.md) disponibles con la [cuenta gratuita de Azure](https://azure.microsoft.com/free/). Junto con las recomendaciones que se enumeran a continuación, consulte [Evitar cargos en su cuenta gratuita de Azure](avoid-charges-free-account.md).

## <a name="get-estimated-costs-before-adding-azure-services"></a>Obtención de costos estimados antes de agregar servicios de Azure

Use una de las siguientes herramientas para calcular el costo de usar un servicio de Azure:
- Calculadora de precios de Azure
- Hoja de precios de Azure
- Portal de Azure

Las imágenes de las secciones siguientes muestran ejemplos de precios en dólares estadounidenses.

### <a name="estimate-cost-online-using-the-pricing-calculator"></a>Estimación del coste en línea con la calculadora de precios

Consulte la [calculadora de precios](https://azure.microsoft.com/pricing/calculator/) para obtener un costo mensual estimado del servicio que desee agregar. Puede cambiar la moneda para obtener el presupuesto en la moneda local.

![Captura de pantalla del menú de la calculadora de precios](./media/getting-started/pricing-calc.png)

Puede ver el costo estimado de cualquier servicio de Azure de primera entidad. Por ejemplo, en la captura de pantalla siguiente, se estima que una máquina virtual (VM) Windows A1 cuesta 66,96 dólares al mes en horas de proceso si la deja ejecutándose todo el tiempo:

![Captura de pantalla de la calculadora de precios en la que se muestra un costo estimado de la máquina virtual Windows A1 al mes](./media/getting-started/pricing-calcvm.png)

Para más información sobre los precios, consulte [Preguntas más frecuentes sobre los precios de Azure](https://azure.microsoft.com/pricing/faq/). Si desea comunicarse con un vendedor de Azure, llame al número de teléfono que se muestra en la parte superior de la página de preguntas más frecuentes.

### <a name="view-and-download-azure-price-sheet"></a>Visualización y descarga de la hoja de precios de Azure

Si tiene acceso a Azure a través de un Contrato Enterprise (EA) o un Contrato de cliente de Microsoft (MCA), puede ver y descargar la hoja de precios de su cuenta de Azure. La hoja de precios es un archivo de Excel que contiene los precios de todos los servicios de Azure. Para más información, consulte [Visualización y descarga de los precios de Azure de la organización](ea-pricing.md).

### <a name="review-estimated-costs-in-the-azure-portal"></a>Revisión de los costos estimados en Azure Portal

Puede ver el costo estimado mensual al agregar un servicio en Azure Portal. Por ejemplo, al elegir el tamaño de la máquina virtual Windows, verá el costo mensual estimado de las horas de proceso:

![Ejemplo: una máquina virtual Windows A1 que muestra el costo estimado al mes](./media/getting-started/vm-size-cost.png)

## <a name="monitor-costs-when-using-azure-services"></a>Supervisión de los costos al usar los servicios de Azure
Puede supervisar los costos con las siguientes herramientas:

- Alertas de presupuesto y de costos
- Análisis de costos

### <a name="track-costs-with-budgets-and-cost-alerts"></a>Seguimiento de costos con presupuestos y alertas de costos

Cree [presupuestos](../costs/tutorial-acm-create-budgets.md) para administrar los costos y cree [alertas](../costs/cost-mgt-alerts-monitor-usage-spending.md) que envíen notificaciones automáticamente a las partes interesadas sobre anomalías en los gastos y gastos adicionales.

### <a name="costs"></a> Exploración y análisis de costos con análisis de costos

Cuando todos los servicios de Azure estén en funcionamiento, compruebe regularmente los costos para hacer un seguimiento de los gastos de Azure. Puede usar el análisis de costos para saber dónde se originaron los costos de su uso de Azure.

1. Visite la [página Administración de costos + facturación en Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade).

2. Haga clic en **Análisis de costos** en el lado izquierdo de la pantalla para ver el costo actual desglosado por varias tablas dinámicas como servicio, ubicación y suscripción. Después de agregar un servicio o realizar una compra, espere 24 horas para que se muestren los datos. De manera predeterminada, el análisis de costos muestra el costo del ámbito en que se encuentra. Por ejemplo, en la captura de pantalla siguiente, se muestra el costo de la cuenta de facturación de Contoso. Use la píldora Ámbito para cambiar a otro ámbito del análisis de costos. Para más información sobre los ámbitos, consulte [Descripción y uso de ámbitos](../costs/understand-work-scopes.md#scopes)

    ![Captura de pantalla de la vista de análisis de costes en Azure Portal](./media/getting-started/cost-analysis.png)

4. Puede filtrar por varias propiedades, como etiquetas, tipo de recurso e intervalo de tiempo. Haga clic en **Agregar filtro** para agregar el filtro de una propiedad y seleccionar los valores que se van a filtrar. Seleccione **Exportar**  para exportar la vista a un archivo de valores separados por comas (. csv).

5. Además, puede hacer clic en las etiquetas del gráfico para ver el historial de gastos diarios de la etiqueta. Por ejemplo: en la captura de pantalla siguiente, al hacer clic en máquinas virtuales, se muestra el costo diario de ejecutar las máquinas virtuales.

    ![Captura de pantalla de la vista de historial de gastos en Azure Portal](./media/getting-started/costhistory.png)

## <a name="optimize-and-reduce-costs"></a>Optimización y reducción de costos
Si no está familiarizado con los principios de la administración de costos, consulte el artículo [Optimización de la inversión en la nube con Azure Cost Management](../costs/cost-mgt-best-practices.md).

En Azure Portal, también puede optimizar y reducir los costos de Azure con el apagado automático de las máquinas virtuales y las recomendaciones de Advisor.

### <a name="consider-cost-cutting-features-like-auto-shutdown-for-vms"></a>Posibilidad de usar características de reducción de costos como el apagado automático de las máquinas virtuales

Según el escenario, puede configurar el apagado automático de las VM en Azure Portal. Para más información, consulte [Auto-shutdown for VMs using Azure Resource Manager](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) (Apagado automático de VM mediante Azure Resource Manager).

![Captura de pantalla de la opción de apagado automático en el portal](./media/getting-started/auto-shutdown.png)

El apagado automático no es lo mismo que cuando apaga la VM desde dentro mediante las opciones de energía. El apagado automático detiene y desasigna las VM para impedir gastos por uso adicionales. Para más información, consulte las preguntas más frecuentes sobre precios de [máquinas virtuales Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) y [máquinas virtuales Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) y sobre los estados de máquinas virtuales.

Para conocer más medidas de reducción de costes de los entornos de desarrollo y pruebas, visite [Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab/).

### <a name="turn-on-and-review-azure-advisor-recommendations"></a>Activación y comprobación de las recomendaciones de Azure Advisor

[Azure Advisor](../../advisor/advisor-overview.md) ayuda a reducir los costos al identificar los recursos que se usan poco. Busque **Advisor** en Azure Portal:

![Captura de pantalla del botón de Azure Advisor en Azure Portal](./media/getting-started/advisor-button.png)

Seleccione **Costo** en el lado izquierdo. Verá recomendaciones en la pestaña **Costo**:

![Captura de pantalla de ejemplo de recomendación de coste de Advisor](./media/getting-started/advisor-action.png)

Consulte el tutorial guiado [Optimización de costos a partir de las recomendaciones](../costs/tutorial-acm-opt-recommendations.md) para conocer las recomendaciones de Advisor sobre el ahorro de costos.

## <a name="review-charges-against-your-latest-invoice"></a>Revisión de los cargos con la factura más reciente

Al final del ciclo de facturación, la factura está disponible. Puede [descargar facturas y archivos de uso detallados](download-azure-invoice-daily-usage-date.md), y compararlos para asegurarse de que se le ha cobrado correctamente. Para más información sobre cómo comparar el uso diario con la factura, consulte [Entienda su factura de Microsoft Azure](../understand/review-individual-bill.md).

Si usa Azure a través de un Contrato de cliente de Microsoft (MCA), también puede [comparar la factura con las transacciones ](../understand/review-customer-agreement-bill.md#review-invoiced-transactions-in-the-azure-portal) para conocer los cargos de la factura.

## <a name="integrate-with-billing-and-consumption-apis"></a>Integración con las API de facturación y consumo

Use las API de [facturación](https://docs.microsoft.com/rest/api/billing/) y [consumo](https://docs.microsoft.com/rest/api/consumption/) de Azure para obtener datos de facturación y de costos mediante programación. Use la API de RateCard y la API de uso juntas para obtener el uso facturado. Para más información, consulte [Obtención de información sobre el consumo de recursos de Microsoft Azure](usage-rate-card-overview.md).

## <a name="other-offers"></a> Recursos adicionales y casos especiales

### <a name="ea-csp-and-sponsorship-customers"></a>Clientes patrocinadores, CSP y EA
Hable con el administrador de la cuenta o el asociado de Azure para conocer más información.

| Oferta | Recursos |
|-------------------------------|-----------------------------------------------------------------------------------|
| Contrato Enterprise (EA) | [Portal EA](https://ea.azure.com/), [documentos de ayuda](https://ea.azure.com/helpdocs) e [informe de Power BI](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-enterprise/) |
| Proveedor de soluciones en la nube (CSP) | Hable con el proveedor |
| Patrocinio de Azure | [Portal de patrocinio](https://www.microsoftazuresponsorships.com/) |

Si es el administrador de una infraestructura de TI para una organización grande, es recomendable que vea las [plantillas scaffold empresariales de Azure](/azure/architecture/cloud-adoption-guide/subscription-governance) y las [notas del producto TI empresarial](https://download.microsoft.com/download/F/F/F/FFF60E6C-DBA1-4214-BEFD-3130C340B138/Azure_Onboarding_Guide_for_IT_Organizations_EN_US.pdf) (descarga en .pdf, solo en inglés).

### <a name="EA"></a> Vistas de los costos de Contrato Enterprise en Azure Portal

Las vistas de los costos de Enterprise están actualmente en la versión preliminar pública. Elementos que tener en cuenta:

- Los costos de suscripción se basan en el uso y no incluyen los importes de prepago, uso por encima del límite, cantidades incluidas, ajustes ni impuestos. Los cargos reales se calculan en el nivel de inscripción.
- Los importes que se muestran en Azure Portal podrían ser diferentes de lo que está en Enterprise Portal. Las actualizaciones de Enterprise Portal pueden tardar unos minutos antes de que se muestren los cambios en Azure Portal.
- Si no ve los costos, puede deberse a uno de los siguientes motivos:
    - No tiene permisos en el nivel de suscripción. Para ver las vistas de costos de la empresa, debe ser un lector de facturación, un lector, un colaborador o un propietario en el nivel de suscripción.
    - Es el propietario de la cuenta y su administrador de inscripción ha deshabilitado la opción para que el propietario de la cuenta vea los cargos.  Póngase en contacto con el administrador de inscripción para obtener acceso a los costos.
    - Es el administrador del departamento y su administrador de inscripción ha deshabilitado la opción para que el **administrador del departamento vea los cargos**.  Póngase en contacto con el administrador de inscripciones para acceder.
    - Compró Azure a través de un asociado de canal y el asociado no publicó la información sobre precios.  
- Si actualiza la configuración relacionada con el costo en Enterprise Portal, los cambios tardan unos minutos en mostrarse en Azure Portal.
- El límite de gasto y la orientación de factura no se aplican a las suscripciones de EA.

### <a name="check-your-subscription-and-access"></a>Comprobación de la suscripción y el acceso

Para ver los costos, necesita acceso de nivel de cuenta o de suscripción a la información de costos o de facturación. El acceso varía según el tipo de cuenta de facturación. Para más información sobre las cuentas de facturación y la comprobación del tipo de la cuenta de facturación, consulte [Visualización de las cuentas de facturación en Azure Portal](view-all-accounts.md).

Si tiene acceso a Azure a través de una cuenta de facturación de Microsoft Online Service Program (MOSP), consulte [Administración del acceso a la información de facturación de Azure](manage-billing-access.md).

Si tiene acceso a Azure a través de una cuenta de facturación de Contrato Enterprise (EA), consulte [Roles administrativos del Contrato Enterprise de Azure en Azure](understand-ea-roles.md).

Si tiene acceso a Azure a través de una cuenta del Contrato de cliente de Microsoft (MCA), consulte [Descripción de los roles administrativos del contrato de cliente de Microsoft en Azure](understand-mca-roles.md).

### <a name="request-a-service-level-agreement-credit-for-a-service-incident"></a>Solicitud de un crédito del Acuerdo de Nivel de Servicio por un incidente de servicio

El contrato de nivel de servicio explica los compromisos de Microsoft en cuanto a tiempo de actividad y conectividad. Se notifica de un incidente de servicio cuando los servicios de Azure experimentan un problema que afecta al tiempo de actividad o a la conectividad, lo que se suele conocer como *interrupción*. Si no alcanzamos ni mantenemos los niveles de servicio para cada servicio, como se describe en el Acuerdo de Nivel de Servicio, podría optar a un crédito para una parte de las tarifas mensuales por servicio.

Para solicitar un crédito:

1. Inicie sesión en [Azure Portal](https://portal.azure.com/). Si tiene varias cuentas, asegúrese de usar la que resultó afectada por el tiempo de inactividad de Azure.
2. Cree una nueva solicitud de soporte técnico.
3. En **Tipo de problema** como **Facturación**.
4. En **Tipo de problema**, seleccione **Solicitud de reembolso**.
5. Agregue detalles para especificar que está solicitando un crédito de Acuerdo de Nivel de Servicio, mencione la fecha, hora y zona horaria, así como los servicios afectados (máquinas virtuales, sitios web, etc.).
6. Compruebe los detalles de contacto y seleccione **Crear** para enviar la solicitud.

Los umbrales del Acuerdo de Nivel de Servicio varían según el servicio. Por ejemplo, el nivel web de SQL tiene un Acuerdo de Nivel de Servicio del 99,9 %, las máquinas virtuales tienen un Acuerdo de Nivel de Servicio del 99,95 % y el nivel Estándar de SQL tiene un Acuerdo de Nivel de Servicio del 99,99 %.

Para algunos servicios, hay requisitos previos para que se aplique el Acuerdo de Nivel de Servicio. Por ejemplo, las máquinas virtuales deben tener dos o más instancias implementadas en el mismo conjunto de disponibilidad.

Para más información, consulte [Acuerdos de Nivel de Servicio](https://azure.microsoft.com/support/legal/sla/) y la documentación [Resumen de Acuerdos de Nivel de Servicio para servicios de Azure](https://azure.microsoft.com/support/legal/sla/summary/).

## <a name="need-help-contact-us"></a>¿Necesita ayuda? Póngase en contacto con nosotros.

Si tiene alguna pregunta o necesita ayuda, [cree una solicitud de soporte técnico](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Pasos siguientes
- Obtenga información sobre los [límites de gasto](spending-limit.md) para evitar gastos excesivos.
- Comience a [analizar los costos de Azure](../costs/quick-acm-cost-analysis.md).
