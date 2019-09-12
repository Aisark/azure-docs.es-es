---
title: 'Tutorial: Creación y administración de presupuestos de Azure | Microsoft Docs'
description: Este tutorial le ayuda a planear y tener en cuenta los costos de los servicios de Azure que usted consume.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 09/04/2019
ms.topic: conceptual
ms.service: cost-management
manager: adwise
ms.custom: seodec18
ms.openlocfilehash: 183030a063faa212d7d5788792aa525f530d18ef
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2019
ms.locfileid: "70308388"
---
# <a name="tutorial-create-and-manage-azure-budgets"></a>Tutorial: Creación y administración de presupuestos de Azure

Los presupuestos en Cost Management le ayudan a planear y dirigir la presentación de cuentas de la organización. Con presupuestos, puede tener en cuenta los servicios de Azure que consume o a los que se suscribe durante un período específico. Le ayudan a informar a otros usuarios sobre sus gastos a fin de administrar de manera proactiva los costos y supervisar cómo avanza el gasto a lo largo del tiempo. Cuando se superan los umbrales presupuestarios que ha creado, solo se desencadenan las notificaciones. Ninguno de los recursos se ve afectado y no se detiene el consumo. Puede usar los presupuestos para comparar y realizar un seguimiento de gastos para analizar los costos.

Los presupuestos mensuales se evalúan en función del gasto cada cuatro horas. Sin embargo, los datos de los recursos consumidos están disponibles pasadas ocho horas. Cuando se alcanza un umbral de presupuesto, las notificaciones por correo electrónico se reciben normalmente en menos de ocho horas. 

Los presupuestos se restablecen automáticamente al final de un período (mensual, trimestral o anualmente) para el mismo importe presupuestario al seleccionar una fecha de expiración futura. Dado que se restablecen con el mismo importe presupuestario, deberá crear presupuestos independientes cuando los importes presupuestarios en moneda difieran para períodos futuros.

Los ejemplos de este tutorial le guiarán a través de la creación y edición de un presupuesto para una suscripción de Contrato Enterprise (EA) de Azure.

