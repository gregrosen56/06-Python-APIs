
Observable Trends

1)  As expected, temperature is related to latitude.  The larger the absolute value of a location's latitude, the lower the temperature is likely to be.  This results in a parabolic arrangement of the points in the scatter plot.  

2)  None of the other scatter plots reveal very much information.  Humidity, cloudiness, and wind speed appear almost completely unrelated to latitude.  The only thing we might glean from this data is that the higher recorded wind speeds tend to occur further from the equator.

3)  There is a potential problem with this analysis.  I gathered weather data based on a random selection of coordinates, which is correct; each of the 500 data points sits at a unique location.  However, given that 70% of the planet's surface is covered in water, my random coordinates were likely to land in oceans.  When citipy looked up the closest cities to these coordinates, it led to an overrepresentation of coastal cities or cities in extreme locations.  For example, Ushuaia, Argentina, which is the southernmost city in the world, appears eight times in the list.  
    A more advanced method of random coordinate selection would be required to avoid issues like this, perhaps one that is aware of where the Earth's large land masses are located.  
    
4)  I built a bonus scatter plot linking humidity and cloudiness.  I was hoping there would be some relationship, but alas, there is nothing useful there.


```python
#import libraries

import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import requests
from citipy import citipy
import random
import matplotlib.axes as ax
import datetime
now = datetime.datetime.now()

# OpenWeatherMap API Key from config
from config import api_key
```


```python
#GET LIST OF 500 RANDOM LATITUDES AND LONGITUDES

lat = []
lng = []

#Latitude  #limited to (-70,70) to prevent an excess of arctic/antarctic coordinates
for x in range(500):
    lat.append(round(random.uniform(-70,70),2)) 
#Longitude
for x in range(500):
    lng.append(round(random.uniform(-180,180),2))

cities_df = pd.DataFrame({"Latitude": lat,"Longitude":lng, 
                          "City":"", "Country":"", 
                          "Temperature":0,"Humidity":0,"Cloudiness":0,"Wind Speed":0})
cities_df = cities_df[['Latitude','Longitude','City','Country',
                       'Temperature','Humidity','Cloudiness','Wind Speed']]
cities_df.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Latitude</th>
      <th>Longitude</th>
      <th>City</th>
      <th>Country</th>
      <th>Temperature</th>
      <th>Humidity</th>
      <th>Cloudiness</th>
      <th>Wind Speed</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>-58.22</td>
      <td>-70.26</td>
      <td></td>
      <td></td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>-40.82</td>
      <td>41.40</td>
      <td></td>
      <td></td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1.45</td>
      <td>-42.62</td>
      <td></td>
      <td></td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>26.90</td>
      <td>58.93</td>
      <td></td>
      <td></td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>-5.33</td>
      <td>-77.99</td>
      <td></td>
      <td></td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
</div>




```python
#LINK RANDOM COORDINATES TO CITIES USING CITIPY

for index, row in cities_df.iterrows():
    lat = row['Latitude']
    lng = row['Longitude']
    cities_df.loc[index, "City"] = citipy.nearest_city(lat,lng).city_name
    cities_df.loc[index, "Country"] = citipy.nearest_city(lat,lng).country_code
    
cities_df.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Latitude</th>
      <th>Longitude</th>
      <th>City</th>
      <th>Country</th>
      <th>Temperature</th>
      <th>Humidity</th>
      <th>Cloudiness</th>
      <th>Wind Speed</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>-58.22</td>
      <td>-70.26</td>
      <td>ushuaia</td>
      <td>ar</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>-40.82</td>
      <td>41.40</td>
      <td>margate</td>
      <td>za</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1.45</td>
      <td>-42.62</td>
      <td>cururupu</td>
      <td>br</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>26.90</td>
      <td>58.93</td>
      <td>iranshahr</td>
      <td>ir</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>-5.33</td>
      <td>-77.99</td>
      <td>la peca</td>
      <td>pe</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
</div>




