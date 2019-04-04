---
title: 'Inicio rápido: Ingesta de datos mediante el SDK de .NET Standard de Azure Data Explorer (versión preliminar)'
description: En esta guía de inicio rápido, obtendrá información sobre cómo ingerir (cargar) datos en Azure Data Explorer con el SDK de .Net Standard.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: quickstart
ms.date: 11/18/2018
ms.openlocfilehash: 327ab46b01e15b77e675e052dbfe8b3453b566a6
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/01/2019
ms.locfileid: "58757117"
---
# <a name="quickstart-ingest-data-using-the-azure-data-explorer-net-standard-sdk-preview"></a>Inicio rápido: Ingesta de datos mediante el SDK de .NET Standard de Azure Data Explorer (versión preliminar)

Azure Data Explorer (ADX) es un servicio de exploración de datos altamente escalable y rápido para datos de telemetría y registro. Azure Data Explorer proporciona dos bibliotecas cliente para .NET Standard: una [biblioteca de ingesta](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Ingest.NETStandard) y una [biblioteca de datos](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Data.NETStandard). Estas bibliotecas permiten ingerir (cargar) datos en un clúster y consultar datos desde el código. En esta guía de inicio rápido, primero creará una tabla y la asignación de datos en un clúster de prueba. A continuación, pondrá en cola la ingesta en el clúster y validará los resultados.

## <a name="prerequisites"></a>Requisitos previos

