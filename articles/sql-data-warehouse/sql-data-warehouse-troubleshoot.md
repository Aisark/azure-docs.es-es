---
title: Solución de problemas de Azure SQL Data Warehouse | Microsoft Docs
description: Cómo solucionar los problemas de Azure SQL Data Warehouse.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 4/26/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 02591185914f3b04a70af3b7c5d607f4a2865806
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "65154265"
---
# <a name="troubleshooting-azure-sql-data-warehouse"></a>Solución de problemas de Azure SQL Data Warehouse
En este artículo figuran las preguntas habituales sobre la solución de problemas.

## <a name="connecting"></a>Conexión
| Problema                                                        | Resolución                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Error de inicio de sesión del usuario 'NT AUTHORITY\ANONYMOUS LOGON'. (Microsoft SQL Server, error: 18456) | Este error se produce cuando un usuario de AAD intenta conectarse a la base de datos maestra, pero no tiene un usuario ahí.  Para corregir este problema, especifique la instancia de SQL Data Warehouse a la que desea conectarse en el momento de la conexión o agregue el usuario a la base de datos maestra.  Consulte el artículo [Información general sobre seguridad][Security overview] para obtener más detalles. |
| La entidad de seguridad del servidor "MyUserName" no puede obtener acceso a la base de datos "maestra" en el contexto de seguridad actual. No se puede abrir la base de datos predeterminada del usuario. Error de inicio de sesión. Error de inicio de sesión del usuario 'MyUserName'. (Microsoft SQL Server, error: 916) | Este error se produce cuando un usuario de AAD intenta conectarse a la base de datos maestra, pero no tiene un usuario ahí.  Para corregir este problema, especifique la instancia de SQL Data Warehouse a la que desea conectarse en el momento de la conexión o agregue el usuario a la base de datos maestra.  Consulte el artículo [Información general sobre seguridad][Security overview] para obtener más detalles. |
| Error CTAIP                                                  | Este error puede producirse cuando se ha creado un inicio de sesión en la base de datos maestra de SQL Server, pero no en la base de datos de SQL Data Warehouse.  Si se produce este error, eche un vistazo al artículo sobre la [información general de seguridad][Security overview].  En este artículo se explica cómo crear un inicio de sesión y un usuario en una base de datos maestra y luego cómo crear un usuario en la base de datos de SQL Data Warehouse. |
| Bloqueado por el firewall                                          | Las bases de datos de Azure SQL están protegidas por firewalls de nivel de servidor y base de datos para garantizar que solo las direcciones IP conocidas tienen acceso a una base de datos. Los firewalls están protegidos de manera predeterminada, lo que significa que debe habilitar explícitamente una dirección IP o un intervalo de direcciones para poder conectarse.  Para configurar el firewall para el acceso, siga los pasos de la sección de [configuración del acceso de nivel de firewall para el cliente IP][Configure server firewall access for your client IP] en las [instrucciones de aprovisionamiento][Provisioning instructions]. |
| No se puede conectar con una herramienta o un controlador                           | SQL Data Warehouse recomienda usar [SSMS][SSMS], [SSDT para Visual Studio][SSDT for Visual Studio] o [sqlcmd][sqlcmd] para consultar los datos. Para obtener más información sobre los controladores y cómo conectarse a SQL Data Warehouse, consulte los artículos [Controladores de SQL Data Warehouse de Azure][Drivers for Azure SQL Data Warehouse] y [Conexión a SQL Data Warehouse][Connect to Azure SQL Data Warehouse]. |

## <a name="tools"></a>Herramientas
| Problema                                                        | Resolución                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| El Explorador de objetos de Visual Studio no muestra usuarios de AAD           | Este es un problema conocido.  Como solución alternativa, vea los usuarios de [sys.database_principals][sys.database_principals].  Consulte [Autenticación a Azure SQL Data Warehouse][Authentication to Azure SQL Data Warehouse] para obtener más información sobre Azure Active Directory con SQL Data Warehouse. |
| El scripting manual, mediante el Asistente para scripting, o la conexión a través de SSMS es lenta, no responde o genera errores | Asegúrese de que se han creado usuarios en la base de datos maestra. En las opciones de scripting, asegúrese también de que la edición del motor se ha establecido como "Microsoft Azure SQL Data Warehouse Edition" y que el tipo de motor es "Microsoft Azure SQL Database". |
| Errores de generación de scripts en SSMS                             | La generación de un script para el almacenamiento de datos de SQL genera un error si la opción "Generar script para objetos dependientes" está establecida en "True". Como solución alternativa, los usuarios deben ir manualmente a Herramientas -> Opciones ->Explorador de objetos de SQL Server -> opción "Generar script para objetos dependientes" y establecerla en False. |

