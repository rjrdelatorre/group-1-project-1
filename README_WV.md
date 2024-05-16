# Group 1: Project 1

# Analysis of Summer Olympic Medals

This repository contains an analysis of the Summer Olympic Games, focusing specifically on the performance of a host country during the Summer Olympics. The analysis utilizes data from the `athlete_events.csv` dataset to derive insights into medal counts and participation rates.

## Table of Contents

- [Installation](#installation)
- [Usage](#usage)
- [Data](#data)
- [Analysis Steps](#analysis-steps)
- [Visualizations](#visualizations)
- [Results](#results)
- [Contributing](#contributing)
- [License](#license)


## Usage

1. **Import Libraries**:
    ```python
    import pandas as pd
    import numpy as np
    import matplotlib.pyplot as plt
    from scipy.stats import zscore
    import seaborn as sns
    ```

2. **Read the CSV File**:
    ```python
    olympics_path = "resources/athlete_events.csv"
    olympics_df = pd.read_csv(olympics_path, low_memory=False)
    ```

3. **View the First 5 Rows**:
    ```python
    olympics_df.head(5)
    ```

4. **Focus on Summer Olympics**:
    ```python
    summer_df = olympics_df[olympics_df['Season'] == 'Summer']
    ```

5. **Specify Host Country Details**:
    ```python
    host_country = 'INSERT HOST COUNTRY HERE'
    host_NOC = 'INSERT HOST NOC HERE'
    host_year = 'INSERT HOST YEAR HERE'
    host_city = 'INSERT HOST CITY HERE'
    ```

## Data

The dataset used in this analysis is `athlete_events.csv`, which contains details of athletes' performances in various Olympic Games.

## Analysis Steps

1. **Filter Data for Host Country**:
    ```python
    host_df = summer_df[summer_df['Team'].str.contains(host_country, case=False)]
    ```

2. **Clean Data**:
    - Standardize team names for the host country.
    - Use `NOC` (National Olympic Committee code) for consistency.

3. **Medal Count Calculation**:
    - Calculate gold, silver, bronze, and total medals for both the host country and the rest of the world.
    - Group data by games and NOC.

4. **Participation Analysis**:
    - Calculate participation rates and medal rates for the host country.

5. **Statistical Analysis**:
    - Calculate average medal counts.
    - Compute Z-scores to understand performance deviations.

## Visualizations

1. **Stacked Bar Chart**:
    - Visualize the number of gold, silver, and bronze medals won by the host country in each Summer Olympics.

    ```python
    games_df_host[['gold_medals', 'silver_medals', 'bronze_medals']].plot(
        kind='bar',
        stacked=True,
        figsize=(20, 10),
        color=['#ffd700', '#c0c0c0', '#cd7f32'],
        title='Medals Won by [INSERT HOST COUNTRY] in the Summer Olympics'
    )
    ```

2. **Line Chart**:
    - Plot medal counts and medal rates over time for the host country.

    ```python
    games_df_host['medal_rate'].plot(kind='line', figsize=(20, 10), title='Medal Rate for [INSERT HOST COUNTRY] in the Summer Olympics')
    ```

3. **Distribution Plot**:
    - Show the distribution of total medals won by the host country.

    ```python
    sns.histplot(games_df_host['total_medals'], bins=10, kde=True)
    ```

## Results

- **Average Medal Count**:
    - Calculate and print the average medal count for the host country and the rest of the world.

    ```python
    average_medal_count_host = games_df_host['total_medals'].mean()
    print(average_medal_count_host)
    ```

- **Performance During Host Year**:
    - Show the medal count for the host country during their hosting year.

    ```python
    games_df_host.loc[f'{host_year} Summer']
    ```

- **Comparison with Rest of the World**:
    - Analyze and compare the host country's performance with other countries during the host year.

    ```python
    summer_host_yr_rw = games_df_rw[games_df_rw['Games'] == f'{host_year} Summer']
    summer_host_yr_rw_short = summer_host_yr_rw[['Games', 'NOC', 'Medal']]
    print('Medal Count for the Rest of the World:')
    summer_host_yr_rw_short.head()
    ```

## Contributing

Richard Lankford
Rolando De La Torre
Chris Bolger
Brandi Berry
