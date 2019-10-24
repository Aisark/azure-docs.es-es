---
title: Enmascaramiento dinámico de datos en Azure SQL Database y Data Warehouse | Microsoft Docs
description: El enmascaramiento dinámico de datos limita la exposición de información confidencial enmascarándola a los usuarios de SQL Database y Data Warehouse que no tienen privilegios.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: ronitr
ms.author: ronitr
ms.reviewer: vanto
ms.date: 03/04/2019
ms.openlocfilehash: e36e91330232a90ff51cf92ce8dc920b51e2d914
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/16/2019
ms.locfileid: "72430122"
---
# <a name="dynamic-data-masking-for-azure-sql-database-and-data-warehouse"></a>Enmascaramiento dinámico de datos en Azure SQL Database y Data Warehouse

El enmascaramiento dinámico de datos de SQL Database limita la exposición de información confidencial mediante su enmascaramiento a los usuarios sin privilegios. 

El enmascaramiento de datos dinámicos ayuda a impedir el acceso no autorizado a datos confidenciales permitiendo a los usuarios designar la cantidad de los datos confidenciales que se revelarán con un impacto mínimo en el nivel de aplicación. Es una característica de seguridad basada en directivas que oculta los datos confidenciales en el conjunto de resultados de una consulta sobre los campos designados de la base de datos, aunque que los datos de la base de datos no cambian.

Por ejemplo, un representante de servicio de un centro de llamadas podría identificar a los autores de las llamadas a partir de varios dígitos del número de su tarjeta de crédito, pero esa es una información que no debería exponerse por completo al representante del servicio. Se puede definir una regla de enmascaramiento que enmascare todo excepto los cuatro últimos dígitos de un número de tarjeta de crédito en el conjunto de resultados de cualquier consulta. Otro ejemplo, una máscara de datos apropiada se puede definir para proteger los datos de información de identificación personal, para que un desarrollador pueda consultar los entornos de producción para solucionar problemas sin infringir las reglamentaciones de cumplimiento.

## <a name="dynamic-data-masking-basics"></a>Aspectos básicos del enmascaramiento dinámico de datos

Para configurar una directiva de enmascaramiento dinámico de datos en Azure Portal, se selecciona la operación de enmascaramiento dinámico de datos en la hoja de configuración de SQL Database.

### <a name="dynamic-data-masking-permissions"></a>Permisos de enmascaramiento de datos dinámicos

El enmascaramiento dinámico de datos se puede configurar mediante el administrador de Azure SQL Database, el administrador del servidor o los roles de [Administrador de seguridad de SQL](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-security-manager).

### <a name="dynamic-data-masking-policy"></a>Directiva de enmascaramiento de datos dinámicos

* **Usuarios de SQL excluidos del enmascaramiento**: conjunto de usuarios de SQL o identidades de AAD que obtendrán datos sin máscara en los resultados de consulta SQL. Los usuarios con privilegios de administrador se excluirán siempre del enmascaramiento y verán los datos originales sin ninguna máscara.
* **Reglas de enmascaramiento**: un conjunto de reglas que definen los campos designados para el enmascaramiento y la función de enmascaramiento que se va a usar. Los campos designados se pueden definir mediante un nombre de esquema de base de datos, un nombre de tabla y un nombre de columna.
* **Funciones de enmascaramiento** : un conjunto de métodos que controlan la exposición de datos para diferentes escenarios.

| Función de enmascaramiento | Lógica de enmascaramiento |
| --- | --- |
| **Valor predeterminado** |**Enmascaramiento completo según los tipos de datos de los campos designados**<br/><br/>• Use XXXX o menos X si el tamaño del campo es menor que 4 caracteres para los tipos de datos de cadena (nchar, ntext, nvarchar).<br/>• Use un valor de cero para los tipos de datos numéricos (bigint, bit, decimal, int, money, numeric, smallint, smallmoney, tinyint, float, real).<br/>• Use 01-01-1900 para los tipos de datos de fecha y hora (date, datetime2, datetime, datetimeoffset, smalldatetime, time).<br/>• Para variant SQL, se utiliza el valor predeterminado del tipo actual.<br/>• Para XML, se utiliza el documento \<masked/>.<br/>• Use un valor vacío para los tipos de datos especiales (tipos timestamp table, hierarchyid, GUID, binary, image, varbinary spatial). |
| **Tarjeta de crédito** |**Método de enmascaramiento que expone los últimos cuatro dígitos de los campos designados** y agrega una cadena constante como prefijo en el formato de una tarjeta de crédito.<br/><br/>XXXX-XXXX-XXXX-1234 |
| **Correo electrónico** |**Método de enmascaramiento que expone la primera letra y reemplaza el dominio con XXX.com** con una cadena constante como prefijo en el formato de una dirección de correo electrónico.<br/><br/>aXX@XXXX.com |
| **Número aleatorio** |**Método de enmascaramiento que genera un número aleatorio** según los límites seleccionados y los tipos de datos reales. Si los límites designados son iguales, la función de enmascaramiento será un número constante.<br/><br/>![Panel de navegación](./media/sql-database-dynamic-data-masking-get-started/1_DDM_Random_number.png) |
| **Texto personalizado** |**Método de enmascaramiento que expone el primero y el último carácter** y agrega una cadena de relleno personalizada en el medio. Si la cadena original es más corta que el prefijo y el sufijo expuestos, se usará únicamente la cadena de relleno. <br/>prefijo[relleno]sufijo<br/><br/>![Panel de navegación](./media/sql-database-dynamic-data-masking-get-started/2_DDM_Custom_text.png) |

<a name="Anchor1"></a>

### <a name="recommended-fields-to-mask"></a>Campos recomendados para enmascarar

El motor de recomendaciones de DDM marca determinados campos de la base de datos como campos potencialmente confidenciales, que pueden ser buenos candidatos para el enmascaramiento. En la hoja Enmascaramiento de datos dinámicos del portal, verá las columnas recomendadas para la base de datos. Todo lo que debe hacer es hacer clic en **Agregar máscara** para una o más columnas y, después, en **Guardar** a fin de aplicar una máscara para estos campos.

## <a name="set-up-dynamic-data-masking-for-your-database-using-powershell-cmdlets"></a>Configuración del enmascaramiento de datos dinámicos para la base de datos mediante cmdlets de PowerShell

Consulte [Cmdlets de Azure SQL Database](https://docs.microsoft.com/powershell/module/az.sql).

## <a name="set-up-dynamic-data-masking-for-your-database-using-rest-api"></a>Configuración del enmascaramiento de datos dinámicos para la base de datos mediante la API de REST

Consulte [Operaciones para Azure SQL Database](https://docs.microsoft.com/rest/api/sql/).
