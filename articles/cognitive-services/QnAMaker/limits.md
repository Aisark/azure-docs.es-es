---
title: 'Límites: QnA Maker'
description: QnA Maker tiene meta-límites para partes de la base de conocimiento y el servicio. Es importante mantener la base de conocimiento dentro de esos límites para probar y publicar.
ms.topic: article
ms.date: 01/23/2020
ms.openlocfilehash: 32e12d9e983795d2fb403a5b6bc304289ece92c2
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/26/2020
ms.locfileid: "76760239"
---
# <a name="qna-maker-knowledge-base-limits-and-boundaries"></a>Límites de la base de conocimiento de QnA Maker

Los límites de QnA Maker que se proporcionan a continuación son una combinación de los [límites del plan de tarifa de Azure Cognitive Search](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity) y los [límites del plan de tarifa de QnA Maker](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/). Debe conocer ambos conjuntos de límites para comprender el número de bases de conocimiento que puede crear por recurso y el tamaño que puede alcanzar cada base de conocimiento.

## <a name="knowledge-bases"></a>Bases de conocimiento

El número máximo de bases de conocimiento se basa en los [límites de plan de Azure Cognitive Search](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity).

|**Nivel de Azure Cognitive Search** | **Gratis** | **Basic** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|Número máximo permitido de bases de conocimiento publicadas|2|14|49|199|199|2999|

 Por ejemplo, si el nivel tiene 15 índices permitidos, puede publicar 14 bases de conocimiento (1 índice por base de conocimiento publicada). El índice decimoquinto, `testkb`, se usa con todas las bases de conocimiento para creación y prueba.

## <a name="extraction-limits"></a>Límites de extracción

### <a name="file-naming-constraints"></a>Restricciones de nomenclatura de los archivos

Los nombres de archivo no pueden incluir los siguientes caracteres:

|No use el carácter|
|--|
|Comillas simples `'`|
|Comillas dobles `"`|

### <a name="maximum-file-size"></a>Tamaño de archivo máximo

|Formato|Tamaño máximo de archivo (MB)|
|--|--|
|`.docx`|10|
|`.pdf`|25|
|`.tsv`|10|
|`.txt`|10|
|`.xlsx`|3|

### <a name="maximum-number-of-files"></a>Número máximo de archivos

El número máximo de archivos que se pueden extraer y tamaño máximo del archivo se basa en los **[límites del plan de tarifa de QnA Maker](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/)** .

### <a name="maximum-number-of-deep-links-from-url"></a>Número máximo de vínculos profundos de la dirección URL

El número máximo de vínculos profundos que se pueden rastrear para la extracción de QnA desde una páginas URL es **20**.

## <a name="metadata-limits"></a>Límites de metadatos

### <a name="by-azure-cognitive-search-pricing-tier"></a>Por plan de tarifa de Azure Cognitive Search

El número máximo de campos de metadatos por base de conocimiento se basa en los **[límites de plan de Azure Cognitive Search](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity)** .

|**Nivel de Azure Cognitive Search** | **Gratis** | **Basic** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|Número máximo de metadatos por servicio QnA Maker (en todas las KB)|1,000|100*|1,000|1,000|1,000|1,000|

### <a name="by-name-and-value"></a>Por nombre y valor

La longitud y los caracteres aceptables para el nombre y el valor de los metadatos se muestran en la tabla siguiente.

|Elemento|Caracteres permitidos|Coincidencia de patrón regex|Número máximo de caracteres|
|--|--|--|--|
|Nombre|Permite<br>alfanuméricos (letras y dígitos)<br>`_` (subrayado)|`^[a-zA-Z0-9_]+$`|100|
|Value|Permite todo excepto<br>`:` (dos puntos)<br>`|` (barra vertical)|`^[^:|]+$`|500|
|||||

## <a name="knowledge-base-content-limits"></a>Límites de contenido de la base de conocimiento
Límites generales del contenido de la base de conocimiento:
* Longitud del texto de la respuesta: 25 000
* Longitud del texto de la pregunta: 1,000
* Longitud del texto de clave y valor de metadatos: 100
* Caracteres admitidos para el nombre de metadatos: Alfabéticos, dígitos y `_`
* Caracteres admitidos para el valor de metadatos: Todos excepto `:` y `|`
* Longitud del nombre de archivo: 200
* Formatos de archivo admitidos: ".tsv", ".pdf", ".txt", ".docx", ".xlsx".
* Número máximo de preguntas alternativas: 300
* Número máximo de pares de preguntas y respuestas: en función del **[nivel de Azure Cognitive Search](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity#document-limits)** elegido. Un par de pregunta y respuesta se asigna a un documento en el índice de Azure Cognitive Search.
* Página HTML/URL: 1 millón de caracteres

## <a name="create-knowledge-base-call-limits"></a>Límites de llamada de creación de la base de conocimiento:
Representan los límites para cada acción de creación de base de conocimiento; es decir, al hacer clic en *Crear una base de conocimiento* o al llamar a la API CreateKnowledgeBase.
* Número máximo de preguntas alternativas por respuesta: 300
* Número máximo de direcciones URL: 10
* Número máximo de archivos: 10

## <a name="update-knowledge-base-call-limits"></a>Límites de llamadas de actualización de la base de conocimiento
Representan los límites para cada acción de actualización; es decir, al hacer clic en *Save and train* (Guardar y entrenar) o al llamar a la API UpdateKnowledgeBase.
* Longitud de cada nombre de origen: 300
* Número máximo de preguntas alternativas agregadas o eliminadas: 300
* Número máximo de campos de metadatos agregados o eliminados: 10
* Número máximo de direcciones URL que se pueden actualizar: 5

## <a name="next-steps"></a>Pasos siguientes

Aprenda cuándo y cómo cambiar los [planes de tarifa de servicio](How-To/set-up-qnamaker-service-azure.md#upgrade-qna-maker).
