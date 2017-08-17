
# Pyber Ridesharing Analysis

## Overview

Pyber is a nascent ridesharing company. With two of their data sets, one containing the drivers and cities in their service areas and one containing individual ride records, we will create a bubble plot that shows the relationships among:
* Average Fare ($) Per City
* Total Number of Rides Per City
* Total Number of Drivers Per City
* City Type (Urban, Suburban, Rural)

In addition, we will make three pie charts that show:
* % of Total Fares by City Type
* % of Total Rides by City Type
* % of Total Drivers by City Type


```python
# Dependencies.

import pandas as pd
import numpy as np
import os
import matplotlib.pyplot as plt
```


```python
cd = os.path.abspath(".") # Establish current directory.

# Read our data from csv files into two data frames.

cityDf = pd.read_csv(os.path.join(cd, 'city_data.csv'))
rideDf = pd.read_csv(os.path.join(cd, 'ride_data.csv'))
              
# Print the "city_data" data frame: it appears to describe the city name, number of drivers per city, and
# The city's type (rural, suburban, and rural)

cityDf.head()
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>city</th>
      <th>driver_count</th>
      <th>type</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Kelseyland</td>
      <td>63</td>
      <td>Urban</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Nguyenbury</td>
      <td>8</td>
      <td>Urban</td>
    </tr>
    <tr>
      <th>2</th>
      <td>East Douglas</td>
      <td>12</td>
      <td>Urban</td>
    </tr>
    <tr>
      <th>3</th>
      <td>West Dawnfurt</td>
      <td>34</td>
      <td>Urban</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Rodriguezburgh</td>
      <td>52</td>
      <td>Urban</td>
    </tr>
  </tbody>
</table>
</div>




```python
# We have 126 city records in our dataframe.

len(cityDf)
```




    126




```python
# One of the cities appears twice, before we join our city data with our ride data, 
# we need to eliminate the duplicate. We can do this by grouping by city names and applying
# an aggregate function. Here we use sum, which assumes that we have two separate but valid observations
# for the duplicated city. We could use mean instead if we thought that the duplicate
# reppresents two valid observations over time. 


cityGrouped = cityDf.groupby(['city', 'type']).sum().reset_index()
len(cityGrouped)
```




    125




```python
# Print the head of the "ride_data" data frame: it appears to list records that describe individual rides
# with the city, datetime, fare, and unique ride_id for each ride.

rideDf.head()
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>city</th>
      <th>date</th>
      <th>fare</th>
      <th>ride_id</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Sarabury</td>
      <td>2016-01-16 13:49:27</td>
      <td>38.35</td>
      <td>5403689035038</td>
    </tr>
    <tr>
      <th>1</th>
      <td>South Roy</td>
      <td>2016-01-02 18:42:34</td>
      <td>17.49</td>
      <td>4036272335942</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Wiseborough</td>
      <td>2016-01-21 17:35:29</td>
      <td>44.18</td>
      <td>3645042422587</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Spencertown</td>
      <td>2016-07-31 14:53:22</td>
      <td>6.87</td>
      <td>2242596575892</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Nguyenbury</td>
      <td>2016-07-09 04:42:44</td>
      <td>6.28</td>
      <td>1543057793673</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Our ride data frame has 2375 records. 

len(rideDf)
```




    2375




```python
# Merge the two data frames using a left join with the ride_data data frame as the left table,
# we can use this to summarize stats for our bubble chart and two of our pie charts later on.

ridesCity = pd.merge(rideDf, cityGrouped, how='left', left_on='city', right_on='city')
```


```python
# Print the head of the combined dataframe.

ridesCity.head() 
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>city</th>
      <th>date</th>
      <th>fare</th>
      <th>ride_id</th>
      <th>type</th>
      <th>driver_count</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Sarabury</td>
      <td>2016-01-16 13:49:27</td>
      <td>38.35</td>
      <td>5403689035038</td>
      <td>Urban</td>
      <td>46</td>
    </tr>
    <tr>
      <th>1</th>
      <td>South Roy</td>
      <td>2016-01-02 18:42:34</td>
      <td>17.49</td>
      <td>4036272335942</td>
      <td>Urban</td>
      <td>35</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Wiseborough</td>
      <td>2016-01-21 17:35:29</td>
      <td>44.18</td>
      <td>3645042422587</td>
      <td>Urban</td>
      <td>55</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Spencertown</td>
      <td>2016-07-31 14:53:22</td>
      <td>6.87</td>
      <td>2242596575892</td>
      <td>Urban</td>
      <td>68</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Nguyenbury</td>
      <td>2016-07-09 04:42:44</td>
      <td>6.28</td>
      <td>1543057793673</td>
      <td>Urban</td>
      <td>8</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Print the length of the new data frame

len(ridesCity)
```




    2375




```python
# Aggregate summaries for total 
stats1 = {'ride_id': ['count'], 'fare': ['mean'] }

# Groupby type and aggregate counts and means using our dictionary:

