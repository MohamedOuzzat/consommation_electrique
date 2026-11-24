When we visualize the evolution of electricity consumption over time, we want to understand **how electricity consumption behaves, how it changes, and whether certain patterns repeat over time.**

There are three important concepts in Time Series Analysis:

1. Trend
2. Seasonality
3. Variations

Let's understand each one with examples from your `PJME_hourly.csv` dataset.

# **1. Trend (Long-term direction)**

**Definition:**

A Trend represents the general direction of the data over a long period of time.

It helps us understand whether electricity consumption is generally:

- Increasing (Upward Trend).
- Decreasing (Downward Trend).
- Stable (No significant Trend).

**Example:**

Imagine electricity consumption over several years:

[GCSE Statistics online course | Study Smiles](https://images.openai.com/static-rsc-4/3Zk-yBMK5I33g38Ww7nB1PwIybcYNyOsxrBNmCE8NNuXdosPPz3b7mi_d10rY4ptq-YrgYnxl3Y3Lj6hdfO3RX4ddVft17al_pZldB-Di1b-d5mZlgJAUqt3nSQ07i7gbngQWQBsoRFR6xEbqQzWnNyfGFR5xp3Yqswfz3-BaGY?purpose=inline)

[studysmiles.co.uk](https://www.studysmiles.co.uk/gcse-statistics?utm_source=chatgpt.com)

In your electricity dataset:

- If consumption increases over several years, we have an Upward Trend.
- If consumption decreases over several years, we have a Downward Trend.
- If consumption remains around the same general level, we have a Stable Trend.

**How can we identify it in Python?**

We can use a Moving Average (Rolling Mean) to smooth short-term fluctuations.

```
df['Rolling_Mean'] = df['PJME_MW'].rolling(window=24*30).mean()plt.figure(figsize=(15,5))plt.plot(df.index, df['PJME_MW'], alpha=0.3, label='Actual Consumption')plt.plot(df.index, df['Rolling_Mean'], color='red', label='Trend')plt.legend()plt.title('Electricity Consumption Trend')plt.show()
```

Here:

- `24*30` = 30 days of hourly observations.
- `rolling().mean()` calculates the average over a moving window.
- The red line helps us observe the long-term direction.

# **2. Seasonality (Repeating patterns)**

**Definition:**

Seasonality refers to patterns that repeat at regular and predictable intervals of time.

For example:

- Every day.
- Every week.
- Every month.
- Every year.

[Mastering Trend Analysis: A Step-by-Step Guide | by Michal Szudejko | Medium](https://images.openai.com/static-rsc-4/BI2RnyXxXhG6ZIWjVHoKWUahcZukwJpNiMkZrm0enUeA_m1lbDMEK2TEJnbLylLmhu_83NLi4JCalI7Rl-yA6AwSqXzgbywxpYiaD4wgVYeK0FsejhhDhsfWfeW4NEmdBVswlxIqsd_5oUPWAsouTTompu4BXHr5zteldJTIUS4?purpose=inline)

[medium.com](https://medium.com/%40michalszudejko/mastering-trend-analysis-a-step-by-step-guide-83827130acd4?utm_source=chatgpt.com)

**Examples in electricity consumption:**

Imagine these situations:

- During the morning, people wake up and electricity consumption increases.
- During the night, electricity consumption decreases.
- During weekdays, electricity consumption may be higher than weekends.
- During winter, heating systems may increase electricity demand.

These are examples of possible seasonal patterns.

**How can we identify seasonality in Python?**

First, analyze consumption by hour:

```
df['Hour'] = df.index.hourhourly_consumption = df.groupby('Hour')['PJME_MW'].mean()hourly_consumption.plot(    kind='line',    figsize=(12,5),    marker='o')plt.title('Average Electricity Consumption by Hour')plt.xlabel('Hour')plt.ylabel('Average Consumption (MW)')plt.show()
```

This helps us identify daily seasonality.

We can also analyze weekly seasonality:

```
df['Day'] = df.index.dayofweekdaily_consumption = df.groupby('Day')['PJME_MW'].mean()daily_consumption.plot(kind='bar', figsize=(10,5))plt.title('Average Electricity Consumption by Day')plt.show()
```

And monthly seasonality:

```
df['Month'] = df.index.monthmonthly_consumption = df.groupby('Month')['PJME_MW'].mean()monthly_consumption.plot(kind='bar', figsize=(10,5))plt.title('Average Electricity Consumption by Month')plt.show()
```

# **3. Variations (Short-term fluctuations)**

**Definition:**

Variations represent changes in the data over time.

They can be small or large, regular or irregular.

For example:

- A sudden increase in electricity consumption.
- A sudden decrease in consumption.
- Small fluctuations from one hour to another.
- Unexpected changes caused by weather or special events.

[socrata-api · GitHub Topics · GitHub](https://images.openai.com/static-rsc-4/1YQ7TLN7PyHgqscgJpFtO4p4EoujinICdze6FByWSrtCwMwnWDhZ91we0zzBzk0-elg8VBU34rxKzY-TRXkHKc1JsbRq3Y6uxif18bsp8T2qkyI6iay1t6lqxMt08s69bdT9QqaA8yUMc5NeVl3dqZvps-hMvSoaWH1sIIFMOKw?purpose=inline)

[github.com](https://github.com/topics/socrata-api?utm_source=chatgpt.com)

In your dataset:

Imagine electricity consumption is normally 30,000 MW, but suddenly reaches 40,000 MW.

This is a significant variation that we should investigate.

**How can we identify variations?**

```
plt.figure(figsize=(15,5))plt.plot(df.index, df['PJME_MW'])plt.title('Electricity Consumption Variations')plt.xlabel('Date')plt.ylabel('Consumption (MW)')plt.show()
```

We can also calculate the difference between consecutive observations:

```
df['Difference'] = df['PJME_MW'].diff()df['Difference'].plot(figsize=(15,5))plt.title('Hourly Changes in Electricity Consumption')plt.ylabel('Change in Consumption (MW)')plt.show()
```

- Positive value: consumption increased.
- Negative value: consumption decreased.
- Zero: consumption remained unchanged.

# **4. What is the difference between them?**

| Concept     | Main question                                 | Example                             |
| ----------- | --------------------------------------------- | ----------------------------------- |
| Trend       | What is the long-term direction?              | Consumption increases over years    |
| Seasonality | What patterns repeat regularly?               | Consumption increases every morning |
| Variations  | What changes from one observation to another? | Sudden increase in consumption      |

# **5. Important: How are they related?**

A time series can be represented using an additive model:

\\[ Y_t = T_t + S_t + R_t \\]

Where:

- \\(Y_t\\): Observed data (actual consumption).
- \\(T_t\\): Trend.
- \\(S_t\\): Seasonal component.
- \\(R_t\\): Residual component (irregular variations).

[Why Stationarity Matters in Time Series Analysis and How to Achieve It | by Sahin Ahmed(Data Scientist/MLE) | Medium](https://images.openai.com/static-rsc-4/_at63F7Y7x0KYLSYbIk2vgivzcvx4QCkd7YbjvoDzxzWYCYHyVXjSmZPqO6IWU7cNv53taratWCGty5G9IZ0lXTmQdgytySMIzTWpdVISR3A9xLc0I-Rnhkj39ibCKQJI_s1hShHBlCma19WgT7_GNmrC1J-WRLkR51UxAKl5Jw?purpose=inline)

[medium.com](https://medium.com/%40sahin.samia/why-stationarity-matters-in-time-series-analysis-and-how-to-achieve-it-57646e09b8cc?utm_source=chatgpt.com)

In Python, we can separate these components using `seasonal_decompose()`:

```
from statsmodels.tsa.seasonal import seasonal_decomposeresult = seasonal_decompose(    df['PJME_MW'],    model='additive',    period=24*7)result.plot()plt.show()
```

Here, `period=24*7` means we are analyzing a weekly repeating pattern (168 hours).

**Important:** The residual component is not necessarily every short-term variation. It represents what remains after removing the estimated trend and seasonal components.

# **6. Test your understanding**

**Question 1: Electricity consumption increases gradually over 5 years. What is this?**

Trend

Seasonality

Variation

**Question 2: Electricity consumption increases every morning at 8 AM. What is this?**

Trend

Seasonality

Variation

**Question 3: Electricity consumption suddenly increases from 30,000 MW to 40,000 MW in one hour. What is this?**

Trend

Seasonality

Variation

Check my answers

**My advice:** For your PJME project, make sure you understand these three concepts before moving to Machine Learning. They help you decide which features to create and which patterns your forecasting model needs to learn.