---
title: Niveles de coherencia en Azure Cosmos DB
description: Azure Cosmos DB tiene cinco niveles de coherencia para ayudar a equilibrar la coherencia, la disponibilidad y la latencia finales.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: b5d9df7a0afa9b4270f0eff643e083e5bccfceb8
ms.sourcegitcommit: e6bce4b30486cb19a6b415e8b8442dd688ad4f92
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/09/2020
ms.locfileid: "78933727"
---
# <a name="consistency-levels-in-azure-cosmos-db"></a>Niveles de coherencia en Azure Cosmos DB

Las bases de datos distribuidas que dependen de la replicación para su alta disponibilidad, su baja latencia o ambas, constituyen el compromiso fundamental entre la coherencia de lectura y la disponibilidad, la latencia y el rendimiento. La mayoría de las bases de datos distribuidas disponibles comercialmente solicitan a los desarrolladores que elijan entre los dos modelos de coherencia extrema: coherencia *alta* y *posible* coherencia. La linearización o el modelo de coherencia fuerte es el estándar de oro de la programación de datos, pero obliga a pagar el precio de una latencia más alta (en estado estable) y una menor disponibilidad (durante los errores). Por otro lado, la posible coherencia ofrece una mayor disponibilidad y un mejor rendimiento, pero es difícil programar aplicaciones. 

Azure Cosmos DB se aproxima a la coherencia de datos como un espectro de opciones en lugar de como dos extremos. La coherencia fuerte y la posible coherencia están en los extremos del espectro, pero existen muchas opciones de coherencia en todo el espectro. Los desarrolladores pueden usar estas opciones para elegir opciones precisas y compensaciones pormenorizadas con respecto a la alta disponibilidad y al rendimiento. 

Con Azure Cosmos DB, los desarrolladores pueden elegir entre cinco modelos de coherencia bien definidos en el espectro de coherencia. Desde la coherencia más sólida a una más relajada, los modelos incluyen una coherencia *fuerte*, de *obsolescencia limitada*, de *sesión*, *de prefijo coherente* y *posible* coherencia. Los modelos están bien definidos, son intuitivos y se pueden usar en escenarios reales específicos. Cada modelo proporciona [compensaciones entre la disponibilidad y el rendimiento](consistency-levels-tradeoffs.md) y cuenta con el respaldo de Acuerdos de Nivel de Servicio. En la imagen siguiente se muestran los distintos niveles de coherencia como un espectro.

![Coherencia como un espectro](./media/consistency-levels/five-consistency-levels.png)

Los niveles de coherencia son independientes de la región y están garantizados para todas las operaciones, independientemente de la región desde la que se realizan las operaciones de lectura y escritura, del número de regiones asociadas con la cuenta de Azure Cosmos o de si la cuenta está configurada para una o varias regiones de escritura.

## <a name="scope-of-the-read-consistency"></a>Ámbito de la coherencia de lectura

La coherencia de lectura se aplica a una operación de lectura limitada a un intervalo de claves de partición o una partición lógica. Un cliente remoto o un procedimiento almacenado pueden emitir la operación de lectura.

## <a name="configure-the-default-consistency-level"></a>Configuración del nivel de coherencia predeterminado

