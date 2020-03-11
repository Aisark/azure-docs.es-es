---
title: Authentication
description: Aprenda sobre la autenticación en Azure Synapse Analytics mediante la autenticación de Azure Active Directory (AAD) o SQL Server.
services: sql-data-warehouse
author: julieMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: security
ms.date: 04/02/2019
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019
tag: azure-synapse
ms.openlocfilehash: 235d13cfd6f33830d832a6a79e3bc1c78bbfe53e
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/29/2020
ms.locfileid: "78195981"
---
# <a name="authenticate-to-azure-synapse-analytics"></a>Autenticación en Azure Synapse Analytics
Aprenda sobre la autenticación en SQL Analytics en Azure Synapse mediante la autenticación de Azure Active Directory (AAD) o SQL Server.

Para conectarse a un grupo de SQL, debe transmitir las credenciales de seguridad para realizar la autenticación. Después de establecer una conexión, determinados valores de conexión se configuran como parte del establecimiento de la sesión de la consulta.  

Para más información sobre la seguridad y cómo habilitar las conexiones al almacenamiento de datos, consulte la [documentación sobre cómo proteger una base de datos](sql-data-warehouse-overview-manage-security.md).

## <a name="sql-authentication"></a>Autenticación SQL
Para conectarse al grupo de SQL, debe proporcionar la información siguiente:

* Nombre de servidor completo
* Especificar la autenticación de SQL
* Nombre de usuario
* Contraseña
* Base de datos predeterminada (opcional)

De forma predeterminada, la conexión se realiza a la base de datos *maestra* y no a la base de datos de usuario. Para conectarse a la base de datos de usuario puede hacer dos cosas:

* Especificar la base de datos predeterminada al registrar el servidor con el Explorador de objetos de SQL Server en SSDT, SSMS o en la cadena de conexión de la aplicación. Por ejemplo, incluya el parámetro InitialCatalog para una conexión ODBC.
* Resalte la base de datos de usuario antes de crear una sesión en SSDT.

> [!NOTE]
> La instrucción **USE MyDatabase;** de Transact-SQL no se admite para cambiar la base de datos de una conexión. Puede encontrar instrucciones sobre cómo conectarse a un grupo de SQL con SSDT en el artículo [Consulta con Visual Studio](sql-data-warehouse-query-visual-studio.md).
> 
> 

## <a name="azure-active-directory-aad-authentication"></a>Autenticación de Azure Active Directory (AAD)
La autenticación de [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) es un mecanismo que permite conectar con el grupo de SQL mediante identidades de Azure Active Directory (Azure AD). Con la autenticación de Azure Active Directory puede administrar centralmente las identidades de los usuarios de la base de datos y otros servicios de Microsoft en una ubicación central. La administración central de identificadores ofrece una ubicación única para administrar usuarios de Azure Synapse y simplifica la administración de permisos. 

### <a name="benefits"></a>Ventajas
Entre las ventajas de Azure Active Directory, se incluyen:

* Proporciona una alternativa a la autenticación de SQL Server.
* Ayuda a detener la proliferación de identidades de usuario en los servidores de base de datos.
* Permite la rotación de contraseñas en un solo lugar
* Permite administrar los permisos de la base de datos con grupos externos (AAD).
* Elimina el almacenamiento de contraseñas mediante la habilitación de la autenticación integrada de Windows y otras formas de autenticación compatibles con Azure Active Directory.
* Usa usuarios de base de datos independiente para autenticar las identidades en el nivel de base de datos.
* Admite la autenticación basada en tokens para las aplicaciones que se conectan al grupo de SQL.
* Admite Multi-Factor Authentication mediante autenticación universal de Active Directory para varias herramientas, incluidas [SQL Server Management Studio](../sql-database/sql-database-ssms-mfa-authentication.md) y [SQL Server Data Tools](https://docs.microsoft.com/sql/ssdt/azure-active-directory?toc=/azure/sql-data-warehouse/toc.json).

> [!NOTE]
> Azure Active Directory todavía es relativamente nuevo y tiene algunas limitaciones. Para asegurarse de que Azure Active Directory sea una buena elección para su entorno, consulte [Características y limitaciones de Azure AD](../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations), específicamente las consideraciones adicionales.
> 
> 

### <a name="configuration-steps"></a>Pasos de configuración
Siga estos pasos para configurar la autenticación de Azure Active Directory.

1. Crear y rellenar un Azure Active Directory.
2. Opcional: Asociar o cambiar la instancia de Active Directory que esté asociada a la suscripción de Azure.
3. Crear un administrador de Azure Active Directory para Azure Synapse.
4. Configurar los equipos cliente.
5. Crear usuarios de base de datos independiente  en la base de datos y asignados a identidades de Azure AD.
6. Conectarse al grupo de SQL mediante identidades de Azure AD.

Actualmente los usuarios de Azure Active Directory no se muestran en el Explorador de objetos de SSDT. Como solución alternativa, vea los usuarios de [sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx).

### <a name="find-the-details"></a>Búsqueda de los detalles
* Los pasos para configurar y usar la autenticación de Azure Active Directory son casi idénticos para Azure SQL Database y SQL Analytics en Azure Synapse. Siga los pasos detallados del tema [Conexión a SQL Database o al grupo de SQL mediante la autenticación de Azure Active Directory](../sql-database/sql-database-aad-authentication.md).
* Cree roles de base de datos personalizados y agrégueles usuarios. A continuación, conceda permisos específicos a los roles. Para obtener más información, consulte [Introducción a los permisos de los motores de bases de datos](https://msdn.microsoft.com/library/mt667986.aspx).

## <a name="next-steps"></a>Pasos siguientes
Para empezar a realizar consultas con Visual Studio u otras aplicaciones, consulte [Consulta con Visual Studio](sql-data-warehouse-query-visual-studio.md).