En el vídeo sobre [cómo crear un presupuesto para supervisar los gastos con Azure Cost Management](https://www.youtube.com/watch?v=ExIVG_Gr45A) descubrirá la manera de crear presupuestos en Azure para supervisar los gastos.


En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Crear un presupuesto en Azure Portal
> * Editar un presupuesto

## <a name="prerequisites"></a>Requisitos previos

Los presupuestos se admiten en varios tipos de cuenta de Azure. Para ver la lista completa de tipos de cuenta compatibles, consulte [Understand Cost Management data](understand-cost-mgt-data.md) (Información sobre los datos de Cost Management). Para ver los presupuestos, se necesita al menos acceso de lectura en la cuenta de Azure.

 En el caso de las suscripciones con contrato Enterprise de Azure, debe tener acceso de lectura para ver los presupuestos. Para crear y administrar presupuestos, debe tener permiso de colaborador. Puede crear presupuestos individuales para las suscripciones de EA y los grupos de recursos. Sin embargo, no se pueden crear presupuestos para cuentas de facturación de EA.

Se admiten los siguientes permisos o ámbitos de Azure por suscripción para los presupuestos por usuario y grupo. Para más información sobre los ámbitos, consulte [Descripción y uso de ámbitos](understand-work-scopes.md).

- Propietario: puede crear, modificar o eliminar los presupuestos para una suscripción.
- Colaborador y Colaborador de Cost Management: puede crear, modificar o eliminar sus propios presupuestos. Puede modificar el importe presupuestario para los presupuestos creados por otros usuarios.
- Lector y Lector de Cost Management: puede ver los presupuestos para los que tiene permiso.

Para más información sobre cómo asignar permisos a los datos de Cost Management, consulte [Asignación del acceso a los datos de Cost Management](assign-access-acm-data.md).

## <a name="sign-in-to-azure"></a>Inicio de sesión en Azure

- Inicie sesión en Azure Portal en https://portal.azure.com.

## <a name="create-a-budget-in-the-azure-portal"></a>Crear un presupuesto en Azure Portal

Puede crear un presupuesto de suscripción de Azure durante un período mensual, trimestral o anual. El contenido de navegación en Azure Portal determina si crea un presupuesto para una suscripción o para un grupo de administración.

Para crear o ver un presupuesto, abra el ámbito deseado en Azure Portal y seleccione **Presupuestos** en el menú. Por ejemplo, vaya a **Suscripciones**, seleccione una suscripción de la lista y elija **Presupuestos** en el menú. Use la píldora **Ámbito** para cambiar a un ámbito diferente, como un grupo de administración, en los presupuestos. Para más información sobre los ámbitos, consulte [Descripción y uso de ámbitos](understand-work-scopes.md).

Después de crear los presupuestos, muestran a un lado una vista sencilla de su gasto actual.

Haga clic en **Agregar**.

![Ejemplo en el que se muestra una lista de presupuestos ya creada](./media/tutorial-acm-create-budgets/budgets01.png)

En la ventana **Crear presupuesto**, asegúrese de que el ámbito que se muestra es correcto. Elija los filtros que quiera agregar. Los filtros le permiten crear presupuestos en para costos específicos, como los grupos de recursos de una suscripción o un servicio, como las máquinas virtuales. Todos los filtros que puede usar en el análisis de costos también se pueden aplicar a un presupuesto.

Después de haber identificado el ámbito y los filtros, escriba un nombre de presupuesto. A continuación, elija un período de restablecimiento mensual, trimestral o anual. Este período de restablecimiento determina el período de tiempo que el presupuesto analiza. El costo evaluado por el presupuesto comienza en cero al principio de cada nuevo período. Cuando crea un presupuesto trimestral, funciona de la misma manera que un presupuesto mensual. La diferencia es que el importe presupuestario para el trimestre se divide de manera uniforme entre los tres meses del trimestre. Un importe presupuestario anual se divide de manera uniforme entre los 12 meses del año natural.

Si tiene una suscripción de pago por uso, MSDN o Visual Studio, es posible que el período de facturación no esté alineado con el mes natural. En el caso de esos tipos de suscripciones y grupos de recursos, puede crear un presupuesto que se adapte al período de su factura o a los meses naturales. Para crear un presupuesto adaptado al período de facturación, seleccione el período de restablecimiento **Mes de facturación**, **Trimestre de facturación** o **Año de facturación**. Para crear un presupuesto adaptado al mes natural, seleccione un período de restablecimiento **Mensual**, **Trimestral** o **Anual**.

A continuación, identifique la fecha de expiración en la que presupuesto dejará de ser válido y ya no evaluará los costos.

En función de los campos elegidos en el presupuesto hasta el momento, se muestra un gráfico para ayudarle a seleccionar el umbral que se usará para el presupuesto. El presupuesto sugerido se basa en el costo pronosticado más alto que podría producirse en períodos futuros. Puede cambiar la cantidad del presupuesto.

![Ejemplo que muestra la creación de un presupuesto con datos de costos mensuales ](./media/tutorial-acm-create-budgets/monthly-budget01.png)

Después de configurar la cantidad del presupuesto, haga clic en **Siguiente** para configurar las alertas de presupuesto. Los presupuestos requieren al menos un umbral de costos (% del presupuesto) y una dirección de correo electrónico correspondiente. De manera opcional, puede incluir hasta cinco umbrales y cinco direcciones de correo electrónico en un único presupuesto. Cuando se alcanza un umbral de presupuesto, las notificaciones por correo electrónico se reciben normalmente en menos de ocho horas. Para más información acerca de las notificaciones, consulte [Use cost alerts](cost-mgt-alerts-monitor-usage-spending.md) (Uso de alertas de costos). En el siguiente ejemplo, se genera una alerta por correo electrónico cuando se alcanza el 90 % del presupuesto.

![Ejemplo en el que se muestran las condiciones de la alerta](./media/tutorial-acm-create-budgets/monthly-budget-alert.png)

Después de crear un presupuesto, se muestra en el análisis de costos. Ver el presupuesto en relación con la tendencia del gasto es uno de los primeros pasos cuando empieza a [analizar los costos y los gastos](quick-acm-cost-analysis.md).

![Presupuesto y gasto de ejemplo que se muestra en el análisis de costos](./media/tutorial-acm-create-budgets/cost-analysis.png)

En el ejemplo anterior, creó un presupuesto para una suscripción. Sin embargo, también puede crear un presupuesto para un grupo de recursos. Si quiere crear un presupuesto para un grupo de recursos, vaya a **Administración de costos + facturación** &gt; **Suscripciones** &gt; seleccione una suscripción > **Grupos de recursos** > seleccione un grupo de recursos > **Presupuestos** > y, luego, **Agregar** un presupuesto.

## <a name="trigger-an-action-group"></a>Activación de un grupo de acciones

Al crear o editar un presupuesto para un ámbito de suscripción o grupo de recursos, puede configurarlo para que llame a un grupo de acciones. El grupo de acciones puede realizar una serie de acciones diferentes cuando se alcanza el umbral del presupuesto. Para más información sobre los grupos de acciones, consulte [Creación y administración de grupos de acciones en Azure Portal](../azure-monitor/platform/action-groups.md). Para obtener más información sobre el uso de la automatización basada en presupuestos con grupos de acciones, vea [Administración de costos con presupuestos de Azure](../billing/billing-cost-management-budget-scenario.md).

Para crear o actualizar grupos de acciones, haga clic en **Administrar los grupos de acciones** al crear o editar un presupuesto.

![Ejemplo de creación de un presupuesto para mostrar la opción Administrar los grupos de acciones](./media/tutorial-acm-create-budgets/manage-action-groups01.png)


Después, haga clic en **Agregar grupo de acciones** y cree el grupo de acciones.


![Imagen del cuadro Agregar grupo de acciones](./media/tutorial-acm-create-budgets/manage-action-groups02.png)

Una vez que se haya creado el grupo de acciones, cierre el cuadro para volver al presupuesto.

Configure el presupuesto para que use el grupo de acciones cuando se alcance un umbral individual. Se admiten hasta cinco umbrales diferentes.

![Ejemplo en el que se muestra la selección del grupo de acciones para una condición de alerta](./media/tutorial-acm-create-budgets/manage-action-groups03.png)

En el ejemplo siguiente se muestran los umbrales del presupuesto establecidos en 50 %, 75 % y 100 %. Cada uno está configurado para desencadenar las acciones especificadas en el grupo de acciones indicado.

![Ejemplo en el que se muestran las condiciones de alerta configuradas con varios grupos de acciones y tipos de acciones](./media/tutorial-acm-create-budgets/manage-action-groups04.png)

## <a name="next-steps"></a>Pasos siguientes

En este tutorial aprendió lo siguiente:

> [!div class="checklist"]
> * Crear un presupuesto en Azure Portal
> * Editar un presupuesto

Pase al siguiente tutorial para crear una exportación periódica de los datos de administración de costos.

> [!div class="nextstepaction"]
> [Creación y administración de datos exportados](tutorial-export-acm-data.md)
