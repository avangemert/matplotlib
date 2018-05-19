
# Pyber Ride Sharing

## Analysis

- The majority of fares, rides, and drivers are concentrated in urban city types.
- Average fares tend to be higher in rural areas.
- Although the percent of total fares is highest in urban areas, the average fare is lowest. The quantity of rides and drivers make up for the low average fare in contributing to total fare amount.


```python
# import dependencies
from matplotlib import pyplot as plt
from scipy import stats
import numpy as np
import pandas as pd
import seaborn as sns

# Format floats
pd.options.display.float_format = '${:,.2f}'.format
```


```python
# Load data

city_data = pd.read_csv("Resources/city_data.csv")
ride_data = pd.read_csv("Resources/ride_data.csv")
```


```python
# merge two datasets
city_ride_data = pd.merge(city_data, ride_data, on="city", how="outer")
city_ride_data.head(10)
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
      <th>city</th>
      <th>driver_count</th>
      <th>type</th>
      <th>date</th>
      <th>fare</th>
      <th>ride_id</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Kelseyland</td>
      <td>63</td>
      <td>Urban</td>
      <td>2016-08-19 04:27:52</td>
      <td>$5.51</td>
      <td>6246006544795</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Kelseyland</td>
      <td>63</td>
      <td>Urban</td>
      <td>2016-04-17 06:59:50</td>
      <td>$5.54</td>
      <td>7466473222333</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Kelseyland</td>
      <td>63</td>
      <td>Urban</td>
      <td>2016-05-04 15:06:07</td>
      <td>$30.54</td>
      <td>2140501382736</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Kelseyland</td>
      <td>63</td>
      <td>Urban</td>
      <td>2016-01-25 20:44:56</td>
      <td>$12.08</td>
      <td>1896987891309</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Kelseyland</td>
      <td>63</td>
      <td>Urban</td>
      <td>2016-08-09 18:19:47</td>
      <td>$17.91</td>
      <td>8784212854829</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Kelseyland</td>
      <td>63</td>
      <td>Urban</td>
      <td>2016-10-24 15:15:46</td>
      <td>$33.56</td>
      <td>4797969661996</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Kelseyland</td>
      <td>63</td>
      <td>Urban</td>
      <td>2016-06-06 13:54:23</td>
      <td>$20.81</td>
      <td>9811478565448</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Kelseyland</td>
      <td>63</td>
      <td>Urban</td>
      <td>2016-08-10 07:02:40</td>
      <td>$44.53</td>
      <td>1563171128434</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Kelseyland</td>
      <td>63</td>
      <td>Urban</td>
      <td>2016-07-05 17:37:13</td>
      <td>$29.02</td>
      <td>6897992353955</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Kelseyland</td>
      <td>63</td>
      <td>Urban</td>
      <td>2016-04-25 02:18:31</td>
      <td>$20.05</td>
      <td>1148374505062</td>
    </tr>
  </tbody>
</table>
</div>




```python
# groupby city
groupByCity_df = city_ride_data.groupby(['city'])
```


```python
# Combine dataset

ride_sharing = pd.DataFrame({"Average Fare ($)": groupByCity_df["fare"].mean(),
                            "Total Number of Rides (Per City)": groupByCity_df["ride_id"].count(),
                            "Total Number of Drivers": groupByCity_df["driver_count"].max(),
                            "City Type": groupByCity_df["type"].max()})
```


```python
# Dropping Port James data point because it creates an outlier in the bubble chart. Removing outlier doesn't change 
# remaining results. 
rev_ride_sharing = ride_sharing.drop(['Port James'])
```

## Bubble Plot of Ride Sharing Data


```python
# Create bubble chart using Seaborn

marker_size = (rev_ride_sharing['Total Number of Drivers']*10)
sns.set(style="darkgrid")
bubble_plot = sns.lmplot(x = "Total Number of Rides (Per City)", y = "Average Fare ($)", data = rev_ride_sharing,
                         hue= "City Type", palette=dict(Urban="lightcoral", Suburban="lightskyblue", Rural="gold"), 
                         fit_reg=False, scatter_kws={'s': marker_size}, legend=False)
bubble_plot = (bubble_plot.set(xlim=(0, 40), ylim=(15, 45)))

# Format with matplotlib
plt.title('Pyber Ride Sharing Data (2016)')
plt.text(30, 37, s="Note: Circle size correlates with driver count per city.", horizontalalignment="left")
plt.text(22, 42, s="City Types")
plt.legend(loc='upper right', markerscale = 0.5)

