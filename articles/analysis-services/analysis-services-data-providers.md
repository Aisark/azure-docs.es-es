---
title: Bibliotecas de cliente de Azure Analysis Services | Microsoft Docs
description: Se describen las bibliotecas de cliente necesarias para que las aplicaciones cliente y las herramientas de cliente se conecten a Azure Analysis Services.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: f099546ced64bd911916164879cd533329761c66
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/28/2020
ms.locfileid: "77914677"
---
# <a name="client-libraries-for-connecting-to-analysis-services"></a>Bibliotecas de cliente para la conexión con Analysis Services

Se requieren bibliotecas de cliente para que las aplicaciones cliente y las herramientas de cliente se puedan conectar a servidores de Analysis Services. Las aplicaciones cliente de Microsoft como Power BI Desktop, Excel, SQL Server Management Studio (SSMS) y extensión de proyectos de Analysis Services para Visual Studio instalan las tres bibliotecas de cliente y las actualizan junto con las actualizaciones de aplicaciones normales. En algunos casos, es posible que deba instalar las versiones más recientes de las bibliotecas de cliente. Las aplicaciones de cliente personalizadas también requieren la instalación de las bibliotecas de cliente.

## <a name="download-the-latest-client-libraries-windows-installer"></a>Descargar las bibliotecas de cliente más recientes (Windows Installer)  

