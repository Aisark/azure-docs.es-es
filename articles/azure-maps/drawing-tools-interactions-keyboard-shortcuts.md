---
title: Tipos de interacción y métodos abreviados de teclado de las herramientas de dibujo en el mapa | Microsoft Azure Maps
description: Cómo dibujar y editar formas con un mouse, una pantalla táctil o un teclado en el SDK web de Microsoft Azure Maps
author: rbrundritt
ms.author: richbrun
ms.date: 12/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 267579f7b4bbfe026f3aa01b00f01f3b872cf4a6
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911655"
---
# <a name="interaction-types-and-keyboard-shortcuts-in-the-drawing-tools-module"></a>Tipos de interacción y métodos abreviados de teclado en el módulo de herramientas de dibujo

En este artículo se describen las distintas formas de dibujar y editar formas en un mapa con un mouse, una pantalla táctil o métodos abreviados de teclado.

El administrador de dibujo admite tres formas diferentes de interactuar con el mapa para dibujar formas.

* `click`: se agregan coordenadas cuando se hace clic en el mouse o en la entrada táctil.
* `freehand `: se agregan coordenadas cuando se arrastra el mouse o la entrada táctil en el mapa. 
* `hybrid`: se agregan coordenadas cuando se hace clic o se arrastra el mouse o la entrada táctil.

## <a name="how-to-draw-shapes"></a>Dibujo de formas

A continuación se describen las distintas formas en que se pueden dibujar formas en el mapa. Antes de que se pueda dibujar cualquier forma, la opción `drawingMode` del administrador de dibujo debe establecerse en un valor de dibujo admitido. Esto puede hacerse mediante programación o presionando uno de los botones de dibujo de la barra de herramientas. El modo de dibujo permanece habilitado, incluso después de dibujar una forma, lo que facilita el dibujo de otras formas del mismo tipo. Este modo se puede poner en un estado de inactividad mediante programación o haciendo clic en el botón de modos de dibujo actual de la barra de herramientas. 

### <a name="how-to-draw-a-point"></a>Dibujo de un punto

Cuando el administrador de dibujo está en el modo de dibujo `draw-point`, se pueden realizar las siguientes acciones para dibujar puntos en el mapa. Estos métodos funcionan con todos los modos de interacción.

**Inicio del dibujo**
 - Haga clic en el botón izquierdo del mouse o toque el mapa para agregar un punto al mapa. 
 - Si el mouse está sobre el mapa, presione la tecla `F` y se agregará un punto mediante la coordenada de donde se encuentra el puntero del mouse. Esto proporcionará un método de precisión mayor para agregar un punto al mapa, ya que habrá menos movimiento en el mouse debido al movimiento de presión del botón izquierdo del mouse.
 - Para agregar más puntos al mapa, pulse, toque o presione `F`.
 
**Finalización del dibujo**
 - Haga clic en cualquier botón de la barra de herramientas de dibujo. 
 - Establezca mediante programación el modo de dibujo. 
 - Presione la tecla `C`.

**Cancelación del dibujo**
 - Presione la tecla `Escape`.

### <a name="how-to-draw-a-line"></a>Dibujo de una línea

Cuando el administrador de dibujo está en modo `draw-line`, se pueden realizar las siguientes acciones para dibujar puntos en el mapa dependiendo de lo que se establezca en el modo de interacción.

**Inicio del dibujo**
 - Modo de clic
   * Haga clic en el botón izquierdo del mouse o toque el mapa para agregar cada punto de una línea en el mapa. Se agrega una coordenada a la línea por cada clic o toque. 
   * Si el mouse está sobre el mapa, presione la tecla `F` y se agregará un punto mediante la coordenada de donde se encuentra el puntero del mouse. Esto proporcionará un método de precisión mayor para agregar un punto al mapa, ya que habrá menos movimiento en el mouse debido al movimiento de presión del botón izquierdo del mouse.
   * Siga haciendo clic hasta que se hayan agregado todos los puntos deseados a la línea.
 - Modo de mano alzada
   * Presione el botón izquierdo del mouse o pulse en el mapa y arrastre el mouse o el punto de toque. Las coordenadas se agregan a la línea a medida que el mouse o el punto táctil se mueve por el mapa. En cuanto se desencadene el evento del mouse o de toque, se completa el dibujo. La frecuencia con la que se agregan las coordenadas se define mediante la opción `freehandInterval` de los administradores de dibujos.
 - Modo híbrido
   * Alterne entre los métodos de clic y mano alzada, según sea necesario, mientras se dibuja una sola línea. Por ejemplo, haga clic en unos cuantos puntos, mantenga presionado el botón del mouse y arrástrelo para agregar varios puntos y, a continuación, haga clic y dibuje unos cuantos más. 

**Finalización del dibujo**
 - Modo de clic o híbrido
   * Haga doble clic en el mapa en el último punto. 
   * Haga clic en cualquier botón de la barra de herramientas de dibujo. 
   * Establezca mediante programación el modo de dibujo. 
 - Modo de mano alzada
   * Suelte el botón del mouse o punto táctil.
 - Presione la tecla `C`.

**Cancelación del dibujo**
 - Presione la tecla `Escape`.

