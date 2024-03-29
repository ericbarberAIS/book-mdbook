# Hands On Example

## Coffee Shop Example Description

In this scenario, we explore a coffee shop's daily operations, focusing on various aspects like order processing times, customer satisfaction, and sales data. This coffee shop serves as a microcosm for understanding how small businesses operate and manage quality control, efficiency, and customer satisfaction. By collecting data on how long it takes to process orders, how satisfied customers are with their service, and how much revenue is generated, we can apply statistical methods to analyze and improve the coffee shop's performance.
Objective

The primary aim is to teach users about Statistical Process Control (SPC) using a relatable and easily understood example. SPC is a method of quality control which employs statistical methods to monitor and control a process. This helps ensure the process operates at its fullest potential to produce conforming product with minimal waste (rework or scrap). SPC can identify when a process is behaving as expected or when it deviates significantly from this state, signaling that there might be a particular cause of variation that needs to be addressed.

## Goals in the Coffee Shop Context

- **Understand Process Behavior:** By analyzing order processing times, we can understand how efficiently the coffee shop operates on a daily basis. SPC charts help in identifying trends, shifts, or any outliers in the process.

- **Improve Customer Satisfaction:** Monitoring customer feedback through SPC charts enables us to pinpoint areas of improvement. It can reveal whether changes in the process positively or negatively affect customer satisfaction.

- **Financial Performance Monitoring:** Sales data analysis through SPC can highlight patterns, such as peak hours or days and the effectiveness of promotions, guiding better business decisions.

- **Quality Control:** Through continuous monitoring of these metrics, the coffee shop can maintain high standards of service quality, ensuring that customers receive consistent and satisfactory service.

## Teaching Approach

Using the coffee shop example, we can teach users how to apply SPC charts, such as X-bar and R charts for order processing times, P charts for customer satisfaction, and C charts for defect tracking (e.g., incorrect orders). This practical application helps users grasp the principles of SPC in a familiar setting, making the learning process more intuitive and engaging. By analyzing mock data generated and expanded upon with each "click," users learn how to interpret these charts, identify signals within the data, and make informed decisions to improve the process.

This hands-on approach demystifies statistical methods and provides valuable insights into how small changes can significantly impact a business's overall performance and customer satisfaction.


---


## Mock Data
```python
from pyspark.sql import SparkSession
from pyspark.sql.types import StructType, StructField, DateType, StringType, FloatType, BooleanType
from pyspark.sql.functions import lit, rand, randn
import datetime

# Function to generate data
def generate_data(existing_df, num_records=100):
   
    """
    Generate simulated data for a coffee shop.
    """
    # Generate timestamps
    time_increments = np.random.normal(loc=5, scale=1, size=num_records).clip(min=1)
    timestamps = [start_datetime + timedelta(minutes=np.sum(time_increments[:i])) for i in range(1, num_records + 1)]

    # Order processing times (normally distributed around 5 minutes with a standard deviation of 2)
    order_processing_times = np.random.normal(loc=5, scale=2, size=num_records).clip(min=0)  # Ensure no negative times
    order_processing_times = order_processing_times.tolist()
    
    # Generate customer satisfaction levels
    customer_satisfaction_choices = ['Very Satisfied', 'Satisfied', 'Neutral', 'Unsatisfied', 'Very Unsatisfied']
    customer_satisfaction = np.random.choice(customer_satisfaction_choices, size=num_records).tolist()
    
    # Generate sales amounts and round them
    sales_amount = np.round(np.random.uniform(3, 20, size=num_records), 2)
    sales_amount = sales_amount.tolist()  # Convert to Python float list
    
    # Generate correctness of orders
    order_correct = np.random.choice([True, False], p=[0.9, 0.1], size=num_records)
    order_correct = list(map(bool, order_correct))
    
    # Prepare data for DataFrame creation
    data = list(zip(timestamps, customer_satisfaction, sales_amount, order_correct, order_processing_times))
    
    # Define schema
    schema = StructType([
        StructField("DateTime", TimestampType(), True),
        StructField("Customer Satisfaction", StringType(), True),
        StructField("Sales Amount ($)", FloatType(), True),
        StructField("Order Correct", BooleanType(), True),
        StructField("Order Processing Time (mins)", DoubleType(), True)
    ])
    
    # Create a DataFrame from the generated data
    new_data = spark.createDataFrame(data, schema=schema)
    
    # If there's existing data, append the new data
    if existing_df is not None and not existing_df.rdd.isEmpty():
        updated_df = existing_df.union(new_data)
    else:
        updated_df = new_data
    
    return updated_df

# Initialize Spark Session
spark = SparkSession.builder.appName("CoffeeShopDataGeneration").getOrCreate()

# Define the schema of the DataFrame
schema = StructType([
    StructField("Date", DateType(), True),
    StructField("Order Processing Time (mins)", FloatType(), True),
    StructField("Customer Satisfaction", StringType(), True),
    StructField("Sales Amount ($)", FloatType(), True),
    StructField("Order Correct", StringType(), True)
])

# Create an empty DataFrame with the defined schema
# This is your starting point and can be used as input to the generate_data function
coffee_df = spark.createDataFrame(spark.sparkContext.emptyRDD(), schema)


# Example: Assuming existing_df is your existing PySpark DataFrame
coffee_df = generate_data(coffee_df, 100)
```
  
