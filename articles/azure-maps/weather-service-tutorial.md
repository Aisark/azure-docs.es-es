---
title: 'Tutorial: Combinación de datos de sensores con datos de previsión meteorológica mediante Azure Notebooks (Python) | Microsoft Docs'
description: 'Tutorial: En este tutorial se muestra cómo combinar datos de sensores con datos de previsión meteorológica del servicio meteorológico de Azure Maps mediante Azure Notebooks (Python).'
author: walsehgal
ms.author: v-musehg
ms.date: 12/09/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 809c188dc37aba64de27e89e38acd8692c7de032
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/02/2020
ms.locfileid: "75613574"
---
# <a name="tutorial-join-sensor-data-with-weather-forecast-data-by-using-azure-notebooks-python"></a>Tutorial: Combinación de datos de sensores con datos de previsión meteorológica mediante Azure Notebooks (Python)

La energía eólica es una fuente de energía alternativa a los combustibles fósiles para combatir el cambio climático. Dado que el viento no es coherente por naturaleza, los operadores de energía eólica deben crear modelos de ML (aprendizaje automático) para predecir la capacidad de energía eólica para satisfacer la demanda de electricidad y garantizar la estabilidad de la red. En este tutorial, veremos cómo se pueden combinar los datos de previsión meteorológica de Azure Maps con el conjunto de datos de demostración de las ubicaciones de los sensores con lecturas meteorológicas. Los datos de previsión meteorológica se solicitan mediante una llamada al servicio meteorológico de Azure Maps.

En este tutorial, aprenderá lo siguiente:

