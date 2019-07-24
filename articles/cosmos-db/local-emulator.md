---
title: Desarrollo local con el emulador de Azure Cosmos
description: Con el emulador de Azure Cosmos puede desarrollar y probar su aplicación localmente de forma gratuita, sin necesidad de crear una suscripción a Azure.
ms.service: cosmos-db
ms.topic: tutorial
author: markjbrown
ms.author: mjbrown
ms.date: 07/16/2019
ms.openlocfilehash: 3a03829c39deb954a8baa908de63b9ff6f31238e
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/17/2019
ms.locfileid: "68297850"
---
# <a name="use-the-azure-cosmos-emulator-for-local-development-and-testing"></a>Uso del emulador de Azure Cosmos para desarrollo y pruebas locales

El emulador de Azure Cosmos proporciona un entorno local que emula el servicio Azure Cosmos DB con fines de desarrollo. Mediante el emulador de Azure Cosmos puede desarrollar y probar su aplicación localmente, sin necesidad de crear una suscripción de Azure ni incurrir en costos. Cuando esté satisfecho con el funcionamiento de la aplicación en el emulador, puede cambiar a una cuenta de Azure Cosmos en la nube.

Puede desarrollar mediante el emulador de Azure Cosmos con cuentas de [SQL](local-emulator.md#sql-api), [Cassandra](local-emulator.md#cassandra-api), [MongoDB](local-emulator.md#azure-cosmos-dbs-api-for-mongodb), [Gremlin](local-emulator.md#gremlin-api) y [Table](local-emulator.md#table-api) API. Sin embargo, en este momento la vista del explorador de datos en el emulador solo es totalmente compatible con los clientes de SQL API. 

## <a name="how-the-emulator-works"></a>Funcionamiento del emulador

El emulador de Azure Cosmos proporciona una emulación de gran fidelidad del servicio Azure Cosmos DB. Admite una funcionalidad idéntica a la de Azure Cosmos DB, incluida la compatibilidad con la creación y la consulta de datos, el aprovisionamiento y el escalado de contenedores y la ejecución de procedimientos y desencadenadores almacenados. Puede desarrollar y probar aplicaciones mediante el emulador de Azure Cosmos e implementarlas en Azure a escala global realizando simplemente un cambio de configuración en el punto de conexión de Azure Cosmos DB.

Aunque la emulación del servicio Azure Cosmos DB es fiel, la implementación del emulador es diferente de la del servicio. Por ejemplo, el emulador utiliza componentes del sistema operativo estándar; por ejemplo, utiliza el sistema de archivos local para la persistencia y la pila del protocolo HTTPS para la conectividad. No procede la funcionalidad que se basa en la infraestructura de Azure, como la replicación global, la latencia de milisegundos de un solo dígito para lectura/escritura y los niveles de coherencia ajustable.

Puede migrar datos entre el emulador de Azure Cosmos y el servicio Azure Cosmos DB con la [herramienta de migración de datos de Azure Cosmos DB](https://github.com/azure/azure-documentdb-datamigrationtool).

Puede ejecutar el emulador de Azure Cosmos en el contenedor de Docker de Windows, consulte [Docker Hub](https://hub.docker.com/r/microsoft/azure-cosmosdb-emulator/) para el comando docker pull y [GitHub](https://github.com/Azure/azure-cosmos-db-emulator-docker) para el código fuente del emulador.

## <a name="differences-between-the-emulator-and-the-service"></a>Diferencias entre el emulador y el servicio

Dado que el emulador de Azure Cosmos proporciona un entorno emulado que se ejecuta en una estación de trabajo de desarrollador local, hay algunas diferencias de funcionalidad entre el emulador y una cuenta de Azure Cosmos en la nube:

* En este momento, el explorador de datos del emulador admite los clientes de SQL API. La vista de explorador de datos y las operaciones de las API de Azure Cosmos DB, como MongoDB, Table, Graph y las API de Cassandra no son totalmente compatibles.
* El emulador de Azure Cosmos es compatible con una sola cuenta fija y una clave maestra reconocida. Las claves no se pueden regenerar en el emulador de Azure Cosmos; sin embargo, la clave predeterminada se puede cambiar con la opción de la línea de comandos.
* El emulador de Azure Cosmos no es un servicio escalable y no será compatible con un gran número de contenedores.
* El emulador de Azure Cosmos no ofrece diferentes [niveles de coherencia de Azure Cosmos DB](consistency-levels.md).
* El emulador de Azure Cosmos no ofrece [replicación en varias regiones](distribute-data-globally.md).
* Como la copia del emulador de Azure Cosmos puede no contener siempre los cambios más recientes del servicio Azure Cosmos DB, debe consultar la herramienta de [planeamiento de capacidad de Azure Cosmos DB](https://www.documentdb.com/capacityplanner) para calcular con precisión las necesidades de rendimiento de producción (RU) de la aplicación.
* Cuando se usa el emulador de Azure Cosmos, de forma predeterminada se pueden crear hasta 25 contenedores de tamaño fijo (solo compatibles con los SDK de Azure Cosmos DB) o 5 contenedores ilimitados con el emulador de Azure Cosmos. Para más información acerca de cómo cambiar este valor, consulte la sección sobre la [configuración del valor de PartitionCount](#set-partitioncount).

## <a name="system-requirements"></a>Requisitos del sistema

El emulador de Azure Cosmos presenta los siguientes requisitos de hardware y software:

* Requisitos de software
  * Windows Server 2012 R2, Windows Server 2016 o Windows 10
  * Sistema operativo de 64 bits
* Requisitos mínimos de hardware
  * 2 GB de RAM
  * 10 GB de espacio disponible en el disco duro

## <a name="installation"></a>Instalación

Puede descargar e instalar el emulador de Azure Cosmos desde el [Centro de descarga de Microsoft](https://aka.ms/cosmosdb-emulator) o ejecutarlo en Docker para Windows. Para obtener instrucciones acerca de cómo utilizar el emulador en Docker para Windows, consulte [Ejecución en Docker para Windows](#running-on-docker).

> [!NOTE]
> Para instalar, configurar y ejecutar el emulador de Azure Cosmos, debe tener privilegios administrativos en el equipo. El emulador creará/agregará un certificado y establecerá las reglas de firewall para ejecutar sus servicios; por lo tanto, es necesario que el emulador pueda ejecutar estas operaciones.

## <a name="running-on-windows"></a>Ejecución en Windows

Para iniciar el emulador de Azure Cosmos, seleccione el botón Inicio o presione la tecla Windows. Comience por escribir **Emulador de Azure Cosmos** y seleccione el emulador de la lista de aplicaciones.

![Seleccione el botón Inicio o presione la tecla Windows, comience a escribir **Emulador de Azure Cosmos** y seleccione el emulador en la lista de aplicaciones.](./media/local-emulator/database-local-emulator-start.png)

Cuando se ejecuta el emulador, verá un icono en el área de notificación de la barra de tareas de Windows. ![Notificación en la barra de tareas del emulador local de Azure Cosmos DB](./media/local-emulator/database-local-emulator-taskbar.png)

El emulador de Azure Cosmos se ejecuta de forma predeterminada en la máquina local ("localhost") que escucha en el puerto 8081.

El emulador de Azure Cosmos se instala de forma predeterminada en `C:\Program Files\Azure Cosmos DB Emulator`. También puede iniciar y detener el emulador desde la línea de comandos. Para más información consulte la [referencia de la herramienta de la línea de comandos](#command-line).

## <a name="start-data-explorer"></a>Inicio del Explorador de datos

Cuando se inicia el emulador de Azure Cosmos, el explorador de datos de Azure Cosmos se abre automáticamente en el explorador. La dirección aparece como `https://localhost:8081/_explorer/index.html`. Si cierra el explorador y desea volver a abrirlo más adelante, puede abrir la dirección URL en el explorador o iniciarlo desde el icono del emulador de Azure Cosmos de la bandeja de Windows, tal como se muestra a continuación.

![Iniciador del explorador de datos del emulador local de Azure Cosmos](./media/local-emulator/database-local-emulator-data-explorer-launcher.png)

## <a name="checking-for-updates"></a>Búsqueda de actualizaciones

En el Explorador de datos se indica si hay una nueva actualización disponible para descarga.

> [!NOTE]
> No se garantiza que los datos que se crean en una versión del emulador de Azure Cosmos (consulte %LOCALAPPDATA%\CosmosDBEmulator o la configuración opcional de la ruta de acceso de los datos) estén disponibles cuando se utilice una versión diferente. Si necesita conservar los datos a largo plazo, es recomendable que los almacene en una cuenta de Azure Cosmos y no en el emulador de Azure Cosmos.

## <a name="authenticating-requests"></a>Autenticación de solicitudes

Al igual que con Azure Cosmos DB en la nube, todas las solicitudes que se realicen en el emulador de Azure Cosmos deben autenticarse. El emulador de Azure Cosmos es compatible con una sola cuenta fija y una clave de autenticación reconocida para la autenticación de clave maestra. Esta cuenta y la clave son las únicas credenciales que se admiten para su uso con el emulador de Azure Cosmos. Son las siguientes:

```bash
Account name: localhost:<port>
Account key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
```

> [!NOTE]
> La clave maestra admitida por el emulador de Azure Cosmos está destinada a su uso exclusivo con el emulador. No puede usar su clave y cuenta de producción de Azure Cosmos DB con el emulador de Azure Cosmos.

> [!NOTE]
> Si ha iniciado el emulador con la opción /Key, use la clave generada en lugar de `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==`. Para más información sobre la opción /Key, consulte la [referencia de la herramienta de la línea de comandos](#command-line-syntax).

Al igual que Azure Cosmos DB, el emulador de Azure Cosmos solo admite la comunicación segura a través de SSL.

## <a name="running-on-a-local-network"></a>Ejecución en una red local

Puede ejecutar el emulador en una red local. Para habilitar el acceso de red, especifique la opción `/AllowNetworkAccess` en la [línea de comandos](#command-line-syntax), que también requiere que especifique `/Key=key_string` o `/KeyFile=file_name`. Puede usar `/GenKeyFile=file_name` para generar un archivo con una clave aleatoria por adelantado. Después, puede pasar a `/KeyFile=file_name` o `/Key=contents_of_file`.

La primera vez que se habilita el acceso de red, el usuario debe apagar el emulador y eliminar el directorio de datos del emulador (%LOCALAPPDATA%\CosmosDBEmulator).

## <a name="developing-with-the-emulator"></a>Desarrollo con el emulador

### <a name="sql-api"></a>API DE SQL

Cuando tenga el emulador de Azure Cosmos funcionando en su escritorio, puede usar cualquier [SDK de Azure Cosmos DB](sql-api-sdk-dotnet.md) admitido o la [API REST de Azure Cosmos DB](/rest/api/cosmos-db/) para interactuar con el emulador. El emulador de Azure Cosmos también incluye un explorador de datos integrado que permite crear contenedores para SQL API o Cosmos DB para MongoDB API, así como ver y editar elementos sin escribir ningún código.

```csharp
// Connect to the Azure Cosmos Emulator running locally
DocumentClient client = new DocumentClient(
   new Uri("https://localhost:8081"), "C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==");

```

### <a name="azure-cosmos-dbs-api-for-mongodb"></a>API de Azure Cosmos DB para MongoDB

Si va a utilizar [API de Azure Cosmos DB para MongoDB](mongodb-introduction.md), use la siguiente cadena de conexión:

```bash
mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10255/admin?ssl=true
```

### <a name="table-api"></a>Table API

Cuando tenga el emulador de Azure Cosmos funcionando en su escritorio, puede usar el [SDK de Table API de Azure Cosmos DB](table-storage-how-to-use-dotnet.md) para interactuar con el emulador. Inicie el emulador desde el símbolo del sistema como administrador con "/EnableTableEndpoint". A continuación, ejecute el siguiente código para conectarse a la cuenta de Table API:

```csharp
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Table;
using CloudTable = Microsoft.WindowsAzure.Storage.Table.CloudTable;
using CloudTableClient = Microsoft.WindowsAzure.Storage.Table.CloudTableClient;

string connectionString = "DefaultEndpointsProtocol=http;AccountName=localhost;AccountKey=C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==;TableEndpoint=http://localhost:8902/;";

CloudStorageAccount account = CloudStorageAccount.Parse(connectionString);
CloudTableClient tableClient = account.CreateCloudTableClient();
CloudTable table = tableClient.GetTableReference("testtable");
table.CreateIfNotExists();
table.Execute(TableOperation.Insert(new DynamicTableEntity("partitionKey", "rowKey")));
```

### <a name="cassandra-api"></a>Cassandra API

Inicie el emulador desde un símbolo del sistema como administrador con "/EnableCassandraEndpoint". Como alternativa, también puede establecer la variable de entorno `AZURE_COSMOS_EMULATOR_CASSANDRA_ENDPOINT=true`.

* [Instale Python 2.7](https://www.python.org/downloads/release/python-2716/)

* [Instale la CLI/CQLSH de Cassandra](https://cassandra.apache.org/download/)

* Ejecute los siguientes comandos en una ventana normal del símbolo del sistema:

  ```bash
  set Path=c:\Python27;%Path%
  cd /d C:\sdk\apache-cassandra-3.11.3\bin
  set SSL_VERSION=TLSv1_2
  set SSL_VALIDATE=false
  cqlsh localhost 10350 -u localhost -p C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw== --ssl
  ```

* En el shell de CQLSH, ejecute los siguientes comandos para conectarse al punto de conexión de Cassandra:

  ```bash
  CREATE KEYSPACE MyKeySpace WITH replication = {'class':'MyClass', 'replication_factor': 1};
  DESCRIBE keyspaces;
  USE mykeyspace;
  CREATE table table1(my_id int PRIMARY KEY, my_name text, my_desc text);
  INSERT into table1 (my_id, my_name, my_desc) values( 1, 'name1', 'description 1');
  SELECT * from table1;
  EXIT
  ```

### <a name="gremlin-api"></a>API de Gremlin

Inicie el emulador desde un símbolo del sistema como administrador con "/EnableGremlinEndpoint". Como alternativa, también puede establecer la variable de entorno `AZURE_COSMOS_EMULATOR_GREMLIN_ENDPOINT=true`

* [Instale apache-tinkerpop-gremlin-console-3.3.4](https://tinkerpop.apache.org/downloads.html)

* En el explorador de datos del emulador, cree una base de datos "db1" y una colección "coll1"; para la clave de partición, elija "/nombre"

* Ejecute los siguientes comandos en una ventana normal del símbolo del sistema:

  ```bash
  cd /d C:\sdk\apache-tinkerpop-gremlin-console-3.3.4-bin\apache-tinkerpop-gremlin-console-3.3.4
  
  copy /y conf\remote.yaml conf\remote-localcompute.yaml
  notepad.exe conf\remote-localcompute.yaml
    hosts: [localhost]
    port: 8901
    username: /dbs/db1/colls/coll1
    password: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
    connectionPool: {
    enableSsl: false}
    serializer: { className: org.apache.tinkerpop.gremlin.driver.ser.GraphSONMessageSerializerV1d0,
    config: { serializeResultToString: true  }}

  bin\gremlin.bat
  ```

* En el shell de Gremlin, ejecute los siguientes comandos para conectarse al punto de conexión de Gremlin:

  ```bash
  :remote connect tinkerpop.server conf/remote-localcompute.yaml
  :remote console
  :> g.V()
  :> g.addV('person1').property(id, '1').property('name', 'somename1')
  :> g.addV('person2').property(id, '2').property('name', 'somename2')
  :> g.V()
  ```

## <a name="export-the-ssl-certificate"></a>Exportación del certificado SSL

El sistema en tiempo de ejecución y los lenguajes .NET utilizan el almacén de certificados de Windows para conectarse de forma segura al emulador local de Azure Cosmos DB. Otros lenguajes tienen sus propios métodos de administración y uso de certificados. Java utiliza su propio [almacén de certificados](https://docs.oracle.com/cd/E19830-01/819-4712/ablqw/index.html), mientras que Python utiliza [contenedores de sockets](https://docs.python.org/2/library/ssl.html).

Para obtener un certificado que se pueda utilizar con los lenguajes y runtimes que no están integrados en el almacén de certificados de Windows, tendrá que realizar la exportación por medio del administrador de certificados de Windows. Para iniciarlo, ejecute certlm.msc o siga las instrucciones paso a paso del artículo sobre la [Exportación de los certificados del emulador de Azure Cosmos](./local-emulator-export-ssl-certificates.md). Una vez que el Administrador de certificados se esté ejecutando, abra los certificados personales, tal y como se muestra a continuación, y exporte el certificado con el nombre descriptivo "DocumentDBEmulatorCertificate" como archivo X.509 codificado en BASE-64 (.cer).

![Certificado SSL del emulador local de Azure Cosmos DB](./media/local-emulator/database-local-emulator-ssl_certificate.png)

El certificado X.509 puede importarse en el almacén de certificados de Java siguiendo las instrucciones de [Incorporación de un certificado al almacén de certificados CA de Java](https://docs.microsoft.com/azure/java-add-certificate-ca-store). Cuando el certificado se haya importado en el almacén de certificados, los clientes para las API de SQL y Azure Cosmos DB para MongoDB podrán conectarse al emulador de Azure Cosmos.

Al conectarse al emulador desde los SDK de Node.js y Python, se deshabilita la verificación de SSL.

## <a id="command-line"></a>Referencia de la herramienta de la línea de comandos
Desde la ubicación de instalación, puede usar la línea de comandos para iniciar y detener el emulador, configurar las opciones y realizar otras operaciones.

### <a name="command-line-syntax"></a>Sintaxis de línea de comandos

    CosmosDB.Emulator.exe [/Shutdown] [/DataPath] [/Port] [/MongoPort] [/DirectPorts] [/Key] [/EnableRateLimiting] [/DisableRateLimiting] [/NoUI] [/NoExplorer] [/EnableMongoDbEndpoint] [/?]

Para ver la lista de opciones, escriba `CosmosDB.Emulator.exe /?` en el símbolo del sistema.

|**Opción** | **Descripción** | **Comando**| **Argumentos**|
|---|---|---|---|
|[Sin argumentos] | Inicia el emulador de Azure Cosmos con la configuración predeterminada. |CosmosDB.Emulator.exe| |
|[Ayuda] |Muestra la lista de argumentos de la línea de comandos admitidos.|CosmosDB.Emulator.exe /? | |
| GetStatus |Obtiene el estado del emulador de Azure Cosmos. El estado se indica mediante el código de salida: 1 = iniciado, 2 = en ejecución, 3 = detenido. Un código de salida negativo indica que se ha producido un error. Se produce ninguna otra salida. | CosmosDB.Emulator.exe /GetStatus| |
| Shutdown| Cierra el emulador de Azure Cosmos.| CosmosDB.Emulator.exe /Shutdown | |
|DataPath | Especifica la ruta de acceso en la que se almacenarán los archivos de datos. El valor predeterminado es %LocalAppdata%\CosmosDBEmulator. | CosmosDB.Emulator.exe /DataPath=\<datapath\> | \<datapath\>: una ruta de acceso accesible |
|Port | Especifica el número de puerto que se utilizará para el emulador. El valor predeterminado es 8081. |CosmosDB.Emulator.exe /Port=\<port\> | \<port\>: número de puerto sencillo |
| ComputePort | Especifica el número de puerto que se usará para el servicio Compute Interop Gateway. El puerto de sondeo del punto de conexión HTTP de la puerta de enlace se calcula como ComputePort + 79. Por lo tanto, ComputePort y ComputePort + 79 deben estar abiertos y disponibles. Los valores predeterminados son 8900, 8979. | CosmosDB.Emulator.exe /ComputePort = \<computeport\> | \<computeport\>: número de puerto sencillo |
| EnableMongoDbEndpoint | Habilita la API de MongoDB. | CosmosDB.Emulator.exe /EnableMongoDbEndpoint | |
| MongoPort | Especifica el número de puerto que se utilizará para la API de compatibilidad de MongoDB. El valor predeterminado es 10255. |CosmosDB.Emulator.exe /MongoPort= \<mongoport\>|\<mongoport\>: número de puerto sencillo|
| EnableCassandraEndpoint | Habilita Cassandra API | CosmosDB.Emulator.exe /EnableCassandraEndpoint | |
| CassandraPort | Especifica el número de puerto que se utilizará para el punto de conexión de Cassandra. El valor predeterminado es 10350. | CosmosDB.Emulator.exe /CassandraPort = \<cassandraport\> | \<cassandraport\>: número de puerto sencillo |
| EnableGremlinEndpoint | Habilita Gremlin API | CosmosDB.Emulator.exe /EnableGremlinEndpoint | |
| GremlinPort | Número de puerto que se usará para el punto de conexión de Gremlin. El valor predeterminado es 8901. | CosmosDB.Emulator.exe /GremlinPort=\<port\> | \<port\>: número de puerto sencillo |
|EnableTableEndpoint | Habilita Azure Table API | CosmosDB.Emulator.exe /EnableTableEndpoint | |
|TablePort | Número de puerto que se utilizará para el punto de conexión de Azure Table. El valor predeterminado es 8902. | CosmosDB.Emulator.exe /TablePort=\<port\> | \<port\>: número de puerto sencillo|
| KeyFile | Lee la clave de autorización del archivo especificado. Usa la opción/GenKeyFile para generar un archivo de claves | CosmosDB.Emulator.exe /KeyFile=\<file_name\> | \<file_name\>: Ruta de acceso al archivo |
| ResetDataPath | Quita todos los archivos repetidamente de la ruta de acceso especificada. Si no especifica una ruta de acceso, el valor predeterminado es %LOCALAPPDATA%\CosmosDbEmulator | CosmosDB.Emulator.exe /ResetDataPath[=\<path>] | \<path\>: Ruta de acceso del archivo  |
| StartTraces  |  Comienza a recopilar los registros de seguimiento de la depuración. | CosmosDB.Emulator.exe /StartTraces | |
| StopTraces     | Deja de recopilar los registros de seguimiento de la depuración. | CosmosDB.Emulator.exe /StopTraces  | |
|FailOnSslCertificateNameMismatch | De forma predeterminada, el emulador vuelve a generar su certificado SSL autofirmado, si los SAN de certificado no incluyen el nombre de dominio del host del emulador, la dirección IPv4 local, "localhost" y "127.0.0.1". Con esta opción, el emulador producirá un error al inicio en su lugar. A continuación, debe usar la opción /GenCert para crear e instalar un nuevo certificado SSL autofirmado. | CosmosDB.Emulator.exe /FailOnSslCertificateNameMismatch  | |
| GenCert | Genera e instala un nuevo certificado SSL autofirmado. Puede incluir una lista de nombres DNS adicionales separada por comas para acceder al emulador a través de la red. | CosmosDB.Emulator.exe /GenCert[\<lista de nombres dns adicionales separada por comas\>] | |
| DirectPorts |Especifica los puertos que se usarán para la conectividad directa. Los valores predeterminados son 10251,10252,10253,10254. | CosmosDB.Emulator.exe /DirectPorts:\<directports\> | \<directports\>: una lista delimitada por comas de 4 puertos |
| Clave |Clave de autorización para el emulador. La clave debe ser la codificación en base 64 de un vector de 64 bytes. | CosmosDB.Emulator.exe /Key:\<key\> | \<key\>: Las claves deben ser la codificación en base 64 de un vector de 64 bytes.|
| EnableRateLimiting | Especifica que el comportamiento de limitación de velocidad de solicitudes está habilitado. |CosmosDB.Emulator.exe /EnableRateLimiting | |
| DisableRateLimiting |Especifica que el comportamiento de limitación de velocidad de solicitudes está deshabilitado. |CosmosDB.Emulator.exe /DisableRateLimiting | |
| NoUI | No muestra la interfaz de usuario del emulador. | CosmosDB.Emulator.exe /NoUI | |
| NoExplorer | No muestra el Explorador de datos en el inicio. |CosmosDB.Emulator.exe /NoExplorer | | 
| PartitionCount | Especifica el número máximo de los contenedores particionados. Consulte [Cambio del número de contenedores](#set-partitioncount) para más información. | CosmosDB.Emulator.exe /PartitionCount=\<partitioncount\> | \<partitioncount\>: Número máximo de contenedores de una sola partición permitidos. El valor predeterminado es 25. El máximo permitido es 250.|
| DefaultPartitionCount| Especifica el número predeterminado de particiones para un contenedor. | CosmosDB.Emulator.exe /DefaultPartitionCount=\<defaultpartitioncount\> | El valor predeterminado de \<defaultpartitioncount\> es 25.|
| AllowNetworkAccess | Permite acceder al emulador a través de una red. También debe pasar /Key=\<key_string\> o /KeyFile=\<file_name\> para habilitar el acceso de red. | CosmosDB.Emulator.exe /AllowNetworkAccess /Key=\<key_string\> o CosmosDB.Emulator.exe /AllowNetworkAccess /KeyFile=\<file_name\>| |
| NoFirewall | No ajuste las reglas de firewall cuando se utilice la opción /AllowNetworkAccess. |CosmosDB.Emulator.exe /NoFirewall | |
| GenKeyFile | Genere una nueva clave de autorización y guárdela en el archivo especificado. La clave generada se puede utilizar con las opciones /Key o /KeyFile. | CosmosDB.Emulator.exe /GenKeyFile=\<ruta de acceso al archivo de clave\> | |
| Coherencia | Defina el nivel de coherencia predeterminado de la cuenta. | CosmosDB.Emulator.exe /Consistency=\<consistency\> | \<consistency\>: el valor debe ser de uno de los siguientes [niveles de consistencia](consistency-levels.md): Sesión, Strong, Eventual o BoundedStaleness. El valor predeterminado es Session. |
| ? | Se muestra el mensaje de ayuda.| | |

## <a id="set-partitioncount"></a>Cambio del número de contenedores

De forma predeterminada se pueden crear hasta 25 contenedores de tamaño fijo (solo compatibles con los SDK de Azure Cosmos DB) o 5 contenedores ilimitados con el emulador de Azure Cosmos. Al modificar el valor de **PartitionCount**, se pueden crear hasta 250 contenedores de tamaño fijo o 50 contenedores ilimitados, o cualquier combinación de los dos que no supere los 250 contenedores de tamaño fijo (un contenedor ilimitado = 5 contenedores de tamaño fijo). Sin embargo, no se recomienda configurar el emulador para que se ejecute con más de 200 contenedores de tamaño fijo. Esto se debe a la sobrecarga añadida a las operaciones de E/S del disco, que producen tiempos de expiración impredecibles al usar las API de punto de conexión.

Si intenta crear un contenedor después de que se haya excedido el recuento de particiones actual, el emulador generará una excepción ServiceUnavailable con el siguiente mensaje.

"Sorry, we are currently experiencing high demand in this region, and cannot fulfill your request at this time. We work continuously to bring more and more capacity online, and encourage you to try again.
Please do not hesitate to email askcosmosdb@microsoft.com at any time or for any reason.
ActivityId: 12345678-1234-1234-1234-123456789abc" (Lo sentimos, la demanda actual en esta región es alta y no podemos responder a su solicitud en este momento. Trabajamos constantemente para añadir capacidad en línea, inténtelo de nuevo. Puede enviarnos un correo electrónico a askcosmosdb@microsoft.com en cualquier momento, con cualquier motivo. ActivityId: 12345678-1234-1234-1234-123456789abc)

Para cambiar el número de contenedores disponibles en el emulador de Azure Cosmos, ejecute los siguientes pasos:

1. Elimine todos los datos del emulador local de Azure Cosmos; para ello, haga clic con el botón derecho en el icono del **Emulador de Azure Cosmos DB** de la bandeja del sistema y haga clic en **Reset Data…** (Restablecer datos...).
2. Elimine todos los datos del emulador de la carpeta `%LOCALAPPDATA%\CosmosDBEmulator`.
3. Cierre todas las instancias abiertas; para ello, haga clic con el botón derecho en el icono del **Emulador de Azure Cosmos DB** de la bandeja del sistema y, luego, haga clic en **Salir**. Todas las instancias pueden tardar un minuto en salir.
4. Instale la versión más reciente del [Emulador de Azure Cosmos](https://aka.ms/cosmosdb-emulator).
5. Para iniciar el emulador con la marca PartitionCount, establezca un valor <= 250. Por ejemplo: `C:\Program Files\Azure Cosmos DB Emulator> CosmosDB.Emulator.exe /PartitionCount=100`.

## <a name="controlling-the-emulator"></a>Control del emulador

El emulador incluye un módulo de PowerShell para iniciar, detener, desinstalar y recuperar el estado del servicio. Ejecute el siguiente cmdlet para utilizar el módulo de PowerShell:

```powershell
Import-Module "$env:ProgramFiles\Azure Cosmos DB Emulator\PSModules\Microsoft.Azure.CosmosDB.Emulator"
```

o bien coloque el directorio `PSModules` en su `PSModulesPath` e impórtelo como se muestra en el siguiente comando:

```powershell
$env:PSModulesPath += "$env:ProgramFiles\Azure Cosmos DB Emulator\PSModules"
Import-Module Microsoft.Azure.CosmosDB.Emulator
```

Este es un resumen de los comandos de control del emulador desde PowerShell:

### `Get-CosmosDbEmulatorStatus`

**Sintaxis**

`Get-CosmosDbEmulatorStatus`

**Comentarios:**

Devuelve uno de estos valores de ServiceControllerStatus: ServiceControllerStatus.StartPending, ServiceControllerStatus.Running o ServiceControllerStatus.Stopped.

### `Start-CosmosDbEmulator`

**Sintaxis**

`Start-CosmosDbEmulator [-DataPath <string>] [-DefaultPartitionCount <uint16>] [-DirectPort <uint16[]>] [-MongoPort <uint16>] [-NoUI] [-NoWait] [-PartitionCount <uint16>] [-Port <uint16>] [<CommonParameters>]`

**Comentarios:**

Inicia el emulador. De forma predeterminada, el comando espera hasta que el emulador está listo para aceptar solicitudes. Utilice la opción -NoWait, si desea que el cmdlet realice la devolución en cuanto inicie el emulador.

### `Stop-CosmosDbEmulator`

**Sintaxis**

 `Stop-CosmosDbEmulator [-NoWait]`

**Comentarios:**

Detiene el emulador. De forma predeterminada, este comando espera hasta que el emulador esté completamente apagado. Utilice la opción -NoWait, si desea que el cmdlet realice la devolución en cuanto el emulador comience el apagado.

### `Uninstall-CosmosDbEmulator`

**Sintaxis**

`Uninstall-CosmosDbEmulator [-RemoveData]`

**Comentarios:**

Desinstala el emulador y, opcionalmente, quita todo el contenido de $env: LOCALAPPDATA\CosmosDbEmulator.
El cmdlet garantiza que el emulador se detiene antes de desinstalarlo.

## <a name="running-on-docker"></a>Ejecución en Docker

El emulador de Azure Cosmos se puede ejecutar en Docker para Windows. El emulador no funciona en Docker para Oracle Linux.

Una vez que haya instalado [Docker para Windows](https://www.docker.com/docker-windows), cambie a contenedores de Windows haciendo clic con el botón derecho en el icono de Docker en la barra de herramientas, y seleccione **Switch to Windows containers** (Cambiar a contenedores de Windows).

A continuación, extraiga la imagen del emulador de Docker Hub ejecutando el siguiente comando desde el shell que prefiera.

```bash
docker pull mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator
```
Para iniciar la imagen, ejecute los siguientes comandos.

Desde la línea de comandos:
```cmd

md %LOCALAPPDATA%\CosmosDBEmulator\bind-mount

docker run --name azure-cosmosdb-emulator --memory 2GB --mount "type=bind,source=%LOCALAPPDATA%\CosmosDBEmulator\bind-mount,destination=C:\CosmosDB.Emulator\bind-mount" --interactive --tty -p 8081:8081 -p 8900:8900 -p 8901:8901 -p 8902:8902 -p 10250:10250 -p 10251:10251 -p 10252:10252 -p 10253:10253 -p 10254:10254 -p 10255:10255 -p 10256:10256 -p 10350:10350 mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator --rm
```

> [!NOTE]
> Si ve un error de conflicto de puerto (el puerto especificado ya está en uso) al ejecutar el comando docker run, puede pasar un puerto personalizado modificando los números de puerto. Por ejemplo, puede cambiar "-p 8081:8081" por "-p 443:8081".

Desde PowerShell:
```powershell

md $env:LOCALAPPDATA\CosmosDBEmulator\bind-mount 2>null

docker run --name azure-cosmosdb-emulator --memory 2GB --mount "type=bind,source=$env:LOCALAPPDATA\CosmosDBEmulator\bind-mount,destination=C:\CosmosDB.Emulator\bind-mount" --interactive --tty -p 8081:8081 -p 8900:8900 -p 8901:8901 -p 8902:8902 -p 10250:10250 -p 10251:10251 -p 10252:10252 -p 10253:10253 -p 10254:10254 -p 10255:10255 -p 10256:10256 -p 10350:10350 mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator

```

La respuesta será similar a la siguiente:

```
Starting emulator
Emulator Endpoint: https://172.20.229.193:8081/
Master Key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
Exporting SSL Certificate
You can import the SSL certificate from an administrator command prompt on the host by running:
cd /d %LOCALAPPDATA%\CosmosDBEmulatorCert
powershell .\importcert.ps1
--------------------------------------------------------------------------------------------------
Starting interactive shell
```

Ahora, use el punto de conexión y la clave maestra de la respuesta en el cliente e importe el certificado SSL en el host. Para importar el certificado SSL, haga lo siguiente desde un símbolo del sistema de administración:

Desde la línea de comandos:

```cmd
cd  %LOCALAPPDATA%\CosmosDBEmulator\bind-mount
powershell .\importcert.ps1
```

Desde PowerShell:
```powershell
cd $env:LOCALAPPDATA\CosmosDBEmulator\bind-mount
.\importcert.ps1
```

Si cierra el shell interactivo después de que se haya iniciado el emulador, se cerrará el contenedor de este.

Para abrir el Explorador de datos vaya a la siguiente dirección URL en el explorador. El punto de conexión del emulador se proporciona en el mensaje de respuesta mostrado anteriormente.

    https://<emulator endpoint provided in response>/_explorer/index.html

## Ejecución en Mac o Linux<a id="mac"></a>

Actualmente, el emulador de Cosmos solo se puede ejecutar en Windows. Los usuarios con Mac o Linux pueden ejecutar el emulador en una máquina virtual Windows hospedada por un hipervisor como Parallels o VirtualBox. A continuación se muestran los pasos necesarios para habilitarlo.

En la máquina virtual Windows, ejecute el comando siguiente y tome nota de la dirección IPv4.

```cmd
ipconfig.exe
```

En su aplicación, debe cambiar el URI del objeto DocumentClient para usar la dirección IPv4 que `ipconfig.exe` devuelve. El paso siguiente es ofrecer una solución alternativa a la validación de la CA al construir el objeto DocumentClient. Para ello, tendrá que proporcionar un HttpClientHandler al constructor DocumentClient, que tiene su propia implementación para ServerCertificateCustomValidationCallback.

En el ejemplo siguiente se muestra cómo quedaría el código.

```csharp
using System;
using Microsoft.Azure.Documents;
using Microsoft.Azure.Documents.Client;
using System.Net.Http;

namespace emulator
{
    class Program
    {
        static async void Main(string[] args)
        {
            string strEndpoint = "https://10.135.16.197:8081/";  //IPv4 address from ipconfig.exe
            string strKey = "C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==";

            //Work around the CA validation
            var httpHandler = new HttpClientHandler()
            {
                ServerCertificateCustomValidationCallback = (req,cert,chain,errors) => true
            };

            //Pass http handler to document client
            using (DocumentClient client = new DocumentClient(new Uri(strEndpoint), strKey, httpHandler))
            {
                Database database = await client.CreateDatabaseIfNotExistsAsync(new Database { Id = "myDatabase" });
                Console.WriteLine($"Created Database: id - {database.Id} and selfLink - {database.SelfLink}");
            }
        }
    }
}
```

Por último, desde dentro de la máquina virtual Windows, inicie el emulador de Cosmos desde la línea de comandos mediante las siguientes opciones.

```cmd
Microsoft.Azure.Cosmos.Emulator.exe /AllowNetworkAccess /Key=C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
```

## <a name="troubleshooting"></a>solución de problemas

Use las siguientes sugerencias para solucionar los problemas que podrían surgir con el emulador de Azure Cosmos:

- Si se instala una nueva versión del emulador y se experimentan errores, asegúrese de restablecer los datos. Para restablecer los datos, haga clic con el botón derecho en el icono del Emulador de Azure Cosmos de la bandeja del sistema y haga clic en Reset Data... (Restablecer datos....) Si no se solucionan los errores, puede desinstalar el emulador y las versiones anteriores de este (si se encuentran), quitar el directorio "C:\Program files\Azure Cosmos DB Emulator" y volver a instalar el emulador. Consulte [Desinstalación del emulador local](#uninstall) para obtener instrucciones.

- Si el emulador de Azure Cosmos se bloquea, recopile los archivos de volcado de memoria de la carpeta %LoCALAPPDATA%\CrashDumps, comprímalos y adjúntelos a un correo electrónico para [askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com).

- Si experimenta bloqueos en `Microsoft.Azure.Cosmos.ComputeServiceStartupEntryPoint.exe`, podría ser un síntoma de donde los contadores de rendimiento tienen un estado dañado. Normalmente, ejecutar el siguiente comando desde un símbolo del sistema de administración corrige el problema:

  ```cmd
  lodctr /R
   ```

- Si se produce un problema de conectividad, [recopile los archivos de seguimiento](#trace-files), comprímalos y adjúntelos a un correo electrónico y envíelo a [ askcosmosdb@microsoft.com ](mailto:askcosmosdb@microsoft.com).

- Si recibe un mensaje de **Servicio no disponible**, es posible que el emulador no pueda inicializar la pila de red. Compruebe si tiene instalado el cliente de Pulse Secure o el cliente de Juniper Networks, ya que sus controladores de filtro de red podrían ser la causa del problema. Normalmente el problema se corrige al desinstalar los controladores del filtro de red de otros fabricantes. Como alternativa, puede iniciar el emulador con /DisableRIO, lo cual cambiará la comunicación de red del emulador a una Winsock normal. 

- Mientras se ejecuta el emulador, si el equipo entra en modo de suspensión o se ejecuta alguna actualización del sistema operativo, es posible que aparezca un mensaje del tipo **el servicio no está disponible en estos momentos**. Restablezca el emulador; para ello, haga clic con el botón derecho en el icono que aparece en la bandeja de notificaciones de Windows y seleccione **Reset Data** (Restablecer datos).

### <a id="trace-files"></a>Recopilación de archivos de seguimiento

Para recopilar los seguimientos de depuración, ejecute los siguientes comandos desde un símbolo del sistema administrativo:

1. `cd /d "%ProgramFiles%\Azure Cosmos DB Emulator"`
2. `CosmosDB.Emulator.exe /shutdown`. Vea la bandeja del sistema para asegurarse de que el programa se ha cerrado; esto podría tardar un minuto. También puede hacer clic en **Salir** en la interfaz de usuario del emulador de Azure Cosmos.
3. `CosmosDB.Emulator.exe /starttraces`
4. `CosmosDB.Emulator.exe`
5. Reproduzca el problema. Si el Explorador de datos no funciona, solo necesitará esperar a que el explorador se abra durante unos segundos para detectar el error.
5. `CosmosDB.Emulator.exe /stoptraces`
6. Vaya a `%ProgramFiles%\Azure Cosmos DB Emulator` y localice el archivo docdbemulator_000001.etl.
7. Envíe el archivo .etl junto con los pasos para reproducirlo a [ askcosmosdb@microsoft.com ](mailto:askcosmosdb@microsoft.com) para la depuración.

### <a id="uninstall"></a>Desinstalación del emulador local

1. Cierre todas las instancias abiertas del emulador local; para ello, haga clic con el botón derecho en el icono del emulador de Azure Cosmos en la bandeja del sistema y haga clic en Salir. Todas las instancias pueden tardar un minuto en salir.
2. En el cuadro de búsqueda de Windows, escriba **Aplicaciones y características** y haga clic en el resultado **Aplicaciones y características (configuración del sistema)** .
3. En la lista de aplicaciones, desplácese a **Emulador de Azure Cosmos DB**, selecciónela, haga clic en **Desinstalar**, confirme y haga clic en **Desinstalar** de nuevo.
4. Cuando la aplicación se haya desinstalado, vaya a `%LOCALAPPDATA%\CosmosDBEmulator` y elimine la carpeta.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido a usar el emulador local para desarrollar código en un entorno local gratuito. Ahora puede pasar al siguiente tutorial y aprender a exportar certificados SSL del emulador.

> [!div class="nextstepaction"]
> [Exportación de los certificados del emulador de Azure Cosmos](local-emulator-export-ssl-certificates.md)
