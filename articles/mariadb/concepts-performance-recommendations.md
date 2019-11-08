---
title: Recomendaciones de rendimiento en Azure Database for MariaDB
description: En este artículo se describe la característica Recomendaciones de rendimiento en Azure Database for MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 1c903dcd9015de47c5364c5abd65569d17e7e21f
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2019
ms.locfileid: "73604134"
---
# <a name="performance-recommendations-in-azure-database-for-mariadb"></a>Recomendaciones de rendimiento en Azure Database for MariaDB

**Se aplica a:** Azure Database for MariaDB 10.2

La característica Recomendaciones de rendimiento analiza las bases de datos para crear sugerencias personalizadas para mejorar el rendimiento. Para generar las recomendaciones, el análisis examina diversas características de bases de datos, incluido el esquema. Habilite [Almacén de consultas](concepts-query-store.md) en el servidor para poder usar por completo la característica Recomendaciones de rendimiento. Si el esquema de rendimiento está desactivado, al activarlo en el Almacén de consultas se activarán performance_schema y un subconjunto de los instrumentos del esquema de rendimiento necesarios para la característica. Después de implementar una recomendación de rendimiento, debe probar el rendimiento para evaluar el efecto de estos cambios.

## <a name="permissions"></a>Permisos

Los permisos **Propietario** o **Colaborador** necesarios para ejecutar el análisis con la característica Recomendaciones de rendimiento.

## <a name="performance-recommendations"></a>Recomendaciones de rendimiento

La característica [Recomendaciones de rendimiento](concepts-performance-recommendations.md) analiza las cargas de trabajo en el servidor para identificar los índices con el potencial de mejorar el rendimiento.

Abra **Recomendaciones de rendimiento** desde la sección **Rendimiento inteligente** de la barra de menús en la página de Azure Portal para el servidor MariaDB.

![Página de inicio de Recomendaciones de rendimiento](./media/concepts-performance-recommendations/performance-recommendations-page.png)

Seleccione **Analizar** y elija una base de datos, lo que iniciará el análisis. Según la carga de trabajo, el análisis puede tardar varios minutos. Una vez que se realice el análisis, habrá una notificación en el portal. El análisis realiza un examen en profundidad de la base de datos. Se recomienda realizar el análisis durante los períodos de poca actividad.

La ventana **Recomendaciones** ahora mostrará una lista de las recomendaciones que se encontraron y el identificador de la consulta relacionada que generó cada recomendación. Con el identificador de la consulta, puede usar la vista [mysql.query_store](concepts-query-store.md#mysqlquery_store) para obtener más información sobre la consulta.

![Nueva página Recomendaciones de rendimiento](./media/concepts-performance-recommendations/performance-recommendations-result.png)

Las recomendaciones no se aplican automáticamente. Para aplicar la recomendación, copie el texto de la consulta y ejecútelo desde el cliente que prefiera. No olvide probar y supervisar para evaluar la recomendación.

## <a name="recommendation-types"></a>Tipos de recomendaciones

Actualmente, solo se admiten las recomendaciones de *Crear índice*.

### <a name="create-index-recommendations"></a>Recomendaciones para crear índice

Las recomendaciones de *Crear índice* sugieren nuevos índices para acelerar las consultas que se ejecutan con mayor frecuencia o que consumen mucho tiempo en la carga de trabajo. Este tipo de recomendación requiere que [Almacén de consultas](concepts-query-store.md) esté habilitado. Almacén de consultas recopila información de consultas y proporciona estadísticas detalladas de tiempo de ejecución y frecuencia de las consulta, que el análisis usa para hacer la recomendación.

## <a name="next-steps"></a>Pasos siguientes

- Más información sobre la [supervisión y el ajuste](concepts-monitoring.md) en Azure Database for MariaDB.