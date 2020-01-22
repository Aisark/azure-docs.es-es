---
title: Creación de una solicitud de soporte técnico de Azure | Microsoft Docs
description: Los clientes que necesiten asistencia pueden usar Azure Portal para buscar soluciones de autoservicio y para crear y administrar solicitudes de soporte técnico.
services: Azure Supportability
author: ganganarayanan
manager: scotthit
ms.assetid: fd6841ea-c1d5-4bb7-86bd-0c708d193b89
ms.service: azure-supportability
ms.topic: article
ms.date: 11/13/2019
ms.author: kfollis
ms.openlocfilehash: 35d70d37b881c610d1a38a312d79f39c351c65a5
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/11/2020
ms.locfileid: "75898900"
---
# <a name="how-to-create-an-azure-support-request"></a>Creación de una solicitud de soporte técnico de Azure

## <a name="overview"></a>Información general

Los clientes de Azure pueden crear y administrar las solicitudes de soporte técnico en [Azure Portal](https://portal.azure.com).

> [!NOTE]
> La dirección URL de Azure Portal es específica de la nube de Azure donde está implementada la organización. 
>* Azure Portal para uso comercial: [https://portal.azure.com](https://portal.azure.com)
>* Azure Portal para Alemania: [https://portal.microsoftazure.de](https://portal.microsoftazure.de)
>* Azure Portal para el gobierno de los Estados Unidos: [https://portal.azure.us](https://portal.azure.us)
>
>

En función de los comentarios de los clientes, hemos actualizado la experiencia de solicitud de soporte técnico para centrarnos en tres objetivos principales:

* **Optimizada**: hacer que el soporte técnico y la solución de problemas sean fáciles de encontrar y simplificar el envío de una solicitud de soporte técnico.
* **Integrada**: puede abrir con facilidad una solicitud de soporte técnico para un problema con un recurso de Azure, sin necesidad de cambiar de contexto.
* **Eficaz**: recopile la información clave que necesita el agente de soporte técnico para resolver el problema de forma eficaz.

## <a name="getting-started"></a>Introducción

Puede ir a **Ayuda y soporte técnico** en Azure Portal. Está disponible en el encabezado global o en el menú de recursos de un servicio. Debe tener los permisos adecuados para enviar una solicitud de soporte técnico.

### <a name="role-based-access-control"></a>Control de acceso basado en rol

Para crear una solicitud de soporte técnico, debe ser un administrador o tener asignado el rol de [colaborador de la solicitud de soporte técnico](../../role-based-access-control/built-in-roles.md#support-request-contributor).

### <a name="go-to-help--support-from-the-global-header"></a>Ir a Ayuda y soporte técnico en el encabezado global

Para iniciar una solicitud de soporte técnico desde cualquier lugar de Azure Portal:

1. Seleccione el signo **?** en el encabezado global. Luego seleccione **Ayuda y soporte técnico**.

   ![Ayuda y soporte técnico](./media/how-to-create-azure-support-request/helpandsupportnewlower.png)

2. Seleccione **+ Nueva solicitud de soporte técnico**. Siga las indicaciones para proporcionarnos la información sobre el problema. Sugeriremos algunas soluciones posibles, recopilaremos los detalles del problema y le ayudaremos a enviar y realizar el seguimiento de la solicitud de soporte técnico.

   ![Nueva solicitud de soporte técnico](./media/how-to-create-azure-support-request/newsupportrequest2lower.png)

### <a name="go-to-help--support-from-a-resource-menu"></a>Ir a Ayuda y soporte técnico en un menú de recursos

Para iniciar una solicitud de soporte técnico en el contexto del recurso con el que está trabajando actualmente:

1. En el menú de recursos, en la sección **Soporte técnico y solución de problemas**, seleccione **Nueva solicitud de soporte técnico**.

   ![En contexto](./media/how-to-create-azure-support-request/incontext2lower.png)

2. Siga las indicaciones para proporcionarnos la información sobre el problema que tiene. Al iniciar el proceso de solicitud de soporte técnico desde el recurso, algunas opciones se preseleccionan automáticamente.

## <a name="create-a-support-request"></a>Crear una solicitud de soporte

Le guiaremos a través de algunos pasos para recopilar información sobre el problema y ayudarle a resolverlo. Este paso se describe en las secciones siguientes.

### <a name="basics"></a>Aspectos básicos

En el primer paso del proceso de solicitud de soporte técnico se recopila información básica sobre el problema y el plan de soporte técnico.

En la pestaña **Datos básicos** de **Nueva solicitud de soporte técnico**, use los selectores para empezar a contarnos el problema. En primer lugar, identificará algunas categorías generales para el tipo de problema y elegirá la suscripción relacionada. El paso siguiente para obtener ayuda es la selección del servicio (por ejemplo, "Máquina virtual que ejecuta Windows") y el recurso (el nombre de la máquina virtual). Describa brevemente el problema con sus propias palabras y, a continuación, **Elija un tipo de problema** para obtener más información.

![Hoja Básico](./media/how-to-create-azure-support-request/basics2lower.png)

> [!NOTE]
> Azure proporciona soporte ilimitado para la administración de suscripciones (por ejemplo, facturación, ajustes de cuota y transferencias de cuenta). Para obtener soporte técnico, necesita un plan de soporte técnico. [Obtenga más información sobre los planes de soporte técnico](https://azure.microsoft.com/support/plans).
>
>

### <a name="solutions"></a>Soluciones

Después de recopilar la información básica, le mostraremos soluciones para que pueda probar por su cuenta. En algunos casos, es posible que incluso ejecutemos un diagnóstico rápido. Las soluciones están escritas por ingenieros de Azure y resolverán la mayoría de los problemas comunes.

### <a name="details"></a>Detalles

A continuación, recopilaremos información adicional sobre el problema. Proporcionar información exhaustiva y detallada en este paso nos ayuda a enrutar la solicitud de soporte técnico al agente adecuado y le permite empezar a diagnosticar el problema.

Es una buena idea informarnos de cuándo comenzó el problema y los pasos para reproducirlo. También puede optar por cargar un archivo, como un archivo de registro o una salida de diagnósticos.

Una vez que tengamos toda la información sobre el problema, puede elegir cómo obtener soporte técnico. En la sección **Método de soporte técnico** de la pestaña **Detalles**, seleccione la gravedad del impacto. Proporcione su método de contacto preferido, un buen momento para ponernos en contacto con usted y su idioma de soporte técnico.

A continuación, complete la sección **Información de contacto** para que sepamos cómo ponernos en contacto con usted.

### <a name="review--create"></a>Revisar y crear

Complete toda la información necesaria en cada pestaña y, a continuación, seleccione **Revisar y crear**. Compruebe los detalles que va a enviar al servicio de soporte técnico. Vuelva a cualquier pestaña para realizar un cambio si es necesario. Cuando esté satisfecho con la solicitud de soporte técnico, seleccione **Crear**.

Un agente de soporte técnico se pondrá en contacto con usted mediante el método que haya indicado. Consulte [Ámbito de soporte técnico y capacidad de respuesta](https://azure.microsoft.com/support/plans/response/) para obtener información sobre el tiempo de respuesta inicial.

## <a name="all-support-requests"></a>Todas las solicitudes de soporte técnico

Para ver los detalles y el estado de las solicitudes de soporte técnico, vaya a **Ayuda y soporte técnico** >  **Todas las solicitudes de soporte técnico**.

![Todas las solicitudes de soporte técnico](./media/how-to-create-azure-support-request/allrequestslower.png)

En esta página, puede filtrar las solicitudes de soporte técnico por suscripción, fecha de creación (UTC) y estado. Además, puede ordenar y realizar búsquedas en las solicitudes de soporte técnico en esta página.

Seleccione una solicitud de soporte técnico para ver los detalles, incluida la gravedad y el tiempo esperado que tardará en responder un agente de soporte técnico.

Si quiere cambiar la gravedad de la solicitud, seleccione **Impacto de negocio**. Se muestra una lista de los niveles de gravedad disponibles para asignar.

> [!NOTE]
> El nivel de gravedad máximo depende de su plan de soporte técnico. [Obtenga más información sobre los planes de soporte técnico](https://azure.microsoft.com/support/plans).
>
>
Para obtener más información sobre las opciones de soporte técnico de autoayuda de Azure, vea este vídeo:

> [!VIDEO https://www.youtube.com/embed/gNhzR5FE9DY]

## <a name="next-steps"></a>Pasos siguientes
* [Enviarnos sus comentarios y sugerencias](https://feedback.azure.com/forums/266794-support-feedback)
* Ponerse en contacto con nosotros en [Twitter](https://twitter.com/azuresupport)
* Obtener ayuda de sus compañeros en los [foros de MSDN](https://social.msdn.microsoft.com/Forums/azure)
* Obtener más información en [Preguntas más frecuentes de soporte técnico de Azure](https://azure.microsoft.com/support/faq)