> [!div class="checklist"]
> * Trabajar con archivos de datos en [Azure Notebooks](https://docs.microsoft.com/azure/notebooks) en la nube.
> * Cargar datos de demostración desde un archivo.
> * Llamada a las API REST de Azure Maps en Python.
> * Representar datos de ubicación en el mapa.
> * Enriquecer los datos de demostración con datos meteorológicos de la [previsión diaria](https://aka.ms/AzureMapsWeatherDailyForecast) de Azure Maps.
> * Trazar datos de previsión en gráficos.


## <a name="prerequisites"></a>Prerequisites

Para completar este tutorial, primero debe:

1. Crear una suscripción de cuenta de Azure Maps en el plan de tarifa S0; para ello, siga las instrucciones de [Crear una cuenta](quick-demo-map-app.md#create-an-account-with-azure-maps).
2. Obtener la clave de suscripción principal de la cuenta; para ello, siga las instrucciones que se indican en [Obtención de la clave principal](quick-demo-map-app.md#get-the-primary-key-for-your-account).


Para más información sobre la autenticación en Azure Maps, consulte [Administración de la autenticación en Azure Maps](./how-to-manage-authentication.md).

Para familiarizarse con Azure Notebooks y saber cómo comenzar, siga las instrucciones de [Creación de un cuaderno de Azure](https://docs.microsoft.com/azure/azure-maps/tutorial-ev-routing#create-an-azure-notebook).

> [!Note]
> El archivo de Jupyter Notebook de este proyecto se puede descargar desde el [repositorio de Jupyter Notebook de Azure Maps](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/tree/master/AzureMapsJupyterSamples/Tutorials/Analyze%20Weather%20Data).

## <a name="load-the-required-modules-and-frameworks"></a>Carga de los módulos y marcos necesarios

Ejecute el siguiente script para cargar todos los módulos y marcos necesarios:

```python
import aiohttp
import pandas as pd
import datetime
from IPython.display import Image, display
```

## <a name="import-weather-data"></a>Importación de datos meteorológicos

En este tutorial, usaremos las lecturas de datos meteorológicos de los sensores instalados en cuatro turbinas eólicas diferentes. Los datos de ejemplo se componen de 30 días de lecturas meteorológicas obtenidas de los centros de datos meteorológicos próximos a la ubicación de cada turbina. Los datos de demostración contienen lecturas de datos para la temperatura, la velocidad del viento y la dirección. Puede descargar los datos de demostración desde [aquí](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/tree/master/AzureMapsJupyterSamples/Tutorials/Analyze%20Weather%20Data/data). El script siguiente importa los datos de demostración en Azure Notebooks.

```python
df = pd.read_csv("./data/weather_dataset_demo.csv")
```

## <a name="request-daily-forecast-data"></a>Solicitud de los datos de previsión diarios

En el escenario de ejemplo, nos gustaría solicitar la previsión diaria de cada ubicación de sensores. El siguiente script llama a la [API de previsión diaria ](https://aka.ms/AzureMapsWeatherDailyForecast) del servicio meteorológico de Azure Maps para obtener la previsión meteorológica diaria de cada turbina eólica para los próximos 15 días a partir de la fecha actual.


```python
subscription_key = "Your Azure Maps key"

# Get a lists of unique station IDs and their coordinates 
station_ids = pd.unique(df[['StationID']].values.ravel())
coords = pd.unique(df[['latitude','longitude']].values.ravel())

years,months,days = [],[],[]
dates_check=set()
wind_speeds, wind_direction = [], []

# Call azure maps weather service to get daily forecast data for 15 days from current date
session = aiohttp.ClientSession()
j=-1
for i in range(0, len(coords), 2):
    wind_speeds.append([])
    wind_direction.append([])
    
    query = str(coords[i])+', '+str(coords[i+1])
    forecast_response = await(await session.get("https://atlas.microsoft.com/weather/forecast/daily/json?query={}&api-version=1.0&subscription-key={}&duration=15".format(query, subscription_key))).json()
    j+=1
    for day in range(len(forecast_response['forecasts'])):
            date = forecast_response['forecasts'][day]['date'][:10]
            wind_speeds[j].append(forecast_response['forecasts'][day]['day']['wind']['speed']['value'])
            wind_direction[j].append(forecast_response['forecasts'][day]['day']['windGust']['direction']['degrees'])
            
            if date not in dates_check:
                year,month,day= date.split('-')
                years.append(year)
                months.append(month)
                days.append(day)
                dates_check.add(date)
            
await session.close()
```

El siguiente script representa las ubicaciones de las turbinas en el mapa mediante una llamada al [servicio Get Map Image](https://docs.microsoft.com/rest/api/maps/render/getmapimage) de Azure Maps.

```python
# Render the turbine locations on the map by calling the Azure Maps Get Map Image service
session = aiohttp.ClientSession()

pins="default|la-25+60|ls12|lc003C62|co9B2F15||'Location A'{} {}|'Location B'{} {}|'Location C'{} {}|'Location D'{} {}".format(coords[1],coords[0],coords[3],coords[2],coords[5],coords[4], coords[7],coords[6])

image_response = "https://atlas.microsoft.com/map/static/png?subscription-key={}&api-version=1.0&layer=basic&style=main&zoom=6&center={},{}&pins={}".format(subscription_key,coords[7],coords[6],pins)

static_map_response = await session.get(image_response)

poi_range_map = await static_map_response.content.read()

await session.close()

display(Image(poi_range_map))
```

![Ubicaciones de las turbinas](./media/weather-service-tutorial/location-map.png)


Para aumentar los datos de demostración con los datos de previsión, se agruparán los datos de previsión con los datos de demostración en función del identificador de estación del centro de datos meteorológicos.

```python
# Group forecasted data for all locations
df = df.reset_index(drop=True)
forecast_data = pd.DataFrame(columns=['StationID','latitude','longitude','Year','Month','Day','DryBulbCelsius','WetBulbFarenheit','WetBulbCelsius','DewPointFarenheit','DewPointCelsius','RelativeHumidity','WindSpeed','WindDirection'])

for i in range(len(station_ids)):
    loc_forecast = pd.DataFrame({'StationID':station_ids[i], 'latitude':coords[0], 'longitude':coords[1], 'Year':years, 'Month':months, 'Day':days, 'WindSpeed':wind_speeds[i], 'WindDirection':wind_direction[i]})
    forecast_data = pd.concat([forecast_data,loc_forecast], axis=0, sort=False)
    
combined_weather_data = pd.concat([df,forecast_data])
grouped_weather_data = combined_weather_data.groupby(['StationID'])
```

En la tabla siguiente se muestran los datos históricos y de previsión combinados de una de las ubicaciones de las turbinas.

```python
# Display data for first location
grouped_weather_data.get_group(station_ids[0]).reset_index()
```

<center>

![Datos agrupados](./media/weather-service-tutorial/grouped-data.png)</center>

## <a name="plot-forecast-data"></a>Trazado de los datos de previsión

Para ver cómo cambian la velocidad y la dirección del viento en el transcurso de los próximos 15 días, se van a trazar los valores previstos con respecto a los días para los que se pronostican.

```python
# Plot wind speed
curr_date = datetime.datetime.now().date()
windsPlot_df = pd.DataFrame({ 'Location A': wind_speeds[0], 'Location B': wind_speeds[1], 'Location C': wind_speeds[2], 'Location D': wind_speeds[3]}, index=pd.date_range(curr_date,periods=15))
windsPlot = windsPlot_df.plot.line()
windsPlot.set_xlabel("Date")
windsPlot.set_ylabel("Wind speed")
```

```python
#Plot wind direction 
windsPlot_df = pd.DataFrame({ 'Location A': wind_direction[0], 'Location B': wind_direction[1], 'Location C': wind_direction[2], 'Location D': wind_direction[3]}, index=pd.date_range(curr_date,periods=15))
windsPlot = windsPlot_df.plot.line()
windsPlot.set_xlabel("Date")
windsPlot.set_ylabel("Wind direction")
```

Los gráficos siguientes muestran los datos de previsión para el cambio de velocidad del viento (gráfico izquierdo) y la dirección (gráfico derecho) en los próximos 15 días a partir del día en que se solicitaron los datos.

<center>

![Trazado de la velocidad del viento](./media/weather-service-tutorial/speed-date-plot.png) ![Trazado de la dirección del viento](./media/weather-service-tutorial/direction-date-plot.png)</center>


## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido cómo llamar a las API REST de Azure Maps para obtener los datos de previsión meteorológica y visualizar los datos en los gráficos.

Para más información sobre cómo llamar a las API REST de Azure Maps dentro de Azure Notebooks, consulte [Enrutamiento de vehículos eléctricos mediante Azure Notebooks](https://docs.microsoft.com/azure/azure-maps/tutorial-ev-routing).

Para explorar las API de Azure Maps que se usan en este tutorial, consulte:

* [Previsión diaria](https://aka.ms/AzureMapsWeatherDailyForecast)
* [Render - Get Map Image](https://docs.microsoft.com/rest/api/maps/render/getmapimage)

Para obtener una lista completa de las API REST de Azure Maps, consulte [API REST de Azure Maps](https://docs.microsoft.com/azure/azure-maps/consumption-model).

Para más información sobre Azure Notebooks, consulte [Azure Notebooks](https://docs.microsoft.com/azure/notebooks).
