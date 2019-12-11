---
title: Migración de aplicaciones al esquema más reciente
description: Cómo migrar las aplicaciones lógicas a la versión de esquema más reciente
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
ms.openlocfilehash: dc967c90f28b29a63a1f26d0aae86b6d769c1ccf
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/03/2019
ms.locfileid: "74786935"
---
# <a name="migrate-logic-apps-to-latest-schema-version"></a>Migración de aplicaciones lógicas a la versión de esquema más reciente

Para mover las aplicaciones lógicas existentes al esquema más reciente, siga estos pasos: 

1. En [Azure Portal](https://portal.azure.com), abra la aplicación lógica en Diseñador de aplicación lógica.

2. En el menú de la aplicación lógica, elija **Información general**. En la barra de herramientas, elija **Actualizar esquema**.

   > [!NOTE]
   > Cuando elige **Actualizar esquema**, Azure Logic Apps ejecuta los pasos de migración y proporciona el código de salida automáticamente. Puede usar esta salida para actualizar la definición de aplicación lógica. Sin embargo, asegúrese de seguir los procedimientos recomendados que se describen en la siguiente sección **Procedimientos recomendados**.

   ![Actualizar esquema](./media/connectors-schema-migration/update-schema.png)

   La página Actualizar esquema aparece y muestra un vínculo a un documento que describe las mejoras en el nuevo esquema.

## <a name="best-practices"></a>Procedimientos recomendados

Estos son algunos procedimientos recomendados para migrar las aplicaciones lógicas a la versión de esquema más reciente:

* Copie el script migrado en una nueva aplicación lógica. No sobrescriba la versión anterior hasta que finalice las pruebas y confirme que la aplicación migrada funciona según lo previsto.

* Pruebe la aplicación lógica **antes** de pasarla a producción

* Una vez finalizada la migración, comience a actualizar las aplicaciones lógicas para usar las [API administradas](../connectors/apis-list.md) siempre que sea posible. Por ejemplo, comience a usar Dropbox v2 en cualquier lugar que use DropBox v1.

## <a name="next-steps"></a>Pasos siguientes

* Aprenda a [migrar manualmente las aplicaciones lógicas](../logic-apps/logic-apps-schema-2015-08-01.md).
