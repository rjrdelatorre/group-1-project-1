# Group 1: Project 1
# Testing for "Host Advantage" in the Summer Olympics

## Project Slides 
https://docs.google.com/presentation/d/114nit58BfUmdEd6wU8AzoycH2tson9Whx_KfftLNW9Y/edit#slide=id.p

## Objective
We will investigate whether or not there is a "home field advantage" for a nation that hosts the Olympic games. To do this, we will focus on the following recent hosts:
- Brazil (Rio 2016)
- Great Britain (London 2012)
- China (Beijing 2008)
- Greece (Athens 2004)

## Process
For each host, we will calculate such performance metrics as overall medal count, medal rate (rate at which a team won a medal vs. participated without winning), mean medal count, deviation from average performance, and performance compared to other team (e.g. medal count for a particular Olympic Games).

Each member of our group will perform the analysis on one team. Our objective was to allow for free experimentation with the data, while keeping in mind our goal was to try and quantify the presence or absence of a Host Advantage.

## Findings
More detailed findings and discussion about each team can be found at the bottom of the following files:
- [Brazil](brazil_hostview2.ipynb)
- [United Kingdom](uk_hostview.ipynb)
- [China](china.ipynb)
- [Greece](greece_hostview.ipynb)

In general, we were able to show a likely Host Advantage for Great Britain, Greece, and China, with these 3 teams demonstrating a significant boost in perfmance during their hosted games vs. historical performances.

Brazil did not demonstrate a clear host advantage, but did indeed prove an interesting case to analyze. 


## Sample Procedures for Conducting the Data Analysis
- [World View sample](README_WV.md)
- [Host sample file](README_HOST.md) (Text below)

## Prerequisites

To run the notebook, you need the following Python packages:
- pandas
- numpy
- matplotlib
- seaborn

You can install these packages using pip:

```bash
pip install pandas numpy matplotlib seaborn
```

## Data Source

The analysis uses the `athlete_events.csv` file, which should be placed in the `resources` directory.

## Notebook Overview

### 1. Read the CSV file into a DataFrame
The notebook starts by reading the CSV file containing Olympic data into a pandas DataFrame.

```python
olympics_df = pd.read_csv("resources/athlete_events.csv", low_memory=False)
```

### 2. Filter for Summer Olympics
Only data from the Summer Olympics is used for the analysis.

```python
summer_df = olympics_df[olympics_df['Season'] == 'Summer']
```

### 3. Specify the Host Country Details
The user needs to input details for the host country, such as the country name, NOC code, year, and city.

```python
host_country = 'INSERT HOST COUNTRY HERE'
host_NOC = 'INSERT HOST NOC HERE'
host_year = 'INSERT HOST YEAR HERE'
host_city = 'INSERT HOST CITY HERE'
```

### 4. Create DataFrame for the Host Country
A DataFrame is created to focus on the host country's Olympic team.

```python
host_df = summer_df[summer_df['NOC'] == host_NOC]
```

### 5. Aggregate Medal Counts by Games
The notebook aggregates medal counts by Olympic games for the host country.

```python
games_df_host = host_df.groupby('Games').agg({'Medal': ['count']})
```

### 6. Add Columns for Specific Medal Types and Clean Data
Columns for gold, silver, and bronze medals are added and the data is cleaned.

```python
games_df_host['gold_medals'] = host_df[host_df['Medal'] == 'Gold'].groupby('Games').agg({'Medal': ['count']})
games_df_host['silver_medals'] = host_df[host_df['Medal'] == 'Silver'].groupby('Games').agg({'Medal': ['count']})
games_df_host['bronze_medals'] = host_df[host_df['Medal'] == 'Bronze'].groupby('Games').agg({'Medal': ['count']})
games_df_host = games_df_host.fillna(0).astype(int)
```

### 7. Standardize Column Names
The notebook standardizes the column names for consistency.

```python
std_df = games_df_host.rename(columns={"Medal": "Total Medals", "gold_medals": "Gold", "silver_medals": "Silver", "bronze_medals": "Bronze"})
std_df.columns = std_df.columns.droplevel(1)
std_df = std_df[['Gold', 'Silver', 'Bronze', 'Total Medals']]
```

### 8. Filter for Years After 2000
Data is filtered to include only Olympic games held after the year 2000.

```python
std_df = std_df[std_df.index.str.contains('2004|2008|2012|2016')]
std_df = std_df.reset_index()
```

### 9. Create Summary DataFrame for a Specific Year
A summary DataFrame for a specific Olympic year is created, focusing on medal counts.

```python
summer_games_df = summer_df[["Team", "Games", "Year", "Season", "Sport", "Event", "Medal", "City"]]
summer_games_df = summer_games_df.rename(columns={"Team": "Country", "City": "Host"})
summer_games_df = summer_games_df[summer_games_df["Year"] == host_year]
```

### 10. Drop Unnecessary Columns and Aggregate Medal Counts
Unnecessary columns are dropped, and medal counts are aggregated by country.