plt.savefig("pyber_ride_sharing_data.png")
plt.show()
```




    <matplotlib.legend.Legend at 0x1a174262b0>




![png](output_8_1.png)



```python
# Determine % of total fares by city type
# Determine total fare amount
total_fare = city_ride_data["fare"].sum()
"${:,.2f}".format(total_fare)
```




    '$64,669.12'




```python
# Groupby city type
groupByCityType_df = city_ride_data.groupby(['type'])
```


```python
fares_type = pd.DataFrame({"Total Fares by City Type": groupByCityType_df["fare"].sum()})
fares_type
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
      <th>Total Fares by City Type</th>
    </tr>
    <tr>
      <th>type</th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Rural</th>
      <td>$4,255.09</td>
    </tr>
    <tr>
      <th>Suburban</th>
      <td>$20,335.69</td>
    </tr>
    <tr>
      <th>Urban</th>
      <td>$40,078.34</td>
    </tr>
  </tbody>
</table>
</div>



## Total Fares by City Type


```python
city_types = ["Rural", "Suburban", "Urban"]
total_fares = [4255.09, 20335.69, 40078.34]
colors = ["gold", "lightskyblue", "lightcoral"]
explode = (0,0,0.1)

# Tell matplotlib to create a pie chart based upon the above data
plt.pie(total_fares, explode=explode, labels=city_types, colors=colors, 
        autopct="%1.1f%%", shadow=True, startangle=100)

# Create axes which are equal so we have a perfect circle
plt.axis("equal")

plt.title('% of Total Fares by City Type')

plt.savefig("total_fares_city_type.png")
plt.show()
```


![png](output_13_0.png)



```python
# total rides by city type
total_rides = city_ride_data["ride_id"].count()
total_rides
```




    2407




```python
rides_type = pd.DataFrame({"Total Rides by City Type": groupByCityType_df["ride_id"].count()})
rides_type
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
      <th>Total Rides by City Type</th>
    </tr>
    <tr>
      <th>type</th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Rural</th>
      <td>125</td>
    </tr>
    <tr>
      <th>Suburban</th>
      <td>657</td>
    </tr>
    <tr>
      <th>Urban</th>
      <td>1625</td>
    </tr>
  </tbody>
</table>
</div>



## Total Rides by City Type


```python
city_types = ["Rural", "Suburban", "Urban"]
rides = [125, 657, 1625]
colors = ["gold", "lightskyblue", "lightcoral"]
explode = (0,0,0.1)

# Tell matplotlib to create a pie chart based upon the above data
plt.pie(rides, explode=explode, labels=city_types, colors=colors, 
        autopct="%1.1f%%", shadow=True, startangle=125)

# Create axes which are equal so we have a perfect circle
plt.axis("equal")

plt.title('% of Total Rides by City Type')

plt.savefig("total_rides_city_type.png")
plt.show()
```


![png](output_17_0.png)



```python
# Total drivers
total_drivers = ride_sharing.sum()['Total Number of Drivers']
total_drivers
```




    3346




```python
# groupby city type
groupByCityType_df = ride_sharing.groupby(['City Type'])

# determine total drivers by city type
total_drivers_city_type = pd.DataFrame({"Total Drivers": groupByCityType_df["Total Number of Drivers"].sum()})
total_drivers_city_type
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
      <th>Total Drivers</th>
    </tr>
    <tr>
      <th>City Type</th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Rural</th>
      <td>104</td>
    </tr>
    <tr>
      <th>Suburban</th>
      <td>635</td>
    </tr>
    <tr>
      <th>Urban</th>
      <td>2607</td>
    </tr>
  </tbody>
</table>
</div>



## Total Drivers by City Type


```python
city_types = ["Rural", "Suburban", "Urban"]
drivers = [104, 635, 2607]
colors = ["gold", "lightskyblue", "lightcoral"]
explode = (0,0,0.1)

# Tell matplotlib to create a pie chart based upon the above data
plt.pie(drivers, explode=explode, labels=city_types, colors=colors, 
        autopct="%1.1f%%", shadow=True, startangle=135)

# Create axes which are equal so we have a perfect circle
plt.axis("equal")

plt.title('% of Total Drivers by City Type')

plt.savefig("total_drivers_city_type.png")
plt.show()
```


![png](output_21_0.png)

