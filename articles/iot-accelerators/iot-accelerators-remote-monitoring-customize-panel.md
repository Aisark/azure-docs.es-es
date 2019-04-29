---
title: Agregar un panel a la solución de supervisión remota UI - Azure | Microsoft Docs
description: En este artículo se muestra cómo agregar un nuevo panel a la interfaz de usuario web del acelerador de soluciones de supervisión remota.
author: dominicbetts
manager: timlt
ms.author: v-yiso
ms.service: iot-accelerators
services: iot-accelerators
origin.date: 10/05/2018
ms.date: 11/26/2018
ms.topic: conceptual
ms.openlocfilehash: 3b855c3bed75945f44b55463bdacd049b7930aa7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61447070"
---
# <a name="add-a-custom-panel-to-the-dashboard-in-the-remote-monitoring-solution-accelerator-web-ui"></a>Adición de un panel personalizado al panel de la interfaz de usuario web del acelerador de soluciones de supervisión remota

En este artículo se muestra cómo agregar un nuevo panel en la página de panel de la interfaz de usuario web del acelerador de soluciones de supervisión remota. El artículo describe:

- Cómo preparar un entorno de desarrollo local.
- Cómo agregar un nuevo panel a una página de panel de la interfaz de usuario web.

El panel de ejemplo de este artículo se muestra en la página de panel existente.

## <a name="prerequisites"></a>Requisitos previos

Para completar los pasos de esta guía, necesita que el software siguiente esté instalado en la máquina de desarrollo local:

- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="before-you-start"></a>Antes de comenzar

Debe completar los pasos descritos en el artículo [Adición de una página personalizada a la interfaz de usuario web del acelerador de soluciones de supervisión remota](iot-accelerators-remote-monitoring-customize-page.md) antes de continuar.

## <a name="add-a-panel"></a>Agregar un panel

Para agregar un panel a la interfaz de usuario web, deberá agregar los archivos de origen que definen el panel y, a continuación, modificar la página de panel para mostrar el panel.

### <a name="add-the-new-files-that-define-the-panel"></a>Adición de los nuevos archivos que definen el panel

Para comenzar, la carpeta **src/walkthrough/components/pages/dashboard/panels/examplePanel** contiene los archivos que definen un panel, incluyendo:

**examplePanel.js**


Copie la carpeta **src/walkthrough/components/pages/dashboard/panels/examplePanel** en la carpeta **src/components/pages/dashboard/panels**.

Agregue la siguiente exportación al archivo **src/walkthrough/components/pages/dashboard/panels/index.js**:

```js
export * from './examplePanel';
```

### <a name="add-the-panel-to-the-dashboard"></a>Adición del panel a la página de panel

Modifique el archivo **src/components/pages/dashboard/dashboard.js** para agregar el panel.

Agregue el panel de ejemplo a la lista de importaciones de paneles:

```js
import {
  OverviewPanel,
  AlertsPanel,
  TelemetryPanel,
  AnalyticsPanel,
  MapPanel,
  transformTelemetryResponse,
  chartColorObjects,
  ExamplePanel
} from './panels';
```

Agregue la siguiente definición de celda a la cuadrícula en el contenido de la página:

```js
          <Cell className="col-2">
            <ExamplePanel t={t} />
          </Cell>
```

## <a name="test-the-flyout"></a>Prueba del control flotante

Si la interfaz de usuario web no se está ejecutando localmente, ejecute el siguiente comando en la raíz de la copia local del repositorio:

```cmd/sh
npm start
```

El comando anterior ejecuta la interfaz de usuario localmente en [http://localhost:3000/dashboard](http://localhost:3000/dashboard). Para ver el nuevo panel, vaya a la página **Panel**.

## <a name="next-steps"></a>Pasos siguientes

En este artículo, aprendió sobre los recursos que tiene a su disposición para ayudarle a agregar o personalizar paneles en la interfaz de usuario web del acelerador de la solución de supervisión remota.

Para información más conceptual sobre el acelerador de soluciones de supervisión remota, vea [Arquitectura de supervisión remota](iot-accelerators-remote-monitoring-sample-walkthrough.md).