|Descargar  |Versión del producto  | 
|---------|---------|
|[MSOLAP (amd64)](https://go.microsoft.com/fwlink/?linkid=829576)    |    15.1.21.27    |
|[MSOLAP (x86)](https://go.microsoft.com/fwlink/?linkid=829575)     |     15.1.21.27       |
|[AMO](https://go.microsoft.com/fwlink/?linkid=829578)     |   18.4.0.5    |
|[ADOMD](https://go.microsoft.com/fwlink/?linkid=829577)     |    18.4.0.5     |

## <a name="amo-and-adomd-nuget-packages"></a>AMO y ADOMD (paquetes NuGet)

Los objetos de administración de Analysis Services (AMO) y las bibliotecas cliente de ADOMD están disponibles como paquetes instalables en [NuGet.org](https://www.nuget.org/). Se recomienda migrar a referencias de NuGet en lugar de usar Windows Installer. 

|Paquete  | Versión del producto  | 
|---------|---------|
|[AMO](https://www.nuget.org/packages/Microsoft.AnalysisServices.retail.amd64/)    |    18.4.0.5     |
|[ADOMD](https://www.nuget.org/packages/Microsoft.AnalysisServices.AdomdClient.retail.amd64/)     |   18.4.0.5      |

Los ensamblados del paquete de NuGet AssemblyVersion siguen el control de versiones semántico: PRINCIPAL.SECUNDARIA.REVISIÓN. Las referencias de NuGet cargan la versión esperada, incluso si hay una versión diferente en la caché global de ensamblados (resultante de la instalación de MSI). El valor de REVISIÓN se incrementa para cada publicación. Las versiones de AMO y ADOMD se mantienen sincronizadas.

## <a name="understanding-client-libraries"></a>Descripción de las bibliotecas de cliente

Analysis Services usa tres bibliotecas de cliente, también conocidas como proveedores de datos. ADOMD.NET y Analysis Services Management Objects (AMO) son bibliotecas de cliente administradas. El proveedor OLE DB de Analysis Services (MSOLAP DLL) es una biblioteca de cliente nativa. Normalmente, las tres bibliotecas se instalan al mismo tiempo. **Azure Analysis Services requiere la versión más reciente de las tres bibliotecas**. 

Las aplicaciones cliente de Microsoft, como Power BI Desktop y Excel, instalan las tres bibliotecas de cliente y las actualizan cuando hay nuevas versiones disponibles. Dependiendo de la versión o la frecuencia de las actualizaciones, algunas bibliotecas de cliente pueden no ser las versiones más recientes que necesita Azure Analysis Services. Esto mismo se aplica a aplicaciones personalizadas u otras interfaces, como AsCmd, TOM, ADOMD.NET. Estas aplicaciones requieren la instalación manual o mediante programación de las bibliotecas. Las bibliotecas de cliente para la instalación manual se incluyen en los paquetes de características de SQL Server como paquetes de distribución. Sin embargo, estas bibliotecas de cliente están asociadas a la versión de SQL Server y no pueden ser la versión más reciente.  

Las bibliotecas de cliente para conexiones de cliente son diferentes de los proveedores de datos necesarios para conectarse desde un servidor de Azure Analysis Services a un origen de datos. Para más información sobre las conexiones a origen de datos, consulte [Conexiones a origen de datos](analysis-services-datasource.md).

## <a name="client-library-types"></a>Tipos de biblioteca de cliente

### <a name="analysis-services-ole-db-provider-msolap"></a>Proveedor OLE DB Analysis Services (MSOLAP) 

 El proveedor OLE DB de Analysis Services (MSOLAP) es la biblioteca de cliente nativa para las conexiones de base de datos de Analysis Services. Es utilizada indirectamente por ADOMD.NET y AMO, que delegan las solicitudes de conexión en el proveedor de datos. También se puede llamar al proveedor OLE DB directamente desde el código de la aplicación.  
  
 El proveedor OLE DB de Analysis Services se instala automáticamente con la mayoría de las herramientas y aplicaciones cliente usadas para tener acceso a las bases de datos de Analysis Services. Debe instalarse en los equipos que se utilizan para tener acceso a datos de Analysis Services.  
  
 Los proveedores OLE DB suelen especificarse en las cadenas de conexión. Una cadena de conexión de Analysis Services usa una nomenclatura diferente para hacer referencia al proveedor OLE DB: MSOLAP.\<versión>.dll.

### <a name="amo"></a>AMO  

 AMO es una biblioteca de cliente administrada que se usa para la administración del servidor y la definición de datos. Las herramientas y aplicaciones de cliente la instalan y utilizan. Por ejemplo, SQL Server Management Studio (SSMS) usa AMO para conectarse a Analysis Services. La conexión mediante AMO suele ser mínima y consta de `"data source=\<servername>"`. Una vez establecida una conexión, use la API para trabajar con las colecciones de base de datos y los objetos principales. Tanto Visual Studio como SSMS usan AMO para conectarse a una instancia de Analysis Services.  

  
### <a name="adomd"></a>ADOMD

 ADOMD.NET es una biblioteca de cliente de datos administrados que se usa para consultar datos de Analysis Services. Las herramientas y aplicaciones de cliente la instalan y utilizan. 
  
 Al conectarse a una base de datos, las propiedades de la cadena de conexión para las tres bibliotecas son similares. Casi cualquier cadena de conexión que defina para ADOMD.NET utilizando [Microsoft.AnalysisServices.AdomdClient.AdomdConnection.ConnectionString](/dotnet/api/microsoft.analysisservices.adomdclient.adomdconnection.connectionstring#Microsoft_AnalysisServices_AdomdClient_AdomdConnection_ConnectionString) funciona también para AMO y el proveedor OLE DB de Analysis Services (MSOLAP). Para más información, consulte [Propiedades de la cadena de conexión de &#40;Analysis Services&#41;](https://docs.microsoft.com/analysis-services/instances/connection-string-properties-analysis-services).  

  
##  <a name="bkmk_LibUpdate"></a>Cómo determinar la versión de la biblioteca de cliente   
  
### <a name="oleddb-msolap"></a>OLEDDB (MSOLAP)  
  
1.  Ir a `C:\Program Files\Microsoft Analysis Services\AS OLEDB\`. Si tiene más de una carpeta, elija el número más alto.
  
2.  Haga clic con el botón derecho en **msolap.dll** > **Propiedades** > **Detalles**. Si el nombre de archivo es msolap140.dll, significa que es anterior a la versión más reciente y debe actualizarse.
    
    ![Detalles de la biblioteca de cliente](media/analysis-services-data-providers/aas-msolap-details.png)
    
  
### <a name="amo"></a>AMO

1. Ir a `C:\Windows\Microsoft.NET\assembly\GAC_MSIL\Microsoft.AnalysisServices\`. Si tiene más de una carpeta, elija el número más alto.
2. Haga clic con el botón derecho en **Microsoft.AnalysisServices** > **Propiedades** > **Detalles**.  

### <a name="adomd"></a>ADOMD

1. Ir a `C:\Windows\Microsoft.NET\assembly\GAC_MSIL\Microsoft.AnalysisServices.AdomdClient\`. Si tiene más de una carpeta, elija el número más alto.
2. Haga clic con el botón derecho en **Microsoft.AnalysisServices.AdomdClient** > **Propiedades** > **Detalles**.  


## <a name="next-steps"></a>Pasos siguientes
[Connect with Excel](analysis-services-connect-excel.md)   (Conexión con Excel)  
[Conexión con Power BI](analysis-services-connect-pbi.md)