```python
summer_games_metals_df = summer_games_df.drop(columns=["Games", "Sport", "Event"])
host_year_medals_df = summer_games_metals_df.dropna(subset=["Medal"])
host_year_medal_countrys_df = host_year_medals_df.groupby('Country').agg({'Medal': ['count']})
```

### 11. Add Specific Medal Counts and Clean Data
Columns for specific medal types are added, and the data is cleaned further.

```python
host_year_medal_countrys_df['Gold'] = host_year_medals_df[host_year_medals_df['Medal'] == 'Gold'].groupby('Country').agg({'Medal': ['count']})
host_year_medal_countrys_df['Silver'] = host_year_medals_df[host_year_medals_df['Medal'] == 'Silver'].groupby('Country').agg({'Medal': ['count']})
host_year_medal_countrys_df['Bronze'] = host_year_medals_df[host_year_medals_df['Medal'] == 'Bronze'].groupby('Country').agg({'Medal': ['count']})
host_year_medal_countrys_df = host_year_medal_countrys_df.fillna(0).astype(int)
```

### 12. Sort and Format Data
Data is sorted and formatted for better readability.

```python
host_year_sorted = host_year_medal_countrys_df.sort_values(by=('Medal', 'count'), ascending=False).reset_index()
host_year_sorted.columns = host_year_sorted.columns.droplevel(1)
host_year_sorted.index.name = 'Rank'
host_year_sorted.index = host_year_sorted.index + 1
host_year_sorted = host_year_sorted.rename(columns={"Medal": "Total Medals"})
host_year_sorted = host_year_sorted[['Country', 'Gold', 'Silver', 'Bronze', 'Total Medals']].reset_index()
```

### 13. Create and Save Styled DataFrame
A styled DataFrame is created and saved as an HTML file.

```python
def highlight(nation):
    if nation['Country'] == 'Great Britain':
        return ['background-color: #add8e6; color: black']*6
    else:
        return ['background-color: white; color: black; border-top: 1px solid black']*6

styles = [
    dict(selector=".row_heading", props=[("display", "none")]),
    dict(selector=".blank", props=[("display", "none")]),
    dict(selector="tr", props=[("border", "1px solid black"), ("padding", "10px"), ("font-size", "10pt")]),
]

styled_df = host_year_sorted[['Rank', 'Country', 'Bronze', 'Silver', 'Gold', 'Total Medals']].iloc[:15].style.set_caption('Medals by Country: Summer Olympic Games sorted by Gold Medals [Top 15]').set_table_styles(styles).bar(subset=['Gold'], color='#f0c05a').bar(subset=['Silver'], color='Lightgray').bar(subset=['Bronze'], color='#a97142').hide(axis="index").apply(highlight, axis=1)
styled_df.to_html('resources/medals_by_country.html')
```

### 14. Analyze Hosting vs Non-Hosting Performance
The notebook analyzes the host country's performance in hosting and non-hosting years.

```python
hosting_summer_df = summer_df[summer_df["City"] == host_city]
hosting_summer_df = hosting_summer_df[hosting_summer_df["NOC"] == host_NOC]
hosting_summer_medals_df = hosting_summer_df.drop(columns=["Name", "Sex", "Age", "ID", "Height", "Weight", "Games", "Sport", "Event"]).dropna(subset=["Medal"]).reset_index(drop=True)
hosting_summer_medals_df = hosting_summer_medals_df.groupby(['Year', 'Medal']).size().unstack(fill_value=0)
hosting_summer_medals_df['Total Medals'] = hosting_summer_medals_df.sum(axis=1)
avg_hosting_summer_medals_df = hosting_summer_medals_df['Total Medals'].mean()

nonhosting_summer_df = summer_df[summer_df["City"] != host_city]
nonhosting_summer_df = nonhosting_summer_df[nonhosting_summer_df["NOC"] == host_NOC]
nonhosting_summer_medals_df = nonhosting_summer_df.drop(columns=["Name", "Sex", "Age", "ID", "Height", "Weight", "Games", "Sport", "Event"]).dropna(subset=["Medal"]).reset_index(drop=True)
nonhosting_summer_medals_df = nonhosting_summer_medals_df.groupby(['Year', 'Medal']).size().unstack(fill_value=0)
nonhosting_summer_medals_df['Total Medals'] = nonhosting_summer_medals_df.sum(axis=1)
avg_nonhostingsummer_medals_df = nonhosting_summer_medals_df['Total Medals'].mean()
```

### 15. Generate and Save Plot
A plot is generated to compare the host country's average medal counts in hosting and non-hosting years.

```python
fig, ax = plt.subplots()
fig.set_size_inches(10, 6)

ax.axhline(y=avg_hosting_summer_medals_df, color='b', linestyle='-', label='Hosting Average Medals')
ax.axhline(y=avg_nonhostingsummer_medals_df, color='r', linestyle='-', label='Non-Hosting Average Medals')
ax.set

## Contributing

- Richard Lankford
- Rolando De La Torre
- Chris Bolger
- Brandi Berry

Dataset contributed by: https://www.randigriffin.com/ and can be found at https://www.kaggle.com/datasets/heesoo37/120-years-of-olympic-history-athletes-and-results/data?select=athlete_events.csv


