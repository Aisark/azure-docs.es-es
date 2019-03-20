---
title: 'Análisis y procesamiento de documentos JSON con Apache Hive: Azure HDInsight'
description: Aprenda a utilizar documentos JSON y a analizarlos mediante Apache Hive en Azure HDInsight
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/27/2019
ms.author: hrasheed
ms.openlocfilehash: 31909d007727ca5b440343e3c5a035984399b77a
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/19/2019
ms.locfileid: "58201745"
---
# <a name="process-and-analyze-json-documents-by-using-apache-hive-in-azure-hdinsight"></a>Procesamiento y análisis de documentos JSON mediante Apache Hive en Azure HDInsight

Aprenda a procesar y analizar los archivos JavaScript Object Notation (JSON) con Apache Hive en Azure HDInsight. En este tutorial se usa el siguiente documento JSON:

```json
{
  "StudentId": "trgfg-5454-fdfdg-4346",
  "Grade": 7,
  "StudentDetails": [
    {
      "FirstName": "Peggy",
      "LastName": "Williams",
      "YearJoined": 2012
    }
  ],
  "StudentClassCollection": [
    {
      "ClassId": "89084343",
      "ClassParticipation": "Satisfied",
      "ClassParticipationRank": "High",
      "Score": 93,
      "PerformedActivity": false
    },
    {
      "ClassId": "78547522",
      "ClassParticipation": "NotSatisfied",
      "ClassParticipationRank": "None",
      "Score": 74,
      "PerformedActivity": false
    },
    {
      "ClassId": "78675563",
      "ClassParticipation": "Satisfied",
      "ClassParticipationRank": "Low",
      "Score": 83,
      "PerformedActivity": true
    }
  ]
}
```

El archivo puede encontrarse en **wasb://processjson\@hditutorialdata.blob.core.windows.net/**. Para obtener más información sobre cómo usar Azure Blob Storage con HDInsight, consulte el artículo sobre el [uso de Azure Blob Storage compatible con HDFS con Apache Hadoop en HDInsight](../hdinsight-hadoop-use-blob-storage.md). Puede copiar el archivo en el contenedor predeterminado del clúster.

En este tutorial, se utiliza la consola de Apache Hive. Para obtener instrucciones sobre cómo abrir la consola de Hive, consulte [Use Apache Ambari Hive View con Hadoop Apache en HDInsight](apache-hadoop-use-hive-ambari-view.md).

## <a name="flatten-json-documents"></a>Acoplamiento de documentos JSON
Los métodos enumerados en la siguiente sección requieren que el documento JSON conste de una sola fila. Por lo tanto, debe acoplar el documento JSON en una cadena. Si el documento JSON ya está acoplado, puede omitir este paso e ir directamente a la sección siguiente sobre el análisis de los datos JSON. Para acoplar el documento JSON, ejecute el script siguiente:

```sql
DROP TABLE IF EXISTS StudentsRaw;
CREATE EXTERNAL TABLE StudentsRaw (textcol string) STORED AS TEXTFILE LOCATION "wasb://processjson@hditutorialdata.blob.core.windows.net/";

DROP TABLE IF EXISTS StudentsOneLine;
CREATE EXTERNAL TABLE StudentsOneLine
(
  json_body string
)
STORED AS TEXTFILE LOCATION '/json/students';

INSERT OVERWRITE TABLE StudentsOneLine
SELECT CONCAT_WS(' ',COLLECT_LIST(textcol)) AS singlelineJSON
      FROM (SELECT INPUT__FILE__NAME,BLOCK__OFFSET__INSIDE__FILE, textcol FROM StudentsRaw DISTRIBUTE BY INPUT__FILE__NAME SORT BY BLOCK__OFFSET__INSIDE__FILE) x
      GROUP BY INPUT__FILE__NAME;

SELECT * FROM StudentsOneLine
```

Se encuentra en el archivo JSON sin formato **wasb://processjson\@hditutorialdata.blob.core.windows.net/**. La tabla de Hive **StudentsRaw** apunta al documento JSON sin formato y no acoplado.

