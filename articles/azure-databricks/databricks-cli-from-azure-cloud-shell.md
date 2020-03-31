---
title: 'Uso de la CLI de Databricks desde Azure Cloud Shell '
description: Aprenda a usar la CLI Databricks desde Azure Cloud Shell para realizar operaciones en Azure Databricks.
services: azure-databricks
author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: mamccrea
ms.openlocfilehash: efb0d3222bfd98b15502163979425d47fa459e07
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/27/2020
ms.locfileid: "73605716"
---
# <a name="use-databricks-cli-from-azure-cloud-shell"></a>Uso de la CLI de Databricks desde Azure Cloud Shell

Aprenda a usar la CLI Databricks desde Azure Cloud Shell para realizar operaciones en Databricks.

## <a name="prerequisites"></a>Prerequisites

* Un área de trabajo y un clúster de Azure Databricks. Para obtener instrucciones, consulte [Introducción a Azure Databricks](quickstart-create-databricks-workspace-portal.md). 

* Configure un token de acceso personal en Databricks. Para obtener instrucciones, consulte [Administración de tokens](/azure/databricks/dev-tools/api/latest/authentication).

## <a name="use-the-azure-cloud-shell"></a>Uso de Azure Cloud Shell

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
 
2. En la esquina superior derecha, haga clic en el icono **Cloud Shell**.

   ![Iniciar Cloud Shell](./media/databricks-cli-from-azure-cloud-shell/launch-azure-cloud-shell.png "Inicio de Azure Cloud Shell")

3. Asegúrese de selecciona **Bash** como entorno de Cloud Shell. Puede hacerlo en la lista desplegable, como se muestra en la captura de pantalla siguiente.

   ![Seleccionar Bash como entorno de Cloud Shell](./media/databricks-cli-from-azure-cloud-shell/select-bash-for-shell.png "Seleccione Bash") 

4. Cree un entorno virtual en el que pueda instalar la CLI de Databricks. En el siguiente fragmento de código, se crea un entorno virtual llamado `databrickscli`.

       virtualenv -p /usr/bin/python2.7 databrickscli

5. Cambie al entorno virtual que ha creado.

       source databrickscli/bin/activate

6. Instale la CLI de Databricks.

       pip install databricks-cli

7. Configure la autenticación con Databricks mediante el token de acceso que debe haber creado, que aparece como parte de los requisitos previos. Use el comando siguiente:

       databricks configure --token

    Recibirá las siguientes solicitudes:

    * En primer lugar, se le solicita que acceda al host de Databricks. Escriba el valor con el formato `https://eastus2.azuredatabricks.net`. En este caso, **Este de EE. UU. 2** es la región de Azure en la que creó el área de trabajo de Azure Databricks.

    * A continuación, se le solicita que escriba un token. Escriba el token que creó anteriormente.

Una vez completados estos pasos, puede empezar a usar la CLI de Databricks desde Azure Cloud Shell.

## <a name="use-databricks-cli"></a>Uso de la CLI de Databricks

Ya puede empezar a usar la CLI de Databricks. Por ejemplo, ejecute el siguiente comando para enumerar todos los clústeres de Databricks que tenga en el área de trabajo.

    databricks clusters list

También puede usar el siguiente comando para acceder el sistema de archivos de Databricks (DBFS).

    databricks fs ls


Para obtener una referencia completa de los comandos, consulte [Databricks CLI](/azure/databricks/dev-tools/databricks-cli) (CLI de Databricks).


## <a name="next-steps"></a>Pasos siguientes

* Para más información acerca de la CLI de Azure, consulte [Introducción a Azure Cloud Shell](../cloud-shell/overview.md)
* Para ver una lista de los comandos de la CLI de Azure, consulte la [referencia de la CLI de Azure](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest)
* Para ver una lista de comandos de la CLI de Databricks, consulte [Databricks CLI](/azure/databricks/dev-tools/databricks-cli) (CLI de Databricks)


