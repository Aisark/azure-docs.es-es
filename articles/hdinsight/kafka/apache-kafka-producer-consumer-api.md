---
title: 'Tutorial: Uso de Producer API y Consumer API de Apache Kafka: Azure HDInsight '
description: Aprenda a usar Producer y Consumer API de Apache Kafka con Kafka en HDInsight. En este tutorial, aprenderá a usar estas API con Kafka en HDInsight desde una aplicación de Java.
author: dhgoelmsft
ms.author: dhgoel
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 04/02/2019
ms.openlocfilehash: 3dead1bdedb75a1b6fafb947da9c88094f0c4de9
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/28/2019
ms.locfileid: "64724158"
---
# <a name="tutorial-use-the-apache-kafka-producer-and-consumer-apis"></a>Tutorial: Uso de Producer API y Consumer API de Apache Kafka

Aprenda a usar Producer y Consumer API de Apache Kafka con Kafka en HDInsight.

Producer API de Kafka permite a las aplicaciones enviar flujos de datos al clúster de Kafka. Consumer API de Kafka permite a las aplicaciones leer flujos de datos del clúster.

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Requisitos previos
> * Comprendiendo el código
> * Compilar e implementar la aplicación
> * Ejecutar la aplicación en el clúster

Para más información acerca de las API, consulte la documentación de Apache relativa a [Producer API](https://kafka.apache.org/documentation/#producerapi) y a [Consumer API](https://kafka.apache.org/documentation/#consumerapi).

## <a name="prerequisites"></a>Requisitos previos

* Apache Kafka en HDInsight 3.6. Para aprender a crear un clúster de Kafka en HDInsight, consulte el documento [Inicio con Apache Kafka en HDInsight](apache-kafka-get-started.md).

* [Java Developer Kit (JDK) versión 8](https://aka.ms/azure-jdks), o un equivalente, como OpenJDK.

* [Apache Maven](https://maven.apache.org/download.cgi) correctamente [instalado](https://maven.apache.org/install.html) según Apache.  Maven es un sistema de compilación de proyectos de Java.

* Un cliente SSH. Para más información, consulte [Conexión a través de SSH con HDInsight (Apache Hadoop)](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="understand-the-code"></a>Comprendiendo el código

La aplicación de ejemplo se encuentra en [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started), en el subdirectorio `Producer-Consumer`. Básicamente, la aplicación consta de cuatro archivos:

* `pom.xml`: este archivo define las dependencias del proyecto, la versión de Java y los métodos de empaquetado.
* `Producer.java`: este archivo envía oraciones aleatorias a Kafka mediante Producer API.
* `Consumer.java`: este archivo usa Consumer API para leer datos de Kafka y emitirlos en STDOUT.
* `Run.java`: la interfaz de línea de comandos que se usa para ejecutar el código de Producer API y Consumer API.

### <a name="pomxml"></a>Pom.xml

Esto es lo más importante que hay que saber del archivo `pom.xml`:

* Dependencias: este proyecto utiliza Producer API y Consumer API de Kafka, ambas proporcionadas por el paquete `kafka-clients`. El siguiente código XML define esta dependencia:

    ```xml
    <!-- Kafka client for producer/consumer operations -->
    <dependency>
      <groupId>org.apache.kafka</groupId>
      <artifactId>kafka-clients</artifactId>
      <version>${kafka.version}</version>
    </dependency>
    ```

    > [!NOTE]  
    > La entrada `${kafka.version}` se declara en la sección `<properties>..</properties>` de `pom.xml` y está configurada para la versión Kafka del clúster de HDInsight.

* Complementos: los complementos de Maven proporcionan varias funcionalidades. En este proyecto, se utilizan los siguientes complementos:

    * `maven-compiler-plugin`: se utiliza para establecer que el proyecto utiliza la versión 8 de Java. Esta es la versión de Java que utiliza HDInsight 3.6.
    * `maven-shade-plugin`: se utiliza para generar un archivo uber-jar que contiene esta aplicación, así como todas las dependencias. También se usa para establecer el punto de entrada de la aplicación, con el fin de que pueda ejecutar directamente el archivo Jar sin tener que especificar la clase principal.

### <a name="producerjava"></a>Producer.java

El productor se comunica con los hosts del agente de Kafka (nodos de trabajo) y envía datos a un tema de Kafka. El siguiente fragmento de código es del archivo [Producer.java](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/blob/master/Producer-Consumer/src/main/java/com/microsoft/example/Producer.java) del [repositorio de GitHub](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started) y muestra cómo establecer las propiedades del productor:

```java
Properties properties = new Properties();
// Set the brokers (bootstrap servers)
properties.setProperty("bootstrap.servers", brokers);
// Set how to serialize key/value pairs
properties.setProperty("key.serializer","org.apache.kafka.common.serialization.StringSerializer");
properties.setProperty("value.serializer","org.apache.kafka.common.serialization.StringSerializer");
KafkaProducer<String, String> producer = new KafkaProducer<>(properties);
```

### <a name="consumerjava"></a>Consumer.java

El consumidor se comunica con los hosts del agente de Kafka (nodos de trabajo) y lee los registros en un bucle. El siguiente fragmento de código del archivo [Consumer.java](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/blob/master/Producer-Consumer/src/main/java/com/microsoft/example/Consumer.java) establece las propiedades del consumidor:

```java
KafkaConsumer<String, String> consumer;
// Configure the consumer
Properties properties = new Properties();
// Point it to the brokers
properties.setProperty("bootstrap.servers", brokers);
// Set the consumer group (all consumers must belong to a group).
properties.setProperty("group.id", groupId);
// Set how to serialize key/value pairs
properties.setProperty("key.deserializer","org.apache.kafka.common.serialization.StringDeserializer");
properties.setProperty("value.deserializer","org.apache.kafka.common.serialization.StringDeserializer");
// When a group is first created, it has no offset stored to start reading from. This tells it to start
// with the earliest record in the stream.
properties.setProperty("auto.offset.reset","earliest");

consumer = new KafkaConsumer<>(properties);
```

En este código, el consumidor está configurado para leer desde el principio del tema (`auto.offset.reset` está establecido en `earliest`).

### <a name="runjava"></a>Run.java

El archivo [Run.java](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/blob/master/Producer-Consumer/src/main/java/com/microsoft/example/Run.java) proporciona una interfaz de línea de comandos que ejecuta el código del productor o del consumidor. Debe proporcionar la información del host del agente de Kafka como parámetro. También puede incluir un valor del identificador de grupo. Dicho valor lo utiliza el proceso de consumidor. Si crea varias instancias de consumidor con el mismo identificador de grupo, equilibrarán la carga de la lectura del tema.

## <a name="build-and-deploy-the-example"></a>Compilación e implementación del ejemplo

1. Descarga y extracción de los ejemplos de [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started).

2. Establezca que el directorio actual sea la ubicación del directorio `hdinsight-kafka-java-get-started\Producer-Consumer` y use el siguiente comando:

    ```cmd
    mvn clean package
    ```

    Este comando crea un directorio denominado `target`, que contiene un archivo denominado `kafka-producer-consumer-1.0-SNAPSHOT.jar`.

3. Reemplace `sshuser` por el usuario de SSH del clúster y `CLUSTERNAME` por el nombre de su clúster. Escriba el siguiente comando para copiar el archivo `kafka-producer-consumer-1.0-SNAPSHOT.jar` en el clúster de HDInsight. Cuando se le solicite, escriba la contraseña del usuario SSH.

    ```cmd
    scp ./target/kafka-producer-consumer-1.0-SNAPSHOT.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:kafka-producer-consumer.jar
    ```

## <a id="run"></a> Ejecutar el ejemplo

1. Reemplace `sshuser` por el usuario de SSH del clúster y `CLUSTERNAME` por el nombre de su clúster. Abra una conexión de SSH con el clúster, para lo que debe escribir el siguiente comando. Si se le solicita, escriba la contraseña de la cuenta de usuario de SSH.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Instale [jq](https://stedolan.github.io/jq/), un procesador JSON de línea de comandos. En la conexión SSH abierta, escriba el siguiente comando para instalar `jq`:

    ```bash
    sudo apt -y install jq
    ```

3. Configure las variables de entorno. Reemplace `PASSWORD` y `CLUSTERNAME` por la contraseña de inicio de sesión del clúster y el nombre del clúster, respectivamente y, después, escriba el comando:

    ```bash
    export password='PASSWORD'
    export clusterNameA='CLUSTERNAME'
    ```

4. Extraiga el nombre del clúster con las mayúsculas y minúsculas correctas. Las mayúsculas y minúsculas reales del nombre del clúster pueden no ser como cabría esperar, dependen de la forma en que se haya creado el clúster. Este comando obtendrá las mayúsculas y minúsculas reales, las almacenará en una variable y, después, mostrará el nombre con las mayúsculas y minúsculas correctas y el nombre que especificó anteriormente. Escriba el comando siguiente:

    ```bash
    export clusterName=$(curl -u admin:$password -sS -G "https://$clusterNameA.azurehdinsight.net/api/v1/clusters" \
  	| jq -r '.items[].Clusters.cluster_name')
    echo $clusterName, $clusterNameA
    ```

5. Para obtener los hosts del agente de Kafka y los hosts de Apache Zookeeper, use el siguiente comando:

    ```bash
    export KAFKABROKERS=`curl -sS -u admin:$password -G https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER \
  	| jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    ```

6. Cree un tema de Kafka, `myTest`, para lo que debe usar el siguiente comando:

    ```bash
    java -jar kafka-producer-consumer.jar create myTest $KAFKABROKERS
    ```

7. Para ejecutar el productor y escribir datos en el tema, utilice el siguiente comando:

    ```bash
    java -jar kafka-producer-consumer.jar producer myTest $KAFKABROKERS
    ```

8. Cuando finalice el productor, use el comando siguiente para leer desde el tema:

    ```bash
    java -jar kafka-producer-consumer.jar consumer myTest $KAFKABROKERS
    ```

    Se muestran los registros leídos, junto con un número de registros.

9. Use __Ctrl + C__ para salir del consumidor.

### <a name="multiple-consumers"></a>Varios consumidores

Los consumidores de Kafka usan un grupo de consumidores al leer los registros. Usar el mismo grupo con varios consumidores deriva en lecturas de un tema con equilibrio de carga. Cada consumidor del grupo recibe una parte de los registros.

La aplicación del consumidor acepta un parámetro que se usa como identificador del grupo. Por ejemplo, el comando siguiente inicia un consumidor mediante un identificador de grupo `myGroup`:

```bash
java -jar kafka-producer-consumer.jar consumer myTest $KAFKABROKERS myGroup
```

Use __Ctrl + C__ para salir del consumidor.

Para ver este proceso en acción, use el siguiente comando:

```bash
tmux new-session 'java -jar kafka-producer-consumer.jar consumer myTest $KAFKABROKERS myGroup' \
\; split-window -h 'java -jar kafka-producer-consumer.jar consumer myTest $KAFKABROKERS myGroup' \
\; attach
```

Este comando usa `tmux` para dividir el terminal en dos columnas. Se inicia un consumidor en cada una de ellas, con el mismo valor de identificador de grupo. Una vez que los consumidores terminan de leer, fíjese en que cada uno de ellos lee solo una parte de los registros. Use __Ctrl + C__ dos veces para salir de `tmux`.

El consumo por clientes del mismo grupo se controla mediante las particiones del tema. En este ejemplo de código, el tema `test` creado anteriormente tiene ocho particiones. Si inicia ocho consumidores, cada uno de ellos lee los registros de una única partición del tema.

> [!IMPORTANT]  
> No puede haber más instancias de consumidor en un grupo de consumidores que particiones. En este ejemplo, un grupo de consumidores puede contener hasta ocho, ya que es el número de particiones del tema. O bien, puede tener varios grupos de consumidores, los cuales no tengan más de ocho consumidores cada uno.

Los registros almacenados en Kafka se almacenan en el orden en que se reciben dentro de una partición. Para lograr la entrega ordenada de registros *dentro de una partición*, cree un grupo de consumidores donde el número de instancias de consumidor coincida con el número de particiones. Para lograr la entrega ordenada de registros *dentro del tema*, cree un grupo de consumidores con solo una instancia de consumidor.

## <a name="next-steps"></a>Pasos siguientes

En este documento, aprendió a usar Producer y Consumer API de Apache Kafka con Kafka en HDInsight. Para más información sobre cómo trabajar con Kafka:

* [Análisis de registros de Apache Kafka](apache-kafka-log-analytics-operations-management.md)
* [Réplica de datos entre clústeres de Apache Kafka](apache-kafka-mirroring.md)
* [Streams API de Apache Kafka con HDInsight](apache-kafka-streams-api.md)