La tabla de Hive **StudentsOneLine** almacena los datos en el sistema de archivos predeterminado de HDInsight en la ruta de acceso **/json/students/**.

La instrucción **INSERT** rellena la tabla **StudentOneLine** con los datos de JSON acoplados.

La instrucción **SELECT** solo devuelve una fila.

Este es el resultado de la instrucción **SELECT**:

![Acoplamiento del documento JSON][image-hdi-hivejson-flatten]

## <a name="analyze-json-documents-in-hive"></a>Análisis de documentos JSON en Hive
Hive proporciona tres mecanismos distintos para ejecutar consultas en documentos JSON, pero también puede escribir las suyas propias:

* Use la función definida por el usuario get_json_object.
* Use la función definida por el usuario json-tuple.
* Use el serializador/deserializador (SerDe) personalizado.
* Escriba su propia función definida por el usuario con Python u otros lenguajes. Para más información sobre cómo ejecutar su propio código Python con Hive, consulte el artículo sobre [las funciones definidas por el usuario de Python con Apache Hive y Apache Pig][hdinsight-python].

### <a name="use-the-getjsonobject-udf"></a>Uso de la función definida por el usuario get_json_object
Hive proporciona una función definida por el usuario integrada llamada [get_json_object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object), que realiza consultas JSON en tiempo de ejecución. Este método toma dos argumentos: el nombre de la tabla y el nombre del método que tiene el documento JSON acoplado y el campo JSON que debe analizarse. Veamos un ejemplo para ver cómo funciona esta UDF.

La siguiente consulta devuelve el nombre y el apellido de cada estudiante:

```sql
SELECT
  GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.FirstName'),
  GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.LastName')
FROM StudentsOneLine;
```

Este es el resultado cuando se ejecuta esta consulta en la ventana de la consola:

![UDF get_json_object][image-hdi-hivejson-getjsonobject]

La función definida por el usuario get-json_object tiene algunas limitaciones:

* Como cada campo de la consulta requiere volver a analizar la consulta, esto afecta al rendimiento.
* **GET\_JSON_OBJECT()** devuelve la representación de cadena de una matriz. Para convertir esta matriz en una de Hive, tiene que utilizar expresiones regulares para reemplazar los corchetes "[" y "]" y llamar también a split para obtener la matriz.

Este es el motivo por el que el sitio wiki de Hive recomienda json_tuple.  

### <a name="use-the-jsontuple-udf"></a>Uso de la función definida por el usuario json_tuple
Otra función definida por el usuario proporcionada por Hive se denomina [json_tuple](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-json_tuple), que es más eficaz que [get_ json _object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object). Este método toma un conjunto de claves y una cadena JSON, y devuelve una tupla de valores mediante una función. La siguiente consulta devuelve el identificador y el curso del estudiante del documento JSON:

```sql
SELECT q1.StudentId, q1.Grade
FROM StudentsOneLine jt
LATERAL VIEW JSON_TUPLE(jt.json_body, 'StudentId', 'Grade') q1
  AS StudentId, Grade;
```

Salida de este script en la consola de Hive:

![UDF json_tuple][image-hdi-hivejson-jsontuple]

La función definida por el usuariojson_tuple usa la sintaxis [lateral view](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+LateralView) de Hive, que permite que json\_tuple cree una tabla virtual mediante la aplicación la función de transferencia de datos uniforme a cada fila de la tabla original. Los JSON complejos se vuelven demasiado difíciles de manejar debido al uso repetido de **LATERAL VIEW**. Además, **JSON_TUPLE** no puede controlar los JSON anidados.

### <a name="use-a-custom-serde"></a>Uso de un SerDe personalizado
Un SerDe es la mejor opción para analizar documentos JSON anidados. Le permite definir el esquema JSON y utilizarlo para analizar los documentos. Para instrucciones, consulte [How to use a custom JSON SerDe with Microsoft Azure HDInsight](https://blogs.msdn.microsoft.com/bigdatasupport/2014/06/18/how-to-use-a-custom-json-serde-with-microsoft-azure-hdinsight/) (Uso de un SerDe personalizado para JSON con Microsoft Azure HDInsight).

## <a name="summary"></a>Resumen
En conclusión, el tipo de operador JSON en Hive que elija depende de su escenario. Si tiene un documento JSON sencillo y solo un campo por el que buscar, puede elegir usar la función definida por el usuario de Hive get_json_object. Si tiene varias claves por las que buscar, puede utilizar json_tuple. Si tiene un documento anidado, debe utilizar el SerDe de JSON.

## <a name="next-steps"></a>Pasos siguientes

Para artículos relacionados, consulte:

* [Uso de Apache Hive y HiveQL con Apache Hadoop en HDInsight para analizar un archivo de Apache Log4j de muestra](../hdinsight-use-hive.md)
* [Análisis de datos de retraso de vuelos con Apache Hive en HDInsight](../hdinsight-analyze-flight-delay-data-linux.md)
* [Análisis de datos de Twitter con Apache Hive en HDInsight](../hdinsight-analyze-twitter-data-linux.md)

[hdinsight-python]:python-udf-hdinsight.md

[image-hdi-hivejson-flatten]: ./media/using-json-in-hive/flatten.png
[image-hdi-hivejson-getjsonobject]: ./media/using-json-in-hive/getjsonobject.png
[image-hdi-hivejson-jsontuple]: ./media/using-json-in-hive/jsontuple.png
[image-hdi-hivejson-jdk]: ./media/hdinsight-using-json-in-hive/jdk.png
[image-hdi-hivejson-maven]: ./media/hdinsight-using-json-in-hive/maven.png
[image-hdi-hivejson-serde]: ./media/hdinsight-using-json-in-hive/serde.png
[image-hdi-hivejson-addjar]: ./media/hdinsight-using-json-in-hive/addjar.png
[image-hdi-hivejson-serde_query1]: ./media/hdinsight-using-json-in-hive/serde_query1.png
[image-hdi-hivejson-serde_query2]: ./media/hdinsight-using-json-in-hive/serde_query2.png
[image-hdi-hivejson-serde_query3]: ./media/hdinsight-using-json-in-hive/serde_query3.png
[image-hdi-hivejson-serde_result]: ./media/hdinsight-using-json-in-hive/serde_result.png

