---
title: Seguimiento de solicitudes entrantes en Azure Application Insights con OpenCensus para Python | Microsoft Docs
description: Supervise las llamadas de solicitud de las aplicaciones Python a través de OpenCensus para Python.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: lzchen
ms.author: lechen
ms.date: 10/15/2019
ms.openlocfilehash: f6b06e7bb2bb8637ca28b2fa4185754f8686798e
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2019
ms.locfileid: "73586977"
---
# <a name="track-incoming-requests-with-opencensus-python"></a>Seguimiento de las solicitudes entrantes con OpenCensus para Python

Los datos de solicitudes entrantes se recopilan con OpenCensus para Python y sus diversas integraciones. Realice el seguimiento de los datos de solicitudes entrantes enviados a las aplicaciones web creadas sobre los marcos web más populares `django`, `flask` y `pyramid`. Los datos se envían después a Application Insights en Azure Monitor como telemetría `requests`.

En primer lugar, instrumente la aplicación con el [SDK de OpenCensus para Python](../../azure-monitor/app/opencensus-python.md) más reciente.

## <a name="tracking-django-applications"></a>Seguimiento de aplicaciones de Django

1. Descargue e instale `opencensus-ext-django` de [PyPI](https://pypi.org/project/opencensus-ext-django/) e instrumente la aplicación con el middleware de `django`. Se realizará el seguimiento de las solicitudes entrantes enviadas a la aplicación `django`.

2. Incluya `opencensus.ext.django.middleware.OpencensusMiddleware` en el archivo `settings.py` en `MIDDLEWARE`.

    ```python
    MIDDLEWARE = (
        ...
        'opencensus.ext.django.middleware.OpencensusMiddleware',
        ...
    )
    ```

3. Asegúrese de que AzureExporter está configurado correctamente en `settings.py` en `OPENCENSUS`.

    ```python
    OPENCENSUS = {
        'TRACE': {
            'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=0.5)',
            'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                service_name='foobar',
            )''',
        }
    }
    ```

4. También puede agregar direcciones URL a `settings.py` en `BLACKLIST_PATHS` para las solicitudes de las que no quiera realizar el seguimiento.

    ```python
    OPENCENSUS = {
        'TRACE': {
            'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=0.5)',
            'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                service_name='foobar',
            )''',
            'BLACKLIST_PATHS': 'https://example.com',  <--- This site will not be traced if a request is sent from it.
        }
    }
    ```

## <a name="tracking-flask-applications"></a>Seguimiento de aplicaciones de Flask

1. Descargue e instale `opencensus-ext-flask` de [PyPI](https://pypi.org/project/opencensus-ext-flask/) e instrumente la aplicación con el middleware de `flask`. Se realizará el seguimiento de las solicitudes entrantes enviadas a la aplicación `flask`.

    ```python
    
    from flask import Flask
    from opencensus.ext.azure.trace_exporter import AzureExporter
    from opencensus.ext.flask.flask_middleware import FlaskMiddleware
    from opencensus.trace.samplers import ProbabilitySampler
    
    app = Flask(__name__)
    middleware = FlaskMiddleware(
        app,
        exporter=AzureExporter(connection_string="InstrumentationKey=<your-ikey-here>"),
        sampler=ProbabilitySampler(rate=1.0),
    )
    
    @app.route('/')
    def hello():
        return 'Hello World!'
    
    if __name__ == '__main__':
        app.run(host='localhost', port=8080, threaded=True)
    
    ```

2. Puede configurar el middleware de `flask` directamente en el código. Para las solicitudes de direcciones URL de las que no quiera realizar el seguimiento, agréguelas a `BLACKLIST_PATHS`.

    ```python
    app.config['OPENCENSUS'] = {
        'TRACE': {
            'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1.0)',
            'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                service_name='foobar',
            )''',
            'BLACKLIST_PATHS': 'https://example.com',  <--- This site will not be traced if a request is sent to it.
        }
    }
    ```

## <a name="tracking-pyramid-applications"></a>Seguimiento de aplicaciones de Pyramid

1. Descargue e instale `opencensus-ext-django` de [PyPI](https://pypi.org/project/opencensus-ext-pyramid/) e instrumente la aplicación con la interpolación de `pyramid`. Se realizará el seguimiento de las solicitudes entrantes enviadas a la aplicación `pyramid`.

    ```python
    def main(global_config, **settings):
        config = Configurator(settings=settings)
    
        config.add_tween('opencensus.ext.pyramid'
                         '.pyramid_middleware.OpenCensusTweenFactory')
    ```

2. Puede configurar la interpolación de `pyramid` directamente en el código. Para las solicitudes de direcciones URL de las que no quiera realizar el seguimiento, agréguelas a `BLACKLIST_PATHS`.

    ```python
    settings = {
        'OPENCENSUS': {
            'TRACE': {
                'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1.0)',
                'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                    service_name='foobar',
                )''',
                'BLACKLIST_PATHS': 'https://example.com',  <--- This site will not be traced if a request is sent to it.
            }
        }
    }
    config = Configurator(settings=settings)
    ```

## <a name="next-steps"></a>Pasos siguientes

* [Mapa de aplicación](../../azure-monitor/app/app-map.md)
* [Disponibilidad](../../azure-monitor/app/monitor-web-app-availability.md)
* [Search](../../azure-monitor/app/diagnostic-search.md)
* [Consulta de Log (Analytics)](../../azure-monitor/log-query/log-query-overview.md)
* [Diagnósticos de transacción](../../azure-monitor/app/transaction-diagnostics.md)
