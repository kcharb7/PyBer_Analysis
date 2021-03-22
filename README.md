# PyBer with Matplotlib
## Overview
### *Purpose*
The purpose of this project was to analyze rideshare data for the ride-sharing app company PyBer from January to May of 2019 and create compelling visualizations for the CEO. Specifically, I was to achieve the following deliverables:
-	Import data into a Pandas DataFrame
-	Merge the DataFrames
-	Create a bubble chart that showcases the average fare versus the total number of rides with bubble size based on the total number of drivers for each city type (i.e., urban, suburban, and rural)
-	Determine the mean, median, and mode for the total number of rides for each city type, the average fares for each city type, and the total number of drivers for each city type
-	Create box-and-whisker plots that visualize and determine any outliers for the number of rides for each city type, the fares for each city type, and the number of drivers for each city type
-	Create pie charts that visualize the percent of total fares, the percent of total rides, and the percent of total drivers

## Analysis
### *Load CSV Files*
To begin, I created a new jupyter notebook file named PyBer and imported the Pandas and Matplotlib libraries with the Pyplot module:
```
# Add Matplotlib inline magic command
%matplotlib inline
# Dependencies and Setup
import matplotlib.pyplot as plt
import pandas as pd
```
Then, I declared variables to connect to my csv files:
```
# Files to load
city_data_to_load = "Resources/city_data.csv"
ride_data_to_load = "Resources/ride_data.csv"
```

### *Read the City and Ride Data Files*
Using the pd.read_csv, I read my city_data.csv file and stored it in a new DataFrame:
```
# Read the city data file and store it in a pandas DataFrame.
city_data_df = pd.read_csv(city_data_to_load)
city_data_df.head(10)
```
The same code was used to read the ride_data.csv file:
```
# Read the ride data file and store it in a pandas DataFrame.
ride_data_df = pd.read_csv(ride_data_to_load)
ride_data_df.head(10)
```

### *Inspect the City Data DataFrame*
First I wanted to determine the number of rows in the city_data_df DataFrame that were not null using the df.count() method:
```
# Get the columns and the rows that are not null.
city_data_df.count()
```
The output indicated there were 120 rows with data:

