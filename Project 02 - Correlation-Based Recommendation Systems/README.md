# Project 02 - Correlation-Based Recommendation Systems

**Correlation-Based Recommendation Systems** offer a basic form of collaborative filtering. It takes user preferences into account and recommends items based on similarties in their user view.

In this section, we will use [Pearson's R Correlation](https://www.wikiwand.com/en/Pearson_correlation_coefficient) to find out the item that is most similar to the item another users have already chosen. In other words:

> We recommend an item that has a review score that correlates with another item that a user has already chosen.

## Refresh on Pearson's R Correlation Coefficient

The first question is how to define the Item-Based Similarity? The Pearson's R Correlation Coefficient is a measure of linear correlation between two variables. (In this case, two items ratings)

- `r = 1` → Strong positive linear relationship
- `r = 0` → Not linearly correlated
- `r = -1` → Strong negative linear relationship

## Program Flow

### 01. Load and Observe Data

- Use standard module `pathlib` deal with the path problem under different operating system.
- Use `pd.read_csv()` method to read csv files.
- Use `pd.DataFrame.head(self, n)` method show first `n` rows (show 5 rows in default).

```python
# import packages
from pathlib import Path

import numpy as np
import pandas as pd

# declare constants
DATA_FOLDER = './data'

# load data
geo_data = pd.read_csv(Path(f'{DATA_FOLDER}/geoplaces2.csv'))
rating_data = pd.read_csv(Path(f'{DATA_FOLDER}/rating_final.csv'))
cuisine_data = pd.read_csv(Path(f'{DATA_FOLDER}/chefmozcuisine.csv'))

# explore data
geo_data.head()
rating_data.head()
cuisine_data.head()
```

### 02. Retreive the Needed Data

Retreive the `id` and `name` column since we dont' need all of the attributes in the `geo_data` dataframe.

```python
# Retreive only `id` and `name` column
places_data = geo_data[['placeID', 'name']]
places_data.head()
```

### 03. Grouping and Ranking Data

- Use [`DataFrame.groupby()`](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.groupby.html) method and [`GroupBy.mean()`](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.core.groupby.GroupBy.mean.html) method to calculate the mean ratings of each place.

```python
# grouping and ranking data with rating
rating = pd.DataFrame(rating_data.groupby('placeID')['rating'].mean())
rating['rating_count'] = pd.DataFrame(rating_data.groupby('placeID')['rating'].count())
rating.sort_values('rating_count', ascending=False).head()

rating.describe()

# According to the data above, we know the most popular restaurant is place 135085
# Get its name and top cuisine
places_data[places_data['placeID']==135085]
cuisine_data[cuisine_data['placeID']==135085]
```

### 04. Preparing Data for Analysis

- Use [`pandas.pivot_table()`](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.pivot_table.html) method to build a user by item utility matrix.

```python
# create the user-item martix
places_crosstable = pd.pivot_table(data=rating_data, values='rating', index='userID', columns='placeID')
places_crosstable.head()

# Exmaple for finding places that are correlated
Tortas_ratings = places_crosstable[135085]
Tortas_ratings[Tortas_ratings>=0]
```

### 05. Evaluating Similarity Based on Correlation

- Use [DataFrame.corrwith()](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.corrwith.html) to calculate the correlations

```python
# calculate correlations
corr_Tortas = pd.DataFrame(places_crosstable.corrwith(Tortas_ratings), columns=['PearsonR'])
corr_Tortas.dropna(inplace=True)
corr_Tortas.head()

# retrieve similar place by correlation
Tortas_corr_summary = corr_Tortas.join(rating['rating_count'])
Tortas_corr_summary[Tortas_corr_summary['rating_count']>=10].sort_values('PearsonR', ascending=False).head(10)

# find out the places and cuisines
places_corr_Tortas = pd.DataFrame([135085, 132754, 135045, 135062, 135028, 135042, 135046], index = np.arange(7), columns=['placeID'])
summary = pd.merge(places_corr_Tortas, cuisine_data,on='placeID')
summary

places_data[places_data['placeID']==135046]
cuisine_data['Rcuisine'].describe()
```

## Summary

The concepts of Correlation-Based Recommendation Systems are:

- Recommend the items that have review scores that correlates with another item that a user has already chosen.
- Find the similar with Pearson's R Correlation Coefficient
- Takes the data of users into account.