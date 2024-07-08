# Airbnb Listing Analysis

This GitHub project demonstrates a comprehensive analysis of Airbnb listings in Paris, focusing on data profiling, preparation, and visualization to understand pricing factors and the impact of 2015 regulations. The project involves exploring and profiling the data to address quality issues, preparing the data for visualization, and creating visualizations to identify key insights and recommendations.

---

## Table of Contents

1. [Project Description](#project-description)
2. [Objectives](#objectives)
   - [Objective 1: Profile & QA the Data](#objective-1-profile--qa-the-data)
   - [Objective 2: Prepare for Visualization](#objective-2-prepare-for-visualization)
   - [Objective 3: Visualize the Data](#objective-3-visualize-the-data)
3. [Data Import Process](#data-import-process)
4. [Data Profiling and Quality Assurance](#data-profiling-and-quality-assurance)
5. [Data Preparation for Visualization](#data-preparation-for-visualization)
6. [Data Visualization](#data-visualization)
7. [Insights and Recommendations](#insights-and-recommendations)

---

## Project Description

As a Performance Analyst for Airbnb, you are tasked with analyzing Airbnb listings in Paris to understand pricing factors and evaluate the impact of regulations adopted in 2015 on the Paris market. This project involves exploring and profiling the data, preparing it for visualization, and creating visualizations to identify key insights and recommendations.

---

## Objectives

### Objective 1: Profile & QA the Data

- Import/Open the `Listings.csv` file.
- Cast any date columns as a datetime format.
- Filter the data to rows where the city is Paris, keeping only the columns `host_since`, `neighbourhood`, `city`, `accommodates`, and `price`.
- QA the Paris listings data: check for missing values, and calculate the minimum, maximum, and average for each numeric field.

### Objective 2: Prepare for Visualization

- Create a table named `paris_listings_neighbourhood` that groups Paris listings by `neighbourhood` and calculates the mean price for each neighborhood, sorted from lowest to the highest average price.
- Create a table named `paris_listings_accommodations`, filtered to the most expensive neighborhood in Paris, grouped by the `accommodates` column, and containing the mean price for each value of `accommodates`, sorted from lowest to the highest average price.
- Create a table named `paris_listings_over_time`, grouped by the year of the `host_since` column. Calculate a count of rows, representing the total number of new hosts, and the average price for each year.

### Objective 3: Visualize the Data

- Create a horizontal bar chart of the average price by neighborhood in Paris. Add a title and change axis labels as needed.
- Create a horizontal bar chart of the average price by `accommodates` in Paris' most expensive neighborhood. Add a title and change axis labels as needed.
- Create two line charts: one for the count of new hosts over time, and one for average price. Set the y-axis limit to 0, add a title, and change axis labels as needed.
- Based on your findings, provide insights about the impact of the 2015 regulations on new hosts and prices.
- **Challenge**: Create a dual-axis line chart containing new hosts and average price over time.

---

## Data Import Process

1. **I imported the Listings.csv File**
   - I used the **Pandas** library to import/open the `Listings.csv` file.
   - Used the **encoding='ISO-8859-1'** and **low_memory=False** to read the file
   - The **encoding='ISO-8859-1'** parameter in ```csv_read``` function assigns meaning to all 256 bytes, including ASCII and non-ASCII characters
   - The **low_memory=False** means that Python will read the entire file once and not in chunks, especially for a large dataset that may have discrepancies in datatype in the columns. For instance, the **host since** column was read as an object when it is a date.
  
```Python
import pandas as pd

listings = pd.read_csv('/content/drive/MyDrive/Airbnb Data/Listings.csv', encoding='ISO-8859-1', low_memory=False)

listings.info()
```

Column **host since** is an object

![pic1](https://github.com/leon-madara/AirBnB_Paris/assets/147078093/2605a9d2-5a15-483e-8874-9035ff51727d)


2. The second task was to convert the `host since` column from an object to a date

```Python
listings["host_since"] = pd.to_datetime(listings["host_since"])

listings.info()
```

![pic2](https://github.com/leon-madara/AirBnB_Paris/assets/147078093/d78ab561-aba3-42e9-a870-8c17ad61538e)

It is now in the date format

---

## Data Profiling and Quality Assurance

1. **Filter the Data**
   - I begin by filtering the data to rows where the city is Paris.
   - Keep only the columns: `host_since`, `neighbourhood`, `city`, `accommodates`, and `price`.

```Python
Paris_listings = (
    listings.query("city == 'Paris'")
    .loc[:, ["host_since", "neighbourhood", "city", "accommodates", "room_type", "price"]]
)

Paris_listings.info()
```
The Ouput

![pic3](https://github.com/leon-madara/AirBnB_Paris/assets/147078093/4f7ccd3a-912c-4c38-9a7f-8fc22db0052d)


2. **Quality Assurance**
   - Check for missing values.
   - Calculate the minimum, maximum, and average for each numeric field (`accommodates` and `price`).

Columns `neighbourhood`, `city`, `accommodates`, and `price` all have 64690 **Non Nulls** matching the 64690 Entries of the 279711.
It means that only the `host since` column has **33** nulls

Verified by the code:

```Python
Paris_listings.isna().sum()
```

The Output

![pic4](https://github.com/leon-madara/AirBnB_Paris/assets/147078093/42852387-797d-4ad5-961e-b09647f67a46)

They are all of dtype: int64

###
I did a further analysis to evaluate the data using the `describe()` code

```Python
Paris_listings[["accommodates", "price"]].describe().round(2)
```

The Ouput

![pic5](https://github.com/leon-madara/AirBnB_Paris/assets/147078093/e3620706-cc34-4a4e-b58b-7402ba6d3427)

---

## Data Preparation for Visualization

1. **Group by Neighbourhood**
   - I created a table named `paris_listings_neighbourhood`.
   - Group by `neighbourhood` and calculated the mean price for each neighborhood, rounded off to the nearest 2 decimals.
   - I then Sorted the table from the lowest to the highest average price.

```Python
Paris_listings_neighbourhood = (
    Paris_listings
    .groupby("neighbourhood")
    .agg({"price": "mean"}).round(2)
    .sort_values("price")
)
#lowest to highest
Paris_listings_neighbourhood.head()
```
The Output

![pic6](https://github.com/leon-madara/AirBnB_Paris/assets/147078093/df9facb7-fc9e-436d-b51d-8903dba7dcae)


2. **Group by Accommodations**
   - I created a table named `paris_listings_accommodations`.
   - Filtered it to the most expensive neighborhood in Paris.
   - Group by the `accommodates` column and calculated the mean price for each value of `accommodates`.
   - Sorted the table from the lowest to the highest average price.

```Python
Paris_listings_accommodates = (
    Paris_listings
    .query("neighbourhood == 'Elysee'")
    .groupby("accommodates")
    .agg({"price": "mean", "neighbourhood": "count"}).round(2)
    .sort_values("price")
)
# lowest to highest avg price
Paris_listings_accommodates.head()
```

The Output

![pic7](https://github.com/leon-madara/AirBnB_Paris/assets/147078093/24d90b00-3c5d-4594-9531-ba01e9bb8665)

###
The minimum is 0 because of potential data entry errors. 
To double check the errors, I used the code:

```Python
Paris_listings.query("price == 0").count()
```

Output

![pic8](https://github.com/leon-madara/AirBnB_Paris/assets/147078093/8b4e4ea0-4ba8-4d1b-ad8a-8e5e3c0d7040)

#### Insight
   - It suggests a correlation on the values with 0.
   - However, since its only 62 entries, which is less than 1% **(0.095%)** of the Paris data, we can leave it or ignore it, won't make a difference to our findings.


3. **Group by Year of Host Since**
   - I created a table named `Paris_listings_over_time`.
   - Grouped by the year of the `host_since` column.
   - Then I callculated a count of rows, representing the total number of new hosts, and the average price for each year.

```Python
Paris_listings_over_time = (
    Paris_listings
    .set_index("host_since")
    .resample("Y") 
    .agg({
        "neighbourhood": "count", 
        "price": "mean"
    })
)

Paris_listings_over_time.head()
```

I used the `.set_index()` to use the `host since` column as the index of our table followed by the `.resample("Y")` to resample or arrange the data based on a year basis
I then used the `.agg` function to perform an aggregate of `count` on **neighbourhood** column and `mean` on **price** column

---

## Data Visualization

1. **Average Price by Neighbourhood**
   - I began by importing the `Seabon` library in order to create a horizontal bar chart of the average price by neighborhood in Paris.
   - I then added a title and change axis labels as needed.

The code

```Python

import seaborn as sns 
    
(Paris_listings_neighbourhood
 .plot
 .barh(
     title="Average Listing Price by Paris Neighbourhood",
     xlabel="Price per Night (Euros)",
     ylabel="Neighbourhood", #.capitalize(),
     color="blue",
     legend=False
 )
)

sns.despine()
```

Output

![image](https://github.com/leon-madara/AirBnB_Paris/assets/147078093/58d19fc7-d290-4e78-ae13-69c664741c84)

I used the `sns.despine()` to remove the top and right spines (borders) from the plot, enhancing its visual appearance.

2. **Average Price by Accommodations**
   - Here, I created a horizontal bar chart of the average price by `accommodates` in Paris' most expensive neighborhood.
   - Then I added a title and changed axis labels as needed.

```Python

(Paris_listings_accommodates
 .plot
 .barh(
     title="Average Listing Price by Accommodation Number",
     xlabel="Price per Night (Euros)",
     ylabel="Accommodation Capacity", #.capitalize(),
     color="green",
     legend=False
 )
)

sns.despine()
```

Output

![image](https://github.com/leon-madara/AirBnB_Paris/assets/147078093/7f044caf-0768-4144-8fc2-e9fee4f25409)

3. **Line Charts Over Time**
   - I created a line chart of the count of new hosts over time.
   - And then I followed up with a line chart of the average price over time.
   - I set the y-axis limit to 0, added a title, and changed axis labels as needed.

```Python
Paris_listings_over_time["neighbourhood"].plot(
    title="New AirBnB Hosts in Paris Over Time",
    ylabel="New Hosts",
    color="darkblue"
)

sns.despine()
```

Output: New AirBnB Hosts in Paris Over Time

![image](https://github.com/leon-madara/AirBnB_Paris/assets/147078093/16ed9e60-0b8f-42ac-a198-eb1bea6ab328)

2nd Code

```Python
Paris_listings_over_time["price"].plot(
    title="Average AirBnB Price in Paris Over Time",
    ylabel="Average Price (Euros)",
    color="darkblue"
)

sns.despine()
```

Output: Average AirBnB Price in Paris Over Time

![image](https://github.com/leon-madara/AirBnB_Paris/assets/147078093/c3e3bcf8-9035-41f7-a380-f301e8e1d21c)

4. **Dual-Axis Line Chart**
   - I created a dual-axis line chart that contains both new hosts and average price over time (Challenge).
   - This was significantly challenging.
   - I began by importing the `matplotlib.pyplot` library

```Python
import matplotlib.pyplot as plt

fig, ax = plt.subplots()

ax.plot(
    Paris_listings_over_time.index,
    Paris_listings_over_time["neighbourhood"],
    label="New Hosts",
    color="darkblue"
)

ax.set_ylabel("New Hosts")

ax2 = ax.twinx()

ax2.plot(
    Paris_listings_over_time.index,
    Paris_listings_over_time["price"],
    label="Average Price",
    color="lightgreen"
)

ax2.set_ylabel("Average Price (Euros)")

ax.set_title("Correlation Between 2015 Regulation And New Hosts And Prices in Paris")

plt.tight_layout()
```
1. I used the `matplotlib.pyplot` to create the graph.
2. I then used `fig, ax = plt.subplots()` to create a figure and a set of subplots (axes). `fig` is the figure object, and `ax` is the primary axes.
3. The `ax.plot()` section plots the number of new hosts over time:
    - `Paris_listings_over_time.index` provides the x-axis values (years).
    - `Paris_listings_over_time["neighbourhood"]` provides the y-axis values (number of new hosts).
    - `label` sets the label for the line (used in the legend).
    - `color` sets the color of the line.
  
4. Then I used `ax.set_ylabel("New Hosts")` to set the y-axis label for the primary axis to "New Hosts".
5. I then use `ax2 = ax.twinx()` to create the **secondary y-axis** that shares the **same x-axis as the primary axis**.
    - This allows for plotting another dataset with a different y-axis scale.
  
6. I used a similar code by with the new parameters `price` and `average price`
7. I used `ax2.set_ylabel("Average Price (Euros)")` to set the y-axis label for the secondary axis to "Average Price (Euros)".
8. Finally, I used `ax.set_title("Correlation Between 2015 Regulation And New Hosts And Prices in Paris")` to set the title for the chart
9. And `plt.tight_layout()` to adjust the layout of the plot and ensure that labels and titles fit within the figure area. 
---

Output: Correlation Between 2015 Regulation And New Hosts And Prices in Paris

![image](https://github.com/leon-madara/AirBnB_Paris/assets/147078093/c2bc5c46-3ebb-406c-91e7-2db9e63efd99)

5. There is an alternative for creating the **Dual Axis Line** chart
     - Begin with importing the `plotly.graph_objects` library
       
```Python
import plotly.graph_objects as go

# Create a figure
fig = go.Figure()

# Add the first trace for new hosts
fig.add_trace(
    go.Scatter(
        x=Paris_listings_over_time.index,
        y=Paris_listings_over_time["neighbourhood"],
        mode='lines',
        name='New Hosts',
        line=dict(color='darkblue')
    )
)

# Add the second trace for average price
fig.add_trace(
    go.Scatter(
        x=Paris_listings_over_time.index,
        y=Paris_listings_over_time["price"],
        mode='lines',
        name='Average Price',
        line=dict(color='lightgreen'),
        yaxis='y2'
    )
)

# Update layout to include two y-axes
fig.update_layout(
    title="Correlation Between 2015 Regulation And New Hosts And Prices in Paris",
    xaxis_title="Year",
     xaxis=dict(
            showgrid=False  # Remove grid lines from x-axis
    ),
    yaxis=dict(
        title="New Hosts",
        showgrid=False
    ),
    yaxis2=dict(
        title="Average Price (Euros)",
        overlaying='y',
        side='right',
        showgrid=False
    ),
    legend=dict(
        x=0.1,
        y=1.1,
        orientation="h"
    )
)

# Display the plot
fig.show()
```
Output: 

![pic10](https://github.com/leon-madara/AirBnB_Paris/assets/147078093/05c9b22e-91ed-4579-9fd0-d056172733e6)


## Insights and Recommendations

Based on the visualizations, there is a clear correlation between the 2015 regulations and the number of new hosts entering the market, as well as the average prices. After the regulations were implemented, the number of new entries into the market decreased, which correlates with an increase in average prices. As the number of new hosts reduced, the prices went up.

### Recommendations for Airbnb Leadership

- **Monitor Regulatory Impact**: Continuously monitor the impact of regulations on new host entries and pricing. This will help in understanding market dynamics and adjusting strategies accordingly.
- **Adjust Pricing Strategies**: With fewer new hosts entering the market, consider strategies to manage and potentially capitalize on the higher average prices.
- **Support New Hosts**: Implement initiatives to support and encourage new hosts to join the platform, which may help in stabilizing prices and maintaining a healthy market balance.
---

Feel free to explore the repository for detailed insights and visualizations on the Airbnb listings in Paris. 😊
