---
title: Preparación del paquete de AppSource | Microsoft Docs
description: Explicación sobre cómo preparar y crear paquetes de AppSource.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Ricardo.Villalobos
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: cc06ddf2fce93bef27e7aaee85b47179608e192b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "60625472"
---
# <a name="appsource-package-preparation"></a>Preparación de paquetes de AppSource

Además de un archivo solution.zip, necesitará un **paquete de AppSource**. Este es un archivo .zip que incluye todos los recursos necesarios para automatizar el proceso de implementación de su solución en el entorno CRM de los clientes. El **paquete de AppSource** consta de los componentes siguientes

* Paquete del implementador de paquetes
* Archivo **Content_Types.xml** con los recursos que use
* Archivo xml con los datos específicos de la aplicación
* Logotipo de 32 x 32 que aparecerá con el anuncio en el centro de administración
* Términos de licencia, directiva de privacidad

Los pasos siguientes le ayudarán a crear el paquete de AppSource.

## <a name="a-create-a-package-for-the-package-deployer"></a> a. Creación de un paquete para el implementador de paquetes

El paquete del implementador de paquetes es una parte del paquete de AppSource.

Para crear un paquete para el implementador de paquetes, utilice las siguientes instrucciones: [https://msdn.microsoft.com/library/dn688182.aspx](https://msdn.microsoft.com/library/dn688182.aspx). Cuando haya finalizado, el paquete constará de los recursos siguientes:

1. Carpeta del paquete: contiene todas las soluciones, datos de configuración, archivos sin formato y contenido del paquete. _Nota: En el ejemplo siguiente, supondremos que la carpeta del paquete se denomina "PkgFolder"_
2. dll: El ensamblado contiene el código personalizado para el paquete. _Nota: En el ejemplo siguiente, supondremos que este archivo se denomina "MicrosoftSample.dll."_

Ahora, deberá crear un archivo denominado "**Content_Types.xml**" Este archivo mostrará todas las extensiones de recursos que forman parte del paquete. Este es el código de ejemplo del archivo.

    <?xml version="1.0" encoding="utf-8"?>
        <Types xmlns="http://schemas.openxmlformats.org/package/2006/content-types">
        <Default Extension="xml" ContentType="application/octet-stream" />
        <Default Extension="xaml" ContentType="application/octet-stream" />
        <Default Extension="dll" ContentType="application/octet-stream" />
        <Default Extension="zip" ContentType="application/octet-stream" />
        <Default Extension="jpg" ContentType="application/octet-stream" />
        <Default Extension="gif" ContentType="application/octet-stream" />
        <Default Extension="png" ContentType="application/octet-stream" />
        <Default Extension="htm" ContentType="application/octet-stream" />
        <Default Extension="html" ContentType="application/octet-stream" />
        <Default Extension="db" ContentType="application/octet-stream" />
        <Default Extension="css" ContentType="application/octet-stream" />
    </Types>

El último paso es comprimir lo siguiente en un archivo. Llámelo **package.zip**. Contendrá

1. PkgFolder (incluidos todos los elementos de la carpeta)
2. dll
3. **Content_Types.xml**

Pasos para crear package.zip:

1. Coloque la carpeta del paquete, el archivo **Content_Types.xml** y PackageName.dll en un directorio.

![CRMScreenShot2](media/CRMScreenShot2.png)

1. Seleccione todos los elementos de la carpeta, haga clic con el botón derecho y elija "Enviar a", y Carpeta comprimida (en zip)

![CRMScreenShot3](media/CRMScreenShot3.png)

1. Cambie el nombre a package.zip

![CRMScreenShot4](media/CRMScreenShot4.png)

## <a name="b-create-an-appsource-package"></a>b. Creación de un paquete de AppSource

El paquete de AppSource requiere algunos archivos adicionales.

1. jpg (resolución de 32 x 32)
2. HTML (archivos con formato HTML)
3. **Content_Types.xml** (igual que el anterior)
4. Xml

Este es el código de ejemplo de input.xml. Consulte las definiciones en la tabla siguiente.

    <PvsPackageData>
        <ProviderName>Microsoft</ProviderName>
        <PackageFile>package.zip</PackageFile>
        <SolutionAnchorName>SampleSolution.zip</SolutionAnchorName>
        <StartDate>01/01/2016</StartDate>
        <EndDate>01/01/2021</EndDate>
        <SupportedCountries>US,CA</SupportedCountries>
        <LearnMoreLink>https://www.microsoft.com</LearnMoreLink>
        <Locales>
        <PackageLocale Code="1033" IsDefault="true">
        <Logo>logo32x32.png</Logo>
        <Terms>
        <PackageTerm File="TermsOfUse.html" />
        </Terms>
        </PackageLocale>
        </Locales>
    </PvsPackageData>
 
**Donde:**

|Campo|Detalles|
|---|---|
|ProviderName|De quién procede la solución. Si procede de un equipo de Microsoft, aquí debe aparecer Microsoft.|
|PackageFile |Recursos del implementador de paquetes comprimidos con un archivo content\_types.xml. Este archivo zip debe contener el ensamblado del implementador de paquetes y la carpeta con los recursos de este. Es decir, package.zip|
|SolutionAnchorName |Nombre del archivo zip de la solución en el implementador de paquetes que se usa como nombre para mostrar y la descripción de los recursos de la solución.|
| StartDate| Se trata de la fecha en la que el paquete de la solución estará disponible. El formato es MM/DD/AAAA|
|EndDate|Se trata de la fecha en la que el paquete de la solución dejará de estar disponible. El formato es MM/DD/AAAA |
|SupportedCountries |Se trata de una lista delimitada por comas de los países que deberían ver este paquete. Póngase en contacto con los servicios en línea para obtener una lista actualizada de todos los códigos de países. En el momento, se escribe este artículo fue la lista: AE, AL., A. M., AO, AR, AT, AU, AZ, BA, BB, BD, SER, BG, BH, BM, BN, BO, BR POR CA, CH, CI, CL, CM, CO, CR, CV, CW, CY, CZ, DE, DK,, DZ, CE Y EE, POR EJEMPLO, ES, FI, FR, GB, GE, GH, GR, GT, HK , HN, RECURSOS HUMANOS, HU, ID., INTERNET EXPLORER, IL, IN, IQ, ES, LO, JM, JO, JP, KE, KG, KN, KR, KW, KY, KZ, LB, LK, LT, LU, LV, LY, MASSACHUSETTS, MC, MD, ME, MK, MN, MES, MT, MU, MX, MY, NG, NI, NL, N, NZ, OM, PA, PE, PH , PK, PL, PR, PS, PT, PY, PREGUNTAS Y RESPUESTAS, RO, RS, RU, RW, SA, SE, SG, SI, SK, SN, SV, TH, TM, TN, TR, TT, TW, UA EE. UU., UY, UZ, QUITAR, VI, VN, ZA, ZW |
|LearnMoreLink | Dirección URL a la página para obtener más información sobre este paquete. |
|Locales|Una instancia de este nodo para cada idioma de la experiencia del usuario que desea admitir en la experiencia de usuario preferida de la solución. Este nodo contiene elementos secundarios que describen la configuración regional, el logotipo y las condiciones para cada idioma|
|Configuraciones regionales: PackageLocale.Code|Identificador de configuración regional del idioma para este nodo. Ejemplo: Inglés de Estados Unidos es 1033|
|Configuraciones regionales: PackageLocale.IsDefault|Indica que este es el idioma predeterminado. Este se usa como idioma de respuesta en caso de que el idioma elegido para la experiencia de usuario por parte del cliente no esté disponible.|
|Configuraciones regionales: Logotipo|Esto es el logotipo que desea usar para este paquete. El tamaño de icono es 32 x 32. Los formatos permitidos son PNG y JPG|
|Locales:Terms: PackageTerm.File|Este es el nombre de archivo del documento HTML que contiene los términos de licencia.|

Aquí es donde se mostrará el logotipo:

![CRMScreenShot5](media/CRMScreenShot5.png)

El último paso es comprimir lo siguiente en un archivo.

1. Archivo .zip (creado anteriormente)
2. **Content_Types.xml**
3. Xml
4. png
5. html

![CRMScreenShot6](media/CRMScreenShot6.png)

Cambie el nombre del archivo a un valor apropiado para la aplicación. Es recomendable que incluya el nombre de la compañía y el nombre de la aplicación. Por ejemplo: **_Microsoft_SamplePackage.zip**.