## <a name="performance"></a>Rendimiento
| Problema                                                        | Resolución                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Solución de problemas de rendimiento de consultas                            | Si está tratando de solucionar los problemas de una consulta determinada, comience por [aprender a supervisar las consultas][Learning how to monitor your queries]. |
| Un bajo rendimiento de las consultas y unos planes mal diseñados suelen ser el resultado de la falta de estadísticas | La causa más común del rendimiento ineficiente es la falta de estadísticas en las tablas.  Para obtener más información sobre cómo crear estadísticas y por qué son tan importantes para el rendimiento, consulte [Mantenimiento de estadísticas de tablas][Statistics]. |
| Baja simultaneidad o consultas en cola                             | Para comprender el modo de equilibrar la asignación de memoria con la simultaneidad, es importante entender la [administración de la carga de trabajo][Workload management]. |
| Implementación de procedimientos recomendados                              | El mejor lugar para empezar a aprender formas de mejorar el rendimiento de las consultas es el artículo [Procedimientos recomendados para SQL Data Warehouse][SQL Data Warehouse best practices]. |
| Mejora del rendimiento con el escalado                      | En ocasiones, la solución para mejorar el rendimiento consiste simplemente en agregar más potencia de proceso a las consultas mediante el [escalado de SQL Data Warehouse][Scaling your SQL Data Warehouse]. |
| Bajo rendimiento de las consultas como resultado de poca calidad del índice     | A veces, la velocidad de las consultas se puede reducir debido a la [baja calidad del índice de almacén de columnas][Poor columnstore index quality].  Para obtener más información al respecto y conocer el modo de [volver a generar los índices para mejorar la calidad del segmento][Rebuild indexes to improve segment quality], consulte este artículo. |

## <a name="system-management"></a>Administración del sistema
| Problema                                                        | Resolución                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Mens. 40847: No se pudo realizar la operación porque el servidor superaría la cuota de la unidad de transacción de la base de datos permitida de 45000. | Reduzca la unidad [DWU][DWU] de la base de datos que intenta crear o[ solicite un aumento de la cuota][request a quota increase]. |
| Investigación del uso del espacio                              | Consulte los [tamaños de tabla][Table sizes] para comprender el uso del espacio del sistema. |
| Ayuda con la administración de tablas                                    | Para obtener ayuda con la administración de las tablas, consulte la [información general sobre las tablas][Overview].  Este artículo también incluye vínculos a temas más detallados como [tipos de datos de tabla][Data types], [distribución de una tabla][Distribute], [indexación de una tabla][Index], [creación de particiones de una tabla][Partition], [mantenimiento de estadísticas de tabla][Statistics] y [tablas temporales][Temporary]. |
| La barra de progreso de Cifrado de datos transparente (TDE) no se actualiza en Azure Portal | El estado de TDE se puede ver a través de [powershell](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption). |

## <a name="polybase"></a>PolyBase
| Problema                                           | Resolución                                                   |
| :---------------------------------------------- | :----------------------------------------------------------- |
| Error al exportar con los tipos TINYINT y DATE             | Para formatos de archivo Parquet y ORC, los valores de tipo DATE deben estar entre 01-01-1970 00:00:01 UTC y 19-01-2038 03:14:07. Los valores de tipo TINYINT deben estar entre 0 y 127.    |
| Problema con el tipo DECIMAL de Parquet: al escribir desde el tipo  DecimalType(18,4) de Spark e importarlo en una columna de tipo DOUBLE o REAL, genera “Error: java.base/java.lang.Long no puede convertirse en java.base/java.lang.Float”. | Debe importar en bigint y dividir por 10 000 o usar el conector [Databricks] de SQL Data Warehouse. |
| Problema con el tipo DATE de Parquet: al escribir desde el tipo DATE de Spark e importarlo en una columna de tipo DATE o DATETIME, genera el “Error: java.base/java.lang.Integer no se puede convertir a parquet.io.api.Binary”. | Debe usar un tipo diferente de Spark (int) y calcular la fecha o usar el conector [Databricks] de SQL Data Warehouse. |