* Si no tiene una suscripción a Azure, cree una [cuenta gratuita de Azure](https://azure.microsoft.com/free/) antes de empezar.

* [Base de datos y clúster de prueba](create-cluster-database-portal.md)

## <a name="install-the-ingest-library"></a>Instalación de la biblioteca de ingesta

```
Install-Package Microsoft.Azure.Kusto.Ingest.NETStandard
```

## <a name="authentication"></a>Authentication

Para autenticar una aplicación, el Explorador de datos de Azure usa el identificador del inquilino de AAD. Para buscar el identificador de inquilino, use la dirección URL siguiente, sustituyendo su dominio por *SuDominio*.

```
https://login.windows.net/<YourDomain>/.well-known/openid-configuration/
```

Por ejemplo, si el nombre de dominio es *contoso.com*, la dirección URL es: [https://login.windows.net/contoso.com/.well-known/openid-configuration/](https://login.windows.net/contoso.com/.well-known/openid-configuration/). Haga clic en esta dirección URL para ver los resultados. la primera línea es como sigue. 

```
"authorization_endpoint":"https://login.windows.net/6babcaad-604b-40ac-a9d7-9fd97c0b779f/oauth2/authorize"
```

En este caso es el id. de inquilino es `6babcaad-604b-40ac-a9d7-9fd97c0b779f`.

En este ejemplo se utiliza un usuario y una contraseña de AAD para la autenticación para acceder al clúster. También puede usar el certificado de la aplicación de AAD y la clave de aplicación de AAD. Establezca los valores de `tenantId`, `user` y `password` antes de ejecutar este código.

```csharp
var tenantId = "<TenantId>";
var user = "<User>";
var password = "<Password>";
```

## <a name="construct-the-connection-string"></a>Creación de la cadena de conexión
Ahora, cree la cadena de conexión. Creará la tabla de destino y la asignación en un paso posterior.

```csharp
var kustoUri = "https://<ClusterName>.<Region>.kusto.windows.net:443/";
var database = "<DatabaseName>";

var kustoConnectionStringBuilder =
    new KustoConnectionStringBuilder(kustoUri)
    {
        FederatedSecurity = true,
        InitialCatalog = database,
        UserID = user,
        Password = password,
        Authority = tenantId
    };
```

## <a name="set-source-file-information"></a>Definición de la información del archivo de origen

Establezca la ruta de acceso del archivo de origen. Este ejemplo utiliza un archivo de ejemplo hospedado en Azure Blob Storage. El conjunto de datos de ejemplo de **StormEvents** contiene datos relacionados con el tiempo de los [National Centers for Environmental Information](https://www.ncdc.noaa.gov/stormevents/).

```csharp
var blobPath = "https://kustosamplefiles.blob.core.windows.net/samplefiles/StormEvents.csv?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D";
```

## <a name="create-a-table-on-your-test-cluster"></a>Creación de una tabla en el clúster de prueba
Cree una tabla que denominada `StormEvents` que coincida con el esquema de los datos del archivo `StormEvents.csv`.

```csharp
var table = "StormEvents";
using (var kustoClient = KustoClientFactory.CreateCslAdminProvider(kustoConnectionStringBuilder))
{
    var command =
        CslCommandGenerator.GenerateTableCreateCommand(
            table,
            new[]
            {
                Tuple.Create("StartTime", "System.DateTime"),
                Tuple.Create("EndTime", "System.DateTime"),
                Tuple.Create("EpisodeId", "System.Int32"),
                Tuple.Create("EventId", "System.Int32"),
                Tuple.Create("State", "System.String"),
                Tuple.Create("EventType", "System.String"),
                Tuple.Create("InjuriesDirect", "System.Int32"),
                Tuple.Create("InjuriesIndirect", "System.Int32"),
                Tuple.Create("DeathsDirect", "System.Int32"),
                Tuple.Create("DeathsIndirect", "System.Int32"),
                Tuple.Create("DamageProperty", "System.Int32"),
                Tuple.Create("DamageCrops", "System.Int32"),
                Tuple.Create("Source", "System.String"),
                Tuple.Create("BeginLocation", "System.String"),
                Tuple.Create("EndLocation", "System.String"),
                Tuple.Create("BeginLat", "System.Double"),
                Tuple.Create("BeginLon", "System.Double"),
                Tuple.Create("EndLat", "System.Double"),
                Tuple.Create("EndLon", "System.Double"),
                Tuple.Create("EpisodeNarrative", "System.String"),
                Tuple.Create("EventNarrative", "System.String"),
                Tuple.Create("StormSummary", "System.Object"),
            });

    kustoClient.ExecuteControlCommand(command);
}
```

## <a name="define-ingestion-mapping"></a>Definición de la asignación de ingesta

Asigna los datos de CSV entrantes a los nombres de columna utilizados al crear la tabla.
Aprovisione un [objeto de asignación de columnas de CSV](/azure/kusto/management/tables#create-ingestion-mapping) en esa tabla.

```csharp
var tableMapping = "StormEvents_CSV_Mapping";
using (var kustoClient = KustoClientFactory.CreateCslAdminProvider(kustoConnectionStringBuilder))
{
    var command =
        CslCommandGenerator.GenerateTableCsvMappingCreateCommand(
            table,
            tableMapping,
            new[]
            {
                new CsvColumnMapping { ColumnName = "StartTime", Ordinal = 0 },
                new CsvColumnMapping { ColumnName = "EndTime", Ordinal = 1 },
                new CsvColumnMapping { ColumnName = "EpisodeId", Ordinal = 2 },
                new CsvColumnMapping { ColumnName = "EventId", Ordinal = 3 },
                new CsvColumnMapping { ColumnName = "State", Ordinal = 4 },
                new CsvColumnMapping { ColumnName = "EventType", Ordinal = 5 },
                new CsvColumnMapping { ColumnName = "InjuriesDirect", Ordinal = 6 },
                new CsvColumnMapping { ColumnName = "InjuriesIndirect", Ordinal = 7 },
                new CsvColumnMapping { ColumnName = "DeathsDirect", Ordinal = 8 },
                new CsvColumnMapping { ColumnName = "DeathsIndirect", Ordinal = 9 },
                new CsvColumnMapping { ColumnName = "DamageProperty", Ordinal = 10 },
                new CsvColumnMapping { ColumnName = "DamageCrops", Ordinal = 11 },
                new CsvColumnMapping { ColumnName = "Source", Ordinal = 12 },
                new CsvColumnMapping { ColumnName = "BeginLocation", Ordinal = 13 },
                new CsvColumnMapping { ColumnName = "EndLocation", Ordinal = 14 },
                new CsvColumnMapping { ColumnName = "BeginLat", Ordinal = 15 },
                new CsvColumnMapping { ColumnName = "BeginLon", Ordinal = 16 },
                new CsvColumnMapping { ColumnName = "EndLat", Ordinal = 17 },
                new CsvColumnMapping { ColumnName = "EndLon", Ordinal = 18 },
                new CsvColumnMapping { ColumnName = "EpisodeNarrative", Ordinal = 19 },
                new CsvColumnMapping { ColumnName = "EventNarrative", Ordinal = 20 },
                new CsvColumnMapping { ColumnName = "StormSummary", Ordinal = 21 },
            });

    kustoClient.ExecuteControlCommand(command);
}
```

## <a name="queue-a-message-for-ingestion"></a>Colocación de un mensaje en cola para la ingesta

Coloca en cola un mensaje para extraer datos desde Blob Storage e introduce esos datos en ADX.

```csharp
var ingestUri = "https://ingest-<ClusterName>.<Region>.kusto.windows.net:443/";
var ingestConnectionStringBuilder =
    new KustoConnectionStringBuilder(ingestUri)
    {
        FederatedSecurity = true,
        InitialCatalog = database,
        UserID = user,
        Password = password,
        Authority = tenantId
    };

using (var ingestClient = KustoIngestFactory.CreateQueuedIngestClient(ingestConnectionStringBuilder))
{
    var properties =
        new KustoQueuedIngestionProperties(database, table)
        {
            Format = DataSourceFormat.csv,
            CSVMappingReference = tableMapping,
            IgnoreFirstRecord = true
        };

    ingestClient.IngestFromSingleBlob(blobPath, deleteSourceOnSuccess: false, ingestionProperties: properties);
}
```

## <a name="validate-data-was-ingested-into-the-table"></a>Validación de la ingesta de los datos en la tabla

Espere entre cinco y diez minutos para que la ingesta en cola programe la ingesta y cargue los datos en ADX. A continuación, ejecute el siguiente código para obtener el recuento de registros de la tabla `StormEvents`.

```csharp
using (var cslQueryProvider = KustoClientFactory.CreateCslQueryProvider(kustoConnectionStringBuilder))
{
    var query = $"{table} | count";

    var results = cslQueryProvider.ExecuteQuery<long>(query);
    Console.WriteLine(results.Single());
}
```

## <a name="run-troubleshooting-queries"></a>Ejecución de consultas de solución de problemas

Inicie sesión en [https://dataexplorer.azure.com](https://dataexplorer.azure.com) y conéctese al clúster Ejecute el siguiente comando en la base de datos para ver si se ha producido algún error de ingesta en las últimas cuatro horas. Reemplace el nombre de la base de datos antes de ejecutarlo.

```Kusto
.show ingestion failures
| where FailedOn > ago(4h) and Database == "<DatabaseName>"
```

Ejecute el siguiente comando para ver el estado de todas las operaciones de ingesta en las últimas cuatro horas. Reemplace el nombre de la base de datos antes de ejecutarlo.

```Kusto
.show operations
| where StartedOn > ago(4h) and Database == "<DatabaseName>" and Operation == "DataIngestPull"
| summarize arg_max(LastUpdatedOn, *) by OperationId
```

## <a name="clean-up-resources"></a>Limpieza de recursos

Si tiene previsto seguir nuestros tutoriales y guías de inicio rápido, conserve los recursos que creó. De lo contrario, ejecute el siguiente comando en la base de datos para limpiar la tabla `StormEvents`.

```Kusto
.drop table StormEvents
```

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Escritura de consultas](write-queries.md)