---


## Line Chart
Below, I'll outline the code for generating three separate line charts in a Databricks notebook, one for each of the specified metrics: Order Processing Time (mins), Sales Amount ($), and Order Correct percentage over time. Each section of code is meant to be run in its own cell within a Databricks notebook.
### 1. Order Processing Time (mins)

This cell will calculate the average order processing time per day.

```python

# Calculate average order processing time per day
order_processing_time_daily_avg = existing_df.groupBy("Date").avg("Order Processing Time (mins)").orderBy("Date")

# Display the DataFrame for plotting in Databricks
display(order_processing_time_daily_avg)
```
### 2. Sales Amount ($)

This cell will calculate the total sales amount per day.

```python

# Calculate total sales amount per day
sales_amount_daily_sum = existing_df.groupBy("Date").sum("Sales Amount ($)").orderBy("Date")

# Display the DataFrame for plotting in Databricks
display(sales_amount_daily_sum)
```
### 3. Order Correct Percentage

For the Order Correct percentage, you'll first need to calculate the daily percentage of orders that were correct. This involves counting the number of correct orders per day, dividing by the total number of orders that day, and then multiplying by 100 to get a percentage.

```python

from pyspark.sql.functions import sum as _sum, count as _count, col

# Calculate daily percentage of orders that were correct
order_correct_daily_percentage = existing_df.groupBy("Date").agg(
    (_sum(col("Order Correct").cast("int")) / _count("*") * 100).alias("Order Correct Percentage")
).orderBy("Date")

# Display the DataFrame for plotting in Databricks
display(order_correct_daily_percentage)
```
### Using Databricks Plotting Tool

After running each cell, you can use Databricks' built-in plotting tool to create line charts for each metric. Here's how to do it for each cell's output:

- For the output of each cell, you'll see a table with your data and a set of options for visualization at the bottom of the cell's output area.
- Select the 'Line' chart option from the visualization menu.
- Configure the chart:
    - For the X-axis, select Date.
    - For the Y-axis, choose the corresponding metric (e.g., the average for order processing time, the sum for sales amount, and the percentage for order correctness).
- Apply any additional customizations as needed, such as titles, axis labels, or line colors.

By following these instructions, you'll be able to visualize trends in order processing time, sales amount, and order accuracy over time, providing valuable insights into the coffee shop's daily operations and areas for potential improvement.
  
---

## Run Chart
Creating run charts for the specified metrics involves a similar process to generating line charts, focusing on the same metrics but with an emphasis on identifying trends, shifts, or patterns over time. In a Databricks notebook, you'll use the same aggregation methods to prepare the data. A run chart essentially is a line chart with a focus on analyzing the data over time, so the preparation of data remains consistent. Below are the code snippets for each metric to be run in separate cells in a Databricks notebook.

### Mean of Median
The choice between mean and median depends on the data's distribution and the presence of outliers. The mean, providing the arithmetic average, is best used for data that is symmetrically distributed with few outliers, as it considers all values. However, it can be misleading for skewed distributions or when outliers significantly impact the average. The median, identifying the middle value, is more robust in skewed distributions or when outliers are present, as it is less affected by extreme values. For small datasets, both can be informative, but for larger datasets, the median can provide a clearer picture of central tendency in the presence of skewness or outliers.

