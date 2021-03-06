# PyBer Analysis with Pandas and Matplotlib

## Overview of the Pyber Analysis

### Purpose
The purpose of this analysis was to create a summary data frame of the PyBer company's ride-sharing data by city type, and create a multiple-line graph showing the total weekly fares for each city type.

## Results

### PyBer Summary Data
To create the summary data frame of the ride-sharing data by city type, I first initialized three variables to get the total rides, total drivers, and total fares.

```
total_rides = pyber_data_df.groupby(["type"]).count()["ride_id"]
total_drivers = city_data_df.groupby(["type"]).sum()["driver_count"]
total_fares = pyber_data_df.groupby(["type"]).sum()["fare"]
```

I then calculated the average fare per ride and the average fare per driver for each city type.

```
average_fare_per_ride = total_fares / total_rides
average_fare_per_driver = total_fares / total_drivers
```

I finally created the PyBer summary data frame and formatted the data frame by adding the dollar sign ($) to the columns related to fares and rounding them to two decimal places.

```
pyber_summary_df = pd.DataFrame({
    "Total Rides": total_rides,
    "Total Drivers": total_drivers,
    "Total Fares": total_fares,
    "Average Fare per Ride": average_fare_per_ride,
    "Average Fare per Driver": average_fare_per_driver})

pyber_summary_df.index.name = None

pyber_summary_df["Total Fares"] = pyber_summary_df["Total Fares"].map("${:,.2f}".format)
pyber_summary_df["Average Fare per Ride"] = pyber_summary_df["Average Fare per Ride"].map("${:.2f}".format)
pyber_summary_df["Average Fare per Driver"] = pyber_summary_df["Average Fare per Driver"].map("${:.2f}".format)

pyber_summary_df
```

The output from running the script produces the following data frame:

![PyBer Summary](./Resources/pyber_summary_df.PNG)

As we can see from the table, both rural and suburban have more total rides than total drivers, while urban has more total drivers than total rides. The averages of fare per ride and fare per driver are also much higher in rural and suburban areas compared to urban areas. This is due to the population density and the differences in the supply and demand, as well as the distance between the starting points and the destination. It seems reasonable to assume that trips have the potential to be longer in rural areas, and therefore cost more, while trips within urban areas have the potential to be shorter, and therefore cost less.

### Total Fare by City Type Line Graph
To create the line graph showing the total weekly fares for each city type, I first used the groupby() function to create a new data frame showing the sum of the fares by the city type and date. After resetting the index, I then created a pivot table with the date as the index, the city type as the columns, and the values as the fares.

```
fares_sum_df = pyber_data_df.groupby(["type","date"]).sum()["fare"]
fares_sum_df = fares_sum_df.reset_index()
fares_sum_pivot = fares_sum_df.pivot(index="date", columns="type", values="fare")
fares_sum_pivot
```

I then created a new dataframe by using the resample() function by week to get the sum of the fares for each week.

```
fares_sum_pivot = fares_sum_pivot.loc['2019-01-01':'2019-04-29']
fares_sum_pivot.index = pd.to_datetime(fares_sum_pivot.index)
fares_sum_pivot_week_df = fares_sum_pivot.resample("W").sum()
```

To create the line graph, I used the object-oriented interface method with the df.plot() function and formatted it with the Matplotlib fivethirtyeight graph style.

```
fig, ax = plt.subplots(figsize=(12,6))
fares_sum_pivot_week_df.plot(ax=ax)
ax.set_xlabel('Date')
ax.set_ylabel('Fare ($USD)')
ax.set_title("Total Fare by City Type")

from matplotlib import style
style.use('fivethirtyeight')
```

The output from running the script produces the following graph:

![Total Fare by City Type](./Resources/total_fare_by_type_graph.png)

As we can see from the graph, the line graph shows that consistently over time urban has the highest total fare, suburban the next highest, and urban the lowest. There is also a noticeable slight peak in total fares for all three city types near the middle to end of February. This could be explained by the holidays during that time, most notably Valentine's Day and President's Day which creates a long weekend for many people.

## Summary
Based on the results of the analysis, I would suggest three business recommendations to the PyBer CEO for addressing the disparities among the city types:

1. PyBer could offer a subscription service with a fixed fee. In urban areas, we see that the demand for a PyBer ride is much lower than the supply of PyBer drivers. As there is a high amount of commuters in urban areas and many people within cities do not own cars, a subscription service could incentivize more people to use PyBer as the costs are more predictable for the consumer.
2. PyBer could offer more bonuses and incentives for being a PyBer driver. For example, PyBer could create a program where drivers receive a bonus for driving a certain amount of people daily or weekly. This could potentially increase the amount of PyBer drivers which is needed in rural and subruban areas where the demand for a PyBer ride is higher than the supply PyBer drivers.
3. In the near future, PyBer could look to begin investing in and building autonomous vehicles. With autonomous vehicles, PyBer can almost instantly control the supply of PyBer drivers in a given area, putting vehicles where they are needed most or removing them from areas where there are too many drivers. Autonomous vehicles could also help reduce costs associated with paying human drivers.