Puede configurar el nivel de coherencia predeterminado de su cuenta de Azure Cosmos en cualquier momento. El nivel de coherencia predeterminado configurado en su cuenta se aplica a todas las bases de datos y contenedores de bases de datos de Azure Cosmos de esa cuenta. Todas las operaciones de lectura y consulta que se emitan con arreglo a un contenedor o una base de datos usan el nivel de coherencia especificado de forma predeterminada. Para más información, consulte cómo [configurar el nivel de coherencia predeterminado](how-to-manage-consistency.md#configure-the-default-consistency-level).

## <a name="guarantees-associated-with-consistency-levels"></a>Garantías asociadas a los niveles de coherencia

Los Acuerdos de Nivel de Servicio que proporciona Azure Cosmos DB garantizan que el 100 por ciento de las solicitudes de lectura cumplan con la garantía de coherencia en cualquier nivel de coherencia que elija. Una solicitud de lectura cumple el Acuerdo de Nivel de Servicio de coherencia si se satisfacen todas las garantías de coherencia asociadas al nivel de coherencia. Las definiciones precisas de los cinco niveles de coherencia de Azure Cosmos DB que usan el lenguaje de especificación TLA+ se proporcionan en el repositorio [azure-cosmos-tla](https://github.com/Azure/azure-cosmos-tla) de GitHub.

Aquí se describe la semántica de los cinco niveles de coherencia:

- **Alta**: La coherencia fuerte ofrece una garantía de linearización. La linearización hace referencia a la capacidad de servir solicitudes simultáneamente. Se garantiza que las lecturas devuelven la versión más reciente de un elemento. Un cliente nunca ve una escritura no confirmada ni parcial. Se garantiza que los usuarios siempre leerán la escritura confirmada más reciente.

  En el gráfico siguiente se ilustra la coherencia fuerte con notas musicales. Una vez que los datos se han escrito en la región "Este de EE. UU.", al leer los datos de otras regiones, obtiene el valor más reciente:

  ![video](media/consistency-levels/strong-consistency.gif)

- **De obsolescencia entrelazada**: Se garantiza que las lecturas respetan la garantía de prefijo coherente. Las lecturas pueden ir con retraso respecto a las escrituras en un máximo de versiones *"K"* (es decir, "actualizaciones") de un elemento o en el intervalo de tiempo *"T"* . En otras palabras, cuando elige la obsolescencia limitada, la "obsolescencia" se puede configurar de dos maneras: 

  * El número de versiones (*K*) del elemento
  * El intervalo de tiempo (*T*) que las lecturas pueden retrasarse con respecto a las escrituras 

  La obsolescencia limitada ofrece un orden global total, excepto dentro de la "ventana de obsolescencia". La garantía de lectura monotónica existe dentro de una región, tanto dentro como fuera de la ventana de obsolescencia. La coherencia fuerte tiene la misma semántica que la que ofrece la obsolescencia limitada. La ventana de obsolescencia es igual a cero. La obsolescencia limitada también se conoce como linealidad retardada. Cuando un cliente realiza operaciones de lectura en una región que acepta las escrituras, las garantías que proporciona la obsolescencia limitada son idénticas a las garantías por la coherencia fuerte.

  Las aplicaciones distribuidas globalmente que esperan bajas latencias de escritura pero que necesitan una garantía de orden global, suelen elegir la obsolescencia limitada. La obsolescencia limitada es ideal para las aplicaciones que se caracterizan por la colaboración en grupo y el uso compartido, el tablero de cotizaciones, la publicación o suscripción, la puesta en cola, etc. En el gráfico siguiente se ilustra la coherencia de obsolescencia limitada con notas musicales. Una vez que los datos se han escrito en la región "Este de EE. UU.", las regiones "Oeste de EE. UU." y "Este de Australia" leen el valor escrito según el tiempo de retardo máximo configurado o las operaciones máximas:

  ![video](media/consistency-levels/bounded-staleness-consistency.gif)

- **Sesión**:  en una sesión de cliente individual, se garantiza que las lecturas respetan las garantías de prefijo coherente (suponiendo que hay una sola sesión de "escritor"), lecturas monotónicas, escrituras monotónicas, lectura de las escrituras y escritura tras lecturas. Los clientes que están fuera de la sesión que realiza escrituras verán la coherencia final.

  La coherencia de la sesión es el nivel de coherencia más usado para regiones únicas, así como para aplicaciones distribuidas globalmente. Proporciona latencias de escritura, disponibilidad y rendimiento de lectura equiparables a los de la coherencia final, pero también proporciona las garantías de coherencia que satisfacen las necesidades de aplicaciones escritas para funcionar en el contexto de un usuario. En el gráfico siguiente se ilustra la coherencia de la sesión con notas musicales. La región "Oeste de EE. UU." y las regiones "Este de EE. UU." usan la misma sesión (sesión A), por lo que ambas leen los datos al mismo tiempo. Sin embargo, la región "Este de Australia" usa la "sesión B", por lo que recibe los datos más tarde pero en el mismo orden en que se escriben.

  ![video](media/consistency-levels/session-consistency.gif)

- **De prefijo coherente**: Las actualizaciones que se devuelven contienen prefijos para todas las actualizaciones, sin espacios. El nivel de coherencia del prefijo coherente garantiza que las lecturas nunca vean escrituras desordenadas.

  Si se realizan escrituras en el orden `A, B, C`, un cliente ve `A`, `A,B` o `A,B,C`, pero nunca un desorden como `A,C` o `B,A,C`. El prefijo coherente proporciona latencias de escritura, disponibilidad y rendimiento de lectura equiparables a los de la coherencia final, pero también proporciona las garantías de orden que se ajustan a las necesidades de escenarios en los que el orden es importante. En el gráfico siguiente se ilustra la coherencia del prefijo de coherencia con notas musicales. En todas las regiones, las lecturas no ven nunca las escrituras desordenadas:

  ![video](media/consistency-levels/consistent-prefix.gif)

- **Ocasional**: No hay ninguna garantía de ordenación para las lecturas. En ausencia de escrituras adicionales, las réplicas terminarán por converger.  
La coherencia final es la forma más débil de coherencia, ya que un cliente puede leer los valores que son más antiguos que los que había leído antes. La coherencia final es adecuada cuando la aplicación no requiere ninguna garantía de ordenación. Entre los ejemplos se incluye el recuento de retweets, Me gusta o comentarios no encadenados. En el gráfico siguiente se ilustra la coherencia final con notas musicales.

  ![video](media/consistency-levels/eventual-consistency.gif)

## <a name="additional-reading"></a>Lecturas adicionales

Para más información sobre los conceptos de coherencia, lea los artículos siguientes:

- [High-level TLA+ specifications for the five consistency levels offered by Azure Cosmos DB](https://github.com/Azure/azure-cosmos-tla) (Especificaciones TLA+ de alto nivel de los cinco niveles de coherencia que ofrece Azure Cosmos DB)
- [Replicated Data Consistency Explained Through Baseball (video)](https://www.youtube.com/watch?v=gluIh8zd26I) (Vídeo sobre Coherencia de datos replicados explicada mediante el béisbol), por Doug Terry
- [Replicated Data Consistency Explained Through Baseball (whitepaper)](https://www.microsoft.com/en-us/research/publication/replicated-data-consistency-explained-through-baseball/?from=http%3A%2F%2Fresearch.microsoft.com%2Fpubs%2F157411%2Fconsistencyandbaseballreport.pdf) (Coherencia de datos replicados explicada mediante el béisbol [notas del producto]), por Doug Terry
- [Session guarantees for weakly consistent replicated data](https://dl.acm.org/citation.cfm?id=383631) (Garantías de sesión para datos replicados con coherencia débil)
- [Consistency Tradeoffs in Modern Distributed Database Systems Design: CAP is Only Part of the Story](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k) (Compromisos de coherencia en el diseño de sistemas modernos de bases de datos distribuidas: CAP es solo parte de la historia)
- [Probabilistic Bounded Staleness (PBS) for Practical Partial Quorums](https://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf) [Obsolescencia limitada probabilística (PBS) para cuórums parciales prácticos].
- [Eventual Consistent - Revisited (Coherencia posible: revisión)](https://www.allthingsdistributed.com/2008/12/eventually_consistent.html)

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre los niveles de coherencia de Azure Cosmos DB, lea los artículos siguientes:

* [Elección del nivel de coherencia adecuado para la aplicación](consistency-levels-choosing.md)
* [Niveles de coherencia en Azure Cosmos DB](consistency-levels-across-apis.md)
* [Availability and performance tradeoffs for various consistency levels](consistency-levels-tradeoffs.md) (Compromisos entre rendimiento y disponibilidad en los distintos niveles de coherencia)
* [Configuración del nivel de coherencia predeterminado](how-to-manage-consistency.md#configure-the-default-consistency-level)
* [Invalidación del nivel de coherencia predeterminado](how-to-manage-consistency.md#override-the-default-consistency-level)

