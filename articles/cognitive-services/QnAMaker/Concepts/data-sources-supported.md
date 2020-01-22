---
title: 'Orígenes de datos compatibles: QnA Maker'
titleSuffix: Azure Cognitive Services
description: QnA Maker extrae automáticamente los pares de pregunta y respuesta almacenados como páginas web, archivos PDF, archivos de documento de MS Word o archivos de contenido de QnA estructurados.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 01/09/2020
ms.author: diberry
ms.openlocfilehash: 2978ffa68814d176ea1caf485e7e4f1ba72f2597
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/10/2020
ms.locfileid: "75867570"
---
# <a name="data-sources-for-qna-maker-content"></a>Orígenes de datos para el contenido de QnA Maker

QnA Maker extrae automáticamente pares de preguntas y respuestas del contenido semiestructurado, como preguntas más frecuentes, manuales de productos, directrices, documentos de soporte técnico y directivas almacenadas como páginas web, archivos PDF o archivos de documento (DOC) de MS Word. El contenido también se puede agregar a la base de conocimiento de archivos de contenido de preguntas y respuestas estructurados.

<a name="data-types"></a>

## <a name="file-and-url-data-types"></a>Tipos de datos de archivo y URL

En la tabla siguiente se resumen los tipos de contenido y los formatos de archivo que son compatibles con QnA Maker.