![city_data_df.count().png](https://github.com/kcharb7/PyBer_Analysis/blob/main/Analysis/city_data_df.count().png)

Additionally running the following code determined there were no null values:
```
# Get the columns and the rows that are not null.
city_data_df.isnull().sum()
```
For my analyses I planned to perform mathematical calculations on the driver_count column and thus needed to ensure that that column was a numerical data type. To do so, I used the dtypes on the DataFrame:
```
# Get the data types of each column.
city_data_df.dtypes
```
The output confirmed that the data type for the driver_count column was an integer.

Next, I determined how many data points there were for each type of city by first using the unique() method on the “type” column to get a list of the types of cities: 
```
# Get the unique values of the type of city.
city_data_df["type"].unique()
```
Within the “type” column there were 3 types of cities: urban, suburban, and rural.

Using the sum() method, I determined the number of data point for the urban cities:
```
# Get the number of data points from the Urban cities.
sum(city_data_df["type"]=="Urban")
```
There were 66 data points for the urban cities. 

Using the same code, I found that there were 36 and 18 data points for suburban and rural cities, respectively. 

### *Inspect the Ride Data DataFrame*
To determine the number of rows in the ride_data_df and ensure there were no null values, I used the same code as above:
```
# Get the columns and the rows that are not null.
ride_data_df.count()
```
The output indicated that each column within the DataFrame had 2,375 rows of data.

![ride_data_df.count().png](https://github.com/kcharb7/PyBer_Analysis/blob/main/Analysis/ride_data_df.count()%5D.png)

 To ensure there were no null values, I additionally ran the following code:
```
# Get the columns and the rows that are not null.
ride_data_df.isnull().sum()
```
The output confirmed there were zero null values in the three columns. 

For my calculations, I needed to determine whether the “fare” and “ride_id” columns were numerical data types and thus added the following code:
```
# Get the data types of each column.
ride_data_df.dtypes
```
The output indicated that the data within the “fare” column was floating-point decimal and the data within the “ride_id” column was an integer. 

### *Merge DataFrames*
As the data within both DataFrames looked good, I merged the DataFrames on the “city” column, adding the city_data_df to the end of the ride_data_df using the following code:
```
# Combine the data into a single dataset
pyber_data_df = pd.merge(ride_data_df, city_data_df, how="left", on=["city", "city"])

# Display the DataFrame
pyber_data_df.head()
```
The new pyber_data_df DataFrame looked as follows:

![PyBer_DataFrame.png](https://github.com/kcharb7/PyBer_Analysis/blob/main/Analysis/PyBer_DataFrame.png)

### *Create DataFrames for Each Type of City*
In order to create a bubble chart with the average fare for each type of city on the y-axis, the total number of rides for each type of city on the x-axis, and the size of each marker correlated to the average number of drivers for each type of city, I began by creating three separate DataFrames for each city type so I could perform calculations to create the bubble chart.

First, to create the urban city DataFrame, I filtered the pyber_data_df DataFrame where the “type” column is equal to “urban”:
```
# Create the Urban city DataFrame.
urban_cities_df = pyber_data_df[pyber_data_df["type"] == "Urban"]
urban_cities_df.head()
```
The first five rows of the urban_cities_df looked as follows:

![urban_cities_df.png](https://github.com/kcharb7/PyBer_Analysis/blob/main/Analysis/urban_cities_df.png)

The same method was used to create the suburban and rural city DataFrames:
```
# Create the Suburban and Rural city DataFrames.
suburban_cities_df = pyber_data_df[pyber_data_df["type"] == "Suburban"]
rural_cities_df = pyber_data_df[pyber_data_df["type"] == "Rural"]
```
### *Number of Rides for Each City Type*
To get the number of rides for each city, I needed to create a Series with the name of the city as the index and the column as the number of rides for that city. To do this, I used the groupby() function and applied the count() method to the “ride_id” column within the urban_cities_df DataFrame:
```
# Get the number of rides for urban cities.
urban_ride_count = urban_cities_df.groupby(["city"]).count()["ride_id"]
urban_ride_count.head()
```
The same method was used for the suburban and rural DataFrames:
```
# Create the suburban and rural ride count.
suburban_ride_count = suburban_cities_df.groupby(["city"]).count()["ride_id"]

rural_ride_count = rural_cities_df.groupby(["city"]).count()["ride_id"]
```

### *Average Fare for Each City Type*
To get the average city fare for each city type, I again used the groupby() function and chained the mean() method to get the average of the “fare” column:
```
# Get average fare for each city in the urban cities.
urban_avg_fare = urban_cities_df.groupby(["city"]).mean()["fare"]

# Get average fare for each city in the suburban and rural cities.
suburban_avg_fare = suburban_cities_df.groupby(["city"]).mean()["fare"]
rural_avg_fare = rural_cities_df.groupby(["city"]).mean()["fare"]
```

### *Average Number of Drivers for Each City Type*
To get the average number of drivers for each city, I used the groupby() function and applied the mean() method to the “driver_count” column within each of the DataFrames:
```
# Get the average number of drivers for each urban city.
urban_driver_count = urban_cities_df.groupby(["city"]).mean()["driver_count"]
urban_driver_count.head()

# Get the average number of drivers for each city for the suburban and rural cities.
suburban_driver_count = suburban_cities_df.groupby(["city"]).mean()["driver_count"]
rural_driver_count = rural_cities_df.groupby(["city"]).mean()["driver_count"]
```

### *Create Bubble Charts*
I began by making the urban cities bubble chart using the MATLAB method with the x-axis as the urban_ride_count and the y-axis as the urban_avg_fare:
```
# Build the scatter plots for urban cities.
plt.scatter(urban_ride_count, urban_avg_fare)
```
Then, I edited the scatter plot so that the size of the markers would equal to the urban_driver_count:
```
# Build the scatter plots for urban cities.
plt.scatter(urban_ride_count,
      urban_avg_fare,
      s=urban_driver_count)
```
As some of the bubbles were too small, I further edited the marker sizes by increasing them by a factor of 10 and additionally added a black edge color to the circles with a line width of 1. Furthermore, to align with the company’s color scheme, I changed the bubbles color to coral and made them 20% transparent. A title, labels for the axes, a legend, and a grid were also added:
```
# Build the scatter plots for urban cities.
plt.scatter(urban_ride_count,
      suburban_avg_fare,
      s=10*suburban_driver_count, c="coral",
      edgecolor="black", linewidths=1,
      alpha=0.8, label="Urban")
plt.title("PyBer Ride-Sharing Data (2019)")
plt.ylabel("Average Fare ($)")
plt.xlabel("Total Number of Rides (Per City)")
plt.grid(True)
# Add the legend.
plt.legend()
```
The final chart looked as follows:

![urban_cities_scatter.png](https://github.com/kcharb7/PyBer_Analysis/blob/main/Analysis/urban_cities_scatter.png)

Repurposing the code, I made the bubble chart for the suburban cities:
```
# Build the scatter plots for suburban cities.
plt.scatter(suburban_ride_count,
      suburban_avg_fare,
      s=10*suburban_driver_count, c="skyblue",
      edgecolor="black", linewidths=1,
      alpha=0.8, label="Suburban")
plt.title("PyBer Ride-Sharing Data (2019)")
plt.ylabel("Average Fare ($)")
plt.xlabel("Total Number of Rides (Per City)")
plt.grid(True)
# Add the legend.
plt.legend()
```

![suburban_cities_scatter.png](https://github.com/kcharb7/PyBer_Analysis/blob/main/Analysis/suburban_cities_scatter.png)

Using the code again, I created the bubble chart for rural cities:
```
# Build the scatter plots for rural cities.
plt.scatter(rural_ride_count,
      rural_avg_fare,
      s=10*rural_driver_count, c="gold",
      edgecolor="black", linewidths=1,
      alpha=0.8, label="Rural")
plt.title("PyBer Ride-Sharing Data (2019)")
plt.ylabel("Average Fare ($)")
plt.xlabel("Total Number of Rides (Per City)")
plt.grid(True)
# Add the legend.
plt.legend()
```

![rural_cities_scatter.png](https://github.com/kcharb7/PyBer_Analysis/blob/main/Analysis/rural_cities_scatter.png)

### *Create a Bubble Chart for All Cities*
For better presentation and to show how each city type compared with one another, I combined the three bubble charts. To do this, I combined the three scatter plot code blocks into one cell in Jupyter Notebook:
```
# Add the scatter charts for each type of city.
plt.scatter(urban_ride_count,
      urban_avg_fare,
      s=10*urban_driver_count, c="coral",
      edgecolor="black", linewidths=1,
      alpha=0.8, label="Urban")

plt.scatter(suburban_ride_count,
      suburban_avg_fare,
      s=10*suburban_driver_count, c="skyblue",
      edgecolor="black", linewidths=1,
      alpha=0.8, label="Suburban")

plt.scatter(rural_ride_count,
      rural_avg_fare,
      s=10*rural_driver_count, c="gold",
      edgecolor="black", linewidths=1,
      alpha=0.8, label="Rural")

# Show the plot
plt.show()
```
Then, I edited the code to add a title, labels for the axes, a legend, and a grid. I additionally increased the font size of the axes labels and title, as well as enlarged the figure:
```
# Build the scatter charts for each city type.
plt.subplots(figsize=(10, 6))
plt.scatter(urban_ride_count,
      urban_avg_fare,
      s=10*urban_driver_count, c="coral",
      edgecolor="black", linewidths=1,
      alpha=0.8, label="Urban")

plt.scatter(suburban_ride_count,
      suburban_avg_fare,
      s=10*suburban_driver_count, c="skyblue",
      edgecolor="black", linewidths=1,
      alpha=0.8, label="Suburban")

plt.scatter(rural_ride_count,
      rural_avg_fare,
      s=10*rural_driver_count, c="gold",
      edgecolor="black", linewidths=1,
      alpha=0.8, label="Rural")

# Incorporate the other graph properties
plt.title("PyBer Ride-Sharing Data (2019)", fontsize=20)
plt.ylabel("Average Fare ($)", fontsize=12)
plt.xlabel("Total Number of Rides (Per City)", fontsize=12)
plt.grid(True)

# Add the legend.
plt.legend()
# Show the plot
plt.show()
```
Looking at the plot, however, I noticed that the markers within the legend were not uniform in size. So, I declared a variable for the legend and used the legendHandles[]._sizes to set the font size of the markers, adding this set of code in place of plt.legend():
```
# Create a legend
lgnd = plt.legend(fontsize="12", mode="Expanded",
         scatterpoints=1, loc="best", title="City Types")
lgnd.legendHandles[0]._sizes = [75]
lgnd.legendHandles[1]._sizes = [75]
lgnd.legendHandles[2]._sizes = [75]
lgnd.get_title().set_fontsize(12)
```
Finally, I added a note to the right of the chart to notify viewers that the size of the circles correlate with the driver count for each city using the plt.text() function:
```
# Incorporate a text label about circle size.
plt.text(42, 35, "Note: Circle size correlates with driver count per city.", fontsize="12")
```
The final chart looked as follows:

![Combined_scatter.png](https://github.com/kcharb7/PyBer_Analysis/blob/main/Analysis/Combined_scatter.png)

### *Summary Statistics for Number of Rides by City Type*
Using the describe() function, I gathered the summary statistics for the urban, suburban, and rural DataFrames:
```
# Get summary statistics.
urban_cities_df.describe()
suburban_cities_df.describe()
rural_cities_df.describe()
```
I additionally performed this code on the ride count for each city type:
```
# Get summary statistics.
urban_ride_count.describe()
suburban_ride_count.describe()
rural_ride_count.describe()
```

Urban:

![urban_summary.png](https://github.com/kcharb7/PyBer_Analysis/blob/main/Analysis/urban_summary.png)

Suburban:

![suburban_summary.png](https://github.com/kcharb7/PyBer_Analysis/blob/main/Analysis/suburban_summary.png)

Rural:

![rural_summary.png](https://github.com/kcharb7/PyBer_Analysis/blob/main/Analysis/rural_summary.png)

### *Summary Statistics for the Fare by City Type*
Next, I calculated the summary statistics for the average fares for each city type. To do this, I created a Series with all the fares from the “fare” column for the urban_cities_df DataFrame:
```
# Get the fares for the urban cities.
urban_fares = urban_cities_df["fare"]
urban_fares.head()
```
To get the mean and median, I used the NumPy mean and median functions. To get the mode, I used the SciPy statistics mode function. The code looked as follows:
```
# Calculate the measures of central tendency for the average fare for the urban cities.
mean_urban_fares = np.mean(urban_fares)
print(f"The mean fare price for urban trips is ${mean_urban_fares:.2f}.")

median_urban_fares = np.median(urban_fares)
print(f"The median fare price for urban trips is ${median_urban_fares:.2f}.")

mode_urban_fares = sts.mode(urban_fares)
print(f"The mode fare price for urban trips is {mode_urban_fares}.")
```
The mean fare price for urban trips is $24.53.
The median fare price for urban trips is $24.64.
The mode fare price for urban trips is ModeResult(mode=array([22.86]), count=array([5])).

The code was repurposed to calculate the mean, median, and mode for suburban cities:
```
# Get the fares for the suburban cities.
suburban_fares = suburban_cities_df["fare"]

# Calculate the measures of central tendency for the average fare for the suburban cities.
mean_suburban_fares = np.mean(suburban_fares)
print(f"The mean fare price for suburban trips is ${mean_suburban_fares:.2f}.")

median_suburban_fares = np.median(suburban_fares)
print(f"The median fare price for suburban trips is ${median_suburban_fares:.2f}.")

mode_suburban_fares = sts.mode(suburban_fares)
print(f"The mode fare price for suburban trips is {mode_suburban_fares}.")
```
The mean fare price for suburban trips is $30.97.
The median fare price for suburban trips is $30.75.
The mode fare price for suburban trips is ModeResult(mode=array([17.99]), count=array([3])).

Again, the code was repurposed to calculate the mean, median, and mode for rural cities:
```
# Get the fares for the rural cities.
rural_fares = rural_cities_df["fare"]

# Calculate the measures of central tendency for the average fare for the rural cities.
mean_rural_fares = np.mean(rural_fares)
print(f"The mean fare price for rural trips is ${mean_rural_fares:.2f}.")

median_rural_fares = np.median(rural_fares)
print(f"The median fare price for rural trips is ${median_rural_fares:.2f}.")

mode_rural_fares = sts.mode(rural_fares)
print(f"The mode fare price for rural trips is {mode_rural_fares}.")
```
The mean fare price for rural trips is $34.62.
The median fare price for rural trips is $37.05.
The mode fare price for rural trips is ModeResult(mode=array([37.05]), count=array([2])).

### *Summary Statistics for the Number of Drivers by City Type*
To perform summary statistics for the number of drivers by city type, I created a Series for each city type based on the “drvier_count” column in each city type DataFrame:
```
# Get the driver count data for each city.
urban_drivers = urban_cities_df['driver_count']
suburban_drivers = suburban_cities_df['driver_count']
rural_drivers = rural_cities_df['driver_count']
```
Using the NumPy and SciPy statistics module, I calculated the mean, median, and mode for each city type:
```
# Calculate the measures of central tendency for the driver count for the urban cities.
mean_urban_drivers = np.mean(urban_drivers)
print(f"The mean driver count for urban cities is {mean_urban_drivers:.2f}.")

median_urban_drivers = np.median(urban_drivers)
print(f"The median driver count for urban cities is {median_urban_drivers:.2f}.")

mode_urban_drivers = sts.mode(urban_drivers)
print(f"The mode driver count for urban cities is {mode_urban_drivers}.")

# Calculate the measures of central tendency for the driver count for the suburban cities.
mean_suburban_drivers = np.mean(suburban_drivers)
print(f"The mean driver count for suburban cities is {mean_suburban_drivers:.2f}.")

median_suburban_drivers = np.median(suburban_drivers)
print(f"The median driver count for suburban cities is {median_suburban_drivers:.2f}.")

mode_suburban_drivers = sts.mode(suburban_drivers)
print(f"The mode driver count for suburban cities is {mode_suburban_drivers}.")

# Calculate the measures of central tendency for the driver count for the rural cities.
mean_rural_drivers = np.mean(rural_drivers)
print(f"The mean driver count for rural cities is {mean_rural_drivers:.2f}.")

median_rural_drivers = np.median(rural_drivers)
print(f"The median driver count for rural cities is {median_rural_drivers:.2f}.")

mode_rural_drivers = sts.mode(rural_drivers)
print(f"The mode driver count for rural cities is {mode_rural_drivers}.")
```
The mean driver count for urban cities is 36.68.
The median driver count for urban cities is 37.00.
The mode driver count for urban cities is ModeResult(mode=array([39]), count=array([86])).
The mean driver count for suburban cities is 13.71.
The median driver count for suburban cities is 16.00.
The mode driver count for suburban cities is ModeResult(mode=array([20]), count=array([79])).
The mean driver count for rural cities is 4.30.
The median driver count for rural cities is 4.00.
The mode driver count for rural cities is ModeResult(mode=array([1]), count=array([32])).

### *Create Box-and-Whisker Plots*
To visualize the summary statistics and determine if there are any outliers, I created a box-and-whiskers plot for the urban cities ride count:
```
# Create a box-and-whisker plot for the urban cities ride count.
x_labels = ["Urban"]
fig, ax = plt.subplots()
ax.boxplot(urban_ride_count, labels=x_labels)
# Add the title, y-axis label and grid.
ax.set_title('Ride Count Data (2019)')
ax.set_ylabel('Number of Rides')
ax.set_yticks(np.arange(10, 41, step=2.0))
ax.grid()
plt.show()
```
The urban ride count data box-and-whisker plot looked as follows:

![urban_box.png](https://github.com/kcharb7/PyBer_Analysis/blob/main/Analysis/urban_box.png)

The maximum and minimum data points were 39 and 12, respectively, and there was one outlier close to 40. The median was 24 and the standard deviation was about 5. 

The code was repurposed for the suburban driver count:
```
# Create a box-and-whisker plot for the suburban cities ride count.
x_labels = ["Suburban"]
fig, ax = plt.subplots()
ax.boxplot(suburban_ride_count, labels=x_labels)
# Add the title, y-axis label and grid.
ax.set_title('Ride Count Data (2019)')
ax.set_ylabel('Number of Rides')
ax.set_yticks(np.arange(10, 41, step=2.0))
ax.grid()
plt.show()
```

![suburban_box.png](https://github.com/kcharb7/PyBer_Analysis/blob/main/Analysis/suburban_box.png)

The box and whisker plot showed that the maximum and minimum data points were 27 and 9, respectively, and there were not outliers. The median was 17 and the standard deviation was about 4. 

The code was similarly used for the rural cities ride count:
```
# Create a box-and-whisker plot for the rural cities ride count.
x_labels = ["Rural"]
fig, ax = plt.subplots()
ax.boxplot(rural_ride_count, labels=x_labels)
# Add the title, y-axis label and grid.
ax.set_title('Ride Count Data (2019)')
ax.set_ylabel('Number of Rides')
ax.set_yticks(np.arange(2, 14, step=1.0))
ax.grid()
plt.show() 
```

![rural_box.png](https://github.com/kcharb7/PyBer_Analysis/blob/main/Analysis/rural_%20box.png)

The box and whisker plot showed that the maximum and minimum data points were 12 and 3, respectively, and there were not outliers. The median was 6 and the standard deviation was about 3. 

To combine all the box-and-whisker plots onto one chart, I used the following code which additionally modified the size of the chart, title, and axes labels:
```
# Add all ride count box-and-whisker plots to the same graph.
x_labels = ["Urban", "Suburban","Rural"]
ride_count_data = [urban_ride_count, suburban_ride_count, rural_ride_count]
fig, ax = plt.subplots(figsize=(10, 6))
ax.set_title('Ride Count Data (2019)',fontsize=20)
ax.set_ylabel('Number of Rides',fontsize=14)
ax.set_xlabel("City Types",fontsize=14)
ax.boxplot(ride_count_data, labels=x_labels)
ax.set_yticks(np.arange(0, 45, step=3.0))
ax.grid()
# Save the figure.
plt.savefig("analysis/Fig2.png")
plt.show()
```

The final plot look as follows:

![Fig2.png](https://github.com/kcharb7/PyBer_Analysis/blob/main/Analysis/Fig2.png)

According to the plot, the average number of rides in the rural cities was approximately 4- and 3.5-times lower than the urban and suburban cities, respectively. Only the urban ride count data contained an outlier at 39. Based on this information, I determined which city had the highest rider count within urban cities by determining which urban_ride_count equals 39 and filtering the urban_ride_count Series based on which values were “True”:
```
# Get the city that matches 39.
urban_city_outlier = urban_ride_count[urban_ride_count==39].index[0]
print(f"{urban_city_outlier} has the highest rider count.")
```
West Angela had the highest rider count.

Next, I created a box-and-whisker plot for the urban ride fare data with the following code:
```
# Create a box-and-whisker plot for the urban fare data.
x_labels = ["Urban"]
fig, ax = plt.subplots()
ax.boxplot(urban_fares, labels=x_labels)
# Add the title, y-axis label and grid.
ax.set_title('Ride Fare Data (2019)')
ax.set_ylabel('Fare($USD)')
ax.set_yticks(np.arange(0, 51, step=5.0))
ax.grid()
plt.show()
print("Summary Statistics")
urban_fares.describe()
```

![u_fare_box.png](https://github.com/kcharb7/PyBer_Analysis/blob/main/Analysis/u_fare_box.png)

The code was repurposed for the suburban ride fare data:
```
# Create a box-and-whisker plot for the suburban fare data.
x_labels = ["Subrban"]
fig, ax = plt.subplots()
ax.boxplot(suburban_fares, labels=x_labels)
# Add the title, y-axis label and grid.
ax.set_title('Ride Fare Data (2019)')
ax.set_ylabel('Fare($USD)')
ax.set_yticks(np.arange(0, 51, step=5.0))
ax.grid()
plt.show()
print("Summary Statistics")
suburban_fares.describe()
```

![s_fare_box.png](https://github.com/kcharb7/PyBer_Analysis/blob/main/Analysis/s_fare_box.png)

The code was again used for the rural ride fare data:
```
# Create a box-and-whisker plot for the rural fare data.
x_labels = ["Rural"]
fig, ax = plt.subplots()
ax.boxplot(rural_fares, labels=x_labels)
# Add the title, y-axis label and grid.
ax.set_title('Ride Fare Data (2019)')
ax.set_ylabel('Fare($USD)')
ax.set_yticks(np.arange(0, 51, step=5.0))
ax.grid()
plt.show()
print("Summary Statistics")
rural_fares.describe()
```

![r_fare_box.png](https://github.com/kcharb7/PyBer_Analysis/blob/main/Analysis/r_fare_box.png)

Next, I combined the fare data for all three city types into one plot using a similar code as was used above for the ride count data:
```
# Add all fare box-and-whisker plots to the same graph.
x_labels = ["Urban", "Suburban","Rural"]
fare_data = [urban_fares, suburban_fares, rural_fares]
fig, ax = plt.subplots(figsize=(10, 6))
ax.set_title('Ride Fare Data (2019)',fontsize=20)
ax.set_ylabel('Fare($USD)',fontsize=14)
ax.set_xlabel("City Types",fontsize=14)
ax.boxplot(fare_data, labels=x_labels)
ax.set_yticks(np.arange(0, 55, step=5.0))
ax.grid()
# Save the figure.
plt.savefig("analysis/Fig3.png")
plt.show()
```

![Fig3.png](https://github.com/kcharb7/PyBer_Analysis/blob/main/Analysis/Fig3.png)

According to the plot, the average fare for rides in the rural cities was approximately $11 and $5 more per ride than the urban and suburban cities, respectively. There were not outliers.

Following this, I created a box-and-whisker plot for the driver count data with summary statistics. Starting with the urban_drivers Series, I produced the following code and output:
```
# Create the box-and-whisker plot for the urban driver count data.
x_labels = ["Urban"]
fig, ax = plt.subplots()
ax.boxplot(urban_drivers,labels=x_labels)
# Add the title, y-axis label and grid.
ax.set_title('Driver Count Data (2019)')
ax.set_ylabel('Number of Drivers)')
ax.set_yticks(np.arange(0, 90, step=5.0))
ax.grid()
plt.show()
print("Summary Statistics")
urban_drivers.describe()
```

![u_driver_box.png](https://github.com/kcharb7/PyBer_Analysis/blob/main/Analysis/u_driver_box.png)

The code was repurposed for the suburban_drivers and rural_drivers Series:
```
# Create the box-and-whisker plot for the suburban driver count data.
x_labels = ["Subrban"]
fig, ax = plt.subplots()
ax.boxplot(suburban_drivers,labels=x_labels)
# Add the title, y-axis label and grid.
ax.set_title('Driver Count Data (2019)')
ax.set_ylabel('Number of Drivers)')
ax.set_yticks(np.arange(0, 30, step=2.0))
ax.grid()
plt.show()
print("Summary Statistics")
suburban_drivers.describe() 
```

![s_driver_box.png](https://github.com/kcharb7/PyBer_Analysis/blob/main/Analysis/s_driver_box.png)

```
# Create the box-and-whisker plot for the rural driver count data.
x_labels = ["Rural"]
fig, ax = plt.subplots()
ax.boxplot(rural_drivers,labels=x_labels)
# Add the title, y-axis label and grid.
ax.set_title('Driver Count Data (2019)')
ax.set_ylabel('Number of Drivers)')
ax.set_yticks(np.arange(0, 15, step=1.0))
ax.grid()
plt.show()
print("Summary Statistics")
rural_drivers.describe()
```

![r_driver_box.png](https://github.com/kcharb7/PyBer_Analysis/blob/main/Analysis/r_driver_box.png)

Next, I combined the driver data for all three city types into one plot using a similar code as was used above for the ride count and fare data:
```
# Add all driver count box-and-whisker plots to the same graph.
x_labels = ["Urban", "Suburban","Rural"]
fare_data = [urban_drivers, suburban_drivers, rural_drivers]
fig, ax = plt.subplots(figsize=(10, 6))
ax.set_title('Driver Count Data (2019)',fontsize=20)
ax.set_ylabel('Number of Drivers',fontsize=14)
ax.set_xlabel("City Types",fontsize=14)
ax.boxplot(fare_data, labels=x_labels)
ax.set_yticks(np.arange(0, 75, step=5.0))
ax.grid()
# Save the figure.
plt.savefig("analysis/Fig4.png")
plt.show()
```

![Fig4.png](https://github.com/kcharb7/PyBer_Analysis/blob/main/Analysis/Fig4.png)

According to the plot, the average number of drivers in rural cities was approximately 9- and 4- times less per city than the urban and suburban cities, respectively. There were not outliers.

### *Percentage of Total Fares by City Type*
To get the percentage of fares for each city type, I first needed to determine the total fares for each city type and the total for all the fares for all the city types. To get the total fares for city type, I created a Series where the index was the city type from the pyber_data_df DataFrame and the column was the sum of the fares for that city type:
```
# Get the sum of the fares for each city type.
sum_fares_by_type = pyber_data_df.groupby(["type"]).sum()["fare"]
sum_fares_by_type
```

![fares_city_type.png](https://github.com/kcharb7/PyBer_Analysis/blob/main/Analysis/fares_city_type.png)

To get the total fares, I used the sum() method on the fare column within the pyber_data_df DataFrame:
```
# Get the sum of all the fares.
total_fares = pyber_data_df["fare"].sum()
total_fares
```
The total fares was 63538.64. 

With these values, I then calculated the percentage of total fares for each city type by dividing the sum_fares_by_type Series by the total_fares Series and multiplying by 100:
```
# Calculate the percentage of fare for each city type.
type_percents = 100 * sum_fares_by_type / total_fares
type_percents
```

![percentage_fares.png](https://github.com/kcharb7/PyBer_Analysis/blob/main/Analysis/percentage_fares.png)

Using the type_percents and creating an array of the city types for the labels, I created a pie chart: 
```
# Build the percentage of fares by city type pie chart.
plt.pie(type_percents, labels=["Rural", "Suburban", "Urban"])
plt.show()
```
Building off of this code, I added a title, the percentages, the company color scheme, and a shadow to the pie chart. I additionally adjusted the start angle and imported the matplotlib library to use the rcParams for adjusting font size:
```
# Import mpl to change the plot configurations using rcParams.
import matplotlib as mpl
# Build Pie Chart
plt.subplots(figsize=(10, 6))
plt.pie(type_percents,
    labels=["Rural", "Suburban", "Urban"],
    colors=["gold", "lightskyblue", "lightcoral"],
    explode=[0, 0, 0.1],
    autopct='%1.1f%%',
    shadow=True, startangle=150)
plt.title("% of Total Fares by City Type")
# Change the default font size from 10 to 14.
mpl.rcParams['font.size'] = 14
# Save Figure
plt.savefig("analysis/Fig5.png")
# Show Figure
plt.show()
```

![Fig5.png](https://github.com/kcharb7/PyBer_Analysis/blob/main/Analysis/Fig5.png)

### *Percentage of Total Rides by City Type*
Using the same methods as above, I used the groupby() function on the pyber_data_df DataFrame to group by the “type” column and the count() method on the “ride_id” column to get the total number of rides for each city type. To get the total rides for all city types, I used the count() method on the “ride_id” column of the pyber_data_df DataFrame. I combined these two lines of code into a formula to calculate the percentage:
```
# Calculate the percentage of rides for each city type.
ride_percents = 100 * pyber_data_df.groupby(["type"]).count()["ride_id"] / pyber_data_df["ride_id"].count()
ride_percents
```

![ride_percents.png](https://github.com/kcharb7/PyBer_Analysis/blob/main/Analysis/ride_percents.png)

To make the pie chat, I reused the code for creating the percentage of total fares pie chart:
```
# Build percentage of rides by city type pie chart.
plt.subplots(figsize=(10, 6))
plt.pie(ride_percents,
    labels=["Rural", "Suburban", "Urban"],
    colors=["gold", "lightskyblue", "lightcoral"],
    explode=[0, 0, 0.1],
    autopct='%1.1f%%',
    shadow=True, startangle=150)
plt.title("% of Total Rides by City Type")
# Change the default font size from 10 to 14.
mpl.rcParams['font.size'] = 14
# Save Figure
plt.savefig("analysis/Fig6.png")
# Show Figure
plt.show()
```

![Fig6.png](https://github.com/kcharb7/PyBer_Analysis/blob/main/Analysis/Fig6.png)

### *Percentage of Total Drivers by City Type*
The driver_percents was calculated like the type_percents and ride_percents:
```
# Calculate the percentage of drivers for each city type.
driver_percents = 100 * city_data_df.groupby(["type"]).sum()["driver_count"] / city_data_df["driver_count"].sum()
driver_percents
```

![driver_percents.png](https://github.com/kcharb7/PyBer_Analysis/blob/main/Analysis/driver_percents.png)

Repurposing the code used for the previous pie charts, I replaced ride_percents with driver_percents, changed the startangle to 165, and changed the title:
```
# Build percentage of rides by city type pie chart.
plt.subplots(figsize=(10, 6))
plt.pie(driver_percents,
    labels=["Rural", "Suburban", "Urban"],
    colors=["gold", "lightskyblue", "lightcoral"],
    explode=[0, 0, 0.1],
    autopct='%1.1f%%',
    shadow=True, startangle=165)
plt.title("% of Total Drivers by City Type")
# Change the default font size from 10 to 14.
mpl.rcParams['font.size'] = 14
# Save Figure
plt.savefig("analysis/Fig7.png")
# Show Figure
plt.show()
```

![Fig7.png](https://github.com/kcharb7/PyBer_Analysis/blob/main/Analysis/Fig7.png)

# PyBer Challenge
## Overview
### *Purpose*
Using the ride-sharing data for the ride-sharing app company PyBer, the CEO V. Isualize asked for a summary DataFrame containing the total number of rides, total number of drivers, total fares, average fare per ride, and average fare per drive for each city type. Additionally, V. Isualize asked for a multiple-line graph showcasing the total fares for each week by city type. 

## Analysis
### *A Ride-Sharing Summary DataFrame by City Type*
To begin formulating my summary DataFrame, I created a Series with type of city as the index using the groupby() function and applied the count() method to the “ride_id” column within the pyber_data_df DataFrame to get the total number of rides for each city type:
```
# Get the total rides for each city type
total_rides = pyber_data_df.groupby(["type"]).count()["ride_id"]
```
Next, I created a Series with type of city as the index using the groupby() function and applied the sum() method to the “driver_count” column within the city_data_df DataFrame to get the total number of drivers for each city type:
```
# Get the total drivers for each city type
total_drivers = city_data_df.groupby(["type"]).sum()["driver_count"]
```
I created a third Series with type of city as the index using the groupby() function and applied the sum() method to the “fare” column within the pyber_data_df DataFrame to get the total fares for each city type:
```
# Get the total amount of fares for each city type
total_fares = pyber_data_df.groupby(["type"]).sum()["fare"]
```
To calculate the average fare per ride by city type, I divided the sum of all the fares by the total rides:
```
# Get the average fare per ride for each city type. 
average_fare_per_ride = total_fares/total_rides
```
To calculate the average fare per driver by city type, I divided the sum of all the fares by the total drivers:
```
# Get the average fare per driver for each city type. 
average_fare_per_driver = total_fares/total_drivers
```
All above values were combined into a summary DataFrame:
```
# Create a PyBer summary DataFrame. 
PyBer_summary_df = pd.DataFrame({"Total Rides": total_rides, 
                                "Total Drivers": total_drivers, 
                                 "Total Fares": total_fares, 
                                 "Average Fare per Ride": average_fare_per_ride,
                                 "Average Fare per Driver": average_fare_per_driver})

PyBer_summary_df
```

After creating the PyBer_summary_df DataFrame, I removed the index name “type” using the following code:
```
# Cleaning up the DataFrame. Delete the index name
PyBer_summary_df.index.name = None
```
Then, I formatted each column to include a thousands separator. Additionally, for each column relating to fares, I included a dollar sign and two decimal places:
```
# Format the columns.
PyBer_summary_df["Total Rides"] = PyBer_summary_df["Total Rides"].map("{:,}".format)
PyBer_summary_df["Total Drivers"] = PyBer_summary_df["Total Drivers"].map("{:,}".format)
PyBer_summary_df["Total Fares"] = PyBer_summary_df["Total Fares"].map("${:,.2f}".format)
PyBer_summary_df["Average Fare per Ride"] = PyBer_summary_df["Average Fare per Ride"].map("${:.2f}".format)
PyBer_summary_df["Average Fare per Driver"] = PyBer_summary_df["Average Fare per Driver"].map("${:.2f}".format)

PyBer_summary_df
```
The final summary DataFrame looked as follows:

![PyBer_summary.png](https://github.com/kcharb7/PyBer_Analysis/blob/main/Analysis/PyBer_summary.png)

### *A Multiple-Line Chart of Total Fares for each City Type*
I created a new DataFrame with multiple indices using the groupby() function on the “type” and “date” columns of the pyber_data_df DataFrame and applying the sum() method on the “fare” column:
```
# Using groupby() to create a new DataFrame showing the sum of the fares 
# for each date where the indices are the city type and date.
fare_by_date_type_df = pd.DataFrame(pyber_data_df.groupby(["type", "date"]).sum()["fare"])

fare_by_date_type_df.head()
```
Then, in order to use the pivot() function, I reset the index of the fare_by_date_type_df Data Frame:
```
# Reset the index on the fare_by_date_type_df DataFrame. This is needed to use the 'pivot()' function.
fare_by_date_type_df = fare_by_date_type_df.reset_index()
```
After resetting the index I used the pivot() function to convert the fare_by_date_type_df DataFrame so that the index was the “date”, each column was a city “type”, and the values were the “fare”.
```
# Create a pivot table with the 'date' as the index, the columns ='type', and values='fare' 
# to get the total fares for each type of city by the date. 
fare_by_date_type_pivot = fare_by_date_type_df.pivot(index="date", columns="type", values="fare")
```
Using the loc method on the date range 2019-01-01 through 2019-04-29 within the fare_by_date_type_pivot DataFrame, I created a new DataFrame:
```
# Create a new DataFrame from the pivot table DataFrame using loc on the given dates, '2019-01-01':'2019-04-29'.
date_df = fare_by_date_type_pivot.loc["2019-01-01":"2019-04-29"]

date_df.head(10)
```
I reset the index of the date_df DataFrame to a datetime data type with the following code:
```
# Set the "date" index to datetime datatype. This is necessary to use the resample() method.
date_df.index = pd.to_datetime(date_df.index)
```
Then I used the df.info() to check that the “date” was a datetime data type:
```
# Check that the datatype for the index is datetime using df.info()
date_df.info()
```

![date_df.info().png](https://github.com/kcharb7/PyBer_Analysis/blob/main/Analysis/date_df.info().png)

Applying the resample() function and the sum() method to the date_df DataFrame, I created a new DataFrame with the data in weekly bins and the total fares for each week:
```
# Create a new DataFrame using the "resample()" function by week 'W' and get the sum of the fares for each week.
fare_week_df = date_df.resample("W").sum()
fare_week_df
```

![fare_week_df.png](https://github.com/kcharb7/PyBer_Analysis/blob/main/Analysis/fare_week_df.png)

Using the object-oriented interface and the df.plot() methods, as well as the Matplotlib “fiverthirtyeight” graph style code, I created a line chart of the resampled DataFrame:
```
# Import the style from Matplotlib.
from matplotlib import style
# Use the graph style fivethirtyeight.
plt.style.use('fivethirtyeight')

# Using the object-oriented interface method, plot the resample DataFrame using the df.plot() function. 
ax = fare_week_df.plot(figsize=(20,8))

# Create title and y-axis label
ax.set_title("Total Fare by City Type")
ax.set_ylabel("Fare ($USD)")

#Save figure
plt.savefig("Analysis/PyBer_fare_summary.png")

plt.show()
```

![PyBer_fare_summary.png](https://github.com/kcharb7/PyBer_Analysis/blob/main/Analysis/PyBer_fare_summary.png)

### *Results*

![PyBer_summary.png](https://github.com/kcharb7/PyBer_Analysis/blob/main/Analysis/PyBer_summary.png)

The total number of rides in urban cities was 2.6- and 13-times higher than in suburban and rural cities, respectively. Similarly, the total number of drivers in urban cities was approximately 4.9- and 30.8-times higher than in suburban and rural cities, respectively. Given the higher number of rides and drivers in urban cities, it is not surprising that urban cities had the highest total fares at $39, 854.38, followed by $19,356.33 for suburban cities, and $4,327.93 for rural cities. Urban cities had the lowest average fare per ride at $24.53 per ride, while suburban cities had an average fare of $30.97 per ride and rural cities has the highest average fare at $34.62 per ride. The average fare per driver was 2.4- and 3.3-times lower in urban cities than in suburban and rural cities, respectively.

![PyBer_fare_summary.png](https://github.com/kcharb7/PyBer_Analysis/blob/main/Analysis/PyBer_fare_summary.png)

Total fare for urban cities was lowest in the first week of January and continued to climb until the last week of February. Fares then fluctuated until the last week of March before plateauing in April. The highest total fare for urban cities was in the second week of March. 

Total fare dramatically increased from the first to second week of January in suburban cities, and then remained relatively stable for the remainder of January before dropping by the first week of February. Total fare continued to decline slightly until the second week of February where it increased and peaked in the last week of February. Total fare dramatically declined at the beginning of March and then remained relatively stable before increasing after the second week of April. 

Total fare fluctuated the most in rural cities from January to April. Total fare was lowest for rural cities in the second week of January and highest in the first week of April. 

### *Recommendations*
To address the disparities among the city types, it is recommended that PyBer increase the number of drivers in suburban, and most importantly rural, cities so that that there are more drivers to offer rides to customers. Secondly, the average fare per driver needs to reduce by atleast half in rural cities and suburban cities to better match the average fare per driver in urban cities and entice more customers to take rides with PyBer. Thirdly, the average fare per ride should reduce in rural cities to better match those in suburban and urban cities. Together, these recommendations will help to increase the number of rides available to customers, as well as increase the number of customers, and in turn improve total fares within rural and suburban cities. 