### <a name="how-to-draw-a-polygon"></a>Dibujo de un polígono

Cuando el administrador de dibujo está en modo `draw-polygon`, se pueden realizar las siguientes acciones para dibujar puntos en el mapa dependiendo de lo que se establezca en el modo de interacción.

**Inicio del dibujo**
 - Modo de clic
   * Haga clic en el botón izquierdo del mouse o toque el mapa para agregar cada punto de un polígono en el mapa. Se agrega una coordenada al polígono por cada clic o toque. 
   * Si el mouse está sobre el mapa, presione la tecla `F` y se agregará un punto mediante la coordenada de donde se encuentra el puntero del mouse. Esto proporcionará un método de precisión mayor para agregar un punto al mapa, ya que habrá menos movimiento en el mouse debido al movimiento de presión del botón izquierdo del mouse.
   * Siga haciendo clic hasta que se hayan agregado todos los puntos deseados al polígono.
 - Modo de mano alzada
   * Presione el botón izquierdo del mouse o pulse en el mapa y arrastre el mouse o el punto de toque. Las coordenadas se agregan al polígono a medida que el mouse o el punto táctil se mueve por el mapa. En cuanto se desencadene el evento del mouse o de toque, se completa el dibujo. Tenga en cuenta que la frecuencia con la que se agregan las coordenadas se define mediante la opción `freehandInterval` de los administradores de dibujos.
 - Modo híbrido
   * Alterne entre los métodos de clic y mano alzada, según sea necesario, mientras se dibuja un único polígono. Por ejemplo, haga clic en unos cuantos puntos, mantenga presionado el botón del mouse y arrástrelo para agregar varios puntos y, a continuación, haga clic y dibuje unos cuantos más. 

**Finalización del dibujo**
 - Modo de clic o híbrido
   * Haga doble clic en el mapa en el último punto. 
   * Haga clic en el primer punto del polígono.
   * Haga clic en cualquier botón de la barra de herramientas de dibujo. 
   * Establezca mediante programación el modo de dibujo. 
 - Modo de mano alzada
   * Suelte el botón del mouse o punto táctil.
 - Presione la tecla `C`.

**Cancelación del dibujo**
 - Presione la tecla `Escape`.

### <a name="how-to-draw-a-rectangle"></a>Dibujo de un rectángulo

Cuando el administrador de dibujo está en modo `draw-rectangle`, se pueden realizar las siguientes acciones para dibujar puntos en el mapa dependiendo de lo que se establezca en el modo de interacción. La forma generada seguirá la [especificación de GeoJSON extendida para los rectángulos](extend-geojson.md#rectangle).

**Inicio del dibujo**
 - Presione el botón izquierdo del mouse o pulse en el mapa para agregar la primera esquina del rectángulo y arrástrelo para crear el rectángulo. 

**Finalización del dibujo**
 - Suelte el botón del mouse o punto táctil.
 - Establezca mediante programación el modo de dibujo. 
 - Presione la tecla `C`.

**Cancelación del dibujo**
 - Presione la tecla `Escape`.

### <a name="how-to-draw-a-circle"></a>Dibujo de un círculo

Cuando el administrador de dibujo está en modo `draw-circle`, se pueden realizar las siguientes acciones para dibujar puntos en el mapa dependiendo de lo que se establezca en el modo de interacción. La forma generada seguirá la [especificación de GeoJSON extendida para los círculos](extend-geojson.md#circle).

**Inicio del dibujo**
 - Presione el botón izquierdo del mouse o pulse en el mapa para agregar el centro del círculo y arrástrelo para crear un radio al círculo. 

**Finalización del dibujo**
 - Suelte el botón del mouse o punto táctil.
 - Establezca mediante programación el modo de dibujo. 
 - Presione la tecla `C`.

**Cancelación del dibujo**
 - Presione la tecla `Escape`.

## <a name="keyboard-shortcuts"></a>Accesos directos del teclado

Las herramientas de dibujo admiten los métodos abreviados de teclado que facilitan la tarea de dibujar y editar formas en el mapa. Estos métodos abreviados de teclado son funcionales cuando el mapa tiene el foco.

| Clave      | Acción                            |
|----------|-----------------------------------|
| `C` | Completa cualquier dibujo que esté en curso y establece el modo de dibujo en inactivo. El foco se desplazará al elemento de mapa de nivel superior.  |
| `Escape` | Cancela cualquier dibujo que esté en curso y establece el modo de dibujo en inactivo. El foco se desplazará al elemento de mapa de nivel superior.  |
| `F` | Agrega una coordenada a un punto, línea o polígono si el mouse está sobre el mapa. Acción equivalente de hacer clic en el mapa cuando se está en modo de clic o híbrido. Este método abreviado permite dibujos más precisos y más rápidos, ya que puede usar una mano para colocar el mouse y otro para presionar el botón sin mover el mouse desde el gesto de presionar. |

## <a name="next-steps"></a>Pasos siguientes

Obtenga más información sobre las clases en el módulo de herramientas de dibujo:

> [!div class="nextstepaction"]
> [Administrador de dibujo](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest)

> [!div class="nextstepaction"]
> [Barra de herramientas de dibujo](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest)