---
title: Personalización de la directiva de SQL Information Protection en Azure Security Center | Microsoft Docs
description: Obtenga información acerca de cómo personalizar las directivas de protección de información en Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 2ebf2bc7-232a-45c4-a06a-b3d32aaf2500
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/29/2019
ms.author: memildin
ms.openlocfilehash: f9b161bbb692c1336083640250b93f9d87f1e0d9
ms.sourcegitcommit: 3f8017692169bd75483eefa96c225d45cd497f06
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2019
ms.locfileid: "73520798"
---
# <a name="customize-the-sql-information-protection-policy-in-azure-security-center-preview"></a>Personalización de la directiva de SQL Information Protection en Azure Security Center (versión preliminar)
 
En Azure Security Center, puede definirse una directiva de SQL Information Protection y personalizarse para todo el inquilino de Azure.

Protección de la información es una funcionalidad de seguridad avanzada para detectar, clasificar, etiquetar y proteger datos confidenciales en los recursos de datos de Azure. Las funciones de detección y clasificación de la información confidencial más importante (empresarial, financiera, médica, personal, etc.) desempeñan un rol fundamental en el modo en que se protege la información de su organización. Puede servir como infraestructura para:
- Ayudar a cumplir los requisitos de cumplimiento de normas y los estándares relacionados con la privacidad de datos
- Varios escenarios de seguridad, como la supervisión (auditoría) y las alertas relacionadas con accesos anómalos a información confidencial
- Controlar el acceso y mejorar la seguridad de los almacenes de datos que contienen información altamente confidencial.
 
[SQL Information Protection](../sql-database/sql-database-data-discovery-and-classification.md) implementa este paradigma para los almacenes de datos SQL, actualmente es compatible con Azure SQL Database. SQL Information Protection detecta y clasifica automáticamente los datos potencialmente confidenciales, proporciona un mecanismo de etiquetado para los datos confidenciales con atributos de clasificación de forma persistente y proporciona un panel detallado que muestra el estado de clasificación de la base de datos. Además, calcula la confidencialidad del conjunto de resultados de las consultas SQL, para que las consultas que extraen datos confidenciales se puedan auditar explícitamente y se protejan los datos. Para obtener más detalles acerca de SQL Information Protection, consulte [Clasificación y detección de datos de Azure SQL Database](../sql-database/sql-database-data-discovery-and-classification.md).
 
El mecanismo de clasificación se basa en dos construcciones principales que conforman la taxonomía de clasificación: **etiquetas** y **tipos de información**.
- **Etiquetas**: atributos de clasificación principales, que se utilizan para definir el nivel de confidencialidad de los datos almacenados en la columna. 
- **Tipos de información**: proporcionan una granularidad adicional en el tipo de datos almacenados en la columna.
 
Information Protection incluye un conjunto integrado de etiquetas y tipos de información que se usan de forma predeterminada. Para personalizarlos, puede personalizar la directiva de protección de información de Azure Security Center.
 
## <a name="customize-the-information-protection-policy"></a>Personalización de la directiva de protección de la información
Para personalizar la directiva de protección de información de su inquilino de Azure, deberá tener [privilegios administrativos en el grupo de administración raíz del inquilino](security-center-management-groups.md). 
 
1. En el menú principal de Security Center, en **PROTECCIÓN DE SEGURIDAD DE RECURSOS** vaya a **Datos y almacenamiento** y haga clic en el botón **SQL Information Protection**.

   ![Configuración de la directiva de protección de información](./media/security-center-info-protection-policy/security-policy.png) 
 
2. En la página **SQL Information Protection**, puede ver el conjunto actual de etiquetas. Estos son los atributos principales de clasificación que se usan para categorizar el nivel de confidencialidad de los datos. Desde aquí, puede configurar las **etiquetas de protección de la información** y los **tipos de información** para el inquilino. 
 
### <a name="customizing-labels"></a>Personalización de las etiquetas
 
1. Puede editar o eliminar cualquier etiqueta existente, o agregar una nueva. Para editar una etiqueta existente, seleccione esa etiqueta y, después, haga clic en **Configurar**, en la parte superior o en el menú contextual de la derecha. Para agregar una nueva etiqueta, haga clic en **Crear etiqueta** en la barra de menús superior o en la parte inferior de la tabla de etiquetas.
2. En la pantalla **Configurar la etiqueta de confidencialidad**, puede crear o modificar el nombre de la etiqueta y la descripción. También puede establecer si la etiqueta está habilitada o deshabilitada mediante la activación o desactivación del conmutador **Habilitado**. Por último, puede agregar o quitar tipos de información asociados con la etiqueta. Cualquier dato descubierto que coincida con ese tipo de información incluirá automáticamente la etiqueta de sensibilidad asociada en las recomendaciones de clasificación.
3. Haga clic en **OK**.
 
   ![Configurar la etiqueta de confidencialidad](./media/security-center-info-protection-policy/config-sensitivity-label.png)
 
4. Las etiquetas se muestran en orden de sensibilidad ascendente. Para cambiar la clasificación entre las etiquetas, arrastre las etiquetas para volver a ordenarlas en la tabla o use los botones **Subir** y **Bajar** para cambiar el orden. 
 
    ![Configuración de la directiva de protección de información](./media/security-center-info-protection-policy/move-up.png)
 
5. Haga clic en **Guardar** en la parte superior de la pantalla cuando haya terminado.
 
 
## <a name="adding-and-customizing-information-types"></a>Cómo agregar y personalizar los tipos de información
 
1. Puede administrar y personalizar los tipos de información haciendo clic en **Administrar tipos de información**.
2. Para agregar un nuevo **tipo de información**, seleccione **Crear tipo de información** en el menú superior. Puede configurar un nombre, una descripción y cadenas de patrón de búsqueda para el **tipo de información**. Las cadenas de patrón de búsqueda pueden usar opcionalmente palabras clave con caracteres comodín (con el carácter "%"), que el motor de detección automática usa para identificar datos confidenciales en las bases de datos, en función de los metadatos de las columnas.
 
    ![Configuración de la directiva de protección de información](./media/security-center-info-protection-policy/info-types.png)
 
3. También puede configurar la opción integrada **Tipos de información** mediante la adición de las cadenas de patrón de búsqueda adicional, desactivando algunas de las cadenas existentes o cambiando la descripción. Los **tipos de información** integrados no se pueden eliminar ni se pueden editar sus nombres. 
4. Los **tipos de información** se enumeran en orden ascendente de detección, lo que significa que se intentará que los tipos que aparezcan más arriba en la lista coincidan antes. Para cambiar la clasificación entre los tipos de información, arrastre los tipos al lugar correcto para volver a ordenarlas en la tabla o usar los botones **Subir** y **Bajar** para cambiar el orden. 
5. Haga clic en Cuando haya terminado, haga clic en **Aceptar**.
6. Una vez completada la administración de los tipos de información, asegúrese de asociar los tipos pertinentes con las etiquetas pertinentes, haciendo clic en **Configurar** para una etiqueta determinada y agregando o eliminando tipos de información según corresponda.
7. Asegúrese de hacer clic en **Guardar** en la hoja principal **Etiquetas** para aplicar todos los cambios.
 
Después de definir y guardar la directiva de protección de información, esta se aplicará a la clasificación de datos en todas las bases de datos de Azure SQL del inquilino.
 
 
## <a name="next-steps"></a>Pasos siguientes
 
En este artículo, ha aprendido acerca de cómo definir una directiva de SQL Information Protection en Azure Security Center. Para obtener más información acerca de cómo usar SQL Information Protection para clasificar y proteger datos confidenciales en las bases de datos SQL, consulte [Clasificación y detección de datos de Azure SQL Database](../sql-database/sql-database-data-discovery-and-classification.md). 

Para obtener más información acerca de las directivas de seguridad y la seguridad de datos en Azure Security Center, consulte los artículos siguientes:
 
- [Establecimiento de directivas de seguridad en Azure Security Center](tutorial-security-policy.md): aprenda a configurar directivas de seguridad para las suscripciones y los grupos de recursos de Azure.
- [Seguridad de datos de Azure Security Center](security-center-data-security.md): Aprenda cómo Security Center administra y protege los datos.