Using daily averages (mean or median) to analyze 25 days' worth of data involves several assumptions about the data and its distribution. Understanding these assumptions is crucial for ensuring that the analysis is valid and the insights derived are reliable. Here are the key assumptions:
#### 1. Assumptions for Using the Mean

    Normality: When using the mean, it's often assumed that the data are normally distributed, especially if you're planning to apply parametric statistical tests. The mean is sensitive to outliers, which can skew the results if the data are not symmetrically distributed.
    Random Sampling: It's assumed that the daily data points are randomly sampled and represent the population of interest. This ensures that the mean is a fair representation of the overall data.
    Independence: Each day's data is assumed to be independent of the others. This means the result from one day does not influence the results from another day, which is crucial for many statistical tests.
    Homogeneity of Variance: If you're comparing means across different groups or times, it's often assumed that the variances within each group are similar. This assumption is particularly important for analyses like ANOVA.

#### 2. Assumptions for Using the Median

    Fewer Distributional Assumptions: Unlike the mean, the median makes fewer assumptions about the distribution of the data. It does not assume normality and is not affected by outliers or skewed distributions. This makes it a robust measure of central tendency for data that are not normally distributed.
    Independence: Similar to using the mean, it's assumed that each day's data is independent of the others.
    Ordinal or Continuous Data: The median is suitable for ordinal (ranked) or continuous data. It assumes that the data can be ordered or ranked.

#### 3. General Assumptions

    Sufficient Data: While there's no specific minimum number of days required, the assumption is that 25 days of data provide sufficient information to detect the underlying pattern or trend. However, the adequacy of this sample size can depend on the variability in the data and the context of the analysis.
    Consistency in Data Collection: It's assumed that the method of data collection remains consistent across the 25 days. Any changes in how data are collected could introduce bias or variability that affects the analysis.
    Representativeness: The data collected over the 25 days should be representative of the typical conditions or operations. For example, if the data exclude weekends or holidays without accounting for their impact, the analysis may not fully capture the true dynamics.