ridesCitySummary = ridesCity.groupby(['city', 'type','driver_count'], as_index=False).agg(stats1)
ridesCitySummary.reset_index(inplace=True)
list(ridesCitySummary)
```




    [('index', ''),
     ('city', ''),
     ('type', ''),
     ('driver_count', ''),
     ('ride_id', 'count'),
     ('fare', 'mean')]




```python
ridesCitySummary.head()
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr>
      <th></th>
      <th>index</th>
      <th>city</th>
      <th>type</th>
      <th>driver_count</th>
      <th>ride_id</th>
      <th>fare</th>
    </tr>
    <tr>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th>count</th>
      <th>mean</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0</td>
      <td>Alvarezhaven</td>
      <td>Urban</td>
      <td>21</td>
      <td>31</td>
      <td>23.928710</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>Alyssaberg</td>
      <td>Urban</td>
      <td>67</td>
      <td>26</td>
      <td>20.609615</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>Anitamouth</td>
      <td>Suburban</td>
      <td>16</td>
      <td>9</td>
      <td>37.315556</td>
    </tr>
    <tr>
      <th>3</th>
      <td>3</td>
      <td>Antoniomouth</td>
      <td>Urban</td>
      <td>21</td>
      <td>22</td>
      <td>23.625000</td>
    </tr>
    <tr>
      <th>4</th>
      <td>4</td>
      <td>Aprilchester</td>
      <td>Urban</td>
      <td>49</td>
      <td>19</td>
      <td>21.981579</td>
    </tr>
  </tbody>
</table>
</div>




```python
figa = plt.figure(figsize=(8, 5), dpi=100)
colors = ['lightcoral', 'gold', 'lightskyblue']
ax1 = figa.add_subplot(1,1,1)

for i, color in  zip(ridesCitySummary['type'].unique(), colors):
    series = ridesCitySummary[ridesCitySummary['type'] == i]
    
    ax1.scatter(
        series.ride_id, 
        series.fare,
        label=i,
        s=series.driver_count * 5, # Multiplying by 5 overstates the size to make it more like a bubble chart.
        c=color,
        alpha=.85,
        edgecolor='black'
    )
ax1.legend()
ax1.set_xlabel('Number of Rides')
ax1.set_ylabel('Average Fare')
ax1.set_title('Average Fare and Number of Rides per City')

plt.show()
```


![png](output_12_0.png)



```python
# For our first two pie charts we want to find the ride count and average fare by city
# type, which we can do by aggregating our original data frame using sum() and count().

stats = {'ride_id': ['count'], 'fare': ['sum'] }

# Groupby type and aggregate counts and sums using our dictionary:

rideFareSummary = ridesCity.groupby(['type']).agg(stats).reset_index()

colors = ['lightskyblue', 'gold', 'lightcoral']
sizes1 = rideFareSummary.fare
labels1 = rideFareSummary.type

figb = plt.figure(figsize=(6, 6), dpi=100)
ax2 = figb.add_subplot(1,1,1)

ax2.pie(sizes1, labels=labels1, autopct='%1.1f%%', colors=colors, shadow=True, startangle=90)
ax2.set_aspect('equal', adjustable='box') 
ax2.set_title("Total Pyber Fares by City Type")

plt.show()
```


![png](output_13_0.png)



```python
# We can reuse our rideFareSummary data frame for this plot too.

sizes2 = rideFareSummary.ride_id
labels2 = rideFareSummary.type

figc = plt.figure(figsize=(6, 6), dpi=100)
ax3 = figc.add_subplot(1,1,1)

ax3.pie(sizes2, labels=labels2, autopct='%1.1f%%', colors=colors, shadow=True, startangle=90)
ax3.set_aspect('equal', adjustable='box') 
ax3.set_title("Total Rides by City Type")


plt.show()
```


![png](output_14_0.png)



```python
# For our last pie chart, we only need to find the number of drivers per type of city
# All of this data is contained in our original cityDf data frame, which we group by driver
# count using sum().

driverCitySummary = cityGrouped.groupby(['type']).driver_count.sum()

sizes3 = driverCitySummary.values
labels3 = driverCitySummary.index

figd = plt.figure(figsize=(6, 6), dpi= 100)
ax4 = figd.add_subplot(1,1,1)

ax4.pie(sizes3, labels=labels3, autopct='%1.1f%%',  colors=colors, shadow=True, startangle=90)
ax4.set_aspect('equal', adjustable='box') 
ax4.set_title("Total Drivers by City Type")

plt.show()
```


![png](output_15_0.png)


## Some Conclusions

1. With some exceptions, the average fare per ride in a city decreases with the density of the city.
2. Simialry, the productivity of drivers seems to go up as they drive in less populated areas, with the most productive drivers in rural cities (where they are 3.1% of total Pyber drivers making 6.7% of pyber's fares).
3. There are serveral cities whose type doesn't reflect the average fare and ride volume of that city type. If Pyber is targeting features or promotions towards riders using a segmentation strategy that includes city type, they may want to consider how to treat some of these 'misplaced' cities.

