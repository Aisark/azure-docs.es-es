---
title: 'Advanced Threat Protection: Azure Database for PostgreSQL'
description: Protección contra amenazas detecta actividades anómalas en la base de datos que indican posibles amenazas de seguridad.
author: bolzmj
ms.author: mbolz
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/24/2019
ms.openlocfilehash: bbb48708d0e5a7cd07a3971a6966f40696107562
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "60421336"
---
# <a name="advanced-threat-protection-for-azure-database-for-postgresql"></a>Advanced Threat Protection para Azure Database for PostgreSQL

Advanced Threat Protection para Azure Database for PostgreSQL detecta actividades anómalas que indican intentos inusuales y potencialmente dañinos de acceso o ataque a las bases de datos.

Advanced Threat Protection forma parte de la oferta Seguridad de datos avanzada, que es un paquete unificado para capacidades avanzadas de seguridad de SQL. Se puede obtener acceso a Advanced Threat Protection y administrarlo a través de [Azure Portal](https://portal.azure.com) y está actualmente en versión preliminar.

> [!NOTE]
> La característica Advanced Threat Protection **no** está disponible en las siguientes regiones de nube soberana y Azure Government: US Gov Texas, US Gov Arizona, US Gov Iowa, US Gov Virginia, US DoD (este), US DoD (centro), Centro de Alemania, Norte de Alemania, Este de China y Este de China 2. Visite [productos disponibles por región](https://azure.microsoft.com/global-infrastructure/services/) para obtener información de disponibilidad general del producto.
>

> [!NOTE]
> Esta característica está disponible en todas las regiones de Azure donde se implementa Azure Database for PostgreSQL para servidores de uso general y optimizados para memoria.

## <a name="set-up-threat-detection"></a>Configurar la detección de amenazas
1. Inicie Azure Portal en [https://portal.azure.com](https://portal.azure.com).
2. Vaya a la página de configuración del servidor de Azure Database for PostgreSQL que quiere proteger. En la configuración de seguridad, seleccione **Advanced Threat Protection (versión preliminar)**.
3. En la página de configuración de **Advanced Threat Protection (versión preliminar)**:

   - Habilite Protección contra amenazas avanzada en el servidor.
   - En **Configuración de Advanced Threat Protection**, en el cuadro de texto **Enviar alertas a**, proporcione la lista de correos electrónicos para recibir alertas de seguridad cuando se detecten actividades anómalas en la base de datos.
  
   ![Configurar la detección de amenazas](./media/howto-database-threat-protection-portal/set-up-threat-protection.png)

## <a name="explore-anomalous-database-activities"></a>Exploración de las actividades anómalas de las bases de datos

Cuando se detecten actividades anómalas en las bases de datos, recibirá una notificación por correo electrónico. El correo electrónico proporciona información sobre el evento de seguridad sospechoso, en la que se incluyen la naturaleza de las actividades anómalas, el nombre de la base de datos, el nombre del servidor, el nombre de la aplicación y la hora del evento. Además, el correo electrónico proporciona información sobre las posibles causas y las medidas recomendadas para investigar y mitigar la amenaza potencial para la base de datos.
    
1. Haga clic en el vínculo **Ver las alertas recientes** del correo electrónico para iniciar Azure Portal y mostrar la página de alertas de Azure Security Center, que proporciona información general sobre amenazas activas detectadas en la base de datos de SQL.
    
    ![Informes de actividades anómalas](./media/howto-database-threat-protection-portal/anomalous-activity-report.png)

    Ver amenazas activas:

    ![Amenazas activas](./media/howto-database-threat-protection-portal/active-threats.png)

2. Haga clic en una alerta específica para obtener detalles y acciones adicionales para investigar esta amenaza y solucionar amenazas futuras.
    
    ![Alerta específica](./media/howto-database-threat-protection-portal/specific-alert.png)

## <a name="explore-threat-detection-alerts"></a>Explorar alertas de detección de amenazas

Advanced Threat Protection integra sus alerta con [Azure Security Center](https://azure.microsoft.com/services/security-center/). 

Haga clic en **Alertas de seguridad** en **PROTECCIÓN CONTRA AMENAZAS** para iniciar la página de alertas de Azure Security Center y obtener información general de las amenazas de SQL activas detectadas en la base de datos.

  ![Protección contra amenazas](./media/howto-database-threat-protection-portal/threat-detection-alert-asc.png)

## <a name="next-steps"></a>Pasos siguientes

* Más información acerca de [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
* Para obtener más información sobre los precios, vea la [página Precios de Azure Database for PostgreSQL](https://azure.microsoft.com/pricing/details/postgresql/)  
