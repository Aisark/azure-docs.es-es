---
title: Configuración de alertas y notificaciones mediante Azure Portal | Microsoft Docs
description: Use Azure Portal para crear alertas de SQL Database, que pueden desencadenar notificaciones o automatización cuando se cumplen las condiciones que ha especificado.
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: jrasnik, carlrab
manager: craigg
ms.date: 11/02/2018
ms.openlocfilehash: 93337e39a117c1f8d38f24dc416ff8ae95513a34
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "61036068"
---
# <a name="create-alerts-for-azure-sql-database-and-data-warehouse-using-azure-portal"></a>Creación de alertas para Azure SQL Database y Data Warehouse con Azure Portal

## <a name="overview"></a>Información general
En este artículo se muestra cómo configurar alertas de Azure SQL Database y Data Warehouse con Azure Portal. Las alertas pueden enviarle un correo electrónico o llamar a un webhook cuando alguna métrica (por ejemplo, el tamaño de la base de datos o el uso de la CPU) alcanza el umbral. En este artículo también se indican procedimientos recomendados para establecer periodos de alerta.    

> [!IMPORTANT]
> Esta característica todavía no está disponible en la Instancia administrada. Como alternativa, puede utilizar el agente de SQL para enviar alertas por correo electrónico para algunas métricas basadas en [vistas de administración dinámica](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views).

Puede recibir una alerta basada en las métricas de supervisión para los servicios de Azure o los eventos sobre ellos.

* **Valores de métrica** : la alerta se desencadena cuando el valor de una métrica específica cruza un umbral asignado en cualquier dirección. Es decir, se desencadena tanto la primera vez que se cumple la condición como después, cuando dicha condición ya deja de cumplirse.    
* **Eventos de registro de actividades** : una alerta puede desencadenarse en *cada* evento o solo cuando se produce una serie de eventos.

Puede configurar una alerta para hacer lo siguiente cuando se desencadena:

* Enviar notificaciones de correo electrónico al administrador y los coadministradores del servicio.
* Enviar un correo electrónico a direcciones de correo electrónico adicionales que especifique.
* Llamar a un webhook.

Puede obtener información sobre las reglas de alerta y configurarlas mediante:

