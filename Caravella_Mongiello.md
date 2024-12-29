```python
import pandas as pd
import os
import geopandas as gpd
import matplotlib.pyplot as plt
from shapely.geometry import Point
from shapely.geometry import box
```


```python
# Set the working directory
os.chdir('C:/Users/user/Documents/Unibo/Economics and Econometrics/Second Year/First Semester/Introduction to Python for Economists/Project')

nuclear_plants = pd.read_csv('nuclear_power_plants.csv') # data from "https://github.com/cristianst85/GeoNuclearData/blob/master/data/csv/denormalized/nuclear_power_plants.csv"
print(nuclear_plants.head())

# Consider only operational nuclear plants
operational_nuclear_plants = ["Operational"]
nuclear_plants = nuclear_plants[nuclear_plants['Status'].isin(operational_nuclear_plants)]
print(nuclear_plants.head())
```

       Id                  Name   Latitude  Longitude Country CountryCode  \
    0   1                Ågesta  59.206000   18.08290  Sweden          SE   
    1   2  Akademik Lomonosov-1  69.709579  170.30625  Russia          RU   
    2   3  Akademik Lomonosov-2  69.709579  170.30625  Russia          RU   
    3   4              Akhvaz-1        NaN        NaN    Iran          IR   
    4   5              Akhvaz-2        NaN        NaN    Iran          IR   
    
            Status ReactorType        ReactorModel ConstructionStartAt  \
    0     Shutdown        PHWR                 NaN          1957-12-01   
    1  Operational         PWR  KLT-40S 'Floating'          2007-04-15   
    2  Operational         PWR  KLT-40S 'Floating'          2007-04-15   
    3      Planned         NaN                 NaN                 NaN   
    4      Planned         NaN                 NaN                 NaN   
    
      OperationalFrom OperationalTo  Capacity              LastUpdatedAt  \
    0      1964-05-01    1974-06-02       9.0  2015-05-24T04:51:37+03:00   
    1      2020-05-22           NaN      30.0                 2021-05-31   
    2      2020-05-22           NaN      30.0                 2021-05-31   
    3             NaN           NaN       NaN                        NaN   
    4             NaN           NaN       NaN                        NaN   
    
                     Source  IAEAId  
    0              WNA/IAEA   528.0  
    1  WNA/IAEA/Google Maps   895.0  
    2  WNA/IAEA/Google Maps   896.0  
    3                   WNA     NaN  
    4                   WNA     NaN  
        Id                  Name   Latitude  Longitude Country CountryCode  \
    1    2  Akademik Lomonosov-1  69.709579  170.30625  Russia          RU   
    2    3  Akademik Lomonosov-2  69.709579  170.30625  Russia          RU   
    10  11             Almaraz-1  39.807000   -5.69800   Spain          ES   
    11  12             Almaraz-2  39.807000   -5.69800   Spain          ES   
    12  13               Angra-1 -23.008000  -44.45700  Brazil          BR   
    
             Status ReactorType        ReactorModel ConstructionStartAt  \
    1   Operational         PWR  KLT-40S 'Floating'          2007-04-15   
    2   Operational         PWR  KLT-40S 'Floating'          2007-04-15   
    10  Operational         PWR              WH 3LP          1973-07-03   
    11  Operational         PWR              WH 3LP          1973-07-03   
    12  Operational         PWR              WH 2LP          1971-05-01   
    
       OperationalFrom OperationalTo  Capacity              LastUpdatedAt  \
    1       2020-05-22           NaN      30.0                 2021-05-31   
    2       2020-05-22           NaN      30.0                 2021-05-31   
    10      1983-09-01           NaN     900.0  2017-02-10T23:56:15+02:00   
    11      1984-07-01           NaN     900.0  2019-06-02T20:17:55+03:00   
    12      1985-01-01           NaN     609.0  2021-02-14T03:47:53+02:00   
    
                      Source  IAEAId  
    1   WNA/IAEA/Google Maps   895.0  
    2   WNA/IAEA/Google Maps   896.0  
    10              WNA/IAEA   153.0  
    11              WNA/IAEA   154.0  
    12              WNA/IAEA    24.0  
    


