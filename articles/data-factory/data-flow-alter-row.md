---
title: Transformación Alterar fila en el flujo de datos de asignación
description: Cómo actualizar un destino de base de datos mediante la transformación Alterar fila en el flujo de datos de asignación
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/08/2020
ms.openlocfilehash: 0798a3f9ab45ce68086681e7aea96deeb9639f94
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/10/2020
ms.locfileid: "75834536"
---
# <a name="alter-row-transformation-in-mapping-data-flow"></a>Transformación Alterar fila en el flujo de datos de asignación

Use la transformación Alterar fila para establecer directivas de inserción, eliminación, actualización y upsert en las filas. Puede agregar las condiciones de uno a varios como expresiones. Estas condiciones se deben especificar en orden de prioridad, ya que cada fila se marcará con la directiva correspondiente a la primera expresión coincidente. Cada una de esas condiciones puede dar lugar a la inserción, actualización, eliminación o upsert de una fila (o filas). La transformación Alterar fila puede generar tanto acciones DDL como DML en la base de datos.

![Configuración de alteración de fila](media/data-flow/alter-row1.png "Configuración de alteración de fila")

Las transformaciones Alter Row (Alterar fila) solo funcionarán en los receptores de base de datos o CosmosDB del flujo de datos. Las acciones que asigne a las filas (inserción, actualización, eliminación, upsert) no se producirán durante las sesiones de depuración. Ejecute una actividad Ejecutar flujo de datos en una canalización para aplicar las directivas de Alterar fila en las tablas de base de datos.

## <a name="specify-a-default-row-policy"></a>Especificación de una directiva de fila predeterminada

Cree una transformación Alterar fila y especifique una directiva de fila con una condición de `true()`. Cada fila que no coincida con alguna de las expresiones definidas previamente se marcará para la directiva de fila especificada. De forma predeterminada, cada fila que no coincide con alguna expresión condicional se marcará para `Insert`.

![Directiva de alteración de fila](media/data-flow/alter-row4.png "Directiva de alteración de fila")

> [!NOTE]
> Para marcar todas las filas con una directiva, puede crear una condición para dicha directiva y especificar la condición como `true()`.

## <a name="view-policies-in-data-preview"></a>Visualización de directivas en la vista previa de datos

Use el [modo de depuración](concepts-data-flow-debug-mode.md) para ver los resultados de las directivas de Alterar fila en el panel Vista previa de datos. La vista previa de datos de una transformación Alterar fila no producirá acciones DDL o DML en el destino.

![Directivas de alteración de fila](media/data-flow/alter-row3.png "Directivas de alteración de fila")

Cada directiva de Alterar fila se representa con un icono que indica si se producirá una acción de inserción, actualización, upsert o eliminación. El encabezado superior muestra el número de filas afectadas por cada directiva en la vista previa.

## <a name="allow-alter-row-policies-in-sink"></a>Permitir directivas de Alterar fila en el receptor

Para que las directivas de Alterar fila funcionen, el flujo de datos debe escribir en una base de datos o en un receptor Cosmos. En la pestaña **Configuración** del receptor, habilite las directivas de Alterar fila que tiene permitidas.

![Receptor de alteración de fila](media/data-flow/alter-row2.png "Receptor de alteración de fila")

 El comportamiento predeterminado es permitir solamente las inserciones. Para permitir actualizaciones, upserts o eliminaciones, marque la casilla en el receptor correspondiente a esa condición. Si están habilitadas las actualizaciones, upserts o eliminaciones, debe especificar con qué columnas de clave del receptor deben coincidir.

> [!NOTE]
> Si las inserciones, actualizaciones u operaciones upsert modifican el esquema de la tabla de destino en el receptor, se producirá un error en el flujo de datos. Para modificar el esquema de destino en la base de datos, seleccione **Recreate table** (Volver a crear la tabla) como acción de la tabla. De esta forma se quita la tabla y se vuelve a crear con la nueva definición de esquema.

## <a name="data-flow-script"></a>Script de flujo de datos

### <a name="syntax"></a>Sintaxis

```
<incomingStream>
    alterRow(
           insertIf(<condition>?),
           updateIf(<condition>?),
           deleteIf(<condition>?),
           upsertIf(<condition>?),
        ) ~> <alterRowTransformationName>
```

### <a name="example"></a>Ejemplo

El ejemplo siguiente es una transformación Alterar fila denominada `CleanData` que toma un flujo entrante `SpecifyUpsertConditions` y crea tres condiciones de Alterar fila. En la transformación anterior, se calcula una columna denominada `alterRowCondition` que determina si se inserta, actualiza o elimina una fila en la base de datos. Si el valor de la columna tiene un valor de cadena que coincide con la regla de Alterar fila, se le asigna esa directiva.

En la experiencia de usuario de Data Factory, esta transformación es similar a la siguiente imagen:

![Ejemplo de alteración de fila](media/data-flow/alter-row4.png "Ejemplo de alteración de fila")

En el siguiente fragmento de código se muestra el script del flujo de datos para esta transformación:

```
SpecifyUpsertConditions alterRow(insertIf(alterRowCondition == 'insert'),
    updateIf(alterRowCondition == 'update'),
    deleteIf(alterRowCondition == 'delete')) ~> AlterRow
```

## <a name="next-steps"></a>Pasos siguientes

Después de la transformación Alterar fila, puede desear [recibir los datos en un almacén de datos de destino](data-flow-sink.md).