|Tipo de origen|Tipo de contenido| Ejemplos|
|--|--|--|
|URL|Preguntas más frecuentes<br> (sin formato, con secciones o con una página principal de temas)<br>Páginas de soporte técnico <br> (artículos paso a paso de una sola página, artículos de solución de problemas, etc.)|[Preguntas más frecuentes sin formato](https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs), <br>[preguntas más frecuentes con vínculos](https://www.microsoft.com/en-us/software-download/faq),<br> [página principal de preguntas más frecuentes por temas](https://www.microsoft.com/Licensing/servicecenter/Help/Faq.aspx)<br>[artículo de soporte técnico](https://docs.microsoft.com/azure/cognitive-services/qnamaker/concepts/best-practices)|
|PDF/DOC|Preguntas más frecuentes,<br> manual de producto,<br> folletos,<br> documento,<br> directiva de folleto,<br> guía de soporte técnico,<br> archivo de preguntas y respuestas estructurado,<br> etc.|[Archivo de preguntas y respuestas estructurado.doc](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/structured.docx),<br> [Manual de producto de ejemplo.pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf),<br> [Archivo semiestructurado de ejemplo.doc](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/semi-structured.docx),<br> [Notas del producto de ejemplo.pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/white-paper.pdf),<br>[Ejemplo de Multi-turn.docx](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)|
|*Excel|Archivo de preguntas y respuestas estructurado<br> (incluye compatibilidad con RTF y HTML)|[Preguntas más frecuentes de preguntas y respuestas de ejemplo.xls](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/QnA%20Maker%20Sample%20FAQ.xlsx)|
|*TXT/TSV|Archivo de preguntas y respuestas estructurado|[Charla de ejemplo.tsv](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Scenario_Responses_Friendly.tsv)|

### <a name="import-and-export-knowledge-base"></a>Importación y exportación de la base de conocimiento

**Los archivos TSV y XLS**, de las bases de conocimiento exportadas, solo se pueden usar importando los archivos de la página **Configuración** en el portal QnA Maker. No se pueden usar como orígenes de datos durante la creación de la base de conocimiento ni desde las funciones **+ Agregar archivo** o **+ Agregar URL** de la página **Configuración**.

## <a name="data-source-locations"></a>Ubicaciones de orígenes de datos

Las ubicaciones de origen de datos son **direcciones URL o archivos públicos**, que no requieren autenticación.

Si necesita autenticación para el origen de datos, tenga en cuenta los siguientes métodos para introducir esos datos en QnA Maker:

* [Descargar manualmente el archivo](#download-file-from-authenticated-data-source-location) e importarlo en QnA Maker
* Importar el archivo de la [ubicación de SharePoint](#import-file-from-authenticated-sharepoint) autenticada

### <a name="download-file-from-authenticated-data-source-location"></a>Descarga del archivo de la ubicación del origen de datos autenticada

Si tiene un archivo o una dirección URL autenticados (no una ubicación de SharePoint autenticada), una opción alternativa es descargar el archivo del sitio autenticado en el equipo local y luego agregarlo desde el equipo local hasta la base de conocimiento.

### <a name="import-file-from-authenticated-sharepoint"></a>Importación del archivo de SharePoint autenticado

Las [ubicaciones de origen de datos de SharePoint](../How-to/add-sharepoint-datasources.md) tienen permiso para proporcionar **archivos** autenticados. Los recursos de SharePoint deben ser archivos, no páginas web. Si la dirección URL finaliza con una extensión web, como **.ASPX**, no se importará en QnA Maker desde SharePoint.


## <a name="faq-urls"></a>Preguntas más frecuentes sobre las direcciones URL

QnA Maker puede admitir páginas web de preguntas más frecuentes de 3 formas diferentes: Páginas de preguntas más frecuentes sin formato, páginas de preguntas más frecuentes con vínculos, páginas de preguntas más frecuentes con una página principal de temas.

### <a name="plain-faq-pages"></a>Páginas de preguntas más frecuentes sin formato

Este es el tipo más común de página de preguntas más frecuentes, en la que las respuestas siguen inmediatamente las preguntas de la misma página.

Aquí tiene un ejemplo de una página de preguntas más frecuentes sin formato:

![Ejemplo de página de preguntas más frecuentes sin formato para una base de conocimiento](../media/qnamaker-concepts-datasources/plain-faq.png)


### <a name="faq-pages-with-links"></a>Páginas de preguntas más frecuentes con vínculos

En este tipo de página de preguntas más frecuentes, las preguntas se agregan juntas y se vinculan a respuestas que están en secciones diferentes de la misma página o en páginas distintas.

A continuación se muestra un ejemplo de una página de preguntas más frecuentes con vínculos en las secciones que se encuentran en la misma página:

 ![Ejemplo de página de preguntas más frecuentes con sección de vínculos para una base de conocimiento](../media/qnamaker-concepts-datasources/sectionlink-faq.png)


### <a name="faq-pages-with-a-topics-homepage"></a>Páginas de preguntas más frecuentes con una página principal de temas

Este tipo de preguntas más frecuentes tiene una página principal con los temas, donde cada tema es un vínculo a su pregunta y respuesta relevante en una página diferente. Aquí, QnA Maker rastrea todas las páginas vinculadas para extraer las preguntas y respuestas correspondientes.

A continuación se muestra un ejemplo de una página de preguntas más frecuentes en la cual una página principal de temas tiene vínculos a secciones de preguntas más frecuentes en páginas distintas.

 ![Ejemplo de página de preguntas más frecuentes con vínculos profundos para una base de conocimiento](../media/qnamaker-concepts-datasources/topics-faq.png)


### <a name="support-urls"></a>Direcciones URL de soporte técnico

QnA Maker puede procesar páginas web de soporte semiestructuradas, como artículos web que describan cómo realizar una tarea determinada, cómo diagnosticar y resolver un problema determinado y cuáles son las mejores prácticas para un determinado proceso. La extracción funciona mejor en contenido con una estructura clara y con encabezados jerárquicos.

> [!NOTE]
> La extracción para artículos de soporte técnico es una característica nueva que se encuentra en sus primeras etapas. Funciona mejor con páginas simples, que están bien estructuradas y no contienen encabezados ni pies de página complejos.

![QnA Maker admite la extracción en páginas web semiestructuradas con una estructura clara y con encabezados jerárquicos.](../media/qnamaker-concepts-datasources/support-web-pages-with-heirarchical-structure.png)


## <a name="pdf-doc-files"></a>Archivos PDF/DOC

QnA Maker puede procesar contenido semiestructurado en un archivo PDF o de documento (DOC) y convertirlo en preguntas y respuestas. Un buen archivo que pueda extraerse correctamente es aquel en el cual el contenido está organizado de alguna forma estructurada y se representa en secciones bien definidas. Las secciones pueden dividirse adicionalmente en subsecciones o subtemas. La extracción funciona mejor en documentos que tienen una estructura clara con encabezados jerárquicos.

QnA Maker identifican las secciones y subsecciones, así como las relaciones, en el archivo según las indicaciones visuales, como tamaño de fuente, estilo de fuente, numeración, colores, etc. Archivos PDF o de documento (DOC) semiestructurados podrían ser manuales, preguntas más frecuentes, instrucciones, directivas, folletos, prospectos y muchos otros tipos de archivos. A continuación se muestran algunos tipos de ejemplos de estos archivos.

### <a name="product-manuals"></a>Manuales de productos

Un manual suele ser material de orientación que acompaña a un producto. Ayuda al usuario a configurar, usar y mantener el producto y a solucionar problemas con el mismo. Cuando QnA Maker procesa un manual, extrae los títulos y subtítulos como preguntas y el contenido ulterior como respuestas. Consulte un ejemplo [aquí](https://download.microsoft.com/download/2/9/B/29B20383-302C-4517-A006-B0186F04BE28/surface-pro-4-user-guide-EN.pdf).

A continuación se muestra un ejemplo de un manual con una página de índice y contenido jerárquico

 ![Ejemplo de manual del producto para una base de conocimiento](../media/qnamaker-concepts-datasources/product-manual.png)

> [!NOTE]
> La extracción funciona mejor en manuales que tienen una tabla de contenido o una página de índice y una estructura clara con títulos jerárquicos.

### <a name="brochures-guidelines-papers-and-other-files"></a>Folletos, instrucciones, documentos y otros archivos

También se pueden procesar muchos otros tipos de documentos para generar pares de preguntas y respuestas, siempre que tengan una estructura y un diseño claros. Entre ellas se incluyen las siguientes: folletos, instrucciones, informes, documentos técnicos, documentos científicos, directivas, libros, etc. Consulte un ejemplo [aquí](https://qnamakerstore.blob.core.windows.net/qnamakerdata/docs/Manage%20Azure%20Blob%20Storage.docx).

A continuación se muestra un ejemplo de documento semiestructurado, sin un índice:

 ![Documento semiestructurado de Azure Blob Storage](../media/qnamaker-concepts-datasources/semi-structured-doc.png)

### <a name="structured-qna-document"></a>Documento de preguntas y respuestas estructurado

El formato de tipo pregunta-respuestas estructurado en los archivos de documento (DOC) consiste en alternar preguntas y respuestas por línea, una pregunta por línea seguida de su respuesta en la línea siguiente, tal como se muestra a continuación:

```text
Question1

Answer1

Question2

Answer2
```

A continuación se muestra un ejemplo de un documento de Word de preguntas y respuestas estructurado:

 ![Ejemplo de documento de QnA estructurado para una base de conocimiento](../media/qnamaker-concepts-datasources/structured-qna-doc.png)

## <a name="structured-txt-tsv-and-xls-files"></a>Archivos *TXT*, *TSV* y *XLS* estructurados

Las preguntas y respuestas en forma de archivos *.txt*, *.tsv* o *.xls* estructurados también se pueden cargar en QnA Maker para crear o ampliar una base de conocimiento.  Pueden ser texto sin formato o pueden tener contenido en formato RTF o HTML.

| Pregunta  | Respuesta  | Metadatos (1 clave: 1 valor) |
|-----------|---------|-------------------------|
| Pregunta1 | Respuesta1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Pregunta2 | Respuesta2 |      `Key:Value`           |

Se ignoran las columnas adicionales del archivo de origen.

### <a name="example-of-structured-excel-file"></a>Ejemplo de archivo de Excel estructurado

A continuación se muestra un ejemplo de archivo *.xls* de preguntas y respuestas estructurado, con contenido HTML:

 ![Ejemplo de libro Excel de QnA estructurado para una base de conocimiento](../media/qnamaker-concepts-datasources/structured-qna-xls.png)

### <a name="example-of-alternate-questions-for-single-answer-in-excel-file"></a>Ejemplo de preguntas alternativas para una sola respuesta en un archivo de Excel

A continuación se muestra un ejemplo de un archivo *.xls* de QnA estructurado, con varias preguntas alternativas para una sola respuesta:

 ![Ejemplo de preguntas alternativas para una sola respuesta en un archivo de Excel](../media/qnamaker-concepts-datasources/xls-alternate-question-example.png)

Una vez importado el archivo, el par pregunta-respuesta se encuentra en la base de conocimiento, como se muestra a continuación:

 ![Captura de pantalla de preguntas alternativas para una sola respuesta importada en la base de conocimiento](../media/qnamaker-concepts-datasources/xls-alternate-question-example-after-import.png)

## <a name="structured-data-format-through-import"></a>Formato de datos estructurado mediante importación

Al importar una base de conocimiento, se reemplaza el contenido de la base de conocimiento existente. La importación requiere un archivo .tsv estructurado que contiene información del origen de datos. Esta información ayuda a QnA Maker a agrupar los pares pregunta-respuesta y a atribuirlos a un origen de datos particular.

| Pregunta  | Respuesta  | Source| Metadatos (1 clave: 1 valor) |
|-----------|---------|----|---------------------|
| Pregunta1 | Respuesta1 | URL1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Pregunta2 | Respuesta2 | Editorial|    `Key:Value`       |

## <a name="editorially-add-to-knowledge-base"></a>Adición en modo editorial a la base de conocimiento

Si no tiene contenido existente previamente para rellenar la base de conocimiento, puede agregar preguntas y respuestas a nivel editorial en la base de conocimiento de QnA Maker. Consulte [aquí](../How-To/edit-knowledge-base.md) cómo actualizar la base de conocimiento.

<a href="#formatting-considerations"></a>

## <a name="formatting-considerations"></a>Consideraciones de formato

Después de importar un archivo o una dirección URL, QnA Maker convierte y almacena el contenido en [formato de Markdown](https://en.wikipedia.org/wiki/Markdown). El proceso de conversión agrega nuevas líneas al texto, como `\n\n`. Conocer el formato de Markdown le ayuda a comprender el contenido convertido y a administrar el contenido de la base de conocimiento.

Si agrega o edita el contenido directamente en la base de conocimiento, use el **formato de Markdown** para crear contenido de texto enriquecido o cambiar el contenido de dicho formato que ya está en la respuesta. QnA Maker admite gran parte del formato de Markdown para aportar funcionalidades de texto enriquecido al contenido. Sin embargo, es posible que la aplicación cliente, como un bot de chat, no admita el mismo conjunto de formatos de Markdown. Es importante probar la presentación de las respuestas de la aplicación cliente.

Para obtener más información, consulte los ejemplos de Markdown proporcionados en la [referencia de Markdown de QnA Maker](../reference-markdown-format.md).

## <a name="editing-your-knowledge-base-locally"></a>Edición de la base de conocimiento localmente

Una vez que se crea una base de conocimiento, es recomendable realizar modificaciones en el texto de dicha base en el [portal de QnA Maker](https://qnamaker.ai), en lugar de exportar y volver a importar a través de archivos locales. Sin embargo, puede haber ocasiones en las que necesite editar una base de conocimiento localmente.

Exporte la base de conocimiento desde la página **Configuración** y luego edite dicha base con Microsoft Excel. Si decide utilizar otra aplicación para editar el archivo TSV exportado, la aplicación puede introducir errores de sintaxis porque no es totalmente compatible con TSV. Por lo general, los archivos TSV de Microsoft Excel no introducen errores de formato.

Cuando termine de realizar las modificaciones, vuelva a importar el archivo TSV desde la página **Configuración**. Esto reemplazará completamente la base de conocimiento actual por la base de conocimiento importada.

## <a name="testing-your-markdown"></a>Prueba de Markdown

Use el tutorial **[CommonMark](https://commonmark.org/help/tutorial/index.html)** para validar su Markdown. El tutorial tiene una característica **Pruébelo** para la validación rápida de copiar y pegar.

## <a name="version-control-for-data-in-your-knowledge-base"></a>Control de versiones para datos en su base de conocimiento

El control de versión para los datos se proporciona a través de la [función de importación/exportación](development-lifecycle-knowledge-base.md#version-control-of-a-knowledge-base) en la página **Configuración**.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Configurar un servicio QnA Maker](../How-To/set-up-qnamaker-service-azure.md)

## <a name="see-also"></a>Consulte también

[Introducción de QnA Maker](../Overview/overview.md)