```python
#PULL WEATHER DATA FOR ALL CITIES FROM OPENWEATHERMAP, PRINT RESPONSES

base_url = "http://api.openweathermap.org/data/2.5/weather?"
units = "imperial"
counter = 1

print("------------------------------------------")
print("RETRIEVING DATA")
print("------------------------------------------")

for index, row in cities_df.iterrows():
    latitude = row['Latitude']
    longitude = row['Longitude']
    
    query_url = f"{base_url}appid={api_key}&units={units}&lat={latitude}&lon={longitude}"
    
    city = row['City']
    country = row['Country']
    
    response = requests.get(query_url).json()
    
    #Append response data to our DataFrame
    cities_df.loc[index,"Temperature"] = response["main"]["temp"]
    cities_df.loc[index,"Humidity"] = response["main"]["humidity"]
    cities_df.loc[index,"Cloudiness"] = response["clouds"]["all"]
    cities_df.loc[index,"Wind Speed"] = response["wind"]["speed"]
    
    print("City " + str(counter) + ":  " + city + ", " + country)
    print("(" + str(query_url) + ")")
    print("Temperature (F):  " + str(response["main"]["temp"]))
    print("Humidity (%):  " + str(response["main"]["humidity"]))
    print("Cloudiness (%):  " + str(response["clouds"]["all"]))
    print("Wind Speed (MPH):  " + str(response["wind"]["speed"]))
    print("------------------------------------------")
    
    counter = counter + 1

print("FINISHED RETRIEVING DATA")
print("------------------------------------------")
    
#Save DataFrame as csv
cities_df.to_csv("output_analysis/Cities_DataFrame.csv")
```

    ------------------------------------------
    RETRIEVING DATA
    ------------------------------------------
    City 1:  ushuaia, ar
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-58.22&lon=-70.26)
    Temperature (F):  38.75
    Humidity (%):  100
    Cloudiness (%):  92
    Wind Speed (MPH):  22.75
    ------------------------------------------
    City 2:  margate, za
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-40.82&lon=41.4)
    Temperature (F):  53.69
    Humidity (%):  100
    Cloudiness (%):  64
    Wind Speed (MPH):  13.42
    ------------------------------------------
    City 3:  cururupu, br
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=1.45&lon=-42.62)
    Temperature (F):  80.15
    Humidity (%):  100
    Cloudiness (%):  32
    Wind Speed (MPH):  11.3
    ------------------------------------------
    City 4:  iranshahr, ir
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=26.9&lon=58.93)
    Temperature (F):  79.7
    Humidity (%):  32
    Cloudiness (%):  36
    Wind Speed (MPH):  2.91
    ------------------------------------------
    City 5:  la peca, pe
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-5.33&lon=-77.99)
    Temperature (F):  71.24
    Humidity (%):  85
    Cloudiness (%):  88
    Wind Speed (MPH):  2.51
    ------------------------------------------
    City 6:  severo-kurilsk, ru
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=38.97&lon=160.12)
    Temperature (F):  56.66
    Humidity (%):  100
    Cloudiness (%):  88
    Wind Speed (MPH):  10.89
    ------------------------------------------
    City 7:  new norfolk, au
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-62.54&lon=132.03)
    Temperature (F):  30.56
    Humidity (%):  100
    Cloudiness (%):  92
    Wind Speed (MPH):  23.49
    ------------------------------------------
    City 8:  riyadh, sa
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=26.3&lon=46.27)
    Temperature (F):  82.4
    Humidity (%):  21
    Cloudiness (%):  0
    Wind Speed (MPH):  10.96
    ------------------------------------------
    City 9:  kaoma, zm
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-15.55&lon=24.33)
    Temperature (F):  63.14
    Humidity (%):  65
    Cloudiness (%):  0
    Wind Speed (MPH):  6.08
    ------------------------------------------
    City 10:  nikolskoye, ru
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=33.59&lon=178.23)
    Temperature (F):  68.72
    Humidity (%):  100
    Cloudiness (%):  12
    Wind Speed (MPH):  5.93
    ------------------------------------------
    City 11:  sola, vu
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-11.43&lon=170.11)
    Temperature (F):  81.5
    Humidity (%):  100
    Cloudiness (%):  92
    Wind Speed (MPH):  5.3
    ------------------------------------------
    City 12:  itoman, jp
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=22.46&lon=128.89)
    Temperature (F):  84.47
    Humidity (%):  96
    Cloudiness (%):  0
    Wind Speed (MPH):  4.63
    ------------------------------------------
    City 13:  los llanos de aridane, es
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=29.03&lon=-20.31)
    Temperature (F):  66.2
    Humidity (%):  100
    Cloudiness (%):  44
    Wind Speed (MPH):  18.61
    ------------------------------------------
    City 14:  porkhov, ru
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=57.93&lon=29.33)
    Temperature (F):  53.6
    Humidity (%):  54
    Cloudiness (%):  0
    Wind Speed (MPH):  4.47
    ------------------------------------------
    City 15:  riyadh, sa
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=25.24&lon=46.4)
    Temperature (F):  86
    Humidity (%):  15
    Cloudiness (%):  0
    Wind Speed (MPH):  4.7
    ------------------------------------------
    City 16:  richards bay, za
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-30.96&lon=35.7)
    Temperature (F):  69.35
    Humidity (%):  100
    Cloudiness (%):  36
    Wind Speed (MPH):  13.65
    ------------------------------------------
    City 17:  hobart, au
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-46.67&lon=146.16)
    Temperature (F):  52.43
    Humidity (%):  89
    Cloudiness (%):  92
    Wind Speed (MPH):  22.97
    ------------------------------------------
    City 18:  tucuman, ar
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-27.11&lon=-66.86)
    Temperature (F):  56.93
    Humidity (%):  28
    Cloudiness (%):  0
    Wind Speed (MPH):  0.49
    ------------------------------------------
    City 19:  yulara, au
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-23.24&lon=128.89)
    Temperature (F):  50.27
    Humidity (%):  78
    Cloudiness (%):  0
    Wind Speed (MPH):  4.74
    ------------------------------------------
    City 20:  jinchang, cn
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=39.07&lon=103.03)
    Temperature (F):  43.61
    Humidity (%):  38
    Cloudiness (%):  0
    Wind Speed (MPH):  2.84
    ------------------------------------------
    City 21:  avarua, ck
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-37.48&lon=-164.95)
    Temperature (F):  62.42
    Humidity (%):  100
    Cloudiness (%):  92
    Wind Speed (MPH):  32.82
    ------------------------------------------
    City 22:  butaritari, ki
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=16.59&lon=168.61)
    Temperature (F):  79.16
    Humidity (%):  100
    Cloudiness (%):  76
    Wind Speed (MPH):  21.7
    ------------------------------------------
    City 23:  ruteng, id
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-8.91&lon=120.41)
    Temperature (F):  78.17
    Humidity (%):  100
    Cloudiness (%):  92
    Wind Speed (MPH):  9.55
    ------------------------------------------
    City 24:  cape town, za
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-66.19&lon=-7.08)
    Temperature (F):  18.86
    Humidity (%):  100
    Cloudiness (%):  44
    Wind Speed (MPH):  28.19
    ------------------------------------------
    City 25:  faanui, pf
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-16.06&lon=-155.26)
    Temperature (F):  80.33
    Humidity (%):  100
    Cloudiness (%):  76
    Wind Speed (MPH):  8.95
    ------------------------------------------
    City 26:  swabi, pk
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=34.39&lon=72.49)
    Temperature (F):  62.87
    Humidity (%):  57
    Cloudiness (%):  0
    Wind Speed (MPH):  3.24
    ------------------------------------------
    City 27:  hami, cn
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=43.83&lon=91.87)
    Temperature (F):  36.68
    Humidity (%):  33
    Cloudiness (%):  0
    Wind Speed (MPH):  3.58
    ------------------------------------------
    City 28:  salalah, om
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=17.51&lon=55.51)
    Temperature (F):  85.01
    Humidity (%):  95
    Cloudiness (%):  12
    Wind Speed (MPH):  16.33
    ------------------------------------------
    City 29:  korla, cn
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=41.94&lon=88.0)
    Temperature (F):  49.1
    Humidity (%):  44
    Cloudiness (%):  0
    Wind Speed (MPH):  5.14
    ------------------------------------------
    City 30:  carnarvon, au
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-32.83&lon=89.4)
    Temperature (F):  63.59
    Humidity (%):  100
    Cloudiness (%):  100
    Wind Speed (MPH):  16.11
    ------------------------------------------
    City 31:  taoudenni, ml
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=22.85&lon=-3.25)
    Temperature (F):  96.35
    Humidity (%):  13
    Cloudiness (%):  20
    Wind Speed (MPH):  13.69
    ------------------------------------------
    City 32:  bethel, us
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=48.08&lon=-163.72)
    Temperature (F):  43.88
    Humidity (%):  100
    Cloudiness (%):  76
    Wind Speed (MPH):  12.86
    ------------------------------------------
    City 33:  kawalu, id
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-14.53&lon=107.0)
    Temperature (F):  80.6
    Humidity (%):  100
    Cloudiness (%):  68
    Wind Speed (MPH):  22.97
    ------------------------------------------
    City 34:  taolanaro, mg
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-61.97&lon=59.27)
    Temperature (F):  17.87
    Humidity (%):  100
    Cloudiness (%):  36
    Wind Speed (MPH):  4.41
    ------------------------------------------
    City 35:  kamaishi, jp
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=37.17&lon=147.5)
    Temperature (F):  59.54
    Humidity (%):  100
    Cloudiness (%):  20
    Wind Speed (MPH):  7.31
    ------------------------------------------
    City 36:  blackwater, au
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-25.04&lon=148.61)
    Temperature (F):  49.55
    Humidity (%):  91
    Cloudiness (%):  44
    Wind Speed (MPH):  5.37
    ------------------------------------------
    City 37:  ahuimanu, us
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=30.68&lon=-154.15)
    Temperature (F):  67.1
    Humidity (%):  100
    Cloudiness (%):  88
    Wind Speed (MPH):  11.63
    ------------------------------------------
    City 38:  husavik, is
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=69.76&lon=-16.35)
    Temperature (F):  36.95
    Humidity (%):  95
    Cloudiness (%):  48
    Wind Speed (MPH):  9.78
    ------------------------------------------
    City 39:  paamiut, gl
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=59.58&lon=-53.13)
    Temperature (F):  34.7
    Humidity (%):  100
    Cloudiness (%):  12
    Wind Speed (MPH):  11.34
    ------------------------------------------
    City 40:  yerbogachen, ru
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=60.9&lon=106.33)
    Temperature (F):  37.49
    Humidity (%):  100
    Cloudiness (%):  92
    Wind Speed (MPH):  5.41
    ------------------------------------------
    City 41:  rikitea, pf
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-21.48&lon=-116.18)
    Temperature (F):  76.64
    Humidity (%):  98
    Cloudiness (%):  12
    Wind Speed (MPH):  19.46
    ------------------------------------------
    City 42:  dano, bf
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=10.95&lon=-2.96)
    Temperature (F):  81.23
    Humidity (%):  79
    Cloudiness (%):  20
    Wind Speed (MPH):  4.25
    ------------------------------------------
    City 43:  cape town, za
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-68.8&lon=-16.3)
    Temperature (F):  9.86
    Humidity (%):  99
    Cloudiness (%):  64
    Wind Speed (MPH):  20.18
    ------------------------------------------
    City 44:  belyy yar, ru
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=59.24&lon=86.98)
    Temperature (F):  29.75
    Humidity (%):  78
    Cloudiness (%):  0
    Wind Speed (MPH):  4.29
    ------------------------------------------
    City 45:  aksarka, ru
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=68.09&lon=67.49)
    Temperature (F):  30.11
    Humidity (%):  94
    Cloudiness (%):  92
    Wind Speed (MPH):  2.84
    ------------------------------------------
    City 46:  coihaique, cl
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-45.68&lon=-75.19)
    Temperature (F):  49.1
    Humidity (%):  100
    Cloudiness (%):  12
    Wind Speed (MPH):  19.35
    ------------------------------------------
    City 47:  depok, id
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-7.71&lon=110.18)
    Temperature (F):  73.76
    Humidity (%):  91
    Cloudiness (%):  92
    Wind Speed (MPH):  3.8
    ------------------------------------------
    City 48:  lagoa, pt
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=43.29&lon=-31.24)
    Temperature (F):  64.13
    Humidity (%):  93
    Cloudiness (%):  92
    Wind Speed (MPH):  21.25
    ------------------------------------------
    City 49:  san-pedro, ci
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=3.38&lon=-6.09)
    Temperature (F):  81.5
    Humidity (%):  100
    Cloudiness (%):  100
    Wind Speed (MPH):  8.43
    ------------------------------------------
    City 50:  grand river south east, mu
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-19.9&lon=65.36)
    Temperature (F):  78.62
    Humidity (%):  99
    Cloudiness (%):  76
    Wind Speed (MPH):  14.14
    ------------------------------------------
    City 51:  yellowknife, ca
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=62.28&lon=-107.8)
    Temperature (F):  41
    Humidity (%):  100
    Cloudiness (%):  0
    Wind Speed (MPH):  6.04
    ------------------------------------------
    City 52:  ponta do sol, cv
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=21.55&lon=-40.36)
    Temperature (F):  73.67
    Humidity (%):  100
    Cloudiness (%):  0
    Wind Speed (MPH):  17.6
    ------------------------------------------
    City 53:  rikitea, pf
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-58.46&lon=-141.23)
    Temperature (F):  37.58
    Humidity (%):  90
    Cloudiness (%):  92
    Wind Speed (MPH):  20.36
    ------------------------------------------
    City 54:  new norfolk, au
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-66.87&lon=127.47)
    Temperature (F):  16.34
    Humidity (%):  87
    Cloudiness (%):  80
    Wind Speed (MPH):  10.07
    ------------------------------------------
    City 55:  cape town, za
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-47.16&lon=-4.4)
    Temperature (F):  40.82
    Humidity (%):  100
    Cloudiness (%):  32
    Wind Speed (MPH):  26.66
    ------------------------------------------
    City 56:  carnarvon, au
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-21.95&lon=107.92)
    Temperature (F):  72.68
    Humidity (%):  100
    Cloudiness (%):  80
    Wind Speed (MPH):  20.13
    ------------------------------------------
    City 57:  mahebourg, mu
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-35.82&lon=67.95)
    Temperature (F):  61.61
    Humidity (%):  100
    Cloudiness (%):  92
    Wind Speed (MPH):  4.85
    ------------------------------------------
    City 58:  marrakesh, ma
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=31.78&lon=-6.75)
    Temperature (F):  47.03
    Humidity (%):  96
    Cloudiness (%):  32
    Wind Speed (MPH):  1.5
    ------------------------------------------
    City 59:  jamestown, sh
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-13.68&lon=-0.71)
    Temperature (F):  73.04
    Humidity (%):  100
    Cloudiness (%):  8
    Wind Speed (MPH):  17.38
    ------------------------------------------
    City 60:  butaritari, ki
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=21.03&lon=159.97)
    Temperature (F):  77.81
    Humidity (%):  100
    Cloudiness (%):  0
    Wind Speed (MPH):  13.69
    ------------------------------------------
    City 61:  carnarvon, au
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-25.42&lon=114.36)
    Temperature (F):  50.81
    Humidity (%):  87
    Cloudiness (%):  0
    Wind Speed (MPH):  3.51
    ------------------------------------------
    City 62:  navabad, tj
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=39.34&lon=69.84)
    Temperature (F):  37.85
    Humidity (%):  95
    Cloudiness (%):  92
    Wind Speed (MPH):  1.61
    ------------------------------------------
    City 63:  kavaratti, in
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=10.28&lon=74.24)
    Temperature (F):  76.01
    Humidity (%):  100
    Cloudiness (%):  92
    Wind Speed (MPH):  9.95
    ------------------------------------------
    City 64:  hobart, au
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-62.2&lon=152.07)
    Temperature (F):  33.53
    Humidity (%):  87
    Cloudiness (%):  92
    Wind Speed (MPH):  25.77
    ------------------------------------------
    City 65:  rikitea, pf
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-52.15&lon=-135.8)
    Temperature (F):  47.12
    Humidity (%):  100
    Cloudiness (%):  92
    Wind Speed (MPH):  13.58
    ------------------------------------------
    City 66:  goderich, sl
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=3.56&lon=-21.59)
    Temperature (F):  78.71
    Humidity (%):  100
    Cloudiness (%):  92
    Wind Speed (MPH):  6.6
    ------------------------------------------
    City 67:  albany, au
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-68.64&lon=118.54)
    Temperature (F):  1.85
    Humidity (%):  73
    Cloudiness (%):  76
    Wind Speed (MPH):  15.32
    ------------------------------------------
    City 68:  albany, au
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-52.28&lon=119.17)
    Temperature (F):  37.31
    Humidity (%):  100
    Cloudiness (%):  20
    Wind Speed (MPH):  7.83
    ------------------------------------------
    City 69:  kieta, pg
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-2.24&lon=157.43)
    Temperature (F):  84.92
    Humidity (%):  100
    Cloudiness (%):  76
    Wind Speed (MPH):  11.07
    ------------------------------------------
    City 70:  sioux lookout, ca
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=51.21&lon=-91.91)
    Temperature (F):  64.58
    Humidity (%):  92
    Cloudiness (%):  80
    Wind Speed (MPH):  4.07
    ------------------------------------------
    City 71:  isangel, vu
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-24.51&lon=176.36)
    Temperature (F):  70.25
    Humidity (%):  100
    Cloudiness (%):  88
    Wind Speed (MPH):  14.58
    ------------------------------------------
    City 72:  olafsvik, is
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=67.56&lon=-30.39)
    Temperature (F):  32.09
    Humidity (%):  99
    Cloudiness (%):  64
    Wind Speed (MPH):  9.89
    ------------------------------------------
    City 73:  alofi, nu
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-24.46&lon=-170.03)
    Temperature (F):  72.05
    Humidity (%):  100
    Cloudiness (%):  92
    Wind Speed (MPH):  3.36
    ------------------------------------------
    City 74:  portland, au
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-51.3&lon=135.54)
    Temperature (F):  47.03
    Humidity (%):  90
    Cloudiness (%):  92
    Wind Speed (MPH):  34.27
    ------------------------------------------
    City 75:  saint-orens-de-gameville, fr
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=43.37&lon=1.7)
    Temperature (F):  62.6
    Humidity (%):  93
    Cloudiness (%):  24
    Wind Speed (MPH):  4.7
    ------------------------------------------
    City 76:  saint-philippe, re
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-49.59&lon=67.08)
    Temperature (F):  38.03
    Humidity (%):  100
    Cloudiness (%):  76
    Wind Speed (MPH):  32.82
    ------------------------------------------
    City 77:  caravelas, br
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-22.25&lon=-31.54)
    Temperature (F):  74.21
    Humidity (%):  100
    Cloudiness (%):  48
    Wind Speed (MPH):  17.49
    ------------------------------------------
    City 78:  tuktoyaktuk, ca
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=68.55&lon=-131.44)
    Temperature (F):  32.54
    Humidity (%):  95
    Cloudiness (%):  76
    Wind Speed (MPH):  10.85
    ------------------------------------------
    City 79:  novikovo, ru
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=45.45&lon=143.85)
    Temperature (F):  44.87
    Humidity (%):  89
    Cloudiness (%):  8
    Wind Speed (MPH):  12.19
    ------------------------------------------
    City 80:  mataura, pf
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-44.18&lon=-150.16)
    Temperature (F):  53.96
    Humidity (%):  100
    Cloudiness (%):  92
    Wind Speed (MPH):  28.01
    ------------------------------------------
    City 81:  punta arenas, cl
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-55.47&lon=-79.99)
    Temperature (F):  39.92
    Humidity (%):  100
    Cloudiness (%):  48
    Wind Speed (MPH):  15.66
    ------------------------------------------
    City 82:  kruisfontein, za
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-62.99&lon=26.02)
    Temperature (F):  28.4
    Humidity (%):  100
    Cloudiness (%):  80
    Wind Speed (MPH):  8.1
    ------------------------------------------
    City 83:  ornskoldsvik, se
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=64.77&lon=18.89)
    Temperature (F):  48.2
    Humidity (%):  76
    Cloudiness (%):  0
    Wind Speed (MPH):  3.36
    ------------------------------------------
    City 84:  port-gentil, ga
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-0.71&lon=6.76)
    Temperature (F):  79.07
    Humidity (%):  100
    Cloudiness (%):  8
    Wind Speed (MPH):  5.37
    ------------------------------------------
    City 85:  jamestown, sh
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-32.78&lon=-8.4)
    Temperature (F):  64.58
    Humidity (%):  100
    Cloudiness (%):  56
    Wind Speed (MPH):  22.93
    ------------------------------------------
    City 86:  cururupu, br
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=5.23&lon=-41.18)
    Temperature (F):  79.61
    Humidity (%):  100
    Cloudiness (%):  32
    Wind Speed (MPH):  14.99
    ------------------------------------------
    City 87:  saint-philippe, re
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-36.26&lon=58.76)
    Temperature (F):  60.35
    Humidity (%):  100
    Cloudiness (%):  92
    Wind Speed (MPH):  12.64
    ------------------------------------------
    City 88:  taolanaro, mg
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-52.7&lon=52.66)
    Temperature (F):  34.88
    Humidity (%):  100
    Cloudiness (%):  44
    Wind Speed (MPH):  30.42
    ------------------------------------------
    City 89:  puerto ayora, ec
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-5.21&lon=-96.91)
    Temperature (F):  77.18
    Humidity (%):  100
    Cloudiness (%):  24
    Wind Speed (MPH):  14.7
    ------------------------------------------
    City 90:  mar del plata, ar
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-51.57&lon=-47.38)
    Temperature (F):  39.65
    Humidity (%):  100
    Cloudiness (%):  0
    Wind Speed (MPH):  29.97
    ------------------------------------------
    City 91:  saint-philippe, re
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-52.05&lon=70.09)
    Temperature (F):  36.05
    Humidity (%):  100
    Cloudiness (%):  68
    Wind Speed (MPH):  29.91
    ------------------------------------------
    City 92:  meiktila, mm
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=20.66&lon=95.43)
    Temperature (F):  75.11
    Humidity (%):  94
    Cloudiness (%):  88
    Wind Speed (MPH):  9.55
    ------------------------------------------
    City 93:  san patricio, mx
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=5.85&lon=-112.56)
    Temperature (F):  81.23
    Humidity (%):  100
    Cloudiness (%):  56
    Wind Speed (MPH):  15.82
    ------------------------------------------
    City 94:  belmonte, br
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-18.55&lon=-29.21)
    Temperature (F):  77.18
    Humidity (%):  100
    Cloudiness (%):  0
    Wind Speed (MPH):  19.13
    ------------------------------------------
    City 95:  itsukaichi, jp
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=35.7&lon=139.22)
    Temperature (F):  66.38
    Humidity (%):  82
    Cloudiness (%):  90
    Wind Speed (MPH):  3.36
    ------------------------------------------
    City 96:  camacha, pt
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=37.09&lon=-18.18)
    Temperature (F):  61.43
    Humidity (%):  100
    Cloudiness (%):  80
    Wind Speed (MPH):  14.99
    ------------------------------------------
    City 97:  arraial do cabo, br
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-56.67&lon=-18.45)
    Temperature (F):  35.69
    Humidity (%):  94
    Cloudiness (%):  68
    Wind Speed (MPH):  26.22
    ------------------------------------------
    City 98:  jamestown, sh
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-38.32&lon=-16.57)
    Temperature (F):  59
    Humidity (%):  98
    Cloudiness (%):  76
    Wind Speed (MPH):  20.36
    ------------------------------------------
    City 99:  rikitea, pf
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-51.21&lon=-115.38)
    Temperature (F):  41.63
    Humidity (%):  100
    Cloudiness (%):  68
    Wind Speed (MPH):  19.57
    ------------------------------------------
    City 100:  dosso, ne
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=13.2&lon=3.1)
    Temperature (F):  86.36
    Humidity (%):  58
    Cloudiness (%):  0
    Wind Speed (MPH):  5.41
    ------------------------------------------
    City 101:  vaini, to
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-65.04&lon=-177.94)
    Temperature (F):  26.15
    Humidity (%):  100
    Cloudiness (%):  68
    Wind Speed (MPH):  42.84
    ------------------------------------------
    City 102:  avarua, ck
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-56.38&lon=-167.18)
    Temperature (F):  38.66
    Humidity (%):  100
    Cloudiness (%):  20
    Wind Speed (MPH):  25.77
    ------------------------------------------
    City 103:  albany, au
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-58.35&lon=124.9)
    Temperature (F):  33.89
    Humidity (%):  100
    Cloudiness (%):  92
    Wind Speed (MPH):  20.47
    ------------------------------------------
    City 104:  taolanaro, mg
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-55.73&lon=62.78)
    Temperature (F):  31.46
    Humidity (%):  100
    Cloudiness (%):  56
    Wind Speed (MPH):  21.41
    ------------------------------------------
    City 105:  saint-philippe, re
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-49.33&lon=62.57)
    Temperature (F):  38.48
    Humidity (%):  100
    Cloudiness (%):  64
    Wind Speed (MPH):  29.91
    ------------------------------------------
    City 106:  kloulklubed, pw
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=6.14&lon=131.0)
    Temperature (F):  83.75
    Humidity (%):  100
    Cloudiness (%):  56
    Wind Speed (MPH):  5.53
    ------------------------------------------
    City 107:  guerrero negro, mx
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=18.14&lon=-128.0)
    Temperature (F):  73.94
    Humidity (%):  99
    Cloudiness (%):  92
    Wind Speed (MPH):  19.13
    ------------------------------------------
    City 108:  hithadhoo, mv
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-21.38&lon=82.89)
    Temperature (F):  73.94
    Humidity (%):  100
    Cloudiness (%):  92
    Wind Speed (MPH):  21.47
    ------------------------------------------
    City 109:  atuona, pf
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-11.64&lon=-137.17)
    Temperature (F):  79.25
    Humidity (%):  100
    Cloudiness (%):  0
    Wind Speed (MPH):  12.12
    ------------------------------------------
    City 110:  ishigaki, jp
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=26.58&lon=123.76)
    Temperature (F):  81.68
    Humidity (%):  98
    Cloudiness (%):  8
    Wind Speed (MPH):  7.83
    ------------------------------------------
    City 111:  cape town, za
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-60.55&lon=-7.46)
    Temperature (F):  27.05
    Humidity (%):  100
    Cloudiness (%):  12
    Wind Speed (MPH):  24.94
    ------------------------------------------
    City 112:  samusu, ws
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-9.53&lon=-167.69)
    Temperature (F):  82.67
    Humidity (%):  100
    Cloudiness (%):  76
    Wind Speed (MPH):  15.88
    ------------------------------------------
    City 113:  saint-philippe, re
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-64.0&lon=71.05)
    Temperature (F):  6.44
    Humidity (%):  100
    Cloudiness (%):  80
    Wind Speed (MPH):  22.64
    ------------------------------------------
    City 114:  tasiilaq, gl
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=63.2&lon=-35.17)
    Temperature (F):  40.01
    Humidity (%):  100
    Cloudiness (%):  64
    Wind Speed (MPH):  9.66
    ------------------------------------------
    City 115:  bethel, us
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=56.8&lon=-168.5)
    Temperature (F):  38.93
    Humidity (%):  100
    Cloudiness (%):  32
    Wind Speed (MPH):  1.5
    ------------------------------------------
    City 116:  ribeira grande, pt
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=27.75&lon=-42.06)
    Temperature (F):  71.96
    Humidity (%):  100
    Cloudiness (%):  88
    Wind Speed (MPH):  9.33
    ------------------------------------------
    City 117:  luderitz, na
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-32.44&lon=7.29)
    Temperature (F):  65.48
    Humidity (%):  100
    Cloudiness (%):  32
    Wind Speed (MPH):  29.24
    ------------------------------------------
    City 118:  hobart, au
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-58.69&lon=145.26)
    Temperature (F):  38.57
    Humidity (%):  85
    Cloudiness (%):  92
    Wind Speed (MPH):  32.21
    ------------------------------------------
    City 119:  asau, tv
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-13.35&lon=178.98)
    Temperature (F):  81.5
    Humidity (%):  100
    Cloudiness (%):  48
    Wind Speed (MPH):  11.79
    ------------------------------------------
    City 120:  sabha, ly
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=26.08&lon=17.01)
    Temperature (F):  76.1
    Humidity (%):  29
    Cloudiness (%):  0
    Wind Speed (MPH):  9.66
    ------------------------------------------
    City 121:  baruun-urt, mn
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=47.25&lon=113.56)
    Temperature (F):  33.53
    Humidity (%):  89
    Cloudiness (%):  8
    Wind Speed (MPH):  7.09
    ------------------------------------------
    City 122:  nioro, ml
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=14.7&lon=-10.17)
    Temperature (F):  93.02
    Humidity (%):  47
    Cloudiness (%):  92
    Wind Speed (MPH):  2.51
    ------------------------------------------
    City 123:  orodara, bf
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=11.18&lon=-5.12)
    Temperature (F):  78.44
    Humidity (%):  85
    Cloudiness (%):  0
    Wind Speed (MPH):  5.41
    ------------------------------------------
    City 124:  bengkulu, id
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-13.5&lon=93.41)
    Temperature (F):  80.6
    Humidity (%):  100
    Cloudiness (%):  88
    Wind Speed (MPH):  23.87
    ------------------------------------------
    City 125:  kapaa, us
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=16.8&lon=-164.65)
    Temperature (F):  77.54
    Humidity (%):  100
    Cloudiness (%):  68
    Wind Speed (MPH):  11.79
    ------------------------------------------
    City 126:  paita, pe
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-5.54&lon=-81.98)
    Temperature (F):  68.9
    Humidity (%):  100
    Cloudiness (%):  0
    Wind Speed (MPH):  11.45
    ------------------------------------------
    City 127:  paamiut, gl
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=61.11&lon=-48.29)
    Temperature (F):  28.85
    Humidity (%):  100
    Cloudiness (%):  8
    Wind Speed (MPH):  5.03
    ------------------------------------------
    City 128:  vila, vu
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-18.71&lon=167.73)
    Temperature (F):  75.47
    Humidity (%):  100
    Cloudiness (%):  76
    Wind Speed (MPH):  11.07
    ------------------------------------------
    City 129:  severnyy, ru
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=68.18&lon=64.34)
    Temperature (F):  31.64
    Humidity (%):  100
    Cloudiness (%):  88
    Wind Speed (MPH):  14.36
    ------------------------------------------
    City 130:  castro, cl
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-45.3&lon=-101.55)
    Temperature (F):  47.21
    Humidity (%):  100
    Cloudiness (%):  0
    Wind Speed (MPH):  18.34
    ------------------------------------------
    City 131:  silivri, tr
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=40.67&lon=28.22)
    Temperature (F):  64.42
    Humidity (%):  82
    Cloudiness (%):  75
    Wind Speed (MPH):  11.41
    ------------------------------------------
    City 132:  byron bay, au
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-27.7&lon=156.08)
    Temperature (F):  69.53
    Humidity (%):  100
    Cloudiness (%):  76
    Wind Speed (MPH):  15.82
    ------------------------------------------
    City 133:  cape town, za
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-48.89&lon=-3.98)
    Temperature (F):  39.56
    Humidity (%):  100
    Cloudiness (%):  56
    Wind Speed (MPH):  25.77
    ------------------------------------------
    City 134:  lampa, pe
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-15.39&lon=-70.56)
    Temperature (F):  59
    Humidity (%):  38
    Cloudiness (%):  0
    Wind Speed (MPH):  11.41
    ------------------------------------------
    City 135:  rikitea, pf
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-30.39&lon=-114.06)
    Temperature (F):  66.56
    Humidity (%):  100
    Cloudiness (%):  92
    Wind Speed (MPH):  13.98
    ------------------------------------------
    City 136:  nipawin, ca
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=53.12&lon=-104.0)
    Temperature (F):  75.2
    Humidity (%):  25
    Cloudiness (%):  1
    Wind Speed (MPH):  17.22
    ------------------------------------------
    City 137:  lolua, tv
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-6.53&lon=173.96)
    Temperature (F):  81.05
    Humidity (%):  100
    Cloudiness (%):  88
    Wind Speed (MPH):  6.53
    ------------------------------------------
    City 138:  georgetown, sh
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-12.98&lon=-23.42)
    Temperature (F):  77.45
    Humidity (%):  100
    Cloudiness (%):  8
    Wind Speed (MPH):  18.72
    ------------------------------------------
    City 139:  mazamari, pe
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-11.45&lon=-74.54)
    Temperature (F):  71.96
    Humidity (%):  82
    Cloudiness (%):  20
    Wind Speed (MPH):  1.34
    ------------------------------------------
    City 140:  umba, ru
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=66.52&lon=35.4)
    Temperature (F):  43.88
    Humidity (%):  58
    Cloudiness (%):  32
    Wind Speed (MPH):  3.62
    ------------------------------------------
    City 141:  saint-augustin, ca
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=52.28&lon=-57.58)
    Temperature (F):  36.14
    Humidity (%):  84
    Cloudiness (%):  44
    Wind Speed (MPH):  9.62
    ------------------------------------------
    City 142:  lebu, cl
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-30.76&lon=-102.06)
    Temperature (F):  66.11
    Humidity (%):  100
    Cloudiness (%):  68
    Wind Speed (MPH):  22.19
    ------------------------------------------
    City 143:  itoman, jp
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=24.34&lon=128.85)
    Temperature (F):  82.22
    Humidity (%):  98
    Cloudiness (%):  0
    Wind Speed (MPH):  9.33
    ------------------------------------------
    City 144:  ushuaia, ar
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-58.74&lon=-51.25)
    Temperature (F):  26.15
    Humidity (%):  100
    Cloudiness (%):  48
    Wind Speed (MPH):  14.47
    ------------------------------------------
    City 145:  miahuatlan, mx
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=16.22&lon=-96.65)
    Temperature (F):  87.8
    Humidity (%):  66
    Cloudiness (%):  5
    Wind Speed (MPH):  11.41
    ------------------------------------------
    City 146:  evensk, ru
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=61.82&lon=161.3)
    Temperature (F):  33.08
    Humidity (%):  98
    Cloudiness (%):  68
    Wind Speed (MPH):  3.24
    ------------------------------------------
    City 147:  himora, et
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=14.24&lon=36.43)
    Temperature (F):  77.18
    Humidity (%):  69
    Cloudiness (%):  80
    Wind Speed (MPH):  7.09
    ------------------------------------------
    City 148:  ahumada, mx
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=29.85&lon=-106.39)
    Temperature (F):  94.1
    Humidity (%):  11
    Cloudiness (%):  8
    Wind Speed (MPH):  4.81
    ------------------------------------------
    City 149:  port alfred, za
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-55.71&lon=34.06)
    Temperature (F):  32.09
    Humidity (%):  100
    Cloudiness (%):  12
    Wind Speed (MPH):  17.67
    ------------------------------------------
    City 150:  rikitea, pf
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-23.2&lon=-120.2)
    Temperature (F):  71.42
    Humidity (%):  100
    Cloudiness (%):  80
    Wind Speed (MPH):  14.92
    ------------------------------------------
    City 151:  hithadhoo, mv
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-4.64&lon=65.74)
    Temperature (F):  82.13
    Humidity (%):  100
    Cloudiness (%):  48
    Wind Speed (MPH):  10.18
    ------------------------------------------
    City 152:  busselton, au
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-40.84&lon=101.45)
    Temperature (F):  53.51
    Humidity (%):  100
    Cloudiness (%):  92
    Wind Speed (MPH):  20.85
    ------------------------------------------
    City 153:  nouadhibou, mr
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=21.66&lon=-17.69)
    Temperature (F):  66.2
    Humidity (%):  94
    Cloudiness (%):  0
    Wind Speed (MPH):  26.17
    ------------------------------------------
    City 154:  san miguel de cauri, pe
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-10.22&lon=-76.58)
    Temperature (F):  55.49
    Humidity (%):  39
    Cloudiness (%):  100
    Wind Speed (MPH):  1.05
    ------------------------------------------
    City 155:  ostrow wielkopolski, pl
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=51.5&lon=17.79)
    Temperature (F):  70.25
    Humidity (%):  56
    Cloudiness (%):  0
    Wind Speed (MPH):  10.89
    ------------------------------------------
    City 156:  kirovskiy, ru
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=45.15&lon=48.96)
    Temperature (F):  67.28
    Humidity (%):  100
    Cloudiness (%):  0
    Wind Speed (MPH):  12.68
    ------------------------------------------
    City 157:  severobaykalsk, ru
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=54.93&lon=108.54)
    Temperature (F):  32
    Humidity (%):  83
    Cloudiness (%):  0
    Wind Speed (MPH):  2.06
    ------------------------------------------
    City 158:  avarua, ck
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-36.2&lon=-158.69)
    Temperature (F):  63.86
    Humidity (%):  96
    Cloudiness (%):  68
    Wind Speed (MPH):  20.4
    ------------------------------------------
    City 159:  dargaville, nz
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-36.51&lon=173.13)
    Temperature (F):  59.27
    Humidity (%):  100
    Cloudiness (%):  80
    Wind Speed (MPH):  21.36
    ------------------------------------------
    City 160:  plouzane, fr
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=48.78&lon=-5.29)
    Temperature (F):  57.65
    Humidity (%):  96
    Cloudiness (%):  64
    Wind Speed (MPH):  12.46
    ------------------------------------------
    City 161:  gat, ly
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=27.41&lon=10.14)
    Temperature (F):  88.16
    Humidity (%):  16
    Cloudiness (%):  92
    Wind Speed (MPH):  9.33
    ------------------------------------------
    City 162:  wanning, cn
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=17.26&lon=112.91)
    Temperature (F):  87.35
    Humidity (%):  97
    Cloudiness (%):  0
    Wind Speed (MPH):  4.85
    ------------------------------------------
    City 163:  abinsk, ru
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=44.79&lon=38.14)
    Temperature (F):  64.4
    Humidity (%):  72
    Cloudiness (%):  0
    Wind Speed (MPH):  15.66
    ------------------------------------------
    City 164:  nanortalik, gl
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=53.09&lon=-39.8)
    Temperature (F):  42.8
    Humidity (%):  96
    Cloudiness (%):  92
    Wind Speed (MPH):  15.32
    ------------------------------------------
    City 165:  kapaa, us
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=38.23&lon=-179.86)
    Temperature (F):  62.42
    Humidity (%):  98
    Cloudiness (%):  76
    Wind Speed (MPH):  19.91
    ------------------------------------------
    City 166:  taolanaro, mg
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-51.31&lon=60.16)
    Temperature (F):  36.68
    Humidity (%):  100
    Cloudiness (%):  68
    Wind Speed (MPH):  31.47
    ------------------------------------------
    City 167:  cabo san lucas, mx
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=17.8&lon=-114.14)
    Temperature (F):  77.9
    Humidity (%):  100
    Cloudiness (%):  0
    Wind Speed (MPH):  15.7
    ------------------------------------------
    City 168:  hualmay, pe
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-20.5&lon=-95.57)
    Temperature (F):  68.81
    Humidity (%):  100
    Cloudiness (%):  64
    Wind Speed (MPH):  6.76
    ------------------------------------------
    City 169:  east london, za
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-57.23&lon=45.58)
    Temperature (F):  32.36
    Humidity (%):  100
    Cloudiness (%):  76
    Wind Speed (MPH):  38.03
    ------------------------------------------
    City 170:  yulara, au
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-29.64&lon=131.15)
    Temperature (F):  45.86
    Humidity (%):  80
    Cloudiness (%):  0
    Wind Speed (MPH):  3.51
    ------------------------------------------
    City 171:  salalah, om
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=17.13&lon=55.57)
    Temperature (F):  85.01
    Humidity (%):  95
    Cloudiness (%):  12
    Wind Speed (MPH):  16.33
    ------------------------------------------
    City 172:  yerbogachen, ru
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=63.34&lon=108.31)
    Temperature (F):  36.86
    Humidity (%):  75
    Cloudiness (%):  48
    Wind Speed (MPH):  8.16
    ------------------------------------------
    City 173:  zumarraga, es
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=42.85&lon=-2.35)
    Temperature (F):  60.46
    Humidity (%):  87
    Cloudiness (%):  75
    Wind Speed (MPH):  8.05
    ------------------------------------------
    City 174:  callaway, us
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=28.94&lon=-85.03)
    Temperature (F):  74.66
    Humidity (%):  100
    Cloudiness (%):  92
    Wind Speed (MPH):  29.75
    ------------------------------------------
    City 175:  yulara, au
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-25.25&lon=130.22)
    Temperature (F):  43.88
    Humidity (%):  85
    Cloudiness (%):  0
    Wind Speed (MPH):  3.36
    ------------------------------------------
    City 176:  taolanaro, mg
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-61.35&lon=58.76)
    Temperature (F):  23.36
    Humidity (%):  100
    Cloudiness (%):  68
    Wind Speed (MPH):  6.31
    ------------------------------------------
    City 177:  severo-kurilsk, ru
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=47.56&lon=153.77)
    Temperature (F):  37.4
    Humidity (%):  97
    Cloudiness (%):  88
    Wind Speed (MPH):  13.13
    ------------------------------------------
    City 178:  bida, ng
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=9.13&lon=5.97)
    Temperature (F):  81.05
    Humidity (%):  87
    Cloudiness (%):  8
    Wind Speed (MPH):  2.8
    ------------------------------------------
    City 179:  shahreza, ir
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=32.02&lon=51.8)
    Temperature (F):  49.73
    Humidity (%):  83
    Cloudiness (%):  24
    Wind Speed (MPH):  2.13
    ------------------------------------------
    City 180:  san patricio, mx
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=3.31&lon=-117.73)
    Temperature (F):  80.78
    Humidity (%):  100
    Cloudiness (%):  12
    Wind Speed (MPH):  14.14
    ------------------------------------------
    City 181:  tarko-sale, ru
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=65.13&lon=77.71)
    Temperature (F):  35.6
    Humidity (%):  96
    Cloudiness (%):  76
    Wind Speed (MPH):  13.35
    ------------------------------------------
    City 182:  tsihombe, mg
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-47.98&lon=45.43)
    Temperature (F):  39.38
    Humidity (%):  100
    Cloudiness (%):  80
    Wind Speed (MPH):  19.35
    ------------------------------------------
    City 183:  quatre cocos, mu
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-16.88&lon=70.03)
    Temperature (F):  79.34
    Humidity (%):  99
    Cloudiness (%):  68
    Wind Speed (MPH):  19.91
    ------------------------------------------
    City 184:  maghama, mr
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=17.74&lon=-11.2)
    Temperature (F):  90.68
    Humidity (%):  20
    Cloudiness (%):  0
    Wind Speed (MPH):  7.38
    ------------------------------------------
    City 185:  victoria, sc
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=3.58&lon=57.27)
    Temperature (F):  83.3
    Humidity (%):  100
    Cloudiness (%):  92
    Wind Speed (MPH):  11.9
    ------------------------------------------
    City 186:  raga, sd
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=8.31&lon=25.39)
    Temperature (F):  72.68
    Humidity (%):  73
    Cloudiness (%):  36
    Wind Speed (MPH):  3.91
    ------------------------------------------
    City 187:  kapaa, us
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=34.39&lon=-161.17)
    Temperature (F):  62.06
    Humidity (%):  97
    Cloudiness (%):  100
    Wind Speed (MPH):  7.76
    ------------------------------------------
    City 188:  toktogul, kg
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=42.17&lon=72.82)
    Temperature (F):  45.05
    Humidity (%):  95
    Cloudiness (%):  92
    Wind Speed (MPH):  1.61
    ------------------------------------------
    City 189:  nizhneudinsk, ru
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=53.76&lon=98.03)
    Temperature (F):  41.9
    Humidity (%):  70
    Cloudiness (%):  0
    Wind Speed (MPH):  5.75
    ------------------------------------------
    City 190:  ucluelet, ca
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=48.02&lon=-125.15)
    Temperature (F):  60.8
    Humidity (%):  55
    Cloudiness (%):  1
    Wind Speed (MPH):  9.17
    ------------------------------------------
    City 191:  sao filipe, cv
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=6.8&lon=-28.65)
    Temperature (F):  78.35
    Humidity (%):  100
    Cloudiness (%):  76
    Wind Speed (MPH):  8.43
    ------------------------------------------
    City 192:  puerto ayora, ec
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-10.89&lon=-104.04)
    Temperature (F):  74.57
    Humidity (%):  100
    Cloudiness (%):  80
    Wind Speed (MPH):  14.36
    ------------------------------------------
    City 193:  cape town, za
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-66.84&lon=-2.85)
    Temperature (F):  29.3
    Humidity (%):  97
    Cloudiness (%):  80
    Wind Speed (MPH):  17.34
    ------------------------------------------
    City 194:  palmer, us
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=61.66&lon=-148.82)
    Temperature (F):  59.59
    Humidity (%):  44
    Cloudiness (%):  40
    Wind Speed (MPH):  5.82
    ------------------------------------------
    City 195:  lorengau, pg
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=3.53&lon=148.07)
    Temperature (F):  81.95
    Humidity (%):  100
    Cloudiness (%):  32
    Wind Speed (MPH):  5.86
    ------------------------------------------
    City 196:  sao miguel do araguaia, br
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-13.22&lon=-50.45)
    Temperature (F):  77.81
    Humidity (%):  70
    Cloudiness (%):  0
    Wind Speed (MPH):  4.63
    ------------------------------------------
    City 197:  mahebourg, mu
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-38.21&lon=71.27)
    Temperature (F):  56.03
    Humidity (%):  100
    Cloudiness (%):  92
    Wind Speed (MPH):  3.29
    ------------------------------------------
    City 198:  taolanaro, mg
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-54.88&lon=60.55)
    Temperature (F):  32.54
    Humidity (%):  100
    Cloudiness (%):  8
    Wind Speed (MPH):  16.71
    ------------------------------------------
    City 199:  sabang, id
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=8.46&lon=94.37)
    Temperature (F):  85.1
    Humidity (%):  100
    Cloudiness (%):  64
    Wind Speed (MPH):  12.46
    ------------------------------------------
    City 200:  fuling, cn
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=30.02&lon=107.93)
    Temperature (F):  60.98
    Humidity (%):  97
    Cloudiness (%):  92
    Wind Speed (MPH):  2.06
    ------------------------------------------
    City 201:  ewo, cg
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-1.33&lon=14.95)
    Temperature (F):  73.76
    Humidity (%):  95
    Cloudiness (%):  24
    Wind Speed (MPH):  6.04
    ------------------------------------------
    City 202:  aracaju, br
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-10.94&lon=-37.05)
    Temperature (F):  77
    Humidity (%):  83
    Cloudiness (%):  20
    Wind Speed (MPH):  4.7
    ------------------------------------------
    City 203:  chiredzi, zw
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-22.24&lon=32.84)
    Temperature (F):  66.11
    Humidity (%):  73
    Cloudiness (%):  0
    Wind Speed (MPH):  3.02
    ------------------------------------------
    City 204:  griffith, au
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-33.67&lon=144.96)
    Temperature (F):  53.42
    Humidity (%):  78
    Cloudiness (%):  68
    Wind Speed (MPH):  8.84
    ------------------------------------------
    City 205:  boa vista, br
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=1.61&lon=-62.33)
    Temperature (F):  72.95
    Humidity (%):  100
    Cloudiness (%):  92
    Wind Speed (MPH):  1.79
    ------------------------------------------
    City 206:  nikolskoye, ru
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=38.46&lon=169.67)
    Temperature (F):  65.84
    Humidity (%):  87
    Cloudiness (%):  92
    Wind Speed (MPH):  29.19
    ------------------------------------------
    City 207:  awjilah, ly
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=25.09&lon=19.42)
    Temperature (F):  77.45
    Humidity (%):  32
    Cloudiness (%):  0
    Wind Speed (MPH):  9.33
    ------------------------------------------
    City 208:  big rapids, us
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=43.52&lon=-85.63)
    Temperature (F):  87.57
    Humidity (%):  25
    Cloudiness (%):  1
    Wind Speed (MPH):  11.41
    ------------------------------------------
    City 209:  dunkwa, gh
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=5.92&lon=-1.7)
    Temperature (F):  76.01
    Humidity (%):  89
    Cloudiness (%):  44
    Wind Speed (MPH):  5.86
    ------------------------------------------
    City 210:  saint anthony, ca
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=55.82&lon=-53.05)
    Temperature (F):  35.15
    Humidity (%):  100
    Cloudiness (%):  36
    Wind Speed (MPH):  15.7
    ------------------------------------------
    City 211:  kapaa, us
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=23.88&lon=-179.12)
    Temperature (F):  75.38
    Humidity (%):  100
    Cloudiness (%):  32
    Wind Speed (MPH):  14.65
    ------------------------------------------
    City 212:  hailey, us
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=43.52&lon=-114.29)
    Temperature (F):  66.2
    Humidity (%):  52
    Cloudiness (%):  40
    Wind Speed (MPH):  5.82
    ------------------------------------------
    City 213:  rikitea, pf
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-25.65&lon=-130.38)
    Temperature (F):  73.67
    Humidity (%):  100
    Cloudiness (%):  68
    Wind Speed (MPH):  14.47
    ------------------------------------------
    City 214:  fuerte olimpo, py
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-21.29&lon=-57.37)
    Temperature (F):  87.26
    Humidity (%):  32
    Cloudiness (%):  0
    Wind Speed (MPH):  8.16
    ------------------------------------------
    City 215:  puerto ayora, ec
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-11.62&lon=-100.17)
    Temperature (F):  73.67
    Humidity (%):  100
    Cloudiness (%):  56
    Wind Speed (MPH):  12.3
    ------------------------------------------
    City 216:  waverly, us
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=42.99&lon=-92.76)
    Temperature (F):  96.98
    Humidity (%):  28
    Cloudiness (%):  1
    Wind Speed (MPH):  9.17
    ------------------------------------------
    City 217:  attawapiskat, ca
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=67.15&lon=-85.97)
    Temperature (F):  29.75
    Humidity (%):  99
    Cloudiness (%):  80
    Wind Speed (MPH):  6.53
    ------------------------------------------
    City 218:  ranghulu, cn
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=47.16&lon=124.95)
    Temperature (F):  45.59
    Humidity (%):  87
    Cloudiness (%):  8
    Wind Speed (MPH):  3.02
    ------------------------------------------
    City 219:  airai, pw
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=19.22&lon=139.05)
    Temperature (F):  85.91
    Humidity (%):  99
    Cloudiness (%):  48
    Wind Speed (MPH):  5.64
    ------------------------------------------
    City 220:  atasu, kz
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=46.41&lon=71.14)
    Temperature (F):  52.34
    Humidity (%):  91
    Cloudiness (%):  36
    Wind Speed (MPH):  12.86
    ------------------------------------------
    City 221:  lebu, cl
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-36.18&lon=-96.71)
    Temperature (F):  56.66
    Humidity (%):  100
    Cloudiness (%):  56
    Wind Speed (MPH):  16.37
    ------------------------------------------
    City 222:  iqaluit, ca
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=57.51&lon=-69.92)
    Temperature (F):  36.86
    Humidity (%):  93
    Cloudiness (%):  44
    Wind Speed (MPH):  12.41
    ------------------------------------------
    City 223:  rikitea, pf
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-27.55&lon=-118.82)
    Temperature (F):  70.79
    Humidity (%):  100
    Cloudiness (%):  24
    Wind Speed (MPH):  12.35
    ------------------------------------------
    City 224:  port blair, in
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=8.34&lon=90.97)
    Temperature (F):  85.73
    Humidity (%):  100
    Cloudiness (%):  48
    Wind Speed (MPH):  18.39
    ------------------------------------------
    City 225:  asosa, et
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=9.73&lon=33.43)
    Temperature (F):  73.76
    Humidity (%):  91
    Cloudiness (%):  76
    Wind Speed (MPH):  2.84
    ------------------------------------------
    City 226:  manacapuru, br
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-3.08&lon=-61.18)
    Temperature (F):  83.21
    Humidity (%):  81
    Cloudiness (%):  68
    Wind Speed (MPH):  3.24
    ------------------------------------------
    City 227:  karauzyak, uz
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=42.74&lon=60.96)
    Temperature (F):  52.7
    Humidity (%):  39
    Cloudiness (%):  0
    Wind Speed (MPH):  7.43
    ------------------------------------------
    City 228:  rikitea, pf
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-28.22&lon=-127.53)
    Temperature (F):  71.15
    Humidity (%):  100
    Cloudiness (%):  64
    Wind Speed (MPH):  19.06
    ------------------------------------------
    City 229:  saint-philippe, re
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-69.79&lon=72.44)
    Temperature (F):  -7.69
    Humidity (%):  60
    Cloudiness (%):  0
    Wind Speed (MPH):  18.72
    ------------------------------------------
    City 230:  artyom, az
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=41.23&lon=51.7)
    Temperature (F):  71.15
    Humidity (%):  97
    Cloudiness (%):  0
    Wind Speed (MPH):  9.17
    ------------------------------------------
    City 231:  beringovskiy, ru
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=54.08&lon=177.09)
    Temperature (F):  41.36
    Humidity (%):  96
    Cloudiness (%):  92
    Wind Speed (MPH):  19.91
    ------------------------------------------
    City 232:  ushuaia, ar
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-69.55&lon=-59.86)
    Temperature (F):  -14.27
    Humidity (%):  85
    Cloudiness (%):  48
    Wind Speed (MPH):  4.25
    ------------------------------------------
    City 233:  luderitz, na
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-28.03&lon=10.21)
    Temperature (F):  64.94
    Humidity (%):  100
    Cloudiness (%):  92
    Wind Speed (MPH):  14.81
    ------------------------------------------
    City 234:  katsuura, jp
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=20.92&lon=147.83)
    Temperature (F):  82.04
    Humidity (%):  100
    Cloudiness (%):  48
    Wind Speed (MPH):  7.05
    ------------------------------------------
    City 235:  east london, za
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-57.76&lon=45.62)
    Temperature (F):  32.36
    Humidity (%):  100
    Cloudiness (%):  44
    Wind Speed (MPH):  38.07
    ------------------------------------------
    City 236:  portel, br
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-2.24&lon=-50.86)
    Temperature (F):  76.55
    Humidity (%):  97
    Cloudiness (%):  76
    Wind Speed (MPH):  4.52
    ------------------------------------------
    City 237:  mataura, pf
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-66.68&lon=-148.24)
    Temperature (F):  26.15
    Humidity (%):  100
    Cloudiness (%):  68
    Wind Speed (MPH):  23.98
    ------------------------------------------
    City 238:  garissa, ke
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=0.26&lon=39.76)
    Temperature (F):  77.36
    Humidity (%):  66
    Cloudiness (%):  0
    Wind Speed (MPH):  13.13
    ------------------------------------------
    City 239:  sao filipe, cv
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=3.02&lon=-26.07)
    Temperature (F):  80.6
    Humidity (%):  100
    Cloudiness (%):  64
    Wind Speed (MPH):  6.87
    ------------------------------------------
    City 240:  jamestown, sh
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-29.94&lon=-9.02)
    Temperature (F):  68.27
    Humidity (%):  100
    Cloudiness (%):  0
    Wind Speed (MPH):  16.82
    ------------------------------------------
    City 241:  hermanus, za
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-61.26&lon=4.74)
    Temperature (F):  32
    Humidity (%):  95
    Cloudiness (%):  92
    Wind Speed (MPH):  24.72
    ------------------------------------------
    City 242:  cape town, za
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-55.95&lon=-5.18)
    Temperature (F):  31.1
    Humidity (%):  100
    Cloudiness (%):  20
    Wind Speed (MPH):  23.2
    ------------------------------------------
    City 243:  saulkrasti, lv
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=57.47&lon=24.27)
    Temperature (F):  57.2
    Humidity (%):  82
    Cloudiness (%):  0
    Wind Speed (MPH):  5.82
    ------------------------------------------
    City 244:  iqaluit, ca
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=59.93&lon=-68.98)
    Temperature (F):  30.2
    Humidity (%):  92
    Cloudiness (%):  75
    Wind Speed (MPH):  18.34
    ------------------------------------------
    City 245:  yellowknife, ca
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=67.39&lon=-111.12)
    Temperature (F):  34.25
    Humidity (%):  100
    Cloudiness (%):  92
    Wind Speed (MPH):  4.29
    ------------------------------------------
    City 246:  lebu, cl
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-36.73&lon=-79.49)
    Temperature (F):  58.01
    Humidity (%):  100
    Cloudiness (%):  92
    Wind Speed (MPH):  26.78
    ------------------------------------------
    City 247:  neiafu, to
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-17.73&lon=-172.41)
    Temperature (F):  79.52
    Humidity (%):  100
    Cloudiness (%):  80
    Wind Speed (MPH):  18.23
    ------------------------------------------
    City 248:  chissamba, ao
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-13.12&lon=18.26)
    Temperature (F):  58.73
    Humidity (%):  69
    Cloudiness (%):  44
    Wind Speed (MPH):  2.51
    ------------------------------------------
    City 249:  moree, au
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-29.48&lon=146.85)
    Temperature (F):  56.39
    Humidity (%):  79
    Cloudiness (%):  88
    Wind Speed (MPH):  7.94
    ------------------------------------------
    City 250:  paamiut, gl
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=62.99&lon=-50.63)
    Temperature (F):  31.28
    Humidity (%):  95
    Cloudiness (%):  20
    Wind Speed (MPH):  6.04
    ------------------------------------------
    City 251:  eyl, so
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=5.55&lon=52.11)
    Temperature (F):  83.93
    Humidity (%):  99
    Cloudiness (%):  56
    Wind Speed (MPH):  25.28
    ------------------------------------------
    City 252:  atuona, pf
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-2.66&lon=-121.89)
    Temperature (F):  79.52
    Humidity (%):  100
    Cloudiness (%):  0
    Wind Speed (MPH):  14.09
    ------------------------------------------
    City 253:  mayo, ca
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=65.03&lon=-135.83)
    Temperature (F):  29.66
    Humidity (%):  90
    Cloudiness (%):  88
    Wind Speed (MPH):  2.91
    ------------------------------------------
    City 254:  macedo de cavaleiros, pt
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=41.46&lon=-6.84)
    Temperature (F):  60.26
    Humidity (%):  69
    Cloudiness (%):  44
    Wind Speed (MPH):  3.58
    ------------------------------------------
    City 255:  paamiut, gl
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=61.87&lon=-51.61)
    Temperature (F):  33.89
    Humidity (%):  100
    Cloudiness (%):  32
    Wind Speed (MPH):  14.65
    ------------------------------------------
    City 256:  avarua, ck
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-23.09&lon=-163.08)
    Temperature (F):  74.93
    Humidity (%):  100
    Cloudiness (%):  92
    Wind Speed (MPH):  6.87
    ------------------------------------------
    City 257:  aviles, es
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=43.56&lon=-5.95)
    Temperature (F):  57.2
    Humidity (%):  93
    Cloudiness (%):  75
    Wind Speed (MPH):  3.36
    ------------------------------------------
    City 258:  souillac, mu
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-51.13&lon=72.82)
    Temperature (F):  36.95
    Humidity (%):  100
    Cloudiness (%):  56
    Wind Speed (MPH):  33.22
    ------------------------------------------
    City 259:  ampanihy, mg
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-26.69&lon=41.56)
    Temperature (F):  74.21
    Humidity (%):  100
    Cloudiness (%):  0
    Wind Speed (MPH):  13.13
    ------------------------------------------
    City 260:  salalah, om
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=18.89&lon=56.85)
    Temperature (F):  84.11
    Humidity (%):  95
    Cloudiness (%):  0
    Wind Speed (MPH):  14.14
    ------------------------------------------
    City 261:  gondanglegi, id
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-11.99&lon=112.07)
    Temperature (F):  82.13
    Humidity (%):  100
    Cloudiness (%):  76
    Wind Speed (MPH):  17.34
    ------------------------------------------
    City 262:  ushuaia, ar
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-65.68&lon=-55.02)
    Temperature (F):  -11.12
    Humidity (%):  73
    Cloudiness (%):  44
    Wind Speed (MPH):  7.16
    ------------------------------------------
    City 263:  tilichiki, ru
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=60.19&lon=165.1)
    Temperature (F):  35.15
    Humidity (%):  98
    Cloudiness (%):  88
    Wind Speed (MPH):  11.68
    ------------------------------------------
    City 264:  mataura, pf
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-48.85&lon=-159.79)
    Temperature (F):  53.87
    Humidity (%):  96
    Cloudiness (%):  48
    Wind Speed (MPH):  29.46
    ------------------------------------------
    City 265:  jalu, ly
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=24.53&lon=20.51)
    Temperature (F):  76.55
    Humidity (%):  32
    Cloudiness (%):  0
    Wind Speed (MPH):  9.44
    ------------------------------------------
    City 266:  tara, ru
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=56.59&lon=74.7)
    Temperature (F):  30.2
    Humidity (%):  68
    Cloudiness (%):  0
    Wind Speed (MPH):  5.41
    ------------------------------------------
    City 267:  ushuaia, ar
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-64.55&lon=-61.81)
    Temperature (F):  -11.03
    Humidity (%):  99
    Cloudiness (%):  80
    Wind Speed (MPH):  1.9
    ------------------------------------------
    City 268:  kathu, th
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=8.24&lon=96.81)
    Temperature (F):  84.92
    Humidity (%):  100
    Cloudiness (%):  88
    Wind Speed (MPH):  5.26
    ------------------------------------------
    City 269:  kapaa, us
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=39.56&lon=-161.98)
    Temperature (F):  52.61
    Humidity (%):  100
    Cloudiness (%):  0
    Wind Speed (MPH):  19.57
    ------------------------------------------
    City 270:  cabo san lucas, mx
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=16.77&lon=-112.55)
    Temperature (F):  79.07
    Humidity (%):  100
    Cloudiness (%):  0
    Wind Speed (MPH):  13.35
    ------------------------------------------
    City 271:  jambi, id
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-1.82&lon=103.79)
    Temperature (F):  73.67
    Humidity (%):  98
    Cloudiness (%):  88
    Wind Speed (MPH):  3.13
    ------------------------------------------
    City 272:  fevralsk, ru
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=54.53&lon=129.64)
    Temperature (F):  50.18
    Humidity (%):  82
    Cloudiness (%):  0
    Wind Speed (MPH):  3.58
    ------------------------------------------
    City 273:  saint-philippe, re
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-59.54&lon=74.16)
    Temperature (F):  26.51
    Humidity (%):  100
    Cloudiness (%):  56
    Wind Speed (MPH):  23.98
    ------------------------------------------
    City 274:  tabiauea, ki
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=0.44&lon=169.88)
    Temperature (F):  83.12
    Humidity (%):  100
    Cloudiness (%):  64
    Wind Speed (MPH):  9.33
    ------------------------------------------
    City 275:  tommot, ru
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=59.42&lon=126.73)
    Temperature (F):  56.21
    Humidity (%):  78
    Cloudiness (%):  0
    Wind Speed (MPH):  4.25
    ------------------------------------------
    City 276:  rikitea, pf
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-62.46&lon=-138.57)
    Temperature (F):  29.84
    Humidity (%):  100
    Cloudiness (%):  0
    Wind Speed (MPH):  4.52
    ------------------------------------------
    City 277:  iracoubo, gf
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=9.85&lon=-51.47)
    Temperature (F):  79.25
    Humidity (%):  100
    Cloudiness (%):  8
    Wind Speed (MPH):  16.44
    ------------------------------------------
    City 278:  puerto ayora, ec
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-10.68&lon=-100.86)
    Temperature (F):  74.48
    Humidity (%):  100
    Cloudiness (%):  8
    Wind Speed (MPH):  13.35
    ------------------------------------------
    City 279:  candia, us
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=43.11&lon=-71.24)
    Temperature (F):  54.39
    Humidity (%):  58
    Cloudiness (%):  90
    Wind Speed (MPH):  9.17
    ------------------------------------------
    City 280:  cockburn town, tc
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=21.46&lon=-71.12)
    Temperature (F):  80.78
    Humidity (%):  93
    Cloudiness (%):  64
    Wind Speed (MPH):  18.05
    ------------------------------------------
    City 281:  mahebourg, mu
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-46.06&lon=78.87)
    Temperature (F):  45.41
    Humidity (%):  100
    Cloudiness (%):  92
    Wind Speed (MPH):  35.34
    ------------------------------------------
    City 282:  hilo, us
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=15.7&lon=-150.31)
    Temperature (F):  78.53
    Humidity (%):  100
    Cloudiness (%):  12
    Wind Speed (MPH):  19.06
    ------------------------------------------
    City 283:  bredasdorp, za
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-52.57&lon=22.7)
    Temperature (F):  37.76
    Humidity (%):  88
    Cloudiness (%):  92
    Wind Speed (MPH):  23.49
    ------------------------------------------
    City 284:  vaini, to
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-25.16&lon=-179.57)
    Temperature (F):  66.65
    Humidity (%):  100
    Cloudiness (%):  92
    Wind Speed (MPH):  6.93
    ------------------------------------------
    City 285:  nikolskoye, ru
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=34.04&lon=175.01)
    Temperature (F):  69.53
    Humidity (%):  98
    Cloudiness (%):  36
    Wind Speed (MPH):  7.43
    ------------------------------------------
    City 286:  atuona, pf
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-10.05&lon=-143.64)
    Temperature (F):  81.5
    Humidity (%):  100
    Cloudiness (%):  56
    Wind Speed (MPH):  13.91
    ------------------------------------------
    City 287:  pevek, ru
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=67.71&lon=169.54)
    Temperature (F):  35.96
    Humidity (%):  91
    Cloudiness (%):  12
    Wind Speed (MPH):  5.64
    ------------------------------------------
    City 288:  urdzhar, kz
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=47.93&lon=81.43)
    Temperature (F):  57.2
    Humidity (%):  32
    Cloudiness (%):  64
    Wind Speed (MPH):  5.26
    ------------------------------------------
    City 289:  nyzy, ua
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=50.71&lon=34.63)
    Temperature (F):  51.89
    Humidity (%):  74
    Cloudiness (%):  0
    Wind Speed (MPH):  3.13
    ------------------------------------------
    City 290:  tabas, ir
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=32.96&lon=56.99)
    Temperature (F):  80.6
    Humidity (%):  22
    Cloudiness (%):  12
    Wind Speed (MPH):  9.89
    ------------------------------------------
    City 291:  vaini, to
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-61.8&lon=-168.82)
    Temperature (F):  31.46
    Humidity (%):  100
    Cloudiness (%):  80
    Wind Speed (MPH):  27.67
    ------------------------------------------
    City 292:  maumere, id
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-7.13&lon=123.0)
    Temperature (F):  82.58
    Humidity (%):  100
    Cloudiness (%):  92
    Wind Speed (MPH):  13.98
    ------------------------------------------
    City 293:  bluff, nz
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-60.93&lon=160.56)
    Temperature (F):  35.06
    Humidity (%):  90
    Cloudiness (%):  92
    Wind Speed (MPH):  24.38
    ------------------------------------------
    City 294:  attawapiskat, ca
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=59.41&lon=-84.89)
    Temperature (F):  33.44
    Humidity (%):  95
    Cloudiness (%):  64
    Wind Speed (MPH):  20.58
    ------------------------------------------
    City 295:  kapaa, us
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=37.72&lon=-176.11)
    Temperature (F):  59.72
    Humidity (%):  98
    Cloudiness (%):  24
    Wind Speed (MPH):  16.49
    ------------------------------------------
    City 296:  arraial do cabo, br
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-41.77&lon=-23.95)
    Temperature (F):  56.75
    Humidity (%):  94
    Cloudiness (%):  92
    Wind Speed (MPH):  24.16
    ------------------------------------------
    City 297:  camana, pe
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-20.87&lon=-75.45)
    Temperature (F):  64.49
    Humidity (%):  100
    Cloudiness (%):  0
    Wind Speed (MPH):  11.12
    ------------------------------------------
    City 298:  sao joao da barra, br
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-26.96&lon=-33.71)
    Temperature (F):  67.01
    Humidity (%):  100
    Cloudiness (%):  76
    Wind Speed (MPH):  12.08
    ------------------------------------------
    City 299:  vidin, bg
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=43.93&lon=22.84)
    Temperature (F):  65.66
    Humidity (%):  77
    Cloudiness (%):  0
    Wind Speed (MPH):  5.64
    ------------------------------------------
    City 300:  butaritari, ki
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=11.41&lon=168.19)
    Temperature (F):  82.67
    Humidity (%):  98
    Cloudiness (%):  44
    Wind Speed (MPH):  25.61
    ------------------------------------------
    City 301:  madimba, tz
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-9.85&lon=41.78)
    Temperature (F):  82.4
    Humidity (%):  96
    Cloudiness (%):  12
    Wind Speed (MPH):  20.62
    ------------------------------------------
    City 302:  mataura, pf
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-65.81&lon=-160.21)
    Temperature (F):  29.3
    Humidity (%):  96
    Cloudiness (%):  88
    Wind Speed (MPH):  14.99
    ------------------------------------------
    City 303:  bengkulu, id
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-8.66&lon=100.63)
    Temperature (F):  80.42
    Humidity (%):  100
    Cloudiness (%):  92
    Wind Speed (MPH):  12.86
    ------------------------------------------
    City 304:  carutapera, br
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=3.01&lon=-43.5)
    Temperature (F):  80.33
    Humidity (%):  100
    Cloudiness (%):  92
    Wind Speed (MPH):  13.09
    ------------------------------------------
    City 305:  el dorado, co
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=3.11&lon=-72.9)
    Temperature (F):  83.21
    Humidity (%):  72
    Cloudiness (%):  48
    Wind Speed (MPH):  3.36
    ------------------------------------------
    City 306:  klaksvik, fo
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=68.38&lon=-7.7)
    Temperature (F):  40.82
    Humidity (%):  93
    Cloudiness (%):  76
    Wind Speed (MPH):  17.94
    ------------------------------------------
    City 307:  okhotsk, ru
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=59.28&lon=142.82)
    Temperature (F):  38.48
    Humidity (%):  91
    Cloudiness (%):  64
    Wind Speed (MPH):  3.91
    ------------------------------------------
    City 308:  banda aceh, id
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=4.59&lon=91.9)
    Temperature (F):  85.37
    Humidity (%):  100
    Cloudiness (%):  76
    Wind Speed (MPH):  8.16
    ------------------------------------------
    City 309:  isangel, vu
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-22.17&lon=172.71)
    Temperature (F):  70.16
    Humidity (%):  100
    Cloudiness (%):  100
    Wind Speed (MPH):  19.62
    ------------------------------------------
    City 310:  maniitsoq, gl
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=63.14&lon=-57.24)
    Temperature (F):  29.66
    Humidity (%):  100
    Cloudiness (%):  32
    Wind Speed (MPH):  0.89
    ------------------------------------------
    City 311:  umm kaddadah, sd
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=15.43&lon=27.85)
    Temperature (F):  85.55
    Humidity (%):  18
    Cloudiness (%):  32
    Wind Speed (MPH):  3.58
    ------------------------------------------
    City 312:  vostok, ru
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=53.84&lon=150.24)
    Temperature (F):  36.41
    Humidity (%):  100
    Cloudiness (%):  88
    Wind Speed (MPH):  2.28
    ------------------------------------------
    City 313:  rikitea, pf
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-29.18&lon=-110.82)
    Temperature (F):  70.34
    Humidity (%):  100
    Cloudiness (%):  92
    Wind Speed (MPH):  17.45
    ------------------------------------------
    City 314:  mahebourg, mu
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-44.66&lon=80.56)
    Temperature (F):  48.2
    Humidity (%):  100
    Cloudiness (%):  24
    Wind Speed (MPH):  27.63
    ------------------------------------------
    City 315:  bethel, us
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=63.31&lon=-159.81)
    Temperature (F):  40.55
    Humidity (%):  86
    Cloudiness (%):  92
    Wind Speed (MPH):  6.76
    ------------------------------------------
    City 316:  petropavlovsk-kamchatskiy, ru
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=51.88&lon=161.02)
    Temperature (F):  39.2
    Humidity (%):  97
    Cloudiness (%):  92
    Wind Speed (MPH):  17.6
    ------------------------------------------
    City 317:  evanston, us
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=40.6&lon=-110.18)
    Temperature (F):  80.6
    Humidity (%):  7
    Cloudiness (%):  1
    Wind Speed (MPH):  13.87
    ------------------------------------------
    City 318:  coihaique, cl
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-48.21&lon=-73.29)
    Temperature (F):  36.59
    Humidity (%):  100
    Cloudiness (%):  88
    Wind Speed (MPH):  1.83
    ------------------------------------------
    City 319:  srednekolymsk, ru
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=67.52&lon=155.35)
    Temperature (F):  34.88
    Humidity (%):  88
    Cloudiness (%):  68
    Wind Speed (MPH):  3.06
    ------------------------------------------
    City 320:  bambous virieux, mu
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-41.48&lon=82.13)
    Temperature (F):  53.78
    Humidity (%):  100
    Cloudiness (%):  76
    Wind Speed (MPH):  21.07
    ------------------------------------------
    City 321:  boguchany, ru
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=59.69&lon=97.57)
    Temperature (F):  34.7
    Humidity (%):  100
    Cloudiness (%):  92
    Wind Speed (MPH):  4.81
    ------------------------------------------
    City 322:  zaqatala, az
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=41.25&lon=46.5)
    Temperature (F):  58.87
    Humidity (%):  77
    Cloudiness (%):  90
    Wind Speed (MPH):  9.17
    ------------------------------------------
    City 323:  severo-kurilsk, ru
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=37.91&lon=162.65)
    Temperature (F):  58.73
    Humidity (%):  100
    Cloudiness (%):  92
    Wind Speed (MPH):  15.88
    ------------------------------------------
    City 324:  sentyabrskiy, ru
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=43.02&lon=153.15)
    Temperature (F):  41.09
    Humidity (%):  96
    Cloudiness (%):  88
    Wind Speed (MPH):  8.05
    ------------------------------------------
    City 325:  shu, kz
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=44.3&lon=73.89)
    Temperature (F):  69.71
    Humidity (%):  39
    Cloudiness (%):  92
    Wind Speed (MPH):  21.25
    ------------------------------------------
    City 326:  georgetown, sh
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-15.47&lon=-17.35)
    Temperature (F):  73.49
    Humidity (%):  100
    Cloudiness (%):  48
    Wind Speed (MPH):  15.59
    ------------------------------------------
    City 327:  gat, ly
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=22.24&lon=11.25)
    Temperature (F):  89.24
    Humidity (%):  11
    Cloudiness (%):  8
    Wind Speed (MPH):  12.41
    ------------------------------------------
    City 328:  aklavik, ca
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=66.37&lon=-136.05)
    Temperature (F):  29.48
    Humidity (%):  80
    Cloudiness (%):  76
    Wind Speed (MPH):  9.44
    ------------------------------------------
    City 329:  skowhegan, us
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=45.6&lon=-69.63)
    Temperature (F):  59
    Humidity (%):  47
    Cloudiness (%):  0
    Wind Speed (MPH):  6.93
    ------------------------------------------
    City 330:  atuona, pf
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-7.25&lon=-143.89)
    Temperature (F):  80.15
    Humidity (%):  100
    Cloudiness (%):  48
    Wind Speed (MPH):  14.2
    ------------------------------------------
    City 331:  tianpeng, cn
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=33.07&lon=102.35)
    Temperature (F):  30.65
    Humidity (%):  98
    Cloudiness (%):  76
    Wind Speed (MPH):  2.91
    ------------------------------------------
    City 332:  hithadhoo, mv
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-0.63&lon=71.25)
    Temperature (F):  84.65
    Humidity (%):  100
    Cloudiness (%):  12
    Wind Speed (MPH):  17.38
    ------------------------------------------
    City 333:  kailua, us
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=22.59&lon=-157.24)
    Temperature (F):  74.75
    Humidity (%):  100
    Cloudiness (%):  68
    Wind Speed (MPH):  16.22
    ------------------------------------------
    City 334:  adrar, dz
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=25.68&lon=4.41)
    Temperature (F):  86.27
    Humidity (%):  18
    Cloudiness (%):  92
    Wind Speed (MPH):  3.13
    ------------------------------------------
    City 335:  hithadhoo, mv
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-2.46&lon=75.08)
    Temperature (F):  81.14
    Humidity (%):  100
    Cloudiness (%):  36
    Wind Speed (MPH):  9.62
    ------------------------------------------
    City 336:  bambous virieux, mu
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-33.56&lon=82.59)
    Temperature (F):  63.41
    Humidity (%):  100
    Cloudiness (%):  88
    Wind Speed (MPH):  17.72
    ------------------------------------------
    City 337:  kapaa, us
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=20.15&lon=-172.7)
    Temperature (F):  77.27
    Humidity (%):  100
    Cloudiness (%):  56
    Wind Speed (MPH):  22.93
    ------------------------------------------
    City 338:  new norfolk, au
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-55.1&lon=140.93)
    Temperature (F):  41.99
    Humidity (%):  85
    Cloudiness (%):  92
    Wind Speed (MPH):  33.33
    ------------------------------------------
    City 339:  east london, za
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-42.21&lon=34.3)
    Temperature (F):  53.24
    Humidity (%):  100
    Cloudiness (%):  0
    Wind Speed (MPH):  10.67
    ------------------------------------------
    City 340:  cap malheureux, mu
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-13.5&lon=60.69)
    Temperature (F):  80.6
    Humidity (%):  98
    Cloudiness (%):  36
    Wind Speed (MPH):  20.07
    ------------------------------------------
    City 341:  trelew, ar
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-43.13&lon=-65.94)
    Temperature (F):  57.83
    Humidity (%):  37
    Cloudiness (%):  80
    Wind Speed (MPH):  14.58
    ------------------------------------------
    City 342:  busselton, au
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-34.51&lon=111.01)
    Temperature (F):  60.35
    Humidity (%):  100
    Cloudiness (%):  0
    Wind Speed (MPH):  18.72
    ------------------------------------------
    City 343:  seydisehir, tr
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=37.4&lon=31.6)
    Temperature (F):  48.11
    Humidity (%):  89
    Cloudiness (%):  8
    Wind Speed (MPH):  1.23
    ------------------------------------------
    City 344:  punta arenas, cl
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-56.76&lon=-102.9)
    Temperature (F):  41.45
    Humidity (%):  95
    Cloudiness (%):  20
    Wind Speed (MPH):  33.71
    ------------------------------------------
    City 345:  mokhsogollokh, ru
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=61.87&lon=128.21)
    Temperature (F):  61.88
    Humidity (%):  54
    Cloudiness (%):  0
    Wind Speed (MPH):  6.64
    ------------------------------------------
    City 346:  muzhi, ru
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=65.95&lon=64.16)
    Temperature (F):  33.62
    Humidity (%):  86
    Cloudiness (%):  48
    Wind Speed (MPH):  14.88
    ------------------------------------------
    City 347:  dubrovytsya, ua
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=51.57&lon=26.75)
    Temperature (F):  61.07
    Humidity (%):  49
    Cloudiness (%):  44
    Wind Speed (MPH):  9.17
    ------------------------------------------
    City 348:  dobric, bg
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=43.65&lon=27.62)
    Temperature (F):  60.8
    Humidity (%):  63
    Cloudiness (%):  0
    Wind Speed (MPH):  4.7
    ------------------------------------------
    City 349:  nioro, ml
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=19.24&lon=-8.8)
    Temperature (F):  93.11
    Humidity (%):  19
    Cloudiness (%):  0
    Wind Speed (MPH):  12.57
    ------------------------------------------
    City 350:  sao filipe, cv
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=14.53&lon=-28.68)
    Temperature (F):  73.49
    Humidity (%):  100
    Cloudiness (%):  76
    Wind Speed (MPH):  16.22
    ------------------------------------------
    City 351:  cape town, za
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-63.67&lon=-14.91)
    Temperature (F):  21.29
    Humidity (%):  100
    Cloudiness (%):  24
    Wind Speed (MPH):  17.72
    ------------------------------------------
    City 352:  toliary, mg
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-24.0&lon=42.43)
    Temperature (F):  76.19
    Humidity (%):  100
    Cloudiness (%):  0
    Wind Speed (MPH):  15.66
    ------------------------------------------
    City 353:  ushuaia, ar
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-66.16&lon=-69.01)
    Temperature (F):  24.08
    Humidity (%):  100
    Cloudiness (%):  20
    Wind Speed (MPH):  13.98
    ------------------------------------------
    City 354:  cape town, za
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-52.42&lon=-4.41)
    Temperature (F):  34.25
    Humidity (%):  98
    Cloudiness (%):  32
    Wind Speed (MPH):  23.87
    ------------------------------------------
    City 355:  cabo san lucas, mx
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=16.83&lon=-119.36)
    Temperature (F):  76.73
    Humidity (%):  100
    Cloudiness (%):  0
    Wind Speed (MPH):  17.72
    ------------------------------------------
    City 356:  petrivka, ua
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=46.86&lon=31.1)
    Temperature (F):  62.6
    Humidity (%):  59
    Cloudiness (%):  0
    Wind Speed (MPH):  2.24
    ------------------------------------------
    City 357:  yamada, jp
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=39.47&lon=143.57)
    Temperature (F):  59.81
    Humidity (%):  100
    Cloudiness (%):  64
    Wind Speed (MPH):  10.11
    ------------------------------------------
    City 358:  umzimvubu, za
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-53.23&lon=46.74)
    Temperature (F):  34.34
    Humidity (%):  100
    Cloudiness (%):  48
    Wind Speed (MPH):  27.18
    ------------------------------------------
    City 359:  viedma, ar
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-43.13&lon=-60.66)
    Temperature (F):  55.13
    Humidity (%):  94
    Cloudiness (%):  76
    Wind Speed (MPH):  16.49
    ------------------------------------------
    City 360:  georgetown, sh
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-11.88&lon=-21.69)
    Temperature (F):  77.09
    Humidity (%):  100
    Cloudiness (%):  92
    Wind Speed (MPH):  19.46
    ------------------------------------------
    City 361:  georgetown, sh
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-12.59&lon=-20.04)
    Temperature (F):  77
    Humidity (%):  100
    Cloudiness (%):  24
    Wind Speed (MPH):  17.38
    ------------------------------------------
    City 362:  viligili, mv
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=0.03&lon=77.17)
    Temperature (F):  84.74
    Humidity (%):  100
    Cloudiness (%):  68
    Wind Speed (MPH):  18.57
    ------------------------------------------
    City 363:  barawe, so
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-4.03&lon=48.35)
    Temperature (F):  77.36
    Humidity (%):  100
    Cloudiness (%):  80
    Wind Speed (MPH):  11.45
    ------------------------------------------
    City 364:  tsihombe, mg
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-46.01&lon=49.77)
    Temperature (F):  40.46
    Humidity (%):  100
    Cloudiness (%):  48
    Wind Speed (MPH):  21.74
    ------------------------------------------
    City 365:  nikolskoye, ru
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=41.83&lon=172.89)
    Temperature (F):  55.22
    Humidity (%):  82
    Cloudiness (%):  92
    Wind Speed (MPH):  27.89
    ------------------------------------------
    City 366:  padang, id
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-5.49&lon=91.51)
    Temperature (F):  83.66
    Humidity (%):  100
    Cloudiness (%):  76
    Wind Speed (MPH):  15.1
    ------------------------------------------
    City 367:  thompson, ca
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=54.7&lon=-96.39)
    Temperature (F):  66.2
    Humidity (%):  72
    Cloudiness (%):  36
    Wind Speed (MPH):  7.54
    ------------------------------------------
    City 368:  lebu, cl
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-35.9&lon=-99.8)
    Temperature (F):  56.03
    Humidity (%):  100
    Cloudiness (%):  64
    Wind Speed (MPH):  15.88
    ------------------------------------------
    City 369:  tilichiki, ru
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=58.19&lon=171.92)
    Temperature (F):  39.56
    Humidity (%):  94
    Cloudiness (%):  92
    Wind Speed (MPH):  21.92
    ------------------------------------------
    City 370:  lasa, cn
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=33.39&lon=87.8)
    Temperature (F):  5.99
    Humidity (%):  75
    Cloudiness (%):  32
    Wind Speed (MPH):  2.39
    ------------------------------------------
    City 371:  umm lajj, sa
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=24.92&lon=38.62)
    Temperature (F):  86.72
    Humidity (%):  19
    Cloudiness (%):  48
    Wind Speed (MPH):  7.83
    ------------------------------------------
    City 372:  sentyabrskiy, ru
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=33.83&lon=156.51)
    Temperature (F):  65.3
    Humidity (%):  100
    Cloudiness (%):  92
    Wind Speed (MPH):  17.83
    ------------------------------------------
    City 373:  albany, au
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-61.98&lon=116.43)
    Temperature (F):  30.83
    Humidity (%):  96
    Cloudiness (%):  32
    Wind Speed (MPH):  0.27
    ------------------------------------------
    City 374:  geraldton, au
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-34.72&lon=86.86)
    Temperature (F):  62.06
    Humidity (%):  98
    Cloudiness (%):  0
    Wind Speed (MPH):  18.45
    ------------------------------------------
    City 375:  grande prairie, ca
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=54.76&lon=-118.51)
    Temperature (F):  66.2
    Humidity (%):  37
    Cloudiness (%):  75
    Wind Speed (MPH):  16.11
    ------------------------------------------
    City 376:  castro, cl
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-44.91&lon=-81.44)
    Temperature (F):  48.47
    Humidity (%):  100
    Cloudiness (%):  88
    Wind Speed (MPH):  23.49
    ------------------------------------------
    City 377:  rikitea, pf
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-23.41&lon=-108.99)
    Temperature (F):  74.48
    Humidity (%):  99
    Cloudiness (%):  36
    Wind Speed (MPH):  14.2
    ------------------------------------------
    City 378:  cabo san lucas, mx
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=10.6&lon=-126.51)
    Temperature (F):  81.68
    Humidity (%):  100
    Cloudiness (%):  64
    Wind Speed (MPH):  11.45
    ------------------------------------------
    City 379:  ribeira grande, pt
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=39.89&lon=-30.35)
    Temperature (F):  66.92
    Humidity (%):  100
    Cloudiness (%):  0
    Wind Speed (MPH):  10.67
    ------------------------------------------
    City 380:  busselton, au
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-65.96&lon=98.7)
    Temperature (F):  15.62
    Humidity (%):  78
    Cloudiness (%):  80
    Wind Speed (MPH):  9.44
    ------------------------------------------
    City 381:  mataura, pf
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-47.98&lon=-158.46)
    Temperature (F):  53.87
    Humidity (%):  97
    Cloudiness (%):  76
    Wind Speed (MPH):  25.99
    ------------------------------------------
    City 382:  filadelfia, py
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-21.17&lon=-59.59)
    Temperature (F):  89.78
    Humidity (%):  34
    Cloudiness (%):  0
    Wind Speed (MPH):  15.37
    ------------------------------------------
    City 383:  maracas, br
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-13.91&lon=-40.58)
    Temperature (F):  69.8
    Humidity (%):  92
    Cloudiness (%):  68
    Wind Speed (MPH):  7.16
    ------------------------------------------
    City 384:  gimli, ca
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=50.73&lon=-96.86)
    Temperature (F):  58.1
    Humidity (%):  62
    Cloudiness (%):  0
    Wind Speed (MPH):  10.11
    ------------------------------------------
    City 385:  albany, au
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-47.22&lon=116.34)
    Temperature (F):  44.06
    Humidity (%):  100
    Cloudiness (%):  44
    Wind Speed (MPH):  25.99
    ------------------------------------------
    City 386:  avarua, ck
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-26.63&lon=-163.42)
    Temperature (F):  71.15
    Humidity (%):  100
    Cloudiness (%):  92
    Wind Speed (MPH):  1.9
    ------------------------------------------
    City 387:  adrar, dz
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=24.22&lon=-0.62)
    Temperature (F):  93.65
    Humidity (%):  12
    Cloudiness (%):  0
    Wind Speed (MPH):  11.45
    ------------------------------------------
    City 388:  tarudant, ma
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=28.75&lon=-7.97)
    Temperature (F):  74.03
    Humidity (%):  19
    Cloudiness (%):  0
    Wind Speed (MPH):  13.58
    ------------------------------------------
    City 389:  daru, pg
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-9.47&lon=142.68)
    Temperature (F):  80.15
    Humidity (%):  98
    Cloudiness (%):  20
    Wind Speed (MPH):  22.7
    ------------------------------------------
    City 390:  ushuaia, ar
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-67.36&lon=-62.0)
    Temperature (F):  -4.82
    Humidity (%):  67
    Cloudiness (%):  76
    Wind Speed (MPH):  1.9
    ------------------------------------------
    City 391:  isangel, vu
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-19.77&lon=169.66)
    Temperature (F):  72.23
    Humidity (%):  100
    Cloudiness (%):  92
    Wind Speed (MPH):  21.47
    ------------------------------------------
    City 392:  sentyabrskiy, ru
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=32.03&lon=161.46)
    Temperature (F):  74.93
    Humidity (%):  93
    Cloudiness (%):  8
    Wind Speed (MPH):  23.98
    ------------------------------------------
    City 393:  nome, us
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=59.55&lon=-168.0)
    Temperature (F):  37.22
    Humidity (%):  100
    Cloudiness (%):  88
    Wind Speed (MPH):  6.31
    ------------------------------------------
    City 394:  bethel, us
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=64.12&lon=-158.66)
    Temperature (F):  55.4
    Humidity (%):  62
    Cloudiness (%):  75
    Wind Speed (MPH):  3.36
    ------------------------------------------
    City 395:  saldanha, za
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-34.69&lon=7.64)
    Temperature (F):  58.73
    Humidity (%):  100
    Cloudiness (%):  80
    Wind Speed (MPH):  32.66
    ------------------------------------------
    City 396:  ribeira grande, pt
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=44.22&lon=-37.4)
    Temperature (F):  61.16
    Humidity (%):  89
    Cloudiness (%):  92
    Wind Speed (MPH):  31.54
    ------------------------------------------
    City 397:  teya, ru
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=59.33&lon=92.76)
    Temperature (F):  31.55
    Humidity (%):  95
    Cloudiness (%):  88
    Wind Speed (MPH):  2.62
    ------------------------------------------
    City 398:  butaritari, ki
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=10.19&lon=179.16)
    Temperature (F):  82.4
    Humidity (%):  100
    Cloudiness (%):  64
    Wind Speed (MPH):  24.61
    ------------------------------------------
    City 399:  misratah, ly
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=32.98&lon=16.58)
    Temperature (F):  71.06
    Humidity (%):  100
    Cloudiness (%):  0
    Wind Speed (MPH):  6.26
    ------------------------------------------
    City 400:  freeport, bs
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=26.54&lon=-78.65)
    Temperature (F):  77
    Humidity (%):  88
    Cloudiness (%):  75
    Wind Speed (MPH):  3.36
    ------------------------------------------
    City 401:  bambous virieux, mu
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-28.83&lon=72.42)
    Temperature (F):  67.73
    Humidity (%):  100
    Cloudiness (%):  0
    Wind Speed (MPH):  6.42
    ------------------------------------------
    City 402:  bella vista, py
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-22.71&lon=-56.55)
    Temperature (F):  81.5
    Humidity (%):  40
    Cloudiness (%):  0
    Wind Speed (MPH):  6.26
    ------------------------------------------
    City 403:  hermanus, za
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-69.93&lon=-0.53)
    Temperature (F):  11.93
    Humidity (%):  85
    Cloudiness (%):  80
    Wind Speed (MPH):  50.15
    ------------------------------------------
    City 404:  puerto ayora, ec
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=3.16&lon=-91.7)
    Temperature (F):  78.8
    Humidity (%):  100
    Cloudiness (%):  0
    Wind Speed (MPH):  7.61
    ------------------------------------------
    City 405:  mata, cn
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=20.23&lon=111.82)
    Temperature (F):  84.47
    Humidity (%):  98
    Cloudiness (%):  0
    Wind Speed (MPH):  10.45
    ------------------------------------------
    City 406:  barcanesti, ro
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=44.58&lon=26.64)
    Temperature (F):  64.4
    Humidity (%):  59
    Cloudiness (%):  0
    Wind Speed (MPH):  3.36
    ------------------------------------------
    City 407:  vaini, to
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-39.45&lon=-178.14)
    Temperature (F):  56.93
    Humidity (%):  100
    Cloudiness (%):  56
    Wind Speed (MPH):  21.74
    ------------------------------------------
    City 408:  poum, nc
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-17.33&lon=161.47)
    Temperature (F):  76.91
    Humidity (%):  100
    Cloudiness (%):  32
    Wind Speed (MPH):  16.33
    ------------------------------------------
    City 409:  harrisburg, us
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=37.97&lon=-88.32)
    Temperature (F):  82.24
    Humidity (%):  55
    Cloudiness (%):  1
    Wind Speed (MPH):  5.82
    ------------------------------------------
    City 410:  lebu, cl
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-30.19&lon=-85.45)
    Temperature (F):  64.85
    Humidity (%):  100
    Cloudiness (%):  64
    Wind Speed (MPH):  13.13
    ------------------------------------------
    City 411:  hasaki, jp
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=27.39&lon=155.97)
    Temperature (F):  77
    Humidity (%):  100
    Cloudiness (%):  88
    Wind Speed (MPH):  18.34
    ------------------------------------------
    City 412:  taolanaro, mg
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-64.02&lon=63.79)
    Temperature (F):  4.28
    Humidity (%):  87
    Cloudiness (%):  20
    Wind Speed (MPH):  8.16
    ------------------------------------------
    City 413:  gainesville, us
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=34.49&lon=-83.85)
    Temperature (F):  79.86
    Humidity (%):  69
    Cloudiness (%):  40
    Wind Speed (MPH):  6.93
    ------------------------------------------
    City 414:  mataura, pf
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-66.44&lon=-162.41)
    Temperature (F):  26.15
    Humidity (%):  100
    Cloudiness (%):  68
    Wind Speed (MPH):  21.36
    ------------------------------------------
    City 415:  cape town, za
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-50.76&lon=5.94)
    Temperature (F):  38.66
    Humidity (%):  98
    Cloudiness (%):  92
    Wind Speed (MPH):  26.78
    ------------------------------------------
    City 416:  cape town, za
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-47.09&lon=10.87)
    Temperature (F):  46.76
    Humidity (%):  93
    Cloudiness (%):  88
    Wind Speed (MPH):  18.05
    ------------------------------------------
    City 417:  hermanus, za
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-60.22&lon=14.08)
    Temperature (F):  32.36
    Humidity (%):  94
    Cloudiness (%):  92
    Wind Speed (MPH):  38.07
    ------------------------------------------
    City 418:  ceres, za
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-32.82&lon=19.38)
    Temperature (F):  42.62
    Humidity (%):  82
    Cloudiness (%):  64
    Wind Speed (MPH):  3.02
    ------------------------------------------
    City 419:  kabompo, zm
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-13.33&lon=24.38)
    Temperature (F):  64.76
    Humidity (%):  73
    Cloudiness (%):  0
    Wind Speed (MPH):  3.02
    ------------------------------------------
    City 420:  rikitea, pf
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-53.66&lon=-134.79)
    Temperature (F):  44.06
    Humidity (%):  94
    Cloudiness (%):  48
    Wind Speed (MPH):  6.6
    ------------------------------------------
    City 421:  taolanaro, mg
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-59.67&lon=56.14)
    Temperature (F):  29.48
    Humidity (%):  100
    Cloudiness (%):  64
    Wind Speed (MPH):  20.18
    ------------------------------------------
    City 422:  marrakesh, ma
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=29.31&lon=-5.4)
    Temperature (F):  76.64
    Humidity (%):  13
    Cloudiness (%):  0
    Wind Speed (MPH):  11.07
    ------------------------------------------
    City 423:  malavalli, in
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=12.43&lon=77.24)
    Temperature (F):  67.73
    Humidity (%):  97
    Cloudiness (%):  24
    Wind Speed (MPH):  2.28
    ------------------------------------------
    City 424:  busselton, au
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-44.85&lon=89.85)
    Temperature (F):  49.55
    Humidity (%):  100
    Cloudiness (%):  12
    Wind Speed (MPH):  31.99
    ------------------------------------------
    City 425:  hilo, us
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=18.16&lon=-136.36)
    Temperature (F):  74.48
    Humidity (%):  100
    Cloudiness (%):  68
    Wind Speed (MPH):  19.06
    ------------------------------------------
    City 426:  beringovskiy, ru
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=56.81&lon=179.1)
    Temperature (F):  40.37
    Humidity (%):  98
    Cloudiness (%):  80
    Wind Speed (MPH):  20.69
    ------------------------------------------
    City 427:  barra do corda, br
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-6.08&lon=-45.34)
    Temperature (F):  86.99
    Humidity (%):  43
    Cloudiness (%):  36
    Wind Speed (MPH):  5.41
    ------------------------------------------
    City 428:  umm kaddadah, sd
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=15.4&lon=27.69)
    Temperature (F):  85.55
    Humidity (%):  18
    Cloudiness (%):  32
    Wind Speed (MPH):  3.58
    ------------------------------------------
    City 429:  caceres, es
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=39.62&lon=-6.65)
    Temperature (F):  64.4
    Humidity (%):  73
    Cloudiness (%):  32
    Wind Speed (MPH):  6.76
    ------------------------------------------
    City 430:  sao filipe, cv
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=10.3&lon=-29.61)
    Temperature (F):  75.65
    Humidity (%):  100
    Cloudiness (%):  0
    Wind Speed (MPH):  14.99
    ------------------------------------------
    City 431:  mata, cn
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=20.95&lon=111.82)
    Temperature (F):  85.01
    Humidity (%):  97
    Cloudiness (%):  0
    Wind Speed (MPH):  9.84
    ------------------------------------------
    City 432:  ushuaia, ar
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-59.1&lon=-68.79)
    Temperature (F):  36.86
    Humidity (%):  100
    Cloudiness (%):  80
    Wind Speed (MPH):  20.24
    ------------------------------------------
    City 433:  victoria, sc
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-7.12&lon=51.92)
    Temperature (F):  78.98
    Humidity (%):  100
    Cloudiness (%):  32
    Wind Speed (MPH):  7.05
    ------------------------------------------
    City 434:  airai, pw
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=15.7&lon=147.67)
    Temperature (F):  80.6
    Humidity (%):  100
    Cloudiness (%):  24
    Wind Speed (MPH):  11.18
    ------------------------------------------
    City 435:  rikitea, pf
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-49.23&lon=-129.68)
    Temperature (F):  48.02
    Humidity (%):  98
    Cloudiness (%):  20
    Wind Speed (MPH):  14.99
    ------------------------------------------
    City 436:  kuche, cn
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=38.88&lon=83.92)
    Temperature (F):  48.11
    Humidity (%):  28
    Cloudiness (%):  0
    Wind Speed (MPH):  4.36
    ------------------------------------------
    City 437:  san cristobal, ec
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-9.08&lon=-91.42)
    Temperature (F):  74.57
    Humidity (%):  100
    Cloudiness (%):  92
    Wind Speed (MPH):  14.99
    ------------------------------------------
    City 438:  high rock, bs
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=27.24&lon=-78.35)
    Temperature (F):  78.8
    Humidity (%):  98
    Cloudiness (%):  76
    Wind Speed (MPH):  29.64
    ------------------------------------------
    City 439:  okhotsk, ru
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=58.75&lon=145.76)
    Temperature (F):  33.35
    Humidity (%):  93
    Cloudiness (%):  92
    Wind Speed (MPH):  5.08
    ------------------------------------------
    City 440:  mataura, pf
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-59.02&lon=-145.59)
    Temperature (F):  36.77
    Humidity (%):  88
    Cloudiness (%):  92
    Wind Speed (MPH):  19.84
    ------------------------------------------
    City 441:  jamestown, sh
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-51.67&lon=-17.15)
    Temperature (F):  41.18
    Humidity (%):  89
    Cloudiness (%):  92
    Wind Speed (MPH):  28.3
    ------------------------------------------
    City 442:  east london, za
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-56.84&lon=44.56)
    Temperature (F):  32.63
    Humidity (%):  100
    Cloudiness (%):  64
    Wind Speed (MPH):  38.97
    ------------------------------------------
    City 443:  praia da vitoria, pt
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=46.15&lon=-23.75)
    Temperature (F):  58.28
    Humidity (%):  94
    Cloudiness (%):  92
    Wind Speed (MPH):  10.96
    ------------------------------------------
    City 444:  balagansk, ru
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=53.46&lon=103.18)
    Temperature (F):  47.48
    Humidity (%):  70
    Cloudiness (%):  0
    Wind Speed (MPH):  10.96
    ------------------------------------------
    City 445:  mataura, pf
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-42.74&lon=-151.28)
    Temperature (F):  56.48
    Humidity (%):  99
    Cloudiness (%):  92
    Wind Speed (MPH):  17.72
    ------------------------------------------
    City 446:  bambous virieux, mu
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-34.3&lon=76.12)
    Temperature (F):  63.14
    Humidity (%):  100
    Cloudiness (%):  76
    Wind Speed (MPH):  18.05
    ------------------------------------------
    City 447:  palmer, us
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=60.39&lon=-145.01)
    Temperature (F):  48.2
    Humidity (%):  81
    Cloudiness (%):  75
    Wind Speed (MPH):  4.7
    ------------------------------------------
    City 448:  honiara, sb
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-12.16&lon=157.53)
    Temperature (F):  80.51
    Humidity (%):  100
    Cloudiness (%):  76
    Wind Speed (MPH):  19.35
    ------------------------------------------
    City 449:  hilo, us
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=33.44&lon=-142.59)
    Temperature (F):  63.05
    Humidity (%):  100
    Cloudiness (%):  48
    Wind Speed (MPH):  11.56
    ------------------------------------------
    City 450:  provideniya, ru
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=67.18&lon=-175.07)
    Temperature (F):  30.29
    Humidity (%):  100
    Cloudiness (%):  68
    Wind Speed (MPH):  5.3
    ------------------------------------------
    City 451:  cidreira, br
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-57.41&lon=-23.53)
    Temperature (F):  34.16
    Humidity (%):  95
    Cloudiness (%):  88
    Wind Speed (MPH):  23.87
    ------------------------------------------
    City 452:  camacha, pt
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=38.53&lon=-18.27)
    Temperature (F):  60.62
    Humidity (%):  99
    Cloudiness (%):  68
    Wind Speed (MPH):  13.8
    ------------------------------------------
    City 453:  saint george, bm
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=32.2&lon=-57.27)
    Temperature (F):  75.02
    Humidity (%):  100
    Cloudiness (%):  0
    Wind Speed (MPH):  1.57
    ------------------------------------------
    City 454:  chuy, uy
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-37.32&lon=-47.78)
    Temperature (F):  65.66
    Humidity (%):  97
    Cloudiness (%):  92
    Wind Speed (MPH):  15.7
    ------------------------------------------
    City 455:  launceston, au
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-41.19&lon=148.04)
    Temperature (F):  49.82
    Humidity (%):  100
    Cloudiness (%):  56
    Wind Speed (MPH):  5.82
    ------------------------------------------
    City 456:  samarai, pg
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-14.31&lon=151.24)
    Temperature (F):  79.07
    Humidity (%):  100
    Cloudiness (%):  80
    Wind Speed (MPH):  20.69
    ------------------------------------------
    City 457:  coquimbo, cl
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-28.62&lon=-82.49)
    Temperature (F):  64.31
    Humidity (%):  100
    Cloudiness (%):  88
    Wind Speed (MPH):  1.5
    ------------------------------------------
    City 458:  hilo, us
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=23.07&lon=-148.16)
    Temperature (F):  74.21
    Humidity (%):  100
    Cloudiness (%):  36
    Wind Speed (MPH):  15.82
    ------------------------------------------
    City 459:  okhotsk, ru
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=58.44&lon=145.26)
    Temperature (F):  32.9
    Humidity (%):  92
    Cloudiness (%):  100
    Wind Speed (MPH):  7.54
    ------------------------------------------
    City 460:  nanakuli, us
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=13.63&lon=-163.25)
    Temperature (F):  80.51
    Humidity (%):  99
    Cloudiness (%):  8
    Wind Speed (MPH):  15.88
    ------------------------------------------
    City 461:  kyrksaeterora, no
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=63.35&lon=8.95)
    Temperature (F):  61.2
    Humidity (%):  42
    Cloudiness (%):  0
    Wind Speed (MPH):  3.36
    ------------------------------------------
    City 462:  rafaela, ar
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-31.04&lon=-61.82)
    Temperature (F):  76.19
    Humidity (%):  61
    Cloudiness (%):  12
    Wind Speed (MPH):  10.18
    ------------------------------------------
    City 463:  banda aceh, id
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=5.66&lon=89.32)
    Temperature (F):  85.46
    Humidity (%):  100
    Cloudiness (%):  32
    Wind Speed (MPH):  11.41
    ------------------------------------------
    City 464:  gotsu, jp
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=37.69&lon=130.57)
    Temperature (F):  61.61
    Humidity (%):  95
    Cloudiness (%):  32
    Wind Speed (MPH):  3.24
    ------------------------------------------
    City 465:  mataura, pf
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-35.12&lon=-150.69)
    Temperature (F):  63.05
    Humidity (%):  100
    Cloudiness (%):  80
    Wind Speed (MPH):  24.2
    ------------------------------------------
    City 466:  honiara, sb
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-14.47&lon=157.44)
    Temperature (F):  79.16
    Humidity (%):  100
    Cloudiness (%):  76
    Wind Speed (MPH):  18.16
    ------------------------------------------
    City 467:  henties bay, na
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-20.87&lon=9.49)
    Temperature (F):  68.27
    Humidity (%):  100
    Cloudiness (%):  0
    Wind Speed (MPH):  21.41
    ------------------------------------------
    City 468:  huarmey, pe
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-13.52&lon=-86.82)
    Temperature (F):  69.71
    Humidity (%):  100
    Cloudiness (%):  0
    Wind Speed (MPH):  13.87
    ------------------------------------------
    City 469:  nishihara, jp
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=21.76&lon=131.86)
    Temperature (F):  82.49
    Humidity (%):  99
    Cloudiness (%):  0
    Wind Speed (MPH):  5.26
    ------------------------------------------
    City 470:  arraial do cabo, br
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-34.33&lon=-31.16)
    Temperature (F):  62.33
    Humidity (%):  100
    Cloudiness (%):  92
    Wind Speed (MPH):  5.3
    ------------------------------------------
    City 471:  yellowknife, ca
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=66.59&lon=-114.85)
    Temperature (F):  33.35
    Humidity (%):  100
    Cloudiness (%):  92
    Wind Speed (MPH):  4.74
    ------------------------------------------
    City 472:  vaini, to
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-46.1&lon=-179.44)
    Temperature (F):  49.19
    Humidity (%):  100
    Cloudiness (%):  36
    Wind Speed (MPH):  39.08
    ------------------------------------------
    City 473:  inhambane, mz
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-24.77&lon=35.35)
    Temperature (F):  74.03
    Humidity (%):  100
    Cloudiness (%):  0
    Wind Speed (MPH):  8.55
    ------------------------------------------
    City 474:  hermanus, za
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-61.17&lon=6.52)
    Temperature (F):  32.27
    Humidity (%):  94
    Cloudiness (%):  92
    Wind Speed (MPH):  31.7
    ------------------------------------------
    City 475:  torbay, ca
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=44.72&lon=-43.86)
    Temperature (F):  50.09
    Humidity (%):  100
    Cloudiness (%):  32
    Wind Speed (MPH):  36.28
    ------------------------------------------
    City 476:  vaini, to
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-29.2&lon=-176.15)
    Temperature (F):  69.35
    Humidity (%):  100
    Cloudiness (%):  92
    Wind Speed (MPH):  22.64
    ------------------------------------------
    City 477:  maine-soroa, ne
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=14.59&lon=11.78)
    Temperature (F):  86.09
    Humidity (%):  42
    Cloudiness (%):  0
    Wind Speed (MPH):  4.29
    ------------------------------------------
    City 478:  severobaykalsk, ru
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=55.24&lon=108.43)
    Temperature (F):  32
    Humidity (%):  83
    Cloudiness (%):  0
    Wind Speed (MPH):  2.06
    ------------------------------------------
    City 479:  rikitea, pf
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-29.48&lon=-131.28)
    Temperature (F):  68.27
    Humidity (%):  100
    Cloudiness (%):  88
    Wind Speed (MPH):  10.18
    ------------------------------------------
    City 480:  moerai, pf
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-21.61&lon=-150.01)
    Temperature (F):  76.37
    Humidity (%):  100
    Cloudiness (%):  12
    Wind Speed (MPH):  7.61
    ------------------------------------------
    City 481:  kaeo, nz
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-31.26&lon=174.07)
    Temperature (F):  64.85
    Humidity (%):  100
    Cloudiness (%):  12
    Wind Speed (MPH):  21.47
    ------------------------------------------
    City 482:  hermanus, za
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-55.84&lon=15.11)
    Temperature (F):  35.6
    Humidity (%):  90
    Cloudiness (%):  92
    Wind Speed (MPH):  28.63
    ------------------------------------------
    City 483:  sobolevo, ru
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=55.58&lon=156.73)
    Temperature (F):  38.39
    Humidity (%):  100
    Cloudiness (%):  88
    Wind Speed (MPH):  4.59
    ------------------------------------------
    City 484:  samusu, ws
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-4.1&lon=-161.6)
    Temperature (F):  81.68
    Humidity (%):  100
    Cloudiness (%):  92
    Wind Speed (MPH):  16.71
    ------------------------------------------
    City 485:  price, us
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=38.66&lon=-111.79)
    Temperature (F):  53.06
    Humidity (%):  45
    Cloudiness (%):  92
    Wind Speed (MPH):  2.51
    ------------------------------------------
    City 486:  arraial do cabo, br
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-35.51&lon=-29.38)
    Temperature (F):  62.15
    Humidity (%):  100
    Cloudiness (%):  76
    Wind Speed (MPH):  8.1
    ------------------------------------------
    City 487:  luderitz, na
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-33.22&lon=6.72)
    Temperature (F):  65.21
    Humidity (%):  100
    Cloudiness (%):  24
    Wind Speed (MPH):  32.26
    ------------------------------------------
    City 488:  natchitoches, us
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=31.49&lon=-93.35)
    Temperature (F):  87.28
    Humidity (%):  49
    Cloudiness (%):  40
    Wind Speed (MPH):  6.93
    ------------------------------------------
    City 489:  beloha, mg
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-31.65&lon=40.79)
    Temperature (F):  67.73
    Humidity (%):  100
    Cloudiness (%):  36
    Wind Speed (MPH):  25.95
    ------------------------------------------
    City 490:  kaeo, nz
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-32.9&lon=173.33)
    Temperature (F):  63.23
    Humidity (%):  100
    Cloudiness (%):  76
    Wind Speed (MPH):  22.19
    ------------------------------------------
    City 491:  praia da vitoria, pt
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=48.37&lon=-21.13)
    Temperature (F):  56.75
    Humidity (%):  100
    Cloudiness (%):  44
    Wind Speed (MPH):  9.55
    ------------------------------------------
    City 492:  ixtapa, mx
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=2.68&lon=-108.9)
    Temperature (F):  80.06
    Humidity (%):  100
    Cloudiness (%):  68
    Wind Speed (MPH):  14.88
    ------------------------------------------
    City 493:  kruisfontein, za
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-46.04&lon=25.74)
    Temperature (F):  47.03
    Humidity (%):  83
    Cloudiness (%):  88
    Wind Speed (MPH):  26.28
    ------------------------------------------
    City 494:  mitsamiouli, km
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-8.21&lon=43.24)
    Temperature (F):  81.23
    Humidity (%):  99
    Cloudiness (%):  68
    Wind Speed (MPH):  17.38
    ------------------------------------------
    City 495:  prabumulih, id
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-2.8&lon=103.84)
    Temperature (F):  73.13
    Humidity (%):  98
    Cloudiness (%):  92
    Wind Speed (MPH):  2.84
    ------------------------------------------
    City 496:  tungkang, tw
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=20.57&lon=119.97)
    Temperature (F):  87.08
    Humidity (%):  97
    Cloudiness (%):  8
    Wind Speed (MPH):  3.24
    ------------------------------------------
    City 497:  ponta do sol, cv
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=19.02&lon=-37.59)
    Temperature (F):  72.32
    Humidity (%):  100
    Cloudiness (%):  92
    Wind Speed (MPH):  20.07
    ------------------------------------------
    City 498:  ulaangom, mn
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=47.04&lon=91.46)
    Temperature (F):  24.08
    Humidity (%):  63
    Cloudiness (%):  0
    Wind Speed (MPH):  2.28
    ------------------------------------------
    City 499:  vaini, to
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=-66.54&lon=-179.31)
    Temperature (F):  19.76
    Humidity (%):  100
    Cloudiness (%):  68
    Wind Speed (MPH):  39.03
    ------------------------------------------
    City 500:  torbay, ca
    (http://api.openweathermap.org/data/2.5/weather?appid=4de1f9a161b367d3b8a738b0edb16cdb&units=imperial&lat=34.3&lon=-44.35)
    Temperature (F):  75.11
    Humidity (%):  97
    Cloudiness (%):  100
    Wind Speed (MPH):  17.67
    ------------------------------------------
    FINISHED RETRIEVING DATA
    ------------------------------------------
    


```python
# BUILD A SCATTER PLOT FOR TEMPERATURE (F) vs LATITUDE

plt.scatter(x=cities_df['Temperature'],
            y=cities_df['Latitude'],
            edgecolor="black", color = 'r',
            linewidths=1, marker="o", alpha=0.8,
            zorder=2)

# Incorporate the other graph properties
plt.title("Temperature (F) vs. City Latitude (" + str(now.year) + "-" + str(now.month) + "-" + str(now.day) + ")")
plt.ylabel("City Latitude")
plt.xlabel("Temperature (F)")
plt.grid(color='white',zorder=1)
plt.ylim([-75, 75])
ax = plt.gca()
ax.set_facecolor('xkcd:light grey')
ax.axhline(0, color='black')

# Save the figure
plt.savefig("output_analysis/Temperature_Latitude.png")

# Show plot
plt.show()
```


![png](output_5_0.png)



```python
# BUILD A SCATTER PLOT FOR HUMIDITY (%) vs LATITUDE

plt.scatter(x=cities_df['Humidity'],
            y=cities_df['Latitude'],
            edgecolor="black", color = 'b',
            linewidths=1, marker="o", alpha=0.8,
            zorder=2)

# Incorporate the other graph properties
plt.title("Humidity (%) vs. City Latitude (" + str(now.year) + "-" + str(now.month) + "-" + str(now.day) + ")")
plt.ylabel("City Latitude")
plt.xlabel("Humidity (%)")
plt.grid(color='white',zorder=1)
plt.ylim([-75, 75])
ax = plt.gca()
ax.set_facecolor('xkcd:light grey')
ax.axhline(0, color='black')

# Save the figure
plt.savefig("output_analysis/Humidity_Latitude.png")

# Show plot
plt.show()
```


![png](output_6_0.png)



```python
# BUILD A SCATTER PLOT FOR CLOUDINESS (%) vs LATITUDE

plt.scatter(x=cities_df['Cloudiness'],
            y=cities_df['Latitude'],
            edgecolor="black", color = 'w',
            linewidths=1, marker="o", alpha=1,
            zorder=2)

# Incorporate the other graph properties
plt.title("Cloudiness (%) vs. City Latitude (" + str(now.year) + "-" + str(now.month) + "-" + str(now.day) + ")")
plt.ylabel("City Latitude")
plt.xlabel("Cloudiness (%)")
plt.grid(color='white',zorder=1)
plt.ylim([-75, 75])
ax = plt.gca()
ax.set_facecolor('xkcd:light grey')
ax.axhline(0, color='black')

# Save the figure
plt.savefig("output_analysis/Cloudiness_Latitude.png")

# Show plot
plt.show()
```


![png](output_7_0.png)



```python
# BUILD A SCATTER PLOT FOR WIND SPEED (MPH) vs LATITUDE

plt.scatter(x=cities_df['Wind Speed'],
            y=cities_df['Latitude'],
            edgecolor="black", color = 'g',
            linewidths=1, marker="o", alpha=0.8,
            zorder=2)

# Incorporate the other graph properties
plt.title("Wind Speed (MPH) vs. City Latitude (" + str(now.year) + "-" + str(now.month) + "-" + str(now.day) + ")")
plt.ylabel("City Latitude")
plt.xlabel("Wind Speed (MPH)")
plt.grid(color='white',zorder=1)
plt.ylim([-75, 75])
ax = plt.gca()
ax.set_facecolor('xkcd:light grey')
ax.axhline(0, color='black')

# Save the figure
plt.savefig("output_analysis/WindSpeed_Latitude.png")

# Show plot
plt.show()
```


![png](output_8_0.png)



```python
### BONUS
# BUILD A SCATTER PLOT FOR CLOUDINESS (%) vs HUMIDITY (%)

plt.scatter(x=cities_df['Cloudiness'],
            y=cities_df['Humidity'],
            edgecolor="black", color = 'y',
            linewidths=1, marker="o", alpha=0.8,
            zorder=2)

# Incorporate the other graph properties
plt.title("Cloudiness (%) vs. Humidity (%) (" + str(now.year) + "-" + str(now.month) + "-" + str(now.day) + ")")
plt.ylabel("Humidity (%)")
plt.xlabel("Cloudiness (%)")
plt.xlim([-5,105])
plt.ylim([-5,105])
plt.grid(color='white',zorder=1)
ax = plt.gca()
ax.set_facecolor('xkcd:light grey')

# Save the figure
plt.savefig("output_analysis/Cloudiness_Humidity.png")

# Show plot
plt.show()
```