* [Azure Portal](../monitoring-and-diagnostics/insights-alerts-portal.md)
* [PowerShell](../azure-monitor/platform/alerts-classic-portal.md)
* [Interfaz de la línea de comandos (CLI)](../azure-monitor/platform/alerts-classic-portal.md)
* [API de REST de Azure Monitor](https://msdn.microsoft.com/library/azure/dn931945.aspx)

## <a name="create-an-alert-rule-on-a-metric-with-the-azure-portal"></a>Creación de una regla de alerta de una métrica con Azure Portal
1. En el [portal](https://portal.azure.com/), busque el recurso que desea supervisar y selecciónelo.
2. Seleccione **Alertas (clásico)** en la sección SUPERVISIÓN. El texto y el icono pueden variar ligeramente en los distintos recursos.  
   
     ![Supervisión](media/sql-database-insights-alerts-portal/AlertsClassicButton.JPG)
  
   - **SOLO SQL DW**: Haga clic en el gráfico **Uso de DWU**. Seleccione **Ver alertas clásicas**.

3. Seleccione el botón **Agregar alerta de métrica (clásica)** y rellene los campos.
   
    ![Agregar alerta](media/sql-database-insights-alerts-portal/AddDBAlertPageClassic.JPG)
4. Asígnele un **nombre** a la regla de alerta y elija una **descripción**, que también se muestra los correos electrónicos de notificación.
5. Seleccione la **métrica** que desea supervisar y elija un valor de **Condición** y **Umbral** para la métrica. También debe elegir el **período** de la regla de métrica que se debe cumplir antes de que se desencadene la alerta. Por ejemplo, si usa el período "PT5M" y la alerta busca una CPU por encima del 80 %, la alerta se desencadena cuando el **promedio** de CPU ha estado por encima del 80 % durante 5 minutos. Una vez que se desencadena por primera vez, se vuelve a desencadenar cuando el promedio de CPU está por debajo del 80 % durante más de 5 minutos. La CPU se mide cada 1 minuto. Consulte la tabla siguiente para ver las ventanas de tiempo compatibles y el tipo de agregación que usa cada alerta; no todas las alertas usan el valor promedio.   
6. Marque la opción **Enviar correo electrónico a propietarios...** si desea que se envíe un correo electrónico a los administradores y coadministradores cuando se active la alerta.
7. Si desea enviar una notificación a otras direcciones de correo electrónico cuando se active la alerta, agréguelas en el campo **Correos electrónicos de administradores adicionales** . Separe las direcciones de correo electrónico con punto y coma: *email\@contoso.com;email2\@contoso.com*.
8. Escriba un identificador URI válido en el campo **Webhook** si desea llamarlo cuando se active la alerta.
9. Seleccione **Aceptar** cuando termine para crear la alerta.   

En cuestión de minutos, se activa la alerta y se desencadena tal como se describió anteriormente.

## <a name="managing-your-alerts"></a>Administración de las alertas
Una vez que haya creado una alerta, puede seleccionarla y:

* Ver un gráfico que muestre el umbral de las métricas y los valores reales del día anterior.
* Editar la alerta o eliminarla.
* **Deshabilitar** la alerta, si desea dejar de recibir notificaciones de esa alerta de manera temporal, o **habilitarla** si desea reanudar sus notificaciones.


## <a name="sql-database-alert-values"></a>Valores de las alertas de SQL Database

| Tipo de recurso | Nombre de métrica | Nombre descriptivo | Tipo de agregación | Ventana de tiempo mínimo de la alerta|
| --- | --- | --- | --- | --- |
| Base de datos SQL | cpu_percent | Porcentaje de CPU | Media | 5 minutos |
| Base de datos SQL | physical_data_read_percent | Porcentaje de E/S de datos | Media | 5 minutos |
| Base de datos SQL | log_write_percent | Porcentaje de E/S de registro | Media | 5 minutos |
| Base de datos SQL | dtu_consumption_percent | Porcentaje de DTU | Media | 5 minutos |
| Base de datos SQL | storage | Tamaño total de base de datos | Máxima | 30 minutos |
| Base de datos SQL | connection_successful | Conexiones correctas | Total | 10 minutos |
| Base de datos SQL | connection_failed | Conexiones con errores | Total | 10 minutos |
| Base de datos SQL | blocked_by_firewall | Bloqueado por el firewall | Total | 10 minutos |
| Base de datos SQL | deadlock | Interbloqueos | Total | 10 minutos |
| Base de datos SQL | storage_percent | Porcentaje de tamaño de base de datos | Máxima | 30 minutos |
| Base de datos SQL | xtp_storage_percent | Porcentaje de almacenamiento de OLTP en memoria (versión preliminar) | Media | 5 minutos |
| Base de datos SQL | workers_percent | Porcentaje de trabajos | Media | 5 minutos |
| Base de datos SQL | sessions_percent | Porcentaje de sesiones | Media | 5 minutos |
| Base de datos SQL | dtu_limit | Límite de DTU | Media | 5 minutos |
| Base de datos SQL | dtu_used | DTU utilizada | Media | 5 minutos |
||||||
| Grupo elástico | cpu_percent | Porcentaje de CPU | Media | 10 minutos |
| Grupo elástico | physical_data_read_percent | Porcentaje de E/S de datos | Media | 10 minutos |
| Grupo elástico | log_write_percent | Porcentaje de E/S de registro | Media | 10 minutos |
| Grupo elástico | dtu_consumption_percent | Porcentaje de DTU | Media | 10 minutos |
| Grupo elástico | storage_percent | Porcentaje de almacenamiento | Media | 10 minutos |
| Grupo elástico | workers_percent | Porcentaje de trabajos | Media | 10 minutos |
| Grupo elástico | eDTU_limit | Límite de eDTU | Media | 10 minutos |
| Grupo elástico | storage_limit | Límite de almacenamiento | Media | 10 minutos |
| Grupo elástico | eDTU_used | eDTU utilizada | Media | 10 minutos |
| Grupo elástico | storage_used | Almacenamiento utilizado | Media | 10 minutos |
||||||               
| SQL Data Warehouse | cpu_percent | Porcentaje de CPU | Media | 10 minutos |
| SQL Data Warehouse | physical_data_read_percent | Porcentaje de E/S de datos | Media | 10 minutos |
| SQL Data Warehouse | connection_successful | Conexiones correctas | Total | 10 minutos |
| SQL Data Warehouse | connection_failed | Conexiones con errores | Total | 10 minutos |
| SQL Data Warehouse | blocked_by_firewall | Bloqueado por el firewall | Total | 10 minutos |
| SQL Data Warehouse | service_level_objective | Nivel de servicio de la base de datos | Total | 10 minutos |
| SQL Data Warehouse | dwu_limit | Límite de DWU | Máxima | 10 minutos |
| SQL Data Warehouse | dwu_consumption_percent | Porcentaje de DWU | Media | 10 minutos |
| SQL Data Warehouse | dwu_used | DWU utilizada | Media | 10 minutos |
||||||


## <a name="next-steps"></a>Pasos siguientes
* [Obtenga información general sobre la supervisión de Azure](../monitoring-and-diagnostics/monitoring-overview.md) , incluidos los tipos de información que puede recopilar y supervisar.
* Obtenga más información sobre cómo [configurar webhooks en las alertas](../azure-monitor/platform/alerts-webhooks.md).
* Obtenga [información general sobre los registros de diagnóstico](../azure-monitor/platform/diagnostic-logs-overview.md) para recopilar métricas detalladas de alta frecuencia sobre el servicio.
* Obtenga [información general sobre la colección de métricas](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) para garantizar que el servicio está disponible y que responder adecuadamente.