```python
eu_iso2_codes = [
    "AT", "BE", "BG", "HR", "CY",
    "CZ", "DK", "EE", "FI", "FR",
    "DE", "GR", "HU", "IE", "IT",
    "LV", "LT", "LU", "MT", "NL",
    "PL", "PT", "RO", "SK", "SI",
    "ES", "SE"
]

# Filter for EU countries
nuclear_plants = nuclear_plants[nuclear_plants['CountryCode'].isin(eu_iso2_codes)]
```


```python
# Load the shapefile
sp_path = "NUTS_RG_01M_2021_4326_LEVL_3/NUTS_RG_01M_2021_4326_LEVL_3_repaired.shp"  
eu_map = gpd.read_file(sp_path)

# Define the bounding box for continental Europe
bounding_box = {
    "minx": -10,  # Western limit
    "maxx": 30,   # Eastern limit
    "miny": 35,   # Southern limit
    "maxy": 70    # Northern limit
}
```


```python
print(eu_map)
print(eu_map.crs)
```

         NUTS_ID  LEVL_CODE CNTR_CODE                            NAME_LATN  \
    0      NO0B2          3        NO                             Svalbard   
    1      NO0B1          3        NO                 Jan Mayen\r\n   \r\n   
    2      HR064          3        HR          Krapinsko-zagorska upanija   
    3      DE21A          3        DE                               Erding   
    4      DE94E          3        DE                 Osnabrück, Landkreis   
    ...      ...        ...       ...                                  ...   
    1509   UKM73          3        UK          East Lothian and Midlothian   
    1510   UKM75          3        UK                   Edinburgh, City of   
    1511   UKM76          3        UK                              Falkirk   
    1512   UKM78          3        UK                         West Lothian   
    1513   UKK24          3        UK  Bournemouth, Christchurch and Poole   
    
                                    NUTS_NAME  MOUNT_TYPE  URBN_TYPE  COAST_TYPE  \
    0                                Svalbard         3.0        3.0           1   
    1                    Jan Mayen\r\n   \r\n         NaN        NaN           1   
    2             Krapinsko-zagorska upanija         4.0        3.0           3   
    3                                  Erding         4.0        3.0           3   
    4                    Osnabrück, Landkreis         4.0        2.0           3   
    ...                                   ...         ...        ...         ...   
    1509          East Lothian and Midlothian         4.0        1.0           1   
    1510                   Edinburgh, City of         4.0        1.0           1   
    1511                              Falkirk         4.0        1.0           1   
    1512                         West Lothian         4.0        1.0           1   
    1513  Bournemouth, Christchurch and Poole         4.0        1.0           1   
    
            FID                                           geometry  
    0     NO0B2  MULTIPOLYGON (((33.09131 80.24908, 33.09929 80...  
    1     NO0B1  POLYGON ((-7.96242 71.16199, -7.9553 71.15968,...  
    2     HR064  POLYGON ((16.25128 46.07165, 16.24254 46.0622,...  
    3     DE21A  POLYGON ((12.01712 48.43068, 12.0221 48.42585,...  
    4     DE94E  POLYGON ((8.01815 52.68391, 8.03442 52.67338, ...  
    ...     ...                                                ...  
    1509  UKM73  MULTIPOLYGON (((-2.36662 55.94611, -2.36697 55...  
    1510  UKM75  MULTIPOLYGON (((-3.07764 55.9469, -3.08167 55....  
    1511  UKM76  POLYGON ((-3.51572 56.00226, -3.51251 55.99411...  
    1512  UKM78  POLYGON ((-3.42533 55.99403, -3.42699 55.9905,...  
    1513  UKK24  POLYGON ((-1.80428 50.79602, -1.8025 50.79206,...  
    
    [1514 rows x 10 columns]
    EPSG:4326
    


```python
# Filter for EU countries
eu_map = eu_map[eu_map['CNTR_CODE'].isin(eu_iso2_codes)]


# Create a bounding box as a shapely object
bounding_polygon = box(bounding_box["minx"], bounding_box["miny"], bounding_box["maxx"], bounding_box["maxy"])

# Clip the map using the bounding box
eu_map = eu_map.clip(bounding_polygon)
```


```python
# Plot the map
fig, ax = plt.subplots(figsize=(18, 18))
eu_map.plot(ax=ax, color="lightblue", edgecolor="black")

# Add a title
ax.set_title("Map of European Union NUTS3 regions", fontsize=16)

# Show the plot
plt.show()
```


    
![png](output_6_0.png)
    



```python
# Create a GeoDataFrame for nuclear plants
plants_gdf = gpd.GeoDataFrame(
    nuclear_plants, geometry=gpd.points_from_xy(nuclear_plants.Longitude, nuclear_plants.Latitude), crs="EPSG:4326"
)
print(plants_gdf.crs)
```

    EPSG:4326
    


```python
# Plot EU map with nuclear plants
fig, ax = plt.subplots(figsize=(18, 18))
eu_map.plot(ax=ax, color="lightblue", edgecolor="black")
plants_gdf.plot(ax=ax, color="red", markersize=10, label="Nuclear Plant")

# Add title and legend
ax.set_title("Operative Nuclear Plants in the European Union", fontsize=16)
ax.legend()

# Show the plot
plt.show()
```


    
![png](output_8_0.png)
    



```python
# Load the file into a pandas DataFrame
data = pd.read_csv("estat_tgs00003.tsv.gz", sep="\t", compression="gzip") # GDP per capita regional

# Display the first few rows
print(data)
```

        freq,unit,geo\TIME_PERIOD     2011      2012      2013      2014   \
    0              A,MIO_EUR,AL01   2214.58   2297.46   2276.67   2290.45   
    1              A,MIO_EUR,AL02   4195.20   4294.60   4319.08   4535.08   
    2              A,MIO_EUR,AL03   2858.55   2993.75   3029.61   3143.06   
    3              A,MIO_EUR,AT11   7012.58   7365.43   7539.32   7737.13   
    4              A,MIO_EUR,AT12  48511.32  49802.60  50470.13  52049.38   
    ..                        ...       ...       ...       ...       ...   
    302            A,MIO_EUR,TRB1   8083.86   9082.60   9560.26   9105.19   
    303            A,MIO_EUR,TRB2   6449.98   8200.98   7759.79   7509.00   
    304            A,MIO_EUR,TRC1  11257.49  13546.61  14946.38  14908.18   
    305            A,MIO_EUR,TRC2  11515.81  13078.78  14050.38  13221.90   
    306            A,MIO_EUR,TRC3   7892.19   9077.43   9685.72   9529.04   
    
            2015      2016      2017       2018       2019       2020       2021   \
    0     2418.75   2549.18   2707.01   2954.85    3165.02    2996.28    3390.4 p   
    1     4837.09   5050.74   5640.06   6327.54    6789.07    6628.26   7646.74 p   
    2     3008.27   3119.93   3211.98   3545.66     3800.1    3685.89   4120.24 p   
    3     8040.55   8351.77   8717.79   8960.75    9222.79    8948.33    9486.83    
    4    53884.36  55570.82  58207.93  60471.34    63009.7   59608.74   63973.78    
    ..        ...       ...       ...        ...        ...        ...        ...   
    302   9982.53  10171.94   9877.99   8296.63    8660.25    8442.13    8582.11    
    303   8003.34   8309.77   7986.81   6880.89    7291.75    6919.75     6611.7    
    304  17015.05  17015.64  16518.67   14289.3   14979.76    15356.4   17142.34    
    305  14740.72  14683.24  14073.93   11405.2   12121.41   11581.17    11674.2    
    306  10105.88  10092.76  10308.14      9032    9668.48    9276.31     9764.3    
    
             2022   
    0           :   
    1           :   
    2           :   
    3    10454.97   
    4    71757.87   
    ..         ...  
    302   9751.77   
    303   7929.98   
    304  21676.41   
    305  15377.97   
    306  12374.91   
    
    [307 rows x 13 columns]
    


```python

```