## <a name="differences-from-sql-database"></a>Diferencias con respecto a SQL Database
| Problema                                 | Resolución                                                   |
| :------------------------------------ | :----------------------------------------------------------- |
| Características de SQL Database no admitidas     | Consulte [Características no compatibles de las tablas][Unsupported table features]. |
| Tipos de datos de SQL Database no admitidos   | Consulte [Tipos de datos no admitidos][Unsupported data types].        |
| Limitaciones de DELETE y UPDATE         | Consulte las [soluciones alternativas para UPDATE][UPDATE workarounds], las [soluciones alternativas para DELETE][DELETE workarounds] y el [uso de CTAS para resolver la sintaxis de UPDATE y DELETE no admitida][Using CTAS to work around unsupported UPDATE and DELETE syntax]. |
| No se admite la instrucción MERGE      | Consulte las [soluciones alternativas para MERGE][MERGE workarounds].                  |
| Limitaciones de procedimientos almacenados          | Consulte [Limitaciones de procedimientos almacenados][Stored procedure limitations] para conocer algunas de las limitaciones de los procedimientos almacenados. |
| Los UDF no admiten instrucciones SELECT | Se trata de una limitación actual de nuestros UDF.  Consulte [CREATE FUNCTION][CREATE FUNCTION] para comprobar la sintaxis que se admite. |

## <a name="next-steps"></a>Pasos siguientes
Para obtener más ayuda en la búsqueda de soluciones para su problema, aquí tiene algunos otros recursos que puede probar.

* [Blogs]
* [Solicitud de función]
* [Vídeos]
* [Blogs del equipo de CAT]
* [Creación de una incidencia de soporte técnico]
* [Foro de MSDN]
* [Foro Stack Overflow]
* [Twitter]

<!--Image references-->

<!--Article references-->
[Security overview]: sql-data-warehouse-overview-manage-security.md
[SSMS]: /sql/ssms/download-sql-server-management-studio-ssms
[SSDT for Visual Studio]: sql-data-warehouse-install-visual-studio.md
[Drivers for Azure SQL Data Warehouse]: sql-data-warehouse-connection-strings.md
[Connect to Azure SQL Data Warehouse]: sql-data-warehouse-connect-overview.md
[Creación de una incidencia de soporte técnico]: sql-data-warehouse-get-started-create-support-ticket.md
[Scaling your SQL Data Warehouse]: sql-data-warehouse-manage-compute-overview.md
[DWU]: sql-data-warehouse-overview-what-is.md
[request a quota increase]: sql-data-warehouse-get-started-create-support-ticket.md
[Learning how to monitor your queries]: sql-data-warehouse-manage-monitor.md
[Provisioning instructions]: sql-data-warehouse-get-started-provision.md
[Configure server firewall access for your client IP]: sql-data-warehouse-get-started-provision.md
[SQL Data Warehouse best practices]: sql-data-warehouse-best-practices.md
[Table sizes]: sql-data-warehouse-tables-overview.md#table-size-queries
[Unsupported table features]: sql-data-warehouse-tables-overview.md#unsupported-table-features
[Unsupported data types]: sql-data-warehouse-tables-data-types.md#unsupported-data-types
[Overview]: sql-data-warehouse-tables-overview.md
[Data types]: sql-data-warehouse-tables-data-types.md
[Distribute]: sql-data-warehouse-tables-distribute.md
[Index]: sql-data-warehouse-tables-index.md
[Partition]: sql-data-warehouse-tables-partition.md
[Statistics]: sql-data-warehouse-tables-statistics.md
[Temporary]: sql-data-warehouse-tables-temporary.md
[Poor columnstore index quality]: sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality
[Rebuild indexes to improve segment quality]: sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[Workload management]: resource-classes-for-workload-management.md
[Using CTAS to work around unsupported UPDATE and DELETE syntax]: sql-data-warehouse-develop-ctas.md
[UPDATE workarounds]: sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-update-statements
[DELETE workarounds]: sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-delete-statements
[MERGE workarounds]: sql-data-warehouse-develop-ctas.md#replace-merge-statements
[Stored procedure limitations]: sql-data-warehouse-develop-stored-procedures.md#limitations
[Authentication to Azure SQL Data Warehouse]: sql-data-warehouse-authentication.md


<!--MSDN references-->
[sys.database_principals]: /sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql
[CREATE FUNCTION]: /sql/t-sql/statements/create-function-sql-data-warehouse
[sqlcmd]: sql-data-warehouse-get-started-connect-sqlcmd.md

<!--Other Web references-->
[Blogs]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Blogs del equipo de CAT]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Solicitud de función]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Foro de MSDN]: https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse
[Foro Stack Overflow]: https://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Vídeos]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[Databricks]: https://docs.microsoft.com/azure/azure-databricks/databricks-extract-load-sql-data-warehouse#load-data-into-azure-sql-data-warehouse