### GO TO:
- [Median as Measure of Central Tendency](#median-as-measure-of-central-tendency)
- [Mean as Measure of Central Tendency](#mean-as-measure-of-central-tendency)

### Median as Measure of Central Tendency

#### 1. Order Processing Time (mins) (Median)

Calculate the average order processing time per day:

```python
from pyspark.sql.functions import expr

# Calculate average order processing time per day
order_processing_time_daily_avg = existing_df.groupBy("Date").avg("Order Processing Time (mins)").orderBy("Date")

# Calculate the overall median of order processing times
median_order_processing_time = existing_df.approxQuantile("Order Processing Time (mins)", [0.5], 0)[0]

# Add the median as a constant column to the daily average DataFrame
order_processing_time_daily_avg = order_processing_time_daily_avg.withColumn("Median Order Processing Time", lit(median_order_processing_time))

# Display the DataFrame for plotting in Databricks as a run chart with median
display(order_processing_time_daily_avg)
```
#### 2. Sales Amount ($) (Median)

Calculate the total sales amount per day:

```python
# Calculate total sales amount per day
sales_amount_daily_sum = existing_df.groupBy("Date").sum("Sales Amount ($)").orderBy("Date")

# Calculate the overall median of sales amounts
median_sales_amount = existing_df.approxQuantile("Sales Amount ($)", [0.5], 0)[0]

# Add the median as a constant column to the daily sum DataFrame
sales_amount_daily_sum = sales_amount_daily_sum.withColumn("Median Sales Amount", lit(median_sales_amount))

# Display the DataFrame for plotting in Databricks as a run chart with median
display(sales_amount_daily_sum)
```
#### 3. Order Correct Percentage (Median)

Calculate the daily percentage of orders that were correct:

```python
from pyspark.sql.functions import sum as _sum, count as _count, col

# Calculate daily percentage of orders that were correct
order_correct_daily_percentage = existing_df.groupBy("Date").agg(
    (_sum(col("Order Correct").cast("int")) / _count("*") * 100).alias("Order Correct Percentage")
).orderBy("Date")

# Calculate the overall median of the order correct percentage
median_order_correct_percentage = order_correct_daily_percentage.approxQuantile("Order Correct Percentage", [0.5], 0)[0]

# Add the median as a constant column to the daily percentage DataFrame
order_correct_daily_percentage = order_correct_daily_percentage.withColumn("Median Order Correct Percentage", lit(median_order_correct_percentage))

# Display the DataFrame for plotting in Databricks as a run chart with median
display(order_correct_daily_percentage)
```

### Mean as Measure of Central Tendency

#### 1. Order Processing Time (mins) (Mean)

Calculate the average order processing time per day:

```python
from pyspark.sql.functions import avg, lit

# Calculate average order processing time per day
order_processing_time_daily_avg = existing_df.groupBy("Date").avg("Order Processing Time (mins)").orderBy("Date")

# Calculate the overall mean of order processing times
mean_order_processing_time = existing_df.agg(avg("Order Processing Time (mins)").alias("mean")).collect()[0]["mean"]

# Add the mean as a constant column to the daily average DataFrame
order_processing_time_daily_avg = order_processing_time_daily_avg.withColumn("Mean Order Processing Time", lit(mean_order_processing_time))

# Display the DataFrame for plotting in Databricks as a run chart with mean
display(order_processing_time_daily_avg)
```
#### 2. Sales Amount ($) (Mean)

Calculate the total sales amount per day:

```python
from pyspark.sql.functions import sum as _sum, count as _count, col

# Calculate daily percentage of orders that were correct
order_correct_daily_percentage = existing_df.groupBy("Date").agg(
    (_sum(col("Order Correct").cast("int")) / _count("*") * 100).alias("Order Correct Percentage")
).orderBy("Date")

# Calculate the overall mean of the order correct percentage
mean_order_correct_percentage = order_correct_daily_percentage.approxQuantile("Order Correct Percentage", [0.5], 0)[0]

# Add the mean as a constant column to the daily percentage DataFrame
order_correct_daily_percentage = order_correct_daily_percentage.withColumn("Mean Order Correct Percentage", lit(mean_order_correct_percentage))

# Display the DataFrame for plotting in Databricks as a run chart with mean
display(order_correct_daily_percentage)
```
#### 3. Order Correct Percentage (Mean)

Calculate the daily percentage of orders that were correct:

```python
from pyspark.sql.functions import sum as _sum, count as _count, col

# Calculate daily percentage of orders that were correct
order_correct_daily_percentage = existing_df.groupBy("Date").agg(
    (_sum(col("Order Correct").cast("int")) / _count("*") * 100).alias("Order Correct Percentage")
).orderBy("Date")

# Calculate the overall mean of the order correct percentage
mean_order_correct_percentage = order_correct_daily_percentage.agg(avg("Order Correct Percentage").alias("mean")).collect()[0]["mean"]

# Add the mean as a constant column to the daily percentage DataFrame
order_correct_daily_percentage = order_correct_daily_percentage.withColumn("Mean Order Correct Percentage", lit(mean_order_correct_percentage))

# Display the DataFrame for plotting in Databricks as a run chart with mean
display(order_correct_daily_percentage)
```


### Plotting Run Charts in Databricks

After preparing the data as shown above, you can plot run charts using Databricks' plotting tool. The steps to visualize the data as run charts are the same as for line charts:

- After executing each cell, observe the table and visualization options below the output area.
- Choose the 'Line' chart visualization type. While run charts and line charts use the same type of visualization, the interpretation focuses on process stability and trends for run charts.
- Set up your axes:
    - Use Date for the X-axis.
    - For the Y-axis, select the appropriate metric (average for Order Processing Time, sum for Sales Amount, and percentage for Order Correct).
- Customize your chart as needed, focusing on clarity for analyzing trends and shifts over time.

These run charts will help you identify any patterns, trends, or shifts in the data, which are critical for process analysis and improvement. Pay attention to runs (sequences of points above or below the measure of central tendency), trends (continuous increase or decrease), and any shifts in the process level, as these can indicate changes in the coffee shop's operations.
  
---

## Control Chart

Shewhart control charts are a fundamental tool in statistical process control (SPC) used to determine if a manufacturing or business process is in a state of control. For the metrics chosen for the coffee shop example, different types of control charts are appropriate based on the nature of the data (continuous vs. attribute) and its distribution. Here’s the best type of Shewhart control chart for each metric:
### 1. Order Processing Time (mins)

- **Metric Type:** Continuous data.

- **Best Control Chart:** The Individuals Control Chart (I-MR Chart) is most suitable for order processing time. This chart is ideal for continuous data that comes from a process where data points are collected individually in a sequential order. It helps in monitoring the process mean and variation over time.
### 2. Sales Amount ($)

- **Metric Type:** Continuous data.

- **Best Control Chart:** Similar to order processing time, the Individuals Control Chart (I-MR Chart) is also the best choice for monitoring sales amount. This metric represents continuous data that can vary significantly from one transaction to another, making the I-MR chart an excellent tool for identifying out-of-control signals that could indicate a need for process improvement.
### 3. Order Correct (Boolean: Yes/No)

- **Metric Type:** Attribute data (binary outcomes).

- **Best Control Chart:** The P-Chart (Proportion Chart) is the most appropriate for the "Order Correct" metric. This chart is used for attribute data where the data can be categorized into "conforming" and "non-conforming" (or, in this case, correct and incorrect orders) and the sample size can vary. It monitors the proportion of nonconforming units in a sample, providing insights into the process's stability in terms of order accuracy.
Summary

I-MR Chart for continuous data like "Order Processing Time" and "Sales Amount," to monitor individual measurements and their variability.

P-Chart for attribute data like "Order Correct," to track the proportion of conforming vs. non-conforming items when the sample size may vary.

Each chart provides a visual means of identifying trends, shifts, or instances of the process being out of control, facilitating timely interventions and continuous process improvement.

## I-MR Chart

### Step 1: Aggregate Daily Average for "Order Processing Time (mins)"

```python

from pyspark.sql.window import Window
from pyspark.sql.functions import lag, col, abs, avg, lit

# Assuming existing_df is your initial DataFrame
order_processing_time_daily_avg = existing_df.groupBy("Date").avg("Order Processing Time (mins)").orderBy("Date")
```
### Step 2: Calculate Moving Range of Daily Averages

```python

windowSpec = Window.orderBy("Date")

order_processing_time_daily_avg = order_processing_time_daily_avg.withColumn("PrevDayAvg", lag("avg(Order Processing Time (mins))", 1).over(windowSpec))
order_processing_time_daily_avg = order_processing_time_daily_avg.withColumn("MovingRange", abs(col("avg(Order Processing Time (mins))") - col("PrevDayAvg")))
```
### Step 3: Calculate Mean Moving Range and Estimated Standard Deviation

```python

# Calculate the mean of the Moving Range
mean_moving_range = order_processing_time_daily_avg.select(avg("MovingRange")).first()[0]

# Estimating standard deviation from the Moving Range (using d2 = 1.128 for n=2)
estimated_stddev = mean_moving_range / 1.128
```
### Step 4: Calculate UCL and LCL for the I Chart

```python

# Calculate mean of daily averages for the metric
mean_daily_avg = order_processing_time_daily_avg.select(avg("avg(Order Processing Time (mins))")).first()[0]

# Calculate UCL and LCL for I chart
ucl_i = mean_daily_avg + 3 * estimated_stddev
lcl_i = mean_daily_avg - 3 * estimated_stddev if mean_daily_avg - 3 * estimated_stddev > 0 else 0

order_processing_time_daily_avg = order_processing_time_daily_avg.withColumn("UCL_I", lit(ucl_i)).withColumn("LCL_I", lit(lcl_i))
```
### Step 5: Calculate UCL for the MR Chart (LCL is typically 0)

```python

# UCL for MR chart, using fixed multiplier for n=2
ucl_mr = mean_moving_range * 3.268

order_processing_time_daily_avg = order_processing_time_daily_avg.withColumn("UCL_MR", lit(ucl_mr)).withColumn("LCL_MR", lit(0))
```
### Final DataFrame for Visualization

At this point, order_processing_time_daily_avg contains the following columns, ready for visualization in Databricks:

- Date
- avg(Order Processing Time (mins)) (Daily average of order processing time)
- MovingRange (Daily moving range of average order processing time)
- UCL_I and LCL_I (Upper and lower control limits for the I chart)
- UCL_MR (Upper control limit for the MR chart, with LCL_MR typically set to 0 or not used)

Instructions for Visualization in Databricks

- For the I Chart: When visualizing, plot Date on the X-axis and avg(Order Processing Time (mins)), UCL_I, and LCL_I on the Y-axis to show the daily averages along with their control limits.

- For the MR Chart: Plot Date on the X-axis and MovingRange, UCL_MR (and LCL_MR if applicable) on the Y-axis to visualize the moving range and its upper control limit.

This approach ensures you're visualizing the aggregated summary data with the appropriate statistical control limits to assess process stability and control effectively.

To adjust the process for an X-bar S (mean and standard deviation) chart, you'll be working with groups of samples (subgroups) taken throughout each day rather than individual measurements or daily averages. This method is more appropriate when you have multiple samples per period (e.g., day) and want to monitor the variability within these samples as well as the process average. Here’s how you can adjust your process:
Step 1: Aggregate Subgroup Averages for "Order Processing Time (mins)"

First, you need to calculate the average of each subgroup. A subgroup can be a set of measurements taken at a specific time of the day or batches of orders processed together.

python

# Assuming existing_df is your initial DataFrame and it includes a 'Subgroup' column
subgroup_avg = existing_df.groupBy("Date", "Subgroup").avg("Order Processing Time (mins)").orderBy("Date", "Subgroup")

Step 2: Calculate Daily Subgroup Averages and Standard Deviations

Next, calculate the daily average of these subgroup averages and the standard deviation within each day's subgroups.

python

from pyspark.sql import functions as F

daily_stats = subgroup_avg.groupBy("Date").agg(
    F.avg("avg(Order Processing Time (mins))").alias("DailyAvg"),
    F.stddev("avg(Order Processing Time (mins))").alias("DailyStdDev")
).orderBy("Date")

Step 3: Calculate Overall Mean and Standard Deviation of Subgroup Averages

Now, compute the overall mean of the subgroup averages and the average of the daily standard deviations to estimate the process variability.

python

overall_mean = daily_stats.select(F.avg("DailyAvg")).first()[0]
average_std_dev = daily_stats.select(F.avg("DailyStdDev")).first()[0]

Step 4: Calculate UCL and LCL for the X-bar Chart

Using the overall mean and average standard deviation, calculate the control limits for the X-bar chart.

python

# Assuming 'A2' is the appropriate factor for the number of observations per subgroup
A2 = 0.0  # Replace 0.0 with the actual A2 value for your subgroup size

ucl_xbar = overall_mean + A2 * average_std_dev
lcl_xbar = overall_mean - A2 * average_std_dev if overall_mean - A2 * average_std_dev > 0 else 0

daily_stats = daily_stats.withColumn("UCL_Xbar", F.lit(ucl_xbar)).withColumn("LCL_Xbar", F.lit(lcl_xbar))

Step 5: Calculate UCL and LCL for the S Chart

Similarly, calculate the control limits for the standard deviation (S) chart using factors B3 and B4, which depend on the subgroup size.

python

# Assuming 'B3' and 'B4' are the appropriate factors for the number of observations per subgroup
B3 = 0.0  # Replace 0.0 with the actual B3 value
B4 = 0.0  # Replace 0.0 with the actual B4 value

ucl_s = B4 * average_std_dev
lcl_s = B3 * average_std_dev if B3 * average_std_dev > 0 else 0

daily_stats = daily_stats.withColumn("UCL_S", F.lit(ucl_s)).withColumn("LCL_S", F.lit(lcl_s))

Final DataFrame for Visualization

Your daily_stats DataFrame is now ready for visualization and contains the following columns:

    Date
    DailyAvg (Daily average of subgroup averages)
    DailyStdDev (Daily standard deviation of subgroups)
    UCL_Xbar and LCL_Xbar (Upper and lower control limits for the X-bar chart)
    UCL_S and LCL_S (Upper and lower control limits for the S chart)

Instructions for Visualization in Databricks

    For the X-bar Chart: When visualizing, plot Date on the X-axis and DailyAvg, UCL_Xbar, and LCL_Xbar on the Y-axis to show the daily averages along with their control limits.
    For the S Chart: Plot Date on the X-axis and DailyStdDev, UCL_S, and LCL_S on the Y-axis to visualize the daily standard deviation and its control limits.

This approach enables you to monitor both the central tendency and dispersion of your process, which is crucial for understanding and controlling process variability effectively.